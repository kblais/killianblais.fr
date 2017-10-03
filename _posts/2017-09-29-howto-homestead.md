---
layout: post
title: "Howto : Homestead"
date: 2017-09-29 15:00
tags: [homestead, php]
description: ""
permalink: 2017/09/29/howto-homestead
thumbnail: terminal
categories: PHP
---

## Homestead, kezako ?

Homestead est une box Vagrant intégrant un environnement de développement PHP très complet :
- PHP 5.6, 7.0, 7.1 avec Memcached
- Nginx
- MySQL, MariaDB et PostgreSQL
- Composer
- Redis
- etc...

Cette box fait partie de l'ensemble d'outils fournis par la communauté Laravel, mais reste compatible avec n'importe quelle application PHP (Symfony, Zend, ou même Wordpress).

Cet article a pour but d'expliquer comment installer et utiliser Homestead, et de donner quelques astuces pour se simplifier l'utilisation.

## Installation

Pour les différents exemples, je vais utiliser Homestead avec un projet Laravel tout juste créé :

	$ composer create-projet laravel/laravel demo-homestead
	$ cd demo-homestead

### Les prérequis

- [VirtualBox](https://www.virtualbox.org/wiki/Downloads), qui se chargera de lancer la machine virtuelle
- [Vagrant](https://www.vagrantup.com/downloads.html), qui s'occupe du provisionning de la machine virtuelle
- PHP en version 5.6 ou supérieure à 7.0 (Homestead étant un package PHP), ainsi que [Composer](https://getcomposer.org)

### Préparation

Tout d'abord, nous allons télécharger la box Vagrant :

	$ vagrant box add laravel/homestead

Il faut ensuite ajouter Homestead dans les dépendances Composer :

	$ composer require --dev laravel/homestead

L'ajout dans les dépendances a créé un éxecutable `homestead` dans le dossier `vendor/bin`. Celui-ci va nous permettre de générer les fichiers de configuration nécessaires pour faire tourner Homestead.

	$ ./vendor/bin/homestead make

Cette commande va créer les fichiers `aliases`, `after.sh`, `Homestead.yaml` et `VagrantFile`. Dans un projet Laravel, le fichier `Homestead.yaml` (ainsi que `Homestead.json`) devrait être ignoré par git, et je vous conseille de l'ignorer également dans vos projets (celui-ci contient une configuration spécifique à votre environnement, comme le chemin vers votre projet).

### Configuration

La configuration se trouve dans le fichier `Homestead.yaml`, et c'est ce fichier que vous modifierez principalement.

Par défaut, l'initialisation de Homestead a prérempli certains éléments : le chemin du dossier de votre projet, le nom de domaine par défaut et le nom de la base de données. Vous pouvez les modifier directement, et si nécessaire modifier les éléments de la configuration de votre application (identificant de base de données : `homestead`; mot de passe : `secret`).

Il ne reste plus qu'à lancer la machine virtuelle (le premier lancement peut prendre un peu de temps)
 :

	$ vagrant up

Modifiez ensuite votre fichiers `/etc/hosts` pour ajouter la redirection du nom de domaine vers l'IP de votre machine virtuelle :

	192.168.10.10  homestead.app

Votre application est maintenant disponible au nom de domaine choisi !

## Utilisation quotidienne

### Se connecter en SSH

Pour vous connecter en SSH sur la machine virtuelle :

	$ vagrant ssh

### Modifier la configuration

Si vous avez besoin de modifier la configuration de votre machine virtuelle (pour ajouter un nom de domaine, augmenter la RAM, etc...), modifiez votre fichier `Homestead.yaml`, puis lancez la commande suivante :

	$ vagrant reload --provision

## Tips & tricks

### Utiliser une autre version de PHP

Par défaut, Homestead utilise la dernière version de PHP (à l'heure à laquelle j'écris cet article, PHP 7.1). Si vous souhaitez modifier la version utilisée, ajoutez la ligne pour le site en question :

	sites:
	    - map: homestead.app
	        to: /home/vagrant/Code/Laravel/public
	        php: "5.6"

### Ne plus modifier le fichier `/etc/hosts`

Si vous aussi, vous ne voulez pas avoir à modifier manuellement votre fichier `hosts` après avoir ajouté un nom de domaine, Homestead est compatible avec le plugin Vagrant HostUpdater. Pour l'installer :

	* vagrant plugin install vagrant-hostsupdater

### Ajouter des alias shell

Vous pouvez ajouter vos alias shell directement dans le fichier `aliases` créé par l'initialisation de Homestead. Une fois ajoutés, rechargez votre configuration (`vagrant reload --provision`).

## Le mot de la fin

Voilà quelques astuces pour se faciliter la vie sur la gestion de votre environnement de développement avec Homestead. Si vous voulez voir la documentation complète, rendez-vous directement sur le site de [Laravel](https://laravel.com/docs/5.5/homestead).

N'hésitez pas à me contacter via [Twitter](https://twitter.com/{{ site.social.twitter_username }}) si vous avez des questions ! Dans un prochain article, j'évoquerais une autre solution : Docker !
