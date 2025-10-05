---
title: "De l'IA au Thomson MO5 : mon aventure rétro-computing"
date: 2025-10-05
draft: false
---

Cet été, j’ai lu le livre de **Bill Gates, *Source Code***, ainsi qu’une revue consacrée aux ordinateurs rétro de ma jeunesse.  
Ces lectures m’ont inspiré, et j’ai voulu me replonger dans le contexte de l’époque en développant un **interpréteur BASIC inspiré de l’Altair**.

Mon objectif : obtenir la plus faible empreinte mémoire et processeur possible.  
N’étant pas tout à fait maso 😄, je ne suis pas parti d’un code assembleur, mais plutôt d’un programme en **langage C**, sans utiliser les bibliothèques standard, afin de limiter l’empreinte mémoire.

Pour ce projet, je me suis appuyé sur l’IA **ChatGPT**, via le site web.  
Elle m’a aidé à :
- réfléchir à l’architecture du code (rôles et responsabilités),
- redéfinir les fonctions de base (`printf`, `strcpy`, etc.) pour éviter les bibliothèques standard,    
- m'aider à développer certaines parties plus complexes.

---

### Vers une vraie machine : le Thomson MO5

Fort de cette expérience, j’ai voulu aller plus loin : **développer une application pour un ordinateur de l’époque**.

Sur divers forums, j’avais vu des passionnés coder en C pour le **Commodore 64**, avec un setup de développement sous Windows.  
Je me suis donc mis en quête d’en acheter un sur Le Bon Coin… mais :
- les prix étaient délirants,  
- impossible de vérifier le bon fonctionnement à distance (pas de photo allumé, écran branché, etc.).

Puis je suis tombé sur **une annonce pour un Thomson MO5** : vrai clavier (pas clavier-gomme), lecteur de cassettes, crayon optique, le tout pour **80 € à 30 minutes de chez moi**.  
**Banco !** 🎉 C’est ainsi qu’est née cette aventure.

---

### Pourquoi le MO5 ?

Les ressources en ligne sont plus limitées que pour le Commodore, mais avec l’émergence de l’IA, je me suis dit qu’elle pouvait devenir **un formidable atout** pour m’aider à développer.

Je sais aussi que **plus une IA a de contexte, plus elle est précise et efficace**.  
Mon projet est donc double :
1. Créer une **application pour le Thomson MO5**, avec l’aide de l’IA.  
2. Fournir une **documentation et un contexte technique** exploitables par les IA, afin de permettre à d’autres développeurs en herbe de s’y mettre à leur tour.

---

Bref, une aventure à la croisée du passé et du futur :  
**redonner vie à une machine de 1984 grâce aux outils de 2025.** 🚀