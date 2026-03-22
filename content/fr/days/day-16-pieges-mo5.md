---
title: "Day 16 : Les pièges du MO5, ou comment perdre des soirées pour une variable"
date: 2026-03-22
tags: ["best practices", "SDK", "MO5", ]
description: "Les pièges du MO5, ou comment perdre des soirées pour une variable."
draft: false
weight: 16
translationKey: day-16
---

Je pensais avoir compris le MO5.

En réalité, c'est le MO5 qui me testait 😉

Depuis que je développe mon petit jeu, j'ai accumulé pas mal de galères, certaines prévisibles, d'autres complètement absurdes, et surtout plusieurs pièges dans lesquels je suis tombé... alors que je connaissais déjà la théorie ou que je les avais déjà expérimenté.

C'est probablement ça le plus frustrant.

Tu sais que le piège existe, tu l'as déjà lu quelque part, mais tu tombes quand même dedans.

Et tu perds ta soirée 😉

Je me suis dit que ça valait le coup de faire un petit retour d'expérience, histoire d'éviter à d'autres de passer par les mêmes moments de solitude.

## L'émulateur qui ment

Premier piège, et probablement le plus traître : faire confiance à l'émulateur.

J'utilisais DCMOTO pour tester mon code, ce qui est assez logique au début, c'est pratique, rapide, pas besoin de sortir le matériel.

Sauf que...

DCMOTO ne reproduit pas toujours fidèlement le comportement du vrai MO5.

Je m'étais déjà fait avoir avec les couleurs, qui n'étaient pas du tout celles affichées sur la vraie machine, je pensais avoir mal compris la palette, j'ai perdu du temps à vérifier mon code, alors que le problème venait simplement de l'émulateur.

Et évidemment... j'ai refait la même erreur 😉

Cette fois, c'était avec l'affichage des sprites.

Je travaillais sur un rendu avec fond transparent, tout semblait correct dans le code, logique propre, gestion de la couleur de fond, rien d'anormal.

Et pourtant, à l'écran, j'avais systématiquement un fond noir, une sorte de "bave" lorsque le sprite se déplaçait.

Impossible de comprendre pourquoi.

J'ai passé un bon moment à remettre en question mon code, mes fonctions, mes calculs, tout y est passé.

Puis j'ai eu un doute...  Je teste sur un vrai MO5... Et là... Tout fonctionne parfaitement.

Aucune trace noire, transparence nickel, le sprite ne bave pas, exactement le comportement attendu.

👉 le bug n'existait pas, c'était l'émulateur ! Encore l'émulateur... Et pourtant, je m'étais déjà fait avoir une fois avec DCMOTO...

Depuis, j'évite DCMOTO, et j'utilise plutôt un émulateur en ligne (celui ci est pas mal: https://scemino.github.io/yame_wp/mo5.html), ou mieux, je teste dès que possible sur la vraie machine.

## La variable innocente qui casse tout

Deuxième piège, beaucoup plus subtil., et franchement, celui-là m'a fait perdre plusieurs soirées.

Avec les langages modernes, on a pris de bonnes habitudes, déclarer une variable au moment où on en a besoin, et souvent l'initialiser directement.

C'est propre, lisible, recommandé.

Sauf que... CMOC ne joue pas avec ces règles...

Le compilateur respecte le standard C89, ce qui impose que toutes les variables soient déclarées au début du bloc.

Et surtout, dans mon cas, déclarer une variable au milieu du code, ou pire, l'initialiser directement à la déclaration, peut provoquer un comportement complètement imprévisible.

Et le pire dans tout ça ?

- ça compile parfaitement
- Aucune erreur.
- Aucun warning.
- Rien...

Le programme se lance... et ne fonctionne pas... ou plante... ou fait n'importe quoi.

Du coup, naturellement, tu remets en cause ton code.
- Tu regardes la logique.
- Tu suspectes tes fonctions.
- Tu penses à un problème mémoire.

Alors que... c'était juste la variable, pas déclarée au bon endroit ou directement initialisée.

Et comme on n'ajoute jamais une variable toute seule, mais souvent avec du code autour, tu cherches partout sauf au bon endroit.

Belles soirées en perspective 😉

## La limite invisible de la stack

Troisième piège, encore plus vicieux... le nombre de variables dans une fonction

Sur le MO5, avec CMOC, les variables locales sont stockées sur la stack.

Et la stack est très limitée...

Très très limitée...

Extrêmement limitée...

Dans mon cas, j'ai constaté qu'une fonction pouvait contenir environ 6 ou 7 variables locales, selon leur taille.

Et là encore, le comportement est très particulier.

Tu ajoutes une variable. Une seule.

Et ton programme ne fonctionne plus.

- Pas de message d'erreur.
- Pas de warning.
- Parfois même, il ne démarre pas du tout.
- Il bloque avant même d'entrer dans ta boucle principale.

Et toi, tu cherches... Tu analyses ton code... Tu penses avoir introduit un bug logique.

Alors que... c'est juste la stack qui a explosé

Le genre de problème qui n'existe plus du tout dans les environnements modernes, et qui est extrêmement déroutant quand on retombe dessus.

## Ce que j'ai appris (parfois à mes dépens)

Après toutes ces galères, j'ai fini par accumuler quelques règles simples.

Pas des règles théoriques, mais des choses que j'ai vérifiées en pratique, parfois après plusieurs heures de debug.

Par exemple, préférer `unsigned char` à `int`, parce que le 6809 est un processeur 8 bits et que les opérations 16 bits coûtent beaucoup plus cher, éviter les multiplications et les divisions, parce qu'elles sont très coûteuses en cycles, privilégier les incréments de pointeurs plutôt que les index de tableau, parce que ça évite des calculs inutiles, limiter les accès à la VRAM et regrouper les écritures, parce que chaque accès a un coût, utiliser des opérations bit à bit plutôt que des opérations arithmétiques quand c'est possible, faire attention aux underflows sur les unsigned char, déclarer toutes les variables en début de fonction et les initialiser ensuite, et surtout limiter le nombre de variables locales, en basculant certaines en variables globales statiques quand nécessaire.

Ce sont des choses assez évidentes quand on connaît bien l'architecture.

Mais quand on vient du monde moderne, ça demande un vrai changement de mindset.

J’ai synthétisé toutes ces règles dans un fichier .md partagé via mon SDK.\
Je les ai également injectées dans mon serveur RAG afin qu’elles puissent être utilisées par les coding agents.

## Conclusion

Le MO5 est une machine simple... mais pas indulgente 😉

- Elle ne pardonne pas les approximations.
- Elle ne donne pas toujours d'erreurs claires.
- Elle te force à comprendre ce que tu fais.

Mais c'est aussi ce qui rend l'expérience intéressante.

Parce que chaque bug corrigé, chaque contrainte comprise, donne vraiment l'impression de progresser.

Et même si certaines soirées sont un peu longues... ça reste très fun (enfin, pas toujours 😉)
