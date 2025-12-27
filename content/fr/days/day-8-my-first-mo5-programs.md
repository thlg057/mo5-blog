---
title: "Day 8 – Mes premiers tutos fonctionnels pour le Thomson MO5"
date: 2025-12-27
tags: ["AI", "MO5", "Assembleur", "Retro"]
description: "Huitième jour de vibe coding : J'ai un vrai programme (enfin sample) qui marche 😢"
draft: false
weight: 9
translationKey: "day-8"
---

Suite à Day 7, j’ai atteint une étape importante dans mon exploration du développement C sur Thomson MO5 :  
j’avais enfin deux tutoriels pleinement fonctionnels.

Ils restaient simples, mais ils étaient stables, reproductibles et compréhensibles.

À ce stade, ils reposaient sur deux approches très différentes :

- un tutoriel utilisant mes librairies maison string, stdio et ctype
- un autre se basant exclusivement sur cmoc.h

C’était le bon moment pour prendre du recul et analyser ce que CMOC proposait réellement.

---

## Regarder cmoc.h de plus près

En examinant cmoc.h plus attentivement, plusieurs constats se sont imposés.

### Gestion des chaînes de caractères

Toutes les fonctions que j’avais implémentées dans ma librairie string existaient déjà dans CMOC.

Elles étaient non seulement présentes, mais correctement implémentées et stables.

À partir de là, maintenir une librairie string maison n’avait plus vraiment de sens.

---

### Entrées / sorties standard (stdio)

Ici, la situation était plus subtile.

Certaines fonctions importantes manquaient, notamment getchar().

Cette fonction était essentielle pour moi, car elle permet des interactions simples comme :

Press Y to continue

CMOC fournit readline(), mais celle-ci lit une ligne complète et s’arrête uniquement lorsque l’utilisateur appuie sur ENTER.

En termes d’expérience utilisateur, c’est clairement moins agréable lorsqu’on souhaite réagir à une seule touche.

---

### Classification de caractères (ctype)

Dans ce cas, ma librairie maison gardait tout son intérêt.

CMOC ne propose que :
- isupper()
- islower()

Les autres fonctions (isdigit, isalpha, etc.) restaient absentes et utiles dans mes programmes.

---

## Vers une approche hybride

La conclusion était assez naturelle.

Je ne voulais ni réinventer la roue, ni me priver de ce que CMOC faisait déjà correctement.

Une approche hybride s’est donc imposée :

- utiliser CMOC quand c’est pertinent
- compléter avec des implémentations maison lorsque nécessaire

Au départ, j’avais envisagé que mes librairies incluent cmoc.h, et que les programmes n’incluent que mes headers.

Mais comme ma librairie string était devenue inutile, cette idée m’a rapidement semblé bancale.

La solution finale était beaucoup plus simple :

- cmoc.h est inclus directement
- seules les fonctions manquantes sont fournies par des librairies complémentaires

---

## Problèmes et points d’attention rencontrés

Dès que j’ai commencé à écrire des programmes un peu plus ambitieux, plusieurs pièges importants sont apparus.

Ce sont des problèmes classiques sur machines 8-bits, mais qui ne sont plus du tout évidents aujourd’hui.

---

## 1. L’affichage et le moniteur système

Le Thomson MO5 utilise un automate d’affichage piloté par le BIOS.  
Certains codes ASCII sont interprétés comme des commandes.

### Le secret du retour à la ligne

Sur MO5 :

- \\n (ASCII 10) descend le curseur d’une ligne
- il ne revient pas en colonne 0

Pour un vrai retour à la ligne, il faut impérativement envoyer aussi :

- \\r (ASCII 13)

C’est une source très fréquente d’affichage décalé.

---

### Problèmes de timing

L’automate d’affichage du MO5 est sensible au timing.

Si l’on enchaîne trop vite des appels d’affichage (par exemple fputs() puis mo5_newline()), le processeur vidéo peut recevoir des octets pendant une opération de scrolling.

Résultat :
- glyphes techniques
- caractères incohérents
- affichage corrompu

Solution : toujours regrouper le texte et le saut de ligne dans un seul appel, par exemple :

fputs("Du texte\\n");

---

## 2. Gestion critique de la pile (stack)

Le Motorola 6809 dispose de très peu de mémoire, et par défaut la pile est extrêmement limitée.

### Symptôme

Le programme :
- se comporte de façon aléatoire
- plante sans raison apparente
- affiche des trucs bizarres à l'écran
- ou casse dès qu’on déclare :

char buffer[25];

Après plusieurs tests (compilation + test dans DCMOTO), j'ai identifier que le problème vennait de cette déclaration toute bête, et je ne comprennais pas pourquoi.
C'est Gemini qui est venue à ma  rescousse, en me donnant l'explication.

---

### Cause

La pile descend en mémoire.

Si elle devient trop grande, elle écrase :
- des variables du moniteur système
- la mémoire vidéo

À partir de là, le comportement devient totalement imprévisible.

---

### Remèdes

Augmenter la taille de la pile dans le Makefile :

--stack-space=512

Ou utiliser static pour les buffers (c'est ce que j'ai fait):

static char buffer[30];

La variable est alors placée dans une zone fixe de la RAM.

---

## 3. Les chaînes de caractères : source n°1 de crashs

Sur les machines 8-bits, la manipulation des chaînes est la principale cause de plantages.

### Le piège de strcat()

Ne jamais faire ceci :

strcat(dest, &ch);

strcat() attend une chaîne terminée par \0.

En lui passant l’adresse d’un caractère seul, elle va lire la mémoire jusqu’à trouver un zéro par pur hasard.

---

### Alternative sûre

int len = strlen(dest);
dest[len] = ch;
dest[len + 1] = '\\0';

Toujours fermer explicitement la chaîne.

---

### Le terminateur nul

Toute fonction de conversion ou de construction de chaîne (par exemple itoa) doit impérativement ajouter un \\0 final.

Sinon, des fonctions comme fputs() ou puts() ne s’arrêteront jamais.

---

## Conclusion

Day 8 marque un moment où les choses ont commencé à devenir concrètes.

Je n’étais plus seulement dans l’expérimentation, mais avec :
- des exemples fonctionnels
- une meilleure compréhension des forces et limites de CMOC
- une prise de conscience très nette des contraintes matérielles

Cette approche hybride s’est révélée être un bon compromis.

Et surtout, elle m’a forcé à penser comme un développeur 8-bits, où chaque octet, chaque appel et chaque timing comptent.
Avec les outils de développement modernes, ce genre de réflexes n’est pas naturel.
Au quotidien, je développe plutôt des microservices en .NET, où la priorité est la lisibilité du code et où l’on se soucie assez peu de l’empreinte mémoire.
Ici, c’est un tout autre monde.
Mais revenir aux fondamentaux, à des contraintes fortes et explicites, ça fait souvent beaucoup de bien 😉.

Le code associé à cet article est disponible ici :  
https://github.com/thlg057/mo5-tuto