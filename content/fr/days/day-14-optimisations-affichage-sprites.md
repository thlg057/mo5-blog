---
title: "Day 14: Optimisations de l'affichage des sprites"
date: 2026-03-01
tags: ["mo5", "template", "c", "cmoc", "video", "sdk", "sprite"]
description: "Day 14: Optimisations de l'affichage des sprites"
draft: false
weight: 15
translationKey: "day-14"
---

Ce weekend, j'avais l'intention de bricoler sur un petit jeu type Space Invaders.

J'avais déjà développé une fonction basique pour afficher des sprites, mais là, il me fallait quelque chose de plus propre, plus rapide, plus optimisé.

Sur le principe, l'idée était simple.

Je voulais une fonction d'affichage capable de gérer la transparence, c'est à dire ne pas toucher à la couleur de fond, et une fonction de clean qui n'aurait besoin que d'écrire 0 dans la banque forme pour effacer le sprite, sans jamais modifier la banque couleur.

Sur le papier, tout était limpide.

En pratique... beaucoup moins.

## Adaptation du script et contrainte de transparence

J'ai modifié mon script Python qui transforme un PNG en structure C, en lui ajoutant un paramètre `--transparent`.

Avec ce paramètre, le script garantit que seule la banque couleur FB est utilisée pour dessiner le sprite, concrètement cela signifie que je ne peux plus utiliser deux couleurs par bloc de 8 pixels, je ne peux en utiliser qu'une seule en plus de la couleur de fond.

L'objectif était clair, permettre un affichage rapide, compatible avec un effacement simple basé uniquement sur la banque forme.

## Week-end IA debugging

Pour m'aider dans cette optimisation, j'ai demandé à Claude.

On a discuté, je lui ai expliqué ce que je voulais faire, il m'a fourni du code, ça semblait cohérent.

Malheureusement, la mise en pratique n'a pas été de tout repos.

Impossible d'obtenir un rendu propre lorsque je déplaçais le sprite.

Au début, je pensais que cela venait de ma fonction optimisée de déplacement. Je l'ai donc court-circuitée et remplacée par une approche plus simple, effacer le sprite, puis l'afficher à la nouvelle position.

Toujours des traînées.

Je ne savais pas si le problème venait du clean ou de l'écriture du sprite. Tout me semblait logique, la couleur de fond n'était normalement jamais touchée.

Un bug évident, mais impossible de savoir d'où il venait.

J'ai mis Claude, ChatGPT et Gemini sur le coup.

Ils m'ont tous proposé des théories, lecture incorrecte de la couleur de fond, mauvais alignement mémoire, problème de synchronisation.

ChatGPT pensait que j'avais un souci lors de la lecture de la couleur de fond. J'ai donc décidé de la mettre en mémoire avant modification. Aucun changement.

J'ai même poussé le vice jusqu'à forcer la couleur de fond à C_WHITE, pour éliminer toute ambiguïté.

Toujours cette fameuse traînée.

## Décomposition du problème

J'ai décidé de reprendre calmement étape par étape.

Affichage du fond, ok.

Affichage du sprite, parfois une couleur orange apparaissait comme couleur de fond, mais uniquement en haut du sprite.

Clear du sprite, le fond était remplacé par de l'orange, mais pas partout, seulement à certains endroits.

Rien de cohérent.

Et je comprenais encore moins les explications générées par les IA, qui semblaient plausibles mais ne collaient pas à la réalité.

Frustrant.

## Le test sur la vraie machine

Après une pause bien méritée, j'ai eu une idée simple.

Tester sur un vrai MO5.

Et là...

Plus de traînée.\
Plus d'artefact.\
Sprite parfaitement affiché.\
Déplacement nickel.

Je m'étais déjà fait avoir avec les couleurs, différentes entre l'émulateur et la vraie machine.

Cette fois, ce n'était pas une question de palette, mais de
comportement.

DCMOTO n'émule pas exactement le rendu dans ce cas précis.

À partir de maintenant, je vais éviter de trop lui faire confiance. Je pense plutôt utiliser un émulateur en ligne comme celui-ci https://scemino.github.io/yame_wp/mo5.html

Ou mieux encore, tester directement sur la vraie machine dès que possible.

## Fin de week-end positive

Au final, le week-end s'est terminé sur une bonne note.

J'ai mis à jour mon SDK avec cette nouvelle fonctionnalité, affichage d'un sprite avec fond transparent, compatible avec un effacement rapide basé uniquement sur la banque forme.

Je pense maintenant avoir les briques nécessaires pour commencer sérieusement mon jeu.

Plus qu'à s'y mettre 😉