---
title: "Day 6 – RAG server, embeddings et vibe coding"
date: 2025-12-21
tags: ["AI", "rag", "embeddeddings", "vibe-coding"]
description: "Sixième jour de vibe coding : Développer et déployer mon serveur RAG dédié au MO5. Spoiler alert: le vibe coding ça coute cher 😢"
draft: false
weight: 7
translationKey: "day-6"
---

Dans un épisode précédent, j’ai fait pas mal de tests / d’expériences pour comprendre comment coder pour un MO5.  
J’avais demandé à l’IA de résumer ce que nous avions appris en fichiers markdown. L’idée sous-jacente était de pouvoir partager cette expérience avec mes nouveaux projets MO5 sans avoir à copier les fichiers `.md` dans chaque repo.

Spoiler alert : **vibe coder, ça coûte de l’argent** 😢

## L’idée du RAG server

Une façon simple de partager de la connaissance et du contexte, c’est de le faire au travers d’un **RAG server**. 

Un RAG server (ou Retrieval-Augmented Generation), c'est basiquement une API de recherche, mais capable de fournir du contexte *spécifique* aux IA (MO5 dans mon cas).

Au début, j’avais une vision très naïve de l’implémentation :
- je stocke les documents en base  
- je fais une recherche sur des mots (genre `SQL LIKE`)  
- je retourne les passages contenant ces mots au travers d’une API

Après quelques recherches, cette vision naïve s’est révélée bourrée d’inconvénients.

### Pourquoi la recherche par mots-clés ne marche pas

- **Pas de compréhension sémantique** : si l’utilisateur écrit « Comment authentifier un utilisateur ? » mais que le document parle de *gestion des sessions et des tokens JWT*, aucun mot ne matche, alors que le contenu est pertinent.
- **Même mot, sens différent** : le sens de « clé publique » n’est pas le même en cryptographie, en réseau ou en base de données. La recherche lexicale ne sait pas désambiguïser le contexte.
- **Fragilité face aux reformulations** : pluriels, synonymes, paraphrases, fautes, etc.

Bref, la recherche par mots-clés **ne comprend pas le sens**.  
Elle échoue dès qu’on reformule ou qu’on exprime une notion autrement. Ce n’est clairement pas le bon modèle.

## Chunking, embeddings et magie noire

En faisant quelques recherches sur les RAG servers, on voit vite apparaître les termes **chunks**, **embeddings** et **similarité cosinus**.

Avant de travailler sur ce projet, je n’avais *aucune idée* de l’existence même de ces concepts (et pourtant, on les manipule tous les jours).

Le RAG :
- utilise des **embeddings** pour représenter le sens des textes sous forme de vecteurs
- compare ces vecteurs avec une **similarité cosinus** pour retrouver des passages sémantiquement proches, même sans mots identiques
- s’appuie sur le **chunking** pour indexer des morceaux cohérents plutôt que des documents entiers

Résultat :
- meilleur contexte
- moins de bruit
- moins d’hallucinations
- réponses bien plus fiables qu’avec une simple recherche textuelle

Pour moi, le truc magique, ce sont les **embeddings** : donner une représentation numérique au sens d’un texte.  
C’est peut-être évident pour certains, pas pour moi. Imaginer que quelqu’un ait réussi à formuler mathématiquement le sens d’une phrase, je trouve ça juste hallucinant 😄

## OpenAI ou local ?

En discutant avec ChatGPT de mon projet, il m’a évidemment conseillé de m’interfacer avec OpenAI pour les embeddings.

Même si utiliser une IA est plus performant (meilleure sémantique, rapidité, multilingue), je voulais rester **le moins cher possible**.

Mon objectif est de pouvoir déployer cette API sur Internet pour qu’elle puisse être utilisée par la communauté. Si elle rencontrait un vrai succès, les coûts d’IA pourraient vite freiner mes velléités.

L’architecture cible doit pouvoir abstraire l’implémentation des embeddings :
- provider local « maison »
- ou API d’IA type OpenAI / Azure OpenAI (on ne sait jamais, je pourrais changer d’avis)

## Les embeddings en local

### TF-IDF

**TF-IDF (Term Frequency – Inverse Document Frequency)** est une technique classique pour générer des embeddings.

En résumé :
- **TF** : à quelle fréquence un mot apparaît dans le texte ?
- **IDF** : ce mot est-il rare ou commun dans l’ensemble des documents ?

Un mot rare et présent dans un texte est considéré comme important pour le sens.

Avantages :
- tout est calculé localement
- aucune API externe
- aucun coût d’IA

### Modèle neuronal

Autre option : un **modèle neuronal** pré-entraîné.

Le principe :
- un modèle de deep learning transforme un texte en vecteur dense
- des textes sémantiquement proches ont des vecteurs proches, même avec des mots différents

C’est généralement plus performant côté pertinence, mais :
- plus lourd
- souvent basé sur des scripts Python
- moins performant en temps de réponse

## Déploiement sur Raspberry Pi

Fin novembre, j’ai commencé à coder avec **Augment** (abonnement *Indie Plan* à 20$/mois, 40 000 crédits).

Mise en place de l’API, tests unitaires, inplémentation de TF-IDF et du modèle neuronal,tout se passait bien.

Après la création de mon NAS, j’ai déplacé les sources sur mon nouveau serveur et j’ai voulu déployer l’API dessus.

J’ai demandé à Augment de me créer une image Docker pour un déploiement sur Raspberry Pi (j'ai déployé l'implémentation TF-IDF).

On a passé la soirée ensemble :
- image pas adaptée
- bugs dans l’API
- problèmes de config

Mais vers *citrouille moins le quart* (minuit moins le quart pour ceux qui n'ont pas la ref de Cendrillon), tout était fonctionnel et déployé sur le NAS.

Temps de réponse : ~50 ms. Très correct pour un Raspberry Pi.

## Le modèle neuronal… et la douche froide

Le lendemain, je me suis dit que des résultats plus pertinents seraient mieux.  
J’ai donc demandé à Augment de déployer le modèle neuronal.

Tout l’après-midi y est passé :
- images incompatibles
- problèmes de versions Python
- bugs dans l’implémentation C#
- temps de déploiement

Quand j’ai levé les yeux, il faisait nuit. Il était un peu plus de 18h.

Bonne nouvelle :
- tout fonctionnait
- réponses plus pertinentes

Mauvaise nouvelle :
- **40 secondes** pour une réponse

Conclusion : un Raspberry Pi pour héberger un modèle neuronal, ce n’est pas l'idée du siècle...
Rollback au modèle simple, rapide et efficace.

## Le vrai coût du vibe coding

Pour créer cette API, j’ai **quasi tout délégué à Augment**.  
J’ai poussé le vibe coding très loin, jusqu’à lui demander d’exécuter les commandes de compilation et de déploiement à ma place (fénéantise poussée à l'extrême).

Techniquement, ça marche très bien.  
Le projet est fonctionnel.

Mais le revers de la médaille :
- aucune fierté : ce n’est pas vraiment mon travail
- je n’ai rien appris en profondeur
- **le vibe coding coûte cher** : en une soirée et un après-midi, j’ai quasiment explosé mon quota mensuel

J’estime avoir dépensé environ 47 000 crédits pour ce projet (en l’espace de trois demi-journées, alors que mon quota mensuel est de 40 000 crédits), et je ne parle même pas de mon empreinte carbone...

![Credits utilisés](/assets/rag-server-credits-used.jpg "Credits utilisés")

## Conclusion

Lors du passage en production, je devrai probablement m’appuyer sur une IA comme OpenAI ou Azure OpenAI pour obtenir de meilleures performances en analyse sémantique. On ne peut pas tout faire tout seul, et il faut parfois accepter de déléguer à des professionnels 😄.

Sinon, projet fonctionnel, techniquement réussi, mais avec un goût personnel assez amer.
La prochaine fois, je serai plus actif, je ne laisserai pas l’IA tout faire. C'est mon projet après tout, pas le sien 😄.