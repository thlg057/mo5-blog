---
title: "Day 15 : J'ai arrêté de me battre avec le MO5"
date: 2026-03-22
tags: ["AI", "SDK", "MO5", "VibeCoding", "DevOps", "MCP"]
description: "Marre de configurer des compilateurs et de compter des octets ? J'ai fini par monter une petite usine à gaz pour qu'on puisse enfin s'amuser."
draft: false
weight: 16
translationKey: "day-15"
---

Si vous avez suivi mes précédentes aventures, vous savez que coder sur MO5, c'est... sport ;)

Entre les outils qui datent d'une autre époque, les contraintes hardware pas toujours documentées, et les comportements différents entre émulateur et vraie machine, j'ai passé pas mal de temps à me battre avec mon environnement.

À un moment, j'ai réalisé un truc simple : je passais plus de temps à réparer mes outils qu'à coder mon jeu, et ça, c'est frustrant.

## Le vrai problème

Au début, je pensais que le problème venait du MO5 : Machine ancienne, contraintes fortes, logique.

Mais en réalité, le problème venait surtout de moi : je développais comme en 1985... en 2026

- Installation manuelle
- scripts bricolés
- debug à l'aveugle

Alors qu'aujourd'hui on a : 
- du cloud
- des pipelines automatisés
- des IA capables de coder

Il y avait clairement un décalage.

## Du bricolage à un vrai SDK

Du coup, j'ai pris une décision radicale : arrêter de bricoler, et construire un vrai socle

J'ai regroupé tout ce que je faisais dans un SDK.\
L'idée n'était pas de faire une lib parfaite, juste un truc qui : 
- cache la complexité du hardware, 
- évite les pièges classiques, 
- permet de se concentrer sur le jeu.

Par exemple, toute la gestion vidéo est encapsulée. Plus besoin de manipuler directement les banques mémoire ou le registre
\$A7C0.

Tu initialises l'écran, tu attends le VBL, et tu dessines.

``` c
mo5_video_init(COLOR(C_BLACK, C_BLACK));
while (1) {
    mo5_wait_vbl();
    // logique + rendu
}
```

Simple 😉

## Les sprites sans prise de tête

Là où j'ai vraiment gagné du temps, c'est sur les sprites.

Entre les contraintes du MO5, la gestion des couleurs et la VRAM, c'est vite le bazar.

Du coup j'ai plusieurs niveaux : sprites simples sur fond uni, sprites transparents sur décor, sprites avec sauvegarde du fond (dirty rectangle).

Le mode dirty rectangle est probablement le plus intéressant (mais le plus coûteux en terme de perf). Avant de dessiner, on sauvegarde la zone, puis on la restaure à la frame suivante.\
Résultat, les sprites peuvent se chevaucher sans artefacts, et ça, sur MO5, c'est pas trivial du tout.

## Le vrai hack : les assets

Mais le plus gros gain de productivité vient d'un truc tout bête : arrêter de dessiner des sprites à la main.

J'ai écrit un script qui transforme un PNG en structure C directement utilisable.

Et surtout... je génère mes sprites (enfin les images) avec Claude.

Je demande à claude de me dessiner une image (avec les contraintes), je la passe dans le script, je compile, et le sprite apparaît à l'écran.

Sans me soucier du format mémoire, des contraintes de couleur, ou du layout VRAM.

Franchement, c'est assez magique 😉

## Le template du feignant

Ensuite je me suis attaqué à un autre problème.

👉 l'installation

Parce que réinstaller cmoc, lwtools et compagnie à chaque machine, c'est non.

Du coup j'ai fait un template + Codespaces.

``` bash
make setup-codespace
make install
make
```

Et voilà.

- environnement prêt
-  binaire généré
- image disque prête

Tout ça dans Github codespace.

## L'IA qui comprend enfin le MO5

Au début, j'utilisais l'IA comme tout le monde.

Je lui posais des questions, elle répondait... plus ou moins correctement.

Le problème, c'est qu'elle ne comprend pas les contraintes du MO5.

Du coup j'ai fait un truc un peu différent : J'ai branché un serveur MCP avec un RAG derrière.\
J'y ai injecté la doc de mon SDK, les contraintes du 6809, et mes propres notes.

👉 maintenant l'IA ne devine plus\
👉 elle sait

Et ça change tout.

## Et maintenant ?

Bon... C'est bien beau tout ça... Mais la vraie question c'est :

👉 est-ce que ça marche vraiment ?

C'est ce que je vais tester (en fait, à l'heure où j'écris cet article, je l'ai déjà testé 😉).

Je me lance dans une série de vidéos pour construire un jeu complet étape par étape.

Un petit shoot'em up classique, avec un vaisseau, des ennemis, des tirs, un score.

En partant de zéro.

Juste avec le template, le SDK... et un peu (beaucoup) d'IA 😉


## Conclusion

Je ne pensais pas dire ça un jour.

Mais aujourd'hui... coder sur MO5 devient presque moderne !

Et surtout, ça devient vraiment fun 😉
