---
title: "Day 2: Vibe coding avec ChatGPT"
date: 2025-11-02
tags: ["AI", "ChatGPT", "Coding", "Retro", "C", "Assembleur"]
description: "Retour sur ma première journée de vibe coding : expérimentation avec ChatGPT pour coder un petit jeu rétro."
draft: false
weight: 3
---

Pour mon premier jour, j’ai choisi de commencer avec **ChatGPT**.  
Certes, c’est une IA généraliste et non une IA spécialisée dans le développement, mais je me suis dit que ce serait un bon point de départ.

---

## Mon objectif

J’avais une idée assez précise : **développer un petit jeu en C**.  
J’avais aussi lu que pour tout ce qui touche au graphisme, il valait mieux utiliser de l’assembleur.  
Je comptais donc sur ChatGPT pour me guider dans ce langage. 😉

Je voulais ensuite **tester le programme dans un émulateur**.

---

## Les premiers essais

J’ai commencé par expliquer mon contexte et mes besoins, puis demandé des références utiles.  
ChatGPT m’a bien listé plusieurs blogs et outils… mais la plupart **n’existaient plus** : dépôts supprimés, liens déplacés, outils inconnus ou introuvables.

Côté code, les choses se sont compliquées :
- Les premières propositions **ne compilaient pas**, notamment à cause d’options obsolètes du compilateur.
- ChatGPT me proposait du code utilisant `stdio.h` et `putchar`, alors que **je ne pouvais pas utiliser de bibliothèques externes** dans ce contexte.
- Après plusieurs allers-retours, j’ai enfin obtenu **un code qui compilait**.
- Restait à le **faire tourner dans l’émulateur**…

---

## La galère du `.k7`

ChatGPT s’est mis en tête de générer un fichier `.k7` en utilisant l’option `-k7` de `cmoc`, qui **n’existe pas (ou plus)**.  
Ensuite, il m’a orienté vers **buildcass** (repo disparu : [columboo/buildcass](https://github.com/columboo/buildcass.git)) puis **lwbin2tap**, que je n’ai jamais trouvé.

À défaut, il m’a proposé d’écrire un **script Python** pour convertir un `.bin` en `.k7`.  
On en a testé plusieurs versions, mais **aucune ne fonctionnait** : dans **dcmoto**, le message “rembobinez la cassette” revenait inlassablement.

J’ai tenté de charger directement le `.bin` dans dcmoto, mais rien n’y faisait : les commandes `LOAD`, `EXEC`, etc., ne donnaient aucun résultat.

---

## Un espoir… puis encore une impasse

Après quelques recherches, je suis tombé sur le dépôt d’**Olivier P** :  
👉 [OlivierP-To8/BootFloppyDisk](https://github.com/OlivierP-To8/BootFloppyDisk)

J’ai demandé à ChatGPT de s’en inspirer pour générer un petit programme *“Hello world”* en assembleur.  
Mais là encore, **le programme n’a pas fonctionné**.

---

## Bilan de la journée

Un peu frustrant, je l’avoue.  
Tout semblait prometteur : les échanges avec ChatGPT étaient intéressants, les idées nombreuses, les exemples complets (jusqu’à un mini-jeu !)… mais **rien de réellement exécutable** au final.

Les références n’étaient **pas à jour**, l’IA semblait **perdre le fil du contexte**, et parfois même **tourner en boucle**.  

Il était donc temps de **passer à une IA dédiée au développement**. 🚀

---

*To be continued…*
