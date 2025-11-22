---
title: "Day 3: Vibe coding avec Copilot"
date: 2025-11-03
tags: ["AI", "Copilot", "MO5", "Assembleur", "Retro", "Wibe Coding"]
description: "Deuxième jour de vibe coding : cette fois avec Copilot, en partant du dépôt d’Olivier P pour générer une image MO5 et tenter un premier input clavier."
draft: false
---

Pour cette séance de vibe coding, j’ai décidé de tester **Copilot Pro**, en profitant des quelques **jours d’essai gratuit**.  
Et pour mettre toutes les chances de mon côté, je suis directement parti du dépôt d’**Olivier P** :  
👉 *OlivierP-To8/BootFloppyDisk*

---

## Mon objectif
Avec ChatGPT, je n’étais pas arrivé à créer mon propre code ni à utiliser le projet d’Olivier pour générer une image disque fonctionnelle pour le MO5.  
Pourtant, en théorie, tout semblait simple :  
je prends **le même source**, je le copie dans mon repo, je compile, j’utilise les outils d’Olivier… aucune raison que ça casse.

---

## Première réussite

C’est exactement ce que j’ai demandé à Copilot :  
récupérer l’exemple d’Olivier, le compiler, puis générer l’image disque.

Et là… miracle technologique : **ça a fonctionné du premier coup**.

Copilot a un peu galéré pour remplacer le texte du sample d’Olivier, mais après quelques tentatives, il y est finalement arrivé. Une première victoire bien motivante !

---

## Deuxième objectif : gérer une saisie clavier

Dans la foulée, je me suis lancé dans un test plus interactif.

L’idée était simple :

1. afficher *“Quel est votre nom ?”*  
2. permettre la saisie du prénom  
3. afficher *“Bonjour <prénom>”*

Et c’est précisément là que les choses ont commencé à se compliquer…

---

## Là où tout s’est mis à coincer

Après plusieurs itérations, Copilot arrivait à :

- afficher correctement les messages
- capturer la saisie clavier
- stocker le résultat dans un buffer
- confirmer en debug que les caractères étaient bien enregistrés

Mais **impossible d’afficher le prénom**.  
Après *“Bonjour “*, l’écran restait désespérément vide.

Je pouvais saisir un nom, je pouvais vérifier qu’il était en mémoire… mais l’affichage refusait obstinément d’apparaître.

---

## Analyse finale de Copilot

Après quelques heures à tourner autour du problème, je lui ai demandé de générer une synthèse.  
Voici ce qui en est ressorti.

---

### ✔️ Ce qui fonctionnait

- affichage des messages fixes  
- saisie clavier avec écho  
- stockage correct dans le buffer  
- vérification en debug : le buffer contient bien les caractères saisis  

### ❌ Ce qui ne fonctionnait pas

- la boucle d’affichage du buffer après *“Bonjour “*

---

## 🔬 La découverte critique

En allant jusqu'à analyser le code source de l’émulateur **Maurice** (en Rust), Copilot a découvert que celui-ci ne gère que :

- lecture cassette K7  
- écriture cassette K7  
- contrôle moteur  
- crayon optique  

Et surtout :

> Les appels SWI standards `$02` (PUTCHAR) et `$0A` (GETCHAR) **ne sont pas implémentés** dans l’émulateur.

Ce qui explique pourquoi les routines d'affichage système ne donnaient absolument rien.

---

## 🔧 Les solutions tentées (sans succès)

- SWI standard (`swi` + `fcb $02`)
- appel direct des routines ROM (`jsr $D52D`)
- adresses supposées interceptées par l’émulateur (`jsr $F1B0`)
- approche CWAI (bloque le programme)
- lecture directe des ports I/O

Toutes aboutissaient au même résultat :  
**le prénom ne s’affichait pas**.

---

## 🎯 Conclusion de Copilot

D’après lui, le bug venait très probablement de **DCMOTO / Maurice**, et non du code assembleur lui-même.  
Il recommandait même de tester le programme sur un **vrai Thomson MO5**, ce qui permettrait de confirmer que l'émulation est en défaut.

---

## Bilan

Un peu déçu du résultat : la demande me semblait simple, presque “basique”, et pourtant…  
Sans un émulateur pleinement compatible, certaines fonctionnalités deviennent tout simplement impossibles à valider.

Il était donc temps d’essayer **un autre agent de développement**…

La suite au prochain épisode. 🚀
