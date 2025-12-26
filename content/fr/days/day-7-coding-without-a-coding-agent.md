---
title: "Day 7 – Coding without a coding agent"
date: 2025-12-26
tags: ["AI", "MO5", "Assembleur", "Retro"]
description: "Septième jour de vibe coding : l'IA ne connait pas tout 😢"
draft: false
weight: 8
translationKey: "day-7"
---

Suite à mes essais avec Augment, j’avais une idée en tête depuis quelques jours.  
Me créer **ma propre librairie C** pour faciliter les développements sur MO5.

Pas un truc énorme.  
Juste de quoi éviter de réécrire les mêmes bouts de code encore et encore.

Augment avait déjà généré quelques fonctions utilitaires.  
Mais celles qui me semblaient vraiment fondamentales étaient celles-ci :

```c
char mo5_getchar(void)
{
    asm {
        swi
        fcb $0A
    }
}

void mo5_putchar(char c)
{   
    asm {
        ldb c
        swi
        fcb $02
    }
}
```

Dans ma tête, tout partait de là.  
Ces deux fonctions comme base, et autour, reconstruire le reste.

Je m’étais fixé un objectif simple : Une version "light" / simplifiée pour
- `mo5_stdio`
- `mo5_ctype`
- `mo5_string`

Un bon socle pour commencer.

## Retour à l’ancienne

Pour cette mission, impossible de compter sur Augment.  
J’avais **grillé tous mes crédits** le week-end précédent en développant mon RAG server.

Plus rien.

![No cerdits](/assets/no-credit.jpg "No cerdits")

Donc pas le choix : **coder à l’ancienne**  
- taper le code à la main  
- réfléchir  
- se planter  
- corriger  
- recommencer

J’ai repris ce qu’Augment m’avait déjà donné, et j’ai commencé à implémenter mes librairies **une par une**.  
À chaque fois, je les utilisais dans un petit projet de test, lancé dans l’émulateur **DCMOTO**.

Ça avançait plutôt bien... enfin... presque...

## getchar(), mon premier mur

Je voulais encapsuler `mo5_getchar()` dans un `getchar()` plus propre.  
Sur le papier, ça semblait trivial.

En pratique : buffers corrompus, caractères bizarres, comportements incohérents.

Bref, ça sentait mauvais.

Au bout d’un moment, j’ai lâché l’affaire et fait simple :

```c
#define getchar mo5_getchar
```

Ce n’est pas beau... Mais ça marche....

## Fierté… puis malaise

Une fois mes libs fonctionnelles, j’étais plutôt content du résultat.  
Assez pour commencer à écrire quelques petits tutorials pour les montrer en action et commencer à imaginer le contenu de mon prochain article, où comment à moi seul j'avais réussi à proposer à la communauté LES librairies incontournables pour le MO5!

Mais en codant, une pensée revenait sans cesse :

> *C’est quand même étrange de devoir réécrire ces fonctions primaires…*

Lors de mes premières recherches sur le développement C pour MO5, je n’avais trouvé que des exemples :
- en BASIC
- en l’assembleur

Pas vraiment d’exemples en C.

Mais quand même... Un truc clochait... Ca me semblait gros quand même...

## cmoc, ce traître silencieux

Je suis allé fouiller dans le répertoire d’installation de **cmoc**.  
Et là... surprise!

Dans `cmoc/include`, je découvre plusieurs fichiers `.h`.  
Pas de `stdio.h`, pas de `ctype.h`, pas de `string.h`.

Mais un fichier : **`cmoc.h`**.

Je l’ouvre... Et là... claque. 

Beaucoup des fonctions que j’avais péniblement réécrites étaient **déjà là** :
- `putchar`
- `memset`
- `strcmp`
- `memcpy`
- `strlen`
- `isprint`
- et même `printf`

😑

J’ai donc essayé d’inclure simplement :

```c
#include <cmoc.h>
```

Quelques tests plus tard, le verdict était clair :  
mes librairies maison étaient devenues **totalement inutiles**... Redondantes... Obsolètes avant même d’exister...

## Revenir aux fonctions officielles

J’ai donc réécrit mes samples en utilisant les fonctions fournies par cmoc.  
Et honnêtement… c’est beaucoup mieux comme ça.

Réinventer la roue, c’est amusant.  
Mais quand une roue existe déjà, testée, documentée, autant l’utiliser.

Il reste quand même un manque :  
je n’ai pas trouvé de vrai `getchar()`.

Il y a bien `readline()`, mais elle attend que l’utilisateur appuie sur Entrée.  
Impossible de lire un seul caractère (par exemple : *“Appuie sur Y pour continuer”*).

J’ai aussi eu quelques instabilités avec `printf()`, mais ça vient très probablement de mon code.

## Leçon du jour

J’aurais dû avoir ce réflexe dès le départ: un développeur C regarde **d’abord les `.h`**.  
C’est la base.

Je me suis trop laissé porter par l’IA.  
Elle a répondu à mon besoin, mais sans le bon contexte, elle a préféré **tout recréer**, plutôt que de réutiliser l’existant.

### Ce que j’en retiens

Ma première leçon, c’était que le vibe coding avec l’IA, **ça coûte cher**  (j’ai dépensé mes 40 000 crédits en trois demi-journées).

Cette expérience m’apporte une autre leçon : **sans contexte précis, l’IA a tendance à réinventer la roue** (quand elle n’hallucine pas).

Pour des développements classiques, type microservices .NET, l’IA dispose d’un très bon contexte, parce que c’est du développement « standard ». Pour mon projet MO5, qui est clairement un projet de niche, c’est une toute autre histoire.

Il faut donc **lui fournir explicitement le contexte**, et de manière détaillée.  
Si on ne lui dit pas clairement qu’une chose existe, elle ne pourra pas la retrouver : la documentation est limitée, parfois inexistante.  
Des recherches parallèles sont donc indispensables pour pouvoir guider correctement l’IA.

Mon projet de **RAG server** prend encore plus de sens.  
Je vais pouvoir l’enrichir avec cette expérience, afin que la prochaine fois, l’IA puisse utiliser directement les librairies existantes.

Finalement, le fait de ne plus avoir de crédits pour Augment a été une très bonne chose.  
Ça m’a forcé à investiguer par moi-même et à découvrir ces fonctions « cachées »  
(ou en tout cas inconnues de l’IA… et de moi).

Et au final, ça m’a permis de ne pas réinventer la roue.

En synthèse du day 7:  
- Moins de magie.  
- Plus de réalité.  
- Et une bonne claque de développeur C 😉

Le code associé à cet article est disponible ici :  
https://github.com/thlg057/mo5-tuto
