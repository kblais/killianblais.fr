---
layout: post
title: "Proxy Apache sur Proxmox"
date: 2014-06-27 15:33
tags: [proxmox, apache]
description: La mise en place d'un proxy Apache sous Proxmox
permalink: 2014/06/27/proxy-apache-sur-proxmox
thumbnail: server
categories: proxmox
---

Après mon article précédent sur la [gestion d'un NAT sur une distribution Proxmox](/blog/2013/09/18/proxmox-et-la-gestion-du-nat/), voici la suite : comment mettre en place un proxy Apache sur cette machine.<!-- more -->

En effet, dans mon exemple précédent, le serveur web d'une seule machine virtuelle est accessible directement via le port 80, les autres étant accessibles via des ports différents (6380 et 8480 pour mes exemples). Or, je souhaiterais pouvoir accéder à mon PostfixAdmin directement via mail.example.net, sans avoir à ajouter de numéro de port (un bon développeur est un développeur fénéant, non ?).

Dans ce cas, il suffit de mettre en place un proxy au niveau d'une des machines virtuelles (pour l'exemple, ce sera le serveur web, à l'IP interne 10.0.0.1). Pour cette explication, j'utiliserais Apache, n'ayant pas testé d'alternative, et utilisant d'ores et déjà Apache pour mes serveurs web.

## L'installation

Tout d'abord, il faut vérifier qu'Apache est bien installé sur votre serveur. Si ce n'est pas le cas, un petit ``apt-get install apache2`` suffit (ajoutez ``sudo`` si vous n'êtes pas connecté en tant que root).

Ensuite, il nous faut activer le module "Proxy HTTP", fourni avec Apache, mais désactivé par défaut:

	$ a2enmod proxy proxy_http
	Enabling module proxy.
	Considering dependency proxy for proxy_http:
	Module proxy already enabled
	Enabling module proxy_http.
	To activate the new configuration, you need to run:
	  service apache2 restart
	$ service apache2 restart
	[ ok ] Restarting web server: apache2 ... waiting .

Et voilà, Apache est prêt à accueillir un proxy !

## La configuration

Pour la suite, il suffit de créer de nouveaux VirtualHost, en utilisant le proxy pour rediriger les noms de domaine vers les bons serveurs. Pour ma part, je créé un fichier de configuration Apache par domaine, par soucis de lisibilité, et parce que cela permet d'activer ou désactiver un site tout en conservant les autres très facilement.

Pour commencer, on créé un fichier /etc/apache2/sites-available/mail.example.net. A l'intérieur, rien de plus simple : on va demander au module Proxy d'Apache de rediriger toutes les requêtes pour mail.example.net vers 10.0.0.3, grâce à ces quelques lignes :

	<VirtualHost *:80>
	    ServerName mail.example.net

	    ProxyPass / http://10.0.0.3:80/
	    ProxyPassReverse / http://10.0.0.3:80/
	    ProxyPreserveHost On
	</VirtualHost>

Ensuite, on active le Virtual Host, on recharche la configuration d'Apache, et le tour est joué !

	sudo a2ensite mail.example.net
	sudo service apache2 reload

Et ensuite... C'est fini ! Maintenant, si on se rend sur mail.example.net, c'est bien le serveur mail qui répondra, et plus le serveur web. Plus qu'à paramétrer le serveur mail afin de bien accéder aux bonnes ressources, et le tour est joué !

Bien entendu, il faut que les changements sur le DNS aient été fait au préalable, afin de rediriger les domaines / sous-domaines correspondants sur l'IP du serveur hôte.

__C'est la fin ! Faites moi savoir si les explications vous ont été utiles ou si vous avez des questions.__
