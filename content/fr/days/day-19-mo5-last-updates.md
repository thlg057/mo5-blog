---
title: "Day 19 : Quelques nouvelles du front..."
date: 2026-05-01
tags: ["sdk", "mcp", "MO5", "ai", "samples", "registry"]
description: "Ces derniers temps, j'ai pas mal avancé sur différents sujets, un petit point d'étape..."
draft: false
weight: 20
translationKey: day-19
---

Ces derniers temps, j'ai pas mal avancé... Genre vraiment.

RAG, SDK, MCP, template, communication, j'ai un peu touché à tout, parfois dans le bon ordre, parfois pas du tout 😉

Et surtout, j'ai commencé à sentir un truc sympa : tout commence à se connecter

## Mettre un peu de vérité dans tout ça

Jusqu'ici, une bonne partie de ma doc venait de mes expérimentations.

Ce qui marche, ce qui ne marche pas, ce qui "a l'air de marcher"... bref, du vécu.

C'est utile, mais ça a ses limites.

Du coup, j'ai décidé d'arrêter de deviner.

J'ai pris un abonnement à Claude.ai, et je lui ai fait lire quelques classiques du MO5.

- Thomson MO5 Guide Ed.2 (Cedic Nathan 1985.11) 
- Le Manuel Technique du MO5 (Oury, Cedic Nathan 1985) 
- Clefs Pour MO5 (Blanchard, Memento PSI 1985)

Et là... gros nettoyage.

Des approximations corrigées, des trucs que j'avais mal compris, des détails techniques ajoutés, et surtout des références sérieuses: on passe de "ça marche chez moi" à "ça marche et on sait pourquoi"

Dans la foulée, je lui ai fait relire tous les chunks de mon RAG.

Même logique, correction, complétion, clarification.

Aujourd'hui, la doc est beaucoup plus solide, et surtout, elle ne repose plus uniquement sur mes connaissances 😉

## Le SDK prend du muscle

Avec une doc plus propre, j'ai pu faire évoluer le SDK (https://github.com/thlg057/sdk_mo5) plus sereinement.

J'ai ajouté quelques briques importantes.

- `mo5_audio.h` pour gérer le son, et surtout éviter les bips bien énervants quand on appuie sur une touche
- `mo5_music_swi` pour jouer de la musique, encore perfectible mais fonctionnel
- `mo5_joystick` pour gérer un joystick

Petit point important:  audio et music ne fonctionnent pas sur tous les émulateurs. Par contre, ils fonctionnent très bien sur un vrai mo5 (enfin, sur le mien 😉 ).

J'ai fait un projet d'exemples pour voir le SDK en action: https://github.com/thlg057/mo5_sdk_examples

Toujours le même objectif, zéro installation avec Github Codespace, et en trois commandes ça tourne.

`make setup-codespace`, `make install`, `make`.

Simple... enfin simple-ish 😉

## L'infrastructure aussi a pris un coup de polish

Je me suis aussi pris un petit rappel à la réalité: 

Mon template dépendait des URLs officielles de lwtools et cmoc, et un jour... plus rien... Serveur down... Plus moyen de créer un projet... Bloqué pour une URL.

Du coup, j'ai repris la main et j'ai intégré les setups directement dans un de mes repos, et je passe maintenant par GitHub.

C'est plus robuste, beaucoup plus.

Par contre, ça veut dire que je dois suivre les versions moi-même, mais au moins, ça ne casse plus du jour au lendemain.

NDLR: si vous préférez utiliser les URLs officielles (en http), il suffit de modifier les URLs dans le Makefile, il y a des variables dédiées au début du makefile.

## Premier contact avec "l'extérieur"

J'ai aussi essayé un truc que je n'avais pas encore fait : J'ai contacté l'association MO5 pour leur présenter ma toolchain.

Ils m'ont proposé de faire un post sur leur discord pour présenter mon travail (https://discord.com/channels/281149808931241984/1493228839420301423).

Et là... surprise.

Des retours.

Des gens qui testent.

Qui expérimentent.

Qui proposent des idées.

Ca fait vraiment plaisir 😉

C'est toujours un peu particulier de voir son projet utilisé par d'autres, dans le cadre pro j'ai l'habitude, c'est mon métier, mais là c'est différent, c'est perso, il y a un petit début d'engouement, et franchement ça fait plaisir, c'est vraiment valorisant 😉

## Vibecoding en cours... avec quelques surprises

J'ai aussi commencé un nouveau chantier : Un jeu.

Oui, encore un space invaders 😉

Mais cette fois, en 100% vibecoding avec Claude.

Je suis en train d'enregistrer les sessions pour faire une vidéo complète. Bon, évidemment, rien ne se passe jamais exactement comme prévu. Je me suis acheté un micro pour l'occasion. Et bien évidemment, il ne fonctionne pas... Faudra faire avec...

## Le MCP commence à bouger

J'ai référencé mon MCP dans plusieurs registries, comme expliqué dans le Day 18.

Et doucement, je commence à voir des signaux.

Des repos clonés, des gens qui passent. Ca reste discret, mais ça vit, et ça, c'est déjà énorme (pour moi 😉).

## Conclusion

Tout n'est pas parfait, mais clairement, ça avance.

La doc est plus solide, le SDK devient plus complet, le setup est plus robuste, et surtout... il y a des gens de l'autre côté.

Et ça change tout.

La suite au prochain épisode 😉
