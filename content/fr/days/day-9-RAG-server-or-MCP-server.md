---
title: "Day 9 – RAG server ou MCP Server ?"
date: 2025-12-27
tags: ["AI", "MO5", "RAG", "MCP", "prompt", "Retro"]
description: "Neuvième jour de vibe coding : Est-ce que je ne devrais pas développer un serveur MCP ?"
draft: false
weight: 10
translationKey: "day-9"
---

Dans les épisodes précédents, j’ai parlé de mon RAG server : pourquoi il existe et comment il m’aide à fournir du contexte aux IA.  
Mais au fur et à mesure des expérimentations, je me suis rendu compte d’un point important :

**un serveur RAG, aussi bien fait soit-il, n’est pas suffisant pour une intégration fluide et robuste avec des IA modernes**.
Il manque une brique pour connecter les coding agents à mon RAG server.

Dans cet article, je vais expliquer :
- ce qu’est un **RAG server**,  
- ce qu’est un **MCP server**,  
- pourquoi le RAG seul ne suffit pas,  
- et comment je vais faire évoluer mon RAG server pour devenir utilisable via MCP.

---

## 🧠 RAG : qu’est-ce que c’est ?

Un **RAG server** (*Retrieval-Augmented Generation*) a un objectif simple et puissant :

> **augmenter un modèle d’IA avec du contexte externe pertinent.**

Concrètement, il combine :
- une base de connaissances (documents, notes, fichiers `.md`),
- un moteur de recherche sémantique (vecteurs, embeddings),
- potentiellement des services d’IA qui reconstituent des prompts enrichis.

Quand une IA reçoit une question, le RAG va :
1. chercher dans la base les fragments de texte les plus pertinents,
2. retourner ces fragments pour les inclure dans le prompt,
3. améliorer la qualité finale de la réponse.

Un RAG server est donc essentiellement un **backend de recherche augmentée**, qui sait répondre à des requêtes comme :

```
POST /api/search
```
avec une requête sémantique, des tags, et une liste de résultats.:contentReference[oaicite:0]{index=0}

👉 L’objectif : fournir **du contexte textuel**, pas gérer l’IA elle-même.

---

## 🔌 MCP : ce n’est pas un moteur, c’est une **interface**

Un **MCP server** (*Model Context Protocol*) n’est pas une autre manière de faire de la recherche de documents.  
C’est un **standard d’interface** qui permet à une IA, ou à un agent logiciel, de *découvrir et appeler des capacités externes* de façon uniforme.

Concrètement, avec MCP un serveur expose :
- des **tools** (outils)
- des **resources** (ressources)
- des **prompts** (modèles ou rôles)

et chaque élément est décrit avec un schéma clair (input/output), de sorte qu’une IA puisse :
- savoir ce qui est disponible,
- comment l’appeler,
- quelles entrées attendre.

👉 MCP est une **couche sémantique d’abstraction** au-dessus des API classiques. C’est une manière standard de dire à une IA :

> *Voici ce que je peux faire, et comment tu peux l’utiliser.*

Sans MCP, une IA ne sait pas comment appeler ton API RAG, ni quelles routes existent, ni comment formuler les JSON attendus.

---

## ❗ Pourquoi un RAG server seul ne suffit pas

Dans mes expérimentations (notamment lorsque j’ai testé Augment ou d’autres agents IA), j’ai remarqué :
- les IA **hallucinent** ou réinventent la roue si on ne leur fournit pas un contexte explicite,
- chaque IA / agent a sa propre manière de consommer des API,
- **sans protocole standard**, il faut écrire des code bridges spécifiques à chaque client,
- on perd en portabilité et en maintenabilité.

En d’autres termes :

> Le RAG donne du contexte.
>  
> Le MCP dit à l’IA comment **trouver et utiliser** ce contexte.

Sans MCP, même le meilleur RAG devient presque inutile pour des agents intelligents qui veulent automatiser leur usage.

---

## 🛠️ Ce que je vais faire

Plutôt que de créer un serveur MCP dédié, j’ai choisi une approche plus pragmatique :

> **ajouter des endpoints MCP directement à mon serveur RAG existant**.

L’idée est simple :
1. mon RAG server continue d’exister comme avant (embeddings, recherche sémantique, ingestion),
2. j’y adjoints une **interface MCP** standard,
3. l’IA (Claude, Cursor, Augment ou autre) peut *découvrir* les outils et les appeler automatiquement.

Concrètement, cela signifie ajouter au minimum :
- une route `GET /mcp` qui décrit le serveur,
- une route `GET /mcp/tools` qui liste les outils disponibles,
- des routes `POST /mcp/tools/{toolName}` pour exécuter des recherches sémantiques depuis n’importe quel client MCP,
- des ressources MCP (documents, tags, statut d’ingestion),
- éventuellement des prompts pour guider l’IA (ex : rôle “assistant expert”).

Cette évolution ne casse rien de ce qui existe déjà, mais **ouvre le serveur à un usage automatique généralisé**.

---

## 🧩 En résumé

| Concept | Rôle |
|--------|------|
| **RAG** | Fournit du *contexte textuel* pertinent à une IA |
| **MCP** | Fournit une *interface standard* pour que des IA utilisent ce contexte facilement |

👉 Un RAG sans MCP, c’est utile mais limité.
👉 Un MCP sans RAG, c’est une interface vide.
👉 **La combinaison des deux est ce qui rend un serveur réellement pertinent pour les IA modernes.**

---

Dans les prochains jours, je documenterai l’implémentation MCP de mon serveur, les endpoints ajoutés, et comment tester cela avec différents agents IA.

Stay tuned! 🚀