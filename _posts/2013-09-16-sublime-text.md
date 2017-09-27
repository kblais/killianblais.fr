---
layout: post
title: "Sublime Text"
date: 2013-09-16 17:00
tags: sublimetext
description : Sublime Text est un éditeur de texte simple en apparence, mais d'une puissance assez folle. Pourquoi passer à Sublime Text plutôt que Notepad ou Eclipse ? Je vais vous le dire ici. Je parlerais également des différents plugins que j'utilise quand je code.
thumbnail: code
categories: code
permalink: 2013/09/16/sublime-text
---

Sublime Text est un éditeur de texte simple en apparence, mais d'une puissance assez folle. Pourquoi passer à Sublime Text plutôt que Notepad ou Eclipse ? Je vais vous le dire ici. Je parlerais également des différents plugins que j'utilise quand je code.<!-- more --> Disponible sous Linux, Windows et MacOS, il est utilisable gratuitement, le seul point négatif étant l'affichage d'une popup après un certain nombre de sauvegardes (avis à ceux qui ont le CTRL+S facile). Si vous souhaitez soutenir l'auteur, ou que vous en avez marre de la popup, vous pouvez acheter une licence, au prix de 70$ (il est aussi possible d'acheter une grande quantité de licence, faisant baisser le prix).

## Plus qu'un éditeur de texte

J'ai découvert Sublime Text durant mon stage de fin de DUT, via mon tuteur de stage, [Mathieu Le Gac](http://www.twitter.com/mateoelgaco), et alors que je me prenais la tête sur Eclipse et sa lenteur. Passer à Sublime pour développer en PHP a été une libération ! Un démarrage en quelques secondes au lieu de 2 minutes, de la légèreté dans l'interface (sérieusement, vous n'en avez pas marre des interface avec des boutons partout, dont certains n'ont aucune utilité pour le projet en cours ?), et de la légèreté dans l'utilisation, avec une réactivité au top. Qui plus est, Sublime Text inclut de nombreux petit bonus qui facilitent réellement l'utilisation, comme des raccourcis clavier pour la suppresssion de lignes, le passage en commentaires ou la sélection multiple, ou encore les snippets, de petits morceaux de code réutilisables et totalement personnalisables.

<img src="/images/sublime-command-palette.png" class="left" alt="La palette de commande de Sublime">
Certains diront "Oui, mais là tu compares un éditeur de texte avec un IDE, c'est complètement différent !". Et à ces personnes, je n'ai qu'une chose à répondre : oui, mais non ! Parce qu'en plus de gérer une multitude de langages différents (qui vont de HTML, CSS ou PHP à Erlang, Lisp ou Haskell), il est possible d'ajouter de nombreux packages pour rendre le développement plus simple et ajouter de nombreuses fonctionnalités. De plus, Sublime comprend, de base, une palette de commandes. Via un simple raccourcis clavier (CTRL+Shift+P), on accède à toutes les fonctions proposées par Sublime pour le fichier actuel : changement de syntaxe, accès aux snippets de la syntaxe... Mais aussi les fonctions des plugins. Autres raccourcis plutôt utiles, CTRL+R qui permet de se déplacer à la fonction souhaitée dans le fichier directement en entrant le nom de la fonction, et CTRL+P qui permet d'accéder aux fichiers du projet sans passer par l'arborescence. Et il y en a encore beaucoup d'autres !

Il est également possible d'utiliser les systèmes de build inclus dans Sublime pour certains langages (C++, Python...), ou de créer sont propre système de build si vous utilisez un autre langage, ou si la manière de Sublime ne vous plait pas. Pour ma part, je n'utilise que très peu ce système, développant essentiellement en PHP, mais j'ai récemment créé un petit script de build pour le langage ADA (vestige de mon DUT). On peut également utiliser ce système pour des langages tels que Markdown, LESS, Latex, etc... pour compiler son code à la volée.

Aujourd'hui, Sublime Text est disponible en version 2, la version stable, ainsi qu'en version 3, récemment ouverte au public en beta. Pour ma part, je suis passé en version 3 dès sa sortie, de nombreuses améliorations ayant été mises en place.

## Les plugins

Sublime Text vous permet assez facilement d'ajouter des plugins, afin d'ajouter des fonctionnalités à l'éditeur. Pour les installer, il suffit de télécharger le plugin, et de le placer dans le dossier "Installed Packages" de la configuration de Sublime (sous Debian, ce sera `~/.config/sublime-text-3/Installed Packages`), et le package sera ensuite activé automatiquement. Mais afin de faciliter cette manipulation, [Will Bond](http://wbond.net) a développé le plugin [Package Control](https://sublime.wbond.net/). Celui ci permet d'installer des milliers de plugins directement depuis la palette de commande, via le menu _Package Control : Install Package_. Pour l'installer, ouvrez l'invite de commande de Sublime Text (_View_ &rarr; _Show Console_), et collez le code suivant (pour Sublime Text 3) :

`import urllib.request,os; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); open(os.path.join(ipp, pf), 'wb').write(urllib.request.urlopen( 'http://sublime.wbond.net/' + pf.replace(' ','%20')).read())`

Puis redémarrez Sublime.

Pour ma part, j'ai une vingtaine de plugins d'installés, dont voici les plus intéressants :

- Emmet et Emmet CSS Snippet, qui permettent d'apporter le [Zen Coding](http://docs.emmet.io/) dans Sublime Text.
- SFTP, pour lier un projet local avec un serveur FTP/SFTP
- DocBlokr, qui permet de créer les blocs réservés à la documentation pour plusieurs langages (notamment PHP et JavaScript)
- ColorPicker, qui comme son nom l'indique, permet d'afficher un sélecteur de couleur via CTRL+Shift+C
- Git, qui ajoute le support de Git directement dans la palette de commandes
- GitGutter, qui affiche dans la goutière les modifications/ajouts/suppressions en comparant le fichier au dernier commit
- MarkdownPreview, qui ajoute la compilation pour Markdown (via le raccourcis CTRL+B)
- Less2Css, qui ajoute la compilation pour Less

De plus, j'utilise le thème Flatland Dark, avec la coloration Monokai, que je trouve plus agréable à utiliser.

## Petite astuce

Il est assez fréquent que les conventions de codage demandent à ce que le dernier caractère d'un fichier soit une nouvelle ligne. Sublime permet facilement d'ajouter cette ligne automatiquement !
Pour cela, ajoutez ceci dans votre fichier de préférences utilisateur (CTRL+Shift+P &rarr; _Preferences : Settings - User_) : `"ensure_newline_at_eof_on_save": true`

Ainsi, une nouvelle ligne sera ajoutée à chaque sauvegarde, si elle n'est pas déjà présente bien entendu. Si vous tenez à n'utiliser cette règle que pour un langage donné, ajoutez cette ligne au fichier du langage (_Preferences_ &rarr; _Settings : More_ &rarr; _Syntax Specific - User_)

## En bref...

Sublime est un excellent éditeur de texte, et peut remplacer, pour certains langages, les mastodontes que sont Eclipse ou Netbeans. Si vous cherchez quelque chose de léger, passez à Sublime, vous gagnerez en temps et en productivité. Sinon, essayez le, ça ne coûte rien !

Pour le téléchargement, ça se passe sur [www.sublimetext.com](http://www.sublimetext.com/3) (ou [ici](http://www.sublimetext.com/2) pour ceux qui souhaitent utiliser la version 2).
