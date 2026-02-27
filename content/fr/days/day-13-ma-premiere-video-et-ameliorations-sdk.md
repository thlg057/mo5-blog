---
title: "Day 13: Ma première vidéo et des améliorations pour le SDK"
date: 2026-02-27
tags: ["mo5", "template", "c", "cmoc", "video", "sdk", "example"]
description: "Day 13: Ma première vidéo et des améliorations pour le SDK"
draft: false
weight: 14
translationKey: "day-13"
---

Ça y est, je me suis lancé dans les vidéos type tuto 😉

J'ai découvert très récemment **GitHub Codespaces**, et là... gros coup de cœur.

## GitHub Codespaces, le déclic

Pouvoir disposer d'un environnement Linux complet dans le cloud, accessible depuis un simple navigateur, c'est juste exceptionnel.

Pas d'installation locale compliquée, pas de dépendances exotiques qui cassent tout, pas de "ça marche sur ma machine", tout est prêt en quelques secondes.

Cette possibilité m'a donné envie de faire un tuto pour expliquer comment utiliser mon template pour développer en C pour le MO5.

Dans la vidéo, je montre toutes les étapes, configuration de l'environnement, installation de LWTOOLS et CMOC, compilation du projet de base, un simple programme qui demande un prénom et affiche "hello" + le prénom saisi, puis modification du projet pour passer en mode graphique et déplacer un sprite.

## Mise en pratique du script et du SDK

Dans mon billet précédent, je vous avais parlé de mon script qui génère une structure C à partir d'une image, ainsi que de mon SDK.

Dans cette vidéo, je mets tout ça en œuvre, génération d'un fichier `.h` à partir d'une image, puis utilisation d'une fonction du SDK capable d'afficher le sprite directement à partir de la structure de données générée.

Ça marche plutôt pas mal.

Le résultat est là: https://youtu.be/R9TeEZwZZpI?si=GKu-M0gDR0ndn-D7

Ce que je trouve vraiment sympa, c'est le workflow complet, développement dans le cloud, génération d'une image disque pour le MO5, test dans un émulateur.

Idéalement, j'aimerais pouvoir chaîner automatiquement le build de la disquette avec son ouverture dans un émulateur en ligne, pour le moment je n'ai pas encore trouvé de solution élégante, si vous avez des pistes je suis preneur 😉

## Le template évolue

Depuis la vidéo, j'ai légèrement amélioré l'utilisation du template: https://github.com/thlg057/mo5_template

J'ai ajouté une target `setup-codespace` qui permet de configurer automatiquement l'environnement Codespace, installation des packages manquants, installation de LWTOOLS et CMOC.

Si vous utilisez le template dans Codespace, en trois lignes de commande vous avez une image utilisable sur un MO5 ou dans un émulateur.

`make setup-codespace` configure l'environnement de développement

`make install` installe les outils d'Olivier P pour générer l'image disque `.fd` + mon SDK MO5, dont un script qui permet de convertir un fichier `.fd` en `.sd` pour utilisation sur un vrai MO5 depuis SDDRIVE

`make` compile et génère les fichiers `.fd` et `.sd`

Le côté "clé en main" commence vraiment à prendre forme.

## Gestion de version du SDK

J'ai aussi mis à jour mon sample de sprite :https://github.com/thlg057/mo5-sprite-for-dummy

J'étais un peu embêté, j'avais besoin de faire évoluer mon SDK, je suis en train de tenter d'écrire un petit jeu, mais je ne voulais pas que ces modifications soient utilisées automatiquement par le template ou la démo de sprite, je risquais de casser la compatibilité.

J'ai donc introduit une notion de version pour le SDK.

Au début du `make`, j'ai une variable `SDK_COMPAT_VERSION`. Elle permet d'indiquer avec quelles versions du SDK le projet est compatible.

Je tag maintenant mes versions dans Git.

Résultat, si quelqu'un utilise une certaine version du template, même en faisant un `make install` pour mettre à jour les dépendances, il ne récupérera pas une version du SDK incompatible avec son projet.

Ça fonctionne plutôt bien, et je dois avouer que je suis assez fier de ce premier mécanisme de compatibilité 😉

## Changement d'IA en vue

Je vais annuler mon abonnement à Augment Code.

Je pense passer sur Claude.ai.\
Je l'ai trouvé particulièrement pertinent, même en version web gratuite, lorsque je discutais avec lui de la conception de mon SDK.

Prochain objectif, prendre un abonnement Claude et le brancher sur mon serveur MCP pour qu'il soit encore plus pertinent pour le MO5.

Il m'a déjà pas mal aidé à comprendre certains concepts comme la synchronisation verticale, VBL pour les intimes, l'optimisation des sprites, la compression RLE, et d'autres subtilités liées aux contraintes de la machine.

## En parallèle, un petit shoot'em up

Je suis en train de développer un petit jeu type shoot'em up vertical pour MO5, ce qui m'oblige à creuser sérieusement les questions de performance, de gestion d'affichage, de synchronisation, et d'optimisation mémoire.

En parallèle, je mets à jour la documentation du RAG server avec tout ce que j'apprends.

C'est assez fascinant de voir à quel point on redécouvre des problématiques basses couches que l'on ne voit plus du tout dans le développement moderne.

Les week-ends risquent encore d'être bien remplis 😉
