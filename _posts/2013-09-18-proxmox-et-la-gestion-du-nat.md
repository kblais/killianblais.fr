---
layout: post
title: "Proxmox et la gestion du NAT"
date: 2013-09-18 16:40
categories: proxmox
tags: [proxmox, serveur]
description: Comment faire pour gérer facilement et rapidement le NAT sur une distribution Proxmox ?
thumbnail: server
permalink: 2013/09/18/proxmox-et-la-gestion-du-nat
---

Fin juillet, [OVH](http://ovh.com) a inauguré sa nouvelle gamme de serveur low-cost, les [Kimsufi](http://www.kimsufi.fr). A partir de 2€99 HT le serveur dédié, je n'ai pas hésité longtemps avant de prendre le mien. Mais quelle distribution utiliser ? Je me suis tourné vers Proxmox, distribution de virtualisation libre qui permet l'utilisation de containers OpenVZ pour virtualiser une distribution Linux. Mais avec une seule IP, le seule solution pour accéder aux containers est le NAT. Comment gérer correctement son NAT ?<!-- More -->

## Configuration de base

D'abord, la création des containers. Chaque container se voit attribuer une adresse locale (pour ma part, je suis parti sur un réseau 10.0.0.0/32), accessible depuis la machine hôte. Il faut, bien évidemment, avoir un accès en SSH sur l'hôte.

Ici, l'IP externe du serveur hôte sera 16.32.64.128, tandis que les IP des containers seront 10.0.0.1, 10.0.0.2 et 10.0.0.3, avec ce type de fonctions:
- 10.0.0.1 est un serveur web, avec un Apache et un serveur MySQL
- 10.0.0.2 est un serveur mail, utilisant PostfixAdmin pour la gestion des comptes
- 10.0.0.3 est un serveur de torrent basé sur transmission, pour télécharger vos distributions Linux favorites

## La gestion classique

La solution la plus simple, et la première envisagée, est la création d'un script se lançant au démarrage de la machine pour mettre en place les différentes règles d'iptables.

Un petit exemple :

	#!/bin/sh
	iptables -t filter -F
	iptables -t filter -X
	iptables -t filter -P INPUT DROP
	iptables -t nat -D PREROUTING -p tcp -d 16.32.64.128 --dport 4222 -i vmbr0 -j DNAT --to-destination 10.0.0.1:22
	iptables -t nat -D PREROUTING -p tcp -d 16.32.64.128 --dport 80 -i vmbr0 -j DNAT --to-destination 10.0.0.1:80
	iptables -t nat -D PREROUTING -p tcp -d 16.32.64.128 --dport 6322 -i vmbr0 -j DNAT --to-destination 10.0.0.2:22
	iptables -t nat -D PREROUTING -p tcp -d 16.32.64.128 --dport 6380 -i vmbr0 -j DNAT --to-destination 10.0.0.2:80
	iptables -t nat -D PREROUTING -p tcp -d 16.32.64.128 --dport 993 -i vmbr0 -j DNAT --to-destination 10.0.0.2:993
	iptables -t nat -D PREROUTING -p tcp -d 16.32.64.128 --dport 465 -i vmbr0 -j DNAT --to-destination 10.0.0.2:465
	iptables -t nat -D PREROUTING -p tcp -d 16.32.64.128 --dport 8422 -i vmbr0 -j DNAT --to-destination 10.0.0.3:22
	iptables -t nat -D PREROUTING -p tcp -d 16.32.64.128 --dport 8480 -i vmbr0 -j DNAT --to-destination 10.0.0.3:80
	iptables -t nat -D PREROUTING -p tcp -d 16.32.64.128 --dport 9091 -i vmbr0 -j DNAT --to-destination 10.0.0.3:9091

On lance le script, et voilà, le NAT est en place, on peut accéder à ses serveurs sans problème.

Mais voilà, ce type de fichier n'est pas très propre, et peut rapidement devenir illisible. Alors comment faire pour arranger ça ?

## Utiliser un fichier externe !

Durant mes recherches, je suis tombé sur un [script d'Alexis Ducastel](http://www.area51.fr/2010/11/08/proxmox-nat-port-forwarding/), qui permettait une gestion du NAT en utilisant un fichier de configuration externe. Bien plus lisible, et plus simple à modifier, je l'ai repris et adapté pour permettre un vidage des règles iptable plus simple.

	#!/bin/bash

	function addNat {
	    iptables -t nat -A PREROUTING -p tcp -d $1 --dport $2 -i vmbr0 -j DNAT --to-destination $3:$4
	}

	function removeNat {
	    iptables -t nat -D PREROUTING -p tcp -d $1 --dport $2 -i vmbr0 -j DNAT --to-destination $3:$4
	}

	case "$1" in
	start) echo "Starting iptables NAT for openvz"
	    iptables -t nat -F
	    iptables -t nat -X
	    iptables -t nat -Z
	    iptables -t nat -A POSTROUTING -s 10.0.0.0/24 -o vmbr0 -j SNAT --to 37.187.21.62

	    if [ -f /etc/proxmox-nat.conf ]
	    then
	        # Epuration du fichier de configuration
	        cat /etc/proxmox-nat.conf | grep -v "^\s*#" | grep -v "^\s*$" |sed -e 's/^\s*//g' > /tmp/nat.$$.txt
	        while IFS=:- read ipout portout ipin portin
	        do
	            addNat $ipout $portout $ipin $portin
	        done < /tmp/nat.$$.txt
	        rm /tmp/nat.$$.txt
	    fi
	    ;;
	stop) echo "Stopping iptables NAT for openvz"
	    if [ -f /etc/proxmox-nat.conf ]
	    then
	        # Epuration du fichier de configuration
	        cat /etc/proxmox-nat.conf | grep -v "^\s*#" | grep -v "^\s*$" |sed -e 's/^\s*//g' > /tmp/nat.$$.txt

	        while IFS=:- read ipout portout ipin portin
	        do
	            removeNat $ipout $portout $ipin $portin
	        done < /tmp/nat.$$.txt

	        rm /tmp/nat.$$.txt
	    fi
	    ;;
	*) echo "Usage: /etc/init.d/nat-fw {start|stop}"
	    exit 2
	    ;;
	esac
	exit 0

Et voici la configuration, écrite dans le fichier _/etc/proxmox-nat.conf_

	# Usage :
	#	ip_outside:port  ip_inside:port
	16.32.64.128:4222		10.0.0.1:22
	16.32.64.128:80			10.0.0.1:80
	16.32.64.128:6322		10.0.0.2:22
	16.32.64.128:6380		10.0.0.2:80
	16.32.64.128:993		10.0.0.2:993
	16.32.64.128:465		10.0.0.2:465
	16.32.64.128:8422		10.0.0.3:22
	16.32.64.128:8480		10.0.0.3:80
	16.32.64.128:9091		10.0.0.3:9091

Et voilà, il suffit ensuite d'activer le NAT, et le tour est joué !

## La problématique du serveur web

"C'est bien beau tout ça, mais j'aimerais bien pouvoir accéder directement à Postfixadmin quand je tape mail.example.net dans mon navigateur, et pas devoir ajouter le numéro de port" (oui, parfois, il arrive d'être flemmard).

Dans ce cas de figure, il devient nécessaire de mettre en place un reverse proxy, ce qui fera l'objet de mon prochain article.
<br/>
__J'espère ce que cet article aura été utile. Si vous avez une question, je suis disponible sur Twitter pour vous répondre. Codez bien !__
