---
title: "Day 10 – Fine tuning du RAG server"
date: 2026-01-09
tags: ["mo5", "rag", "ai", "mcp", "docker", "vps"]
description: "Dixième jour de vibe coding : Déploiement et fine tuning du RAG server"
draft: false
weight: 11
translationKey: "day-10"
---

Dans la continuité de l’[épisode précédent](/mo5-blog/days/day-6-rag-server/), je voulais aller un cran plus loin :  
**déployer mon RAG server sur Internet**.

L’objectif était double :

- rendre le serveur accessible depuis l’extérieur
- permettre aux *coding agents* (Copilot, Augment, etc.) d’avoir un **contexte précis** pour aider au développement sur le MO5 (le projet est décrit ici : https://retrocomputing-ai.cloud/)

---

## Déployer un RAG, mais pas seulement une API

Pour faire le lien entre les agents de code et mon RAG server, j’ai créé un **serveur MCP**.  
Il sert d’interface standardisée entre les outils d’IA (Copilot, Augment, etc.) et mon API RAG.

En pratique, la mise en place du MCP server a été presque triviale.  
Je n’ai **rien eu à modifier de fondamental dans le RAG** : toutes les briques existaient déjà.

Le MCP server n’est ni un nouveau moteur, ni une surcouche complexe.  
C’est simplement une **interface / protocole spécifique** qui joue le rôle d’intermédiaire :

- il reçoit les requêtes structurées du coding agent
- les adapte au format attendu par l’API RAG
- renvoie les réponses dans un format directement exploitable par l’agent

Toute l’intelligence reste donc côté RAG.  
Le MCP ne fait que **traduire et orchestrer**.

Quitte à exposer quelque chose sur Internet, je ne voulais pas juste une API brute.  
Avec un nom de domaine, autant en profiter pour :

- héberger un petit site HTML expliquant comment configurer le MCP avec Copilot ou Augment
- exposer proprement l’API
- documenter tout ça avec Swagger

Les sources du MCP server, ainsi qu’une page Markdown expliquant son fonctionnement, sont disponibles ici :

👉 https://github.com/thlg057/mo5-mcp-server

---

## Choix de l’hébergement

Je voulais pouvoir déployer **mon image Docker** simplement.  
En regardant ce qui se faisait, je suis rapidement arrivé sur les **VPS**.

Après comparaison des prix et des configurations, je suis parti sur :

- **Hostinger – offre KVM1**
- 4,99 € / mois
- 1 vCPU
- 4 Go de RAM

Ce n’est pas une grosse config, mais ça devrait être suffisant pour mon usage.  
Bonus appréciable : **le nom de domaine offert**, parfait pour donner une vraie URL au projet.

![Hostinger](/assets/hostinger.png "Hostinger hosting")

---

## Architecture de déploiement

Pour la gestion des URLs, j’ai choisi **Caddy**.

L’idée est simple :

- `/` → redirection vers mon blog
- `/api` → mon API RAG
- `/swagger` → documentation interactive de l’API

Côté serveur, j’ai préparé un répertoire de déploiement avec :

- le blog (Hugo)
- les sources de l’API
- un `docker-compose.yml`
- la configuration Caddy

Copie du tout sur le VPS Hostinger, installation... **aucun souci particulier**, c'est plutôt simple et bien foutu. 
Comme je suis un peu fénéant, j'ai rapidement créé un script powershell pour m'aider dans l'automatisation du déploiement. Je n'ai pas souhaité de faire directement depuis des actions Github, je ne voulais pas mettre mes credentials VPS dans Github et j'avais envie de pouvoir choisir les composants / le contenu que je voulais déployer.

---

## Premier choc : les performances

Sur mon Raspberry Pi 4, j’avais déjà remarqué un problème : **environ 30 secondes** pour obtenir une réponse.

D’après Augment, le diagnostic était clair : le Raspberry n’était tout simplement **pas assez puissant**.

Je m’attendais donc à une amélioration nette sur le VPS.

Premier test en production… **catastrophe** : toujours ~30 secondes, et des résultats pas terribles 😬.

Il fallait clairement creuser...

---

## 1. Performance et architecture (vitesse)

### Le problème

Au départ, j’utilisais un service d’**embedding local** :

- le code .NET appelait des scripts Python
- le modèle était chargé à la volée

Résultat :
- chargement du modèle à chaque requête
- CPU saturé
- application instable

Au début, je pensais que le problème venait de la base de données (mauvaises requêtes, index mal optimisés, etc.).

Après avoir ajouté pas mal de logs, le verdict est tombé : **la base n’était pas en cause**, mais bien la génération des *chunks* et des embeddings.

### Analyse

Charger un modèle de deep learning (même “petit” comme E5) est une opération lourde. Le faire à chaque requête est totalement inefficace (et idiot).

Il fallait une architecture où le modèle reste **“chaud”**, chargé une seule fois en mémoire, donc chargé depuis un microservice.

### Ce que j'ai mis en place

- **Micro-service dédié**  
  Une API Python indépendante, basée sur FastAPI, avec son propre conteneur Docker.

- **Chargement unique du modèle**  
  Le modèle `multilingual-e5-small` est chargé **une seule fois** au démarrage du service.

- **Communication HTTP**  
  Le serveur .NET communique désormais via des requêtes JSON rapides et simples.

### Résultat

Le temps de traitement est passé :
- de plusieurs secondes **par chunk**
- à **quelques millisecondes**

Là, on commence à respirer.

---

## 2. Qualité de la recherche (SimilarityScore)

### Le problème

Avec le modèle initial :
- scores de similarité autour de **0.60**
- résultats souvent peu pertinents
- sections génériques du type *“Errors to avoid”* qui remontaient tout le temps

Bref, l’IA avait du mal à comprendre les nuances techniques spécifiques du MO5.

### Analyse

Deux causes principales :

#### 1. Le modèle
Le modèle d’origine n’était pas assez performant sur :
- le langage technique
- le multilingue (français / anglais)

#### 2. La perte de contexte
Une fois découpé en *chunks*, le moteur :
- voyait une suite d’instructions
- mais oubliait **de quel document et de quelle section** elles provenaient
D'un autre côté, c'est moi qui lui avait dit de découper le texte en chunks de 800 caractères max avec un overlap de 150 caractères...

Par exemple, il ne savait plus si on parlait :
- du mode texte
- ou du mode graphique

### Ce que j'ai changé

- **Changement de modèle**  
  Passage à `intfloat/multilingual-e5-small` → le score de base est passé de **0.61 à 0.86**

- **Enrichissement sémantique**  
  Modification du code C# pour injecter systématiquement dans chaque chunk:
  - le titre du document
  - le titre de la section  

- **Nettoyage du Markdown**  
  Suppression des caractères `#`, `**`, etc. pour ne conserver que le texte “pur” lors de l’indexation

### Résultat

Les documents techniques remontent désormais **en priorité n°1** sur les requêtes matérielles (par exemple sur le NMI).
L’IA “comprend” enfin le **contexte global** de chaque page.

---

## Fine tuning empirique

J’ai fait pas mal de tests pour affiner le comportement :

- suppression des `#`, `*`, etc.
- taille des chunks
- taille de l’overlap
- ordre des champs injectés dans le contexte

Tout a été fait de manière **empirique**, à coups de tests et de comparaisons.

Je trouvais que le découpage automatique en chunk était une bonne idée et que ça allait me faciliter la vie. 
Finalement, j'ai choisi de créer moi même les chunks, de ne plus les générer automatiquement lors de l'ingestion des documents. 
La documentation est à présent plus cohérente, et un ensemble cohérent est retourné aux coding agents.

---

## Aide précieuse des IA

Je ne connaissais pas grand-chose aux modèles neuronaux, et L'IA m'a aidé à appréhender pas mal de concepts. Je ne prétends pas du tout être un spécialiste, mais cette expérience m'a permis de m'initier aux modèles neuronaux et aux bases de données vectorielles que je ne connaissais pas du tout. 

Je trouve fascinant de pouvoir représenter du texte sous forme de nombres en préservant le sens. Naïvement, je voulais partir sur une recherche par mot clé, mais ça aurait été complètement inefficace et mon RAG aurait été inutilisable. 

La découverte de la vectorisation, de cette puissance magique, a vraiment été révélatrice. Je vous conseille d'y jeter un oeil, ne serait-ce que pour l'intérêt intellectuel 😅.

**Gemini** m'a beaucoup aidé sur le choix du modèle `multilingual-e5-small`. Je voulais un modèle léger pour tourner sur mon VPS, multilangue et plutôt orienté recherche sémantique. 

Gemini m'a conseillé `multilingual-e5-small` et je ne le regrette pas, c'était le bon choix pour mon usage. Merci Gemini 😅.

---

## Tester le RAG en pratique

Pour tester concrètement le RAG, le plus simple est de passer par le site :
👉 https://retrocomputing-ai.cloud/

C’est une page de blog qui explique pas à pas comment utiliser le serveur via un **coding agent** (Copilot, Augment, etc.) en s’appuyant sur le serveur MCP.

Si vous voulez simplement explorer l’API sans passer par un agent, la documentation Swagger est disponible ici :
👉 https://retrocomputing-ai.cloud/swagger

Vous y trouverez la liste complète des endpoints, les formats de requêtes et des exemples d’appels pour tester rapidement le RAG.

---

## État actuel de la documentation

La documentation utilisée par le RAG est encore **en cours de mise à jour**.

Suite à mes dernières explorations du code du MO5 — et en particulier tout ce qui concerne les **modes graphiques**, je suis en train de revoir et d’enrichir les fichiers de documentation.

Cela signifie que :
- certaines parties sont déjà très précises (notamment sur le matériel)
- d’autres vont encore évoluer au fil des ajouts
- les résultats du RAG vont continuer à s’améliorer à mesure que la documentation se densifie

Bref, le serveur est opérationnel, mais le contenu qu’il exploite est encore vivant (et c’est aussi ce qui rend l’expérience intéressante).

---

## Conclusion

Ce déploiement m’a permis de comprendre une chose essentielle :

> Un RAG qui “fonctionne” n’est pas forcément un RAG **utilisable**.

Entre :
- l’architecture
- la performance
- la qualité des embeddings
- le contexte injecté

il y a énormément de paramètres à ajuster.

Mais une fois les bons choix faits, le gain est **immédiat** et vraiment satisfaisant.

La suite au prochain épisode 🙂
