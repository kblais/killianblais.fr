---
layout: post
title: "Astuce sur SSH"
date: 2014-06-25 14:04
tags: [proxmox, ssh, tip]
description: Quelques petites astuce avec SSH pour simplifier les connexions à ses machines virtuelles
permalink: 2014/06/25/astuce-sur-ssh
thumbnail: terminal
categories: server
---

Pour ce premier article de l'année (outch, on est déjà fin juin...), je vais vous parler de quelques astuces que j'utilise pour me connecter à mes machines virtuelles, toujours installée derrière un NAT.
<!-- more -->

Tout d'abord, ce que je vais proposer ici est uniquement testé sur Debian. Cela devrait fonctionner sans trop de soucis sur les autres distributions Linux, et si je ne me trompe pas sur Mac également (à confirmer). Sous Windows, mis à part en utilisant un émulateur de console bash, ce n'est pas faisable.

## Le problème

Le problème est assez simple : pour des raisons de sécurité, je n'aime pas ouvrir trop de ports externes sur mon serveur. Or, lorsqu'on utilise Proxmox et que l'on souhaite se connecter en SSH à une VM (admettons que l'IP de la VM est 10.0.0.1), on est obligé soit de se connecter à la machine hôte puis à la VM, soit d'ouvrir un nouveau port externe en paramétrant son NAT correctement (voir mon [article précédent](/blog/2013/09/18/proxmox-et-la-gestion-du-nat/) sur une gestion du NAT avec Proxmox), avec pour résultat autant de port dédiés au SSH ouverts que de VM.

Dans le premier cas, se connecter à une VM est chiant (surtout lorsque la connexion internet n'est pas stable), et dans le second cas, ouvrir un accès direct à sa VM n'est pas une bonne idée. Je me suis donc mis à éplucher internet à la recherche d'une solution plus "propre", et j'ai fini par trouver !

## Une solution

La solution la plus simple que j'ai pu trouver (et la plus propre selon moi), c'est d'utiliser le fichier ``~/.ssh/config``, qui sert à définir des options pour les différentes connexion SSH. Pour l'exemple, je vais reprendre les IP de mon article sur le NAT, avec un serveur hôte en 16.32.64.128 et une VM en 10.0.0.1 (un serveur web par exemple) que je nommerais ``web``.

On créé donc le fichier ``~/.ssh/config``, dans lequel on va ajouter le contenu suivant :

	Host web
		Hostname 10.0.0.1
		Port 22
		ProxyCommand ssh -p 22 -t -q -W %h:%p 16.32.64.128
		IdentityFile ~/.ssh/id_rsa

Petite explication de ces 5 lignes :

``Host web`` : on définit un alias à notre serveur. Ainsi, plus besoin d'entrer une adresse IP lorsqu'on souhaite se connecter au serveur, il suffit d'utiliser la commande ``ssh web``.

``Hostname 10.0.0.1`` : Il s'agit de l'adresse IP de la VM à laquelle on souhaite se connecter (IP dans le réseau local donc)

``Port 22`` : port à utiliser pour se connecter au serveur

``ProxyCommand ssh -p 22 -t -q -W %h:%p 16.32.64.128`` : cette ligne permet d'envoyer toutes les entrées/sorties vers/depuis la VM (%h et %p étant replacés par les valeurs de Host et Port), en mode "quiet" (-q, pas d'avertissement de la part sur serveur hôte concernant la connexion)

``IdentityFile ~/.ssh/id_rsa`` : définit la clé RSA à utiliser pour la connexion (la clé publique étant déjà présente sur le serveur hôte et sur la VM)

Cette petite astuce permet de limiter l'accès en SSH par un accès au serveur hôte, et donc mieux sécuriser votre serveur.

__Voilà, cet article touche à sa fin. Si vous avez des questions, des remarques, ou des critiques, n'hésitez pas à me contacter sur [Twitter](https://twitter.com/killianblais).__

__Have fun ! Et surtout, profitez du soleil (tant qu'il est là)__
