---
title: "Day 12 – Sprite tooling, template upgrades & VPS automation"
date: 2026-02-15
tags: ["mo5", "template", "c", "cmoc", "graphic"]
description: "Day 11 : Sprite tooling, template upgrades & VPS automation"
draft: false
weight: 13
translationKey: "day-12"
---

En ce nouveau jour de **MO5 coding**, j’ai fait pas mal de trucs, et surtout pas mal d’expérimentations autour du mode graphique.

## Génération de sprites à partir d’images

Je me suis attaqué à un problème assez classique : **dessiner des sprites en C pour le MO5**, c’est… comment dire… pas ce qu’il y a de plus fun 😅

Au début, j’étais parti sur une approche très “artisanale” : dessiner en mode texte via Notepad, avec un espace pour la couleur de fond, un `X` pour dessiner la couleur de forme, puis utiliser un script PowerShell pour transformer le texte en tableau de bits.

Franchement, ça marchait plutôt pas mal pour des formes simples.  
Mais dès qu’on veut faire quelque chose d’un peu détaillé… ça devient vite pénible et pas super pratique.

Du coup, je me suis dit :  
> Pourquoi ne pas partir directement d’une image ?

L’idée : analyser une image et la convertir en **structure C exploitable** dans mon programme.

Pour faire ça, j’ai encore utilisé un script PowerShell pour transformer l’image en structure C. Concrètement, la structure générée est un **tableau de bits**, où chaque bit représente un pixel.

Et sur ce coup-là, c’est **Claude.io** qui m’a bien aidé pour la partie analyse d’image + conversion.

Je susi assez fier du résultat, certe je script pourrait être amélioré, mais franchement, il fait bien le job.
Il prend aussi les contraintes du MO5 en compte: 2 couleurs (fond et forme) pour 8 pixels.

## Intégration dans mon SDK

Comme je voulais éviter de copier ce script dans tous mes projets, je l’ai directement intégré dans mon projet mo5_sdk (https://github.com/thlg057/sdk_mo5).

Ensuite, comme ce SDK est utilisé par mon template (https://github.com/thlg057/mo5_template), j’ai juste eu besoin d'ajouter une **target `convert` dans le Makefile**.

Résultat :
- je balance une image dans le projet,  
- je lance `make convert` en lui précisant le nom,  
- ça me génère automatiquement le `.h` correspondant.

Plutôt pratique 👌

Évidemment, j’ai aussi modifié le SDK pour ajouter les fonctions nécessaires à l’affichage du sprite généré (mo5_sprit.h/.c).

Et globalement… ça fonctionne pas trop mal 😉

## Tests dans GitHub Codespaces

Ensuite, j’ai voulu tester mon template dans un environnement clean.

Direction : **GitHub Codespaces**.

Et franchement : super expérience. Je n’avais jamais utilisé avant, et j’ai été plutôt bluffé par cet environnement, simple mais effroyablement efficace.

J’ai juste eu à installer cmoc, python, et hop, ça a marché (presque du premier coup).

Ça m’a surtout permis de :
- valider que mon template est portable,  
- débugger 2–3 trucs dans le SDK,  
- vérifier que l’onboarding est simple.

## Idée de vidéo YouTube

Du coup, je pense que je vais faire une vidéo pour montrer :
- comment configurer l’environnement MO5,  
- comment utiliser le template,  
- comment faire un premier programme graphique,  
- le tout directement dans un Codespace GitHub.

Je pense que ça peut aider pas mal de monde à se lancer sans galérer avec la config locale.

## Documentation & scripts VPS

En parallèle, j’ai aussi bossé sur d’autres sujets.

### Amélioration de la doc MO5

J’ai continué à améliorer la documentation Markdown :
- clarifications,  
- exemples,  
- structure plus propre.

C’est pas la partie la plus fun, mais c’est indispensable.

### Script de mise à jour VPS

J’ai aussi écrit un script PowerShell pour gérer les mises à jour de mon VPS qui héberge mon **RAG**.

Le repo ne contient aucun credential, mais je ne l’ai quand même pas mis en public, pour des raisons évidentes de sécurité.

J’en ai profité pour :
- ajouter des **security headers**,  
- viser un grade **A** sur securityheaders.com,  
- automatiser le déploiement.

Et maintenant le script peut :
- déployer sur mon VPS,  
- ou en local sur mon NAS.

Franchement, ça me simplifie bien la vie.

## Prochaines étapes

À court terme :

➡️ Faire une vidéo montrant comment créer un premier programme graphique MO5 dans GitHub Codespaces.

Ensuite, je pense attaquer quelque chose de plus fun :

🎮 **Programmer un jeu en vibe coding**

Avec une idée en tête : lier Augment à mon serveur MCP.

Je suis curieux de voir jusqu’où je peux pousser le workflow et le vibe coding avec un contexte approprié.

---

Encore une journée bien remplie.

Pas mal d’outillage, moins de “visible”… mais des fondations solides pour la suite 🚀