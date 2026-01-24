---
title: "Day 11 – Créer un template MO5 pour arrêter de me battre avec le build"
date: 2026-01-09
tags: ["mo5", "template", "c", "cmoc"]
description: "Day 11 : Créer un template MO5 pour arrêter de me battre avec le build"
draft: false
weight: 12
translationKey: "day-11"
---

Ces derniers temps, j’avais **beaucoup trop de sujets ouverts en parallèle**.

Vraiment trop 😅

Dans le désordre, il y avait :

* continuer mes tests pour comprendre comment fonctionne le **mode graphique sur MO5**
* compléter la **documentation de mon RAG server**
* modifier ce RAG server pour **simplifier la gestion des mots-clés**, puis le redéployer avec la nouvelle doc
* **héberger mon serveur MCP**
* faire un **petit jeu en mode graphique** sur MO5

Et au milieu de tout ça, un problème très terre-à-terre revenait sans arrêt :

> chaque nouveau projet MO5 me faisait perdre du temps à reconfigurer le build, les outils, et les conversions de disquettes.

C’est comme ça qu’est née l’idée de créer un **template de projet MO5**.

---

## Du graphique sur MO5… de l’émulateur au vrai matériel

Pour mes tests graphiques, j’ai commencé naturellement sur **DCMOTO**.

Rapide, pratique, parfait pour itérer.

Mais dès que j’ai voulu passer sur un **vrai MO5**, un détail important est revenu me rappeler la réalité du hardware :

* l’émulateur utilise des fichiers **.fd** (floppy disk)
* le **SDDRIVE**, lui, attend des fichiers **.sd** sur la carte SD

Donc impossible de tester directement sans une étape de conversion.

---

## La conversion .fd → .sd : le petit grain de sable

Il existe bien un utilitaire (en `.exe`) pour convertir les fichiers `.fd` en `.sd`.

Mais dans mon cas, ce n’était **pas du tout pratique** :

* pas intégré au build
* dépendant de l’OS
* une étape manuelle de plus

Bref, exactement le genre de chose que je voulais **automatiser**.

Je suis donc reparti des sources pour écrire **un script Python** chargé de faire cette conversion.

Plutôt que de le copier partout, je l’ai intégré directement dans mon **SDK MO5**, histoire d’avoir **une seule source de vérité**.

---

## L’idée du template

À ce moment-là, je me suis dit :

> « Si je dois refaire ça à chaque nouveau projet, je vais devenir fou. »

Ce qu’il me fallait, c’était un **template** qui s’occupe de tout le sale boulot.

Un projet de base qui :

* récupère automatiquement le repo d’Olivier P pour générer les fichiers `.fd`
* récupère mon projet **SDK MO5**, le compile et génère une bibliothèque `.a` + les headers
* configure correctement la ligne de commande **cmoc** (includes + lib)
* compile le projet utilisateur
* génère un fichier `.fd` pour tester dans un émulateur
* génère un fichier `.sd` pour tester directement sur un vrai MO5 avec SDDRIVE

Autrement dit : **je code, je fais `make`, et ça marche**.

---

## GitHub + Template = ❤️

GitHub propose une fonctionnalité toute simple mais parfaite pour ça : les **repository templates**.

Exactement ce qu’il me fallait.

Soyons honnêtes :

* ce template reste volontairement simple
* il s’agit essentiellement d’un **Makefile** et d’un `main.c` d’exemple

Mais il pose **toutes les bases** pour démarrer un projet MO5 sans perdre une heure en configuration.

J’ai donc créé le template et je l’ai publié sur GitHub :

👉 [https://github.com/thlg057/mo5_template](https://github.com/thlg057/mo5_template)

---

## Utilisation

Rien de plus simple.

Sur GitHub :

1. aller sur le repo du template
2. cliquer sur **"Use this template"**
3. créer un nouveau repo
4. coder 🎉

Le reste est géré par le Makefile.

---

## Et maintenant ?

Ce template va me servir de **socle** pour :

* continuer mes explorations graphiques sur MO5
* développer un petit jeu
* tester rapidement sur émulateur **et** sur vrai matériel

Et surtout :

> arrêter de perdre du temps sur le build pour me concentrer sur le fun.

Enjoy 👋
