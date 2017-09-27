---
layout: post
title: "Octopress"
date: 2013-08-02 12:00
tags: octopress
permalink: 2013/08/02/octopress
thumbnail: code
categories: blog
---

Comme je l'ai dit dans mon [premier article](/blog/2013/07/31/premier-article-parce-quil-en-faut-un/), ce blog est réalisé avec [Octopress](http://octopress.org), un framework de génération de blog (lui même basé sur Jekyll). Je vais vous expliquer ici pourquoi j'ai choisi ce framework.
<!-- more -->

## A propos de Octopress

Octopress est basé sur [Jekyll](https://github.com/mojombo/jekyll), un générateur de sites statiques développé en Ruby. A partir de pages en Markdown, Octopress génère le code HTML du projet en une commande, et permet ensuite le déploiement sur le serveur cible.

Avantage : le site est 100% statique, et peut encaisser un nombre de connexion très important sans broncher (à condition que la connexion soit suffisante).

Octopress est un projet Open Source, dont le code est disponible sur [Github](https://github.com/imathis/octopress/), libre à vous de le modifier, proposer des améliorations. De plus, une communauté s'est développée autour du projet, et de nombreux tutoriels ont pu naître ([ici](http://techilm.com/blog/2012/07/23/creer-un-blog-sous-octopress-partie-2-tuto/) ou [là](http://webdesign.tutsplus.com/tutorials/applications/getting-started-with-octopress/)), ainsi qu'un grand nombre de plugins et de thèmes.

## A l'utilisation, &ccedil;a donne quoi ?

Pour créer un article, il suffit de lancer une simple commande (<code>rake new_post['Titre de l'article']</code>), et le fichier Markdown est directement généré. Il ne reste plus qu'à ajouter le contenu. En mode rédaction, il est possible de lancer le mode preview, disponible sur le port 4000 du serveur, qui surveille les modifications faites au projet, et re-génère le site à chaque modification. Lorsque l'article est termin&eacute, il n'y a plus qu'à générer le projet (<code>rake generate</code>), et déployer sur le serveur distant (<code>rake deploy</code>).

L'utilisation est très simple, on s'y habitue très vite. Certains pourraient rebuter le fait d'avoir besoin d'un terminal à disposition, comparé à d'autres systèmes de blogging (Wordpress notamment...), mais cela reste annecdotique.

Par la suite, vous pouvez ajouter un système de commentaires via le service [Disqus](http://www.disqus.com), ou encore changer de thème en choisissant parmi la multitude de thèmes disponibles sur internet, ou en créant le vôtre. Personnellement, je suis parti sur le thème [Greyshade](https://github.com/shashankmehta/greyshade), et légèrement modifié (et d'autres modifications arriveront surement).

Si vous comptez faire votre blog et que votre objectif est de proposer quelque chose de simple, alors Octopress est fait pour vous. Si vous voulez tester, la documentation est plutôt bien fournie, et l'installation bien expliquée.

Sur ce, bons tests !
