---
title: "Day 4: Vibe coding avec Augment"
date: 2025-11-05
tags: ["AI", "Augment", "MO5", "Assembleur", "Retro", "Wibe Coding"]
description: "Quatrième jour de vibe coding : cette fois avec Augment, un coding agent étonnamment proactif qui a enfin résolu mon problème d’I/O sur MO5."
draft: false
weight: 5
translationKey: "day-4"
---

Dans le cadre de mon boulot, j’ai eu l’occasion de tester **Augment**.
Lors de ces tests professionnels, je l’avais trouvé *très* pertinent, presque “proactif”.
Par proactif, j’entends : non seulement répondre à la demande, mais aussi **anticiper des problèmes non formulés**, proposer des solutions alternatives, attirer l’attention sur des points critiques… bref, vous voyez l’idée.
J’avais été bluffé, clairement.

Augment propose 7 jours gratuits pour tester son coding agent, ce qui tombait parfaitement : je voulais justement faire mes essais pendant le weekend.

---

## Repartir du même scénario que Copilot

Comme pour Copilot, je lui ai demandé :

1. de partir du dépôt d’**Olivier P**
2. d’utiliser le fichier Markdown généré par Copilot
3. d’analyser la situation et de **trouver une solution au bug d’affichage du prénom dans DCMOTO**

Et là… **incroyable** :
Augment a trouvé la solution **immédiatement**.

---

## Le miracle : le prénom enfin affiché 📟✨

Augment m’a généré un code MO5 qui :

- saisit le prénom
- affiche les caractères en temps réel (l’écho fonctionne !)
- et dès que j’appuie sur *Entrée*, il affiche :
  **“Bonjour <prénom>”**

Et là… vous n’imaginez pas la joie.
Honnêtement, j’ai eu un vrai sourire quand le prénom est apparu à l’écran dans DCMOTO.
Après des heures passées avec Copilot à tourner autour du problème, ça faisait un bien fou de voir enfin le programme fonctionner.

---

## Dans la foulée : pousser les tests plus loin

Porté par cette réussite, j’ai voulu continuer sur ma lancée.

### Nouveaux objectifs

- tester le déplacement d’un caractère à l’écran via les touches clavier
- tester l’affichage graphique

Et là encore… **tout a parfaitement fonctionné** (après quelques itérations tout de même).
Augment ne se contentait pas d'exécuter les instructions : il proposait même des variantes, des améliorations possibles, et des vérifications utiles.

---

## Construire des outils pour la suite

Après ces tests, je lui ai demandé de me créer :

- **des librairies assembleur** pour gérer
  - l’affichage texte
  - les composants graphiques
- une base technique qui me servira pour mes futurs développements
  (logique en C + interaction matérielle en assembleur)

Augment a généré tout cela, proprement organisé.

Ensuite, je lui ai demandé de produire **plusieurs fichiers Markdown** expliquant :

- comment développer sur MO5
- les points d’attention
- les pièges potentiels
- les stratégies pour les contourner
- et les leçons tirées de mes expérimentations

Ces fichiers serviront de base pour mon futur **serveur RAG** dédié au développement MO5.

---

## Pourquoi un RAG pour MO5 ?

Mon objectif parallèle, en plus de développer une petite application MO5, est de créer un **serveur de documentation** pour les développeurs Thomson qui voudraient utiliser un coding agent.

Parce que, et tous ceux qui utilisent un coding agent le savent,
**la qualité du contexte fourni est fondamentale**.

Un agent, même excellent, n’invente pas la documentation technique d’une machine des années 80.
Il ne peut pas deviner les subtilités de :

- l’assembleur 6809
- la ROM du MO5
- les adresses mémoire mappées
- les routines système
- les limites des émulateurs
- les différences entre un MO5 réel et un MO5 émulé

Pour qu’un agent raisonne correctement, il faut lui **donner le terrain de jeu complet** :

- des exemples fonctionnels
- des explications de bas niveau
- des contraintes techniques
- des outils compatibles
- des patterns de code validés
- et une structure de projet claire

C’est exactement ce que je veux construire :
une documentation MO5 *augmentée* (sans mauvais jeu de mots), directement exploitable par un agent.

---

## Bilan

Après l’expérience mitigée avec Copilot, Augment a été une vraie bouffée d’air frais.
Pertinent, rapide, efficace, et vraiment proactif.

Cette journée m’a enfin permis de franchir un cap technique :
**je peux maintenant interagir correctement avec le MO5 dans DCMOTO**, gérer la saisie, l’affichage, et même le début du graphisme.

Et ça… ça change tout pour la suite.

À suivre 🚀
