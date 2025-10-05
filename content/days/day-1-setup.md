---
title: "Day 1 – Configuration de l’environnement de développement"
date: 2025-10-04
draft: false
summary: "Mise en place d’un environnement minimaliste pour développer sur Thomson MO5, en combinant Raspberry Pi, outils du 6809 (CMOC, LWTOOLS) et un soupçon de modernité avec Visual Studio Code."
---

Mon laptop est un modeste **Asus sous Windows** (Core i3, 8 Go de mémoire).  
J’ai **Visual Studio Code** d’installé, ainsi que **SourceTree** pour la gestion du code.  
 
Pour un projet précédent, j’avais installé **MSYS2** ([msys2.org](https://www.msys2.org/)) afin de développer un interpréteur BASIC en C.  
Je pensais qu’avec tout ça, la mise en place d’un environnement de développement pour le **Thomson MO5** serait simple… mais pas tout à fait 😅

---

## Premiers essais sous Windows

D’après les forums, pour développer sur les processeurs **Motorola 6809**, il faut installer deux outils :
- [CMOC](http://gvlsywt.cluster051.hosting.ovh.net/dev/cmoc.html)
- [LWTOOLS](http://www.lwtools.ca/)

Je me suis donc mis en quête de versions précompilées pour Windows, sans grand succès.  
Il semblait que ces outils soient davantage adaptés à **Linux**, j’ai donc tenté une installation sous **MSYS2**.

Mais la compilation de CMOC ne s’est pas bien passée :  
beaucoup d’erreurs liées à des fichiers d’en-tête spécifiques à Linux (`sys/select.h`, `sys/wait.h`, etc.), absents sous Windows, même avec MSYS.

---

## Plan B : un Raspberry Pi comme machine de développement 🧠

Plutôt que d’installer WSL (environnement Linux sous Windows), j’ai préféré utiliser mon **Raspberry Pi 4** — plus simple et surtout plus léger.

J’ai installé **Raspberry Pi OS (Raspbian)** via *Raspberry Pi Imager*, puis activé :
- le **Wi-Fi**
- **Samba**
- et **SSH**, pour travailler à distance depuis mon PC.

Ensuite, connexion SSH au Pi et installation des outils de développement :

```bash
sudo apt install build-essential bison flex git vim nano
```

---

## Installation de CMOC

Téléchargement :

```bash
wget https://freshcode.club/projects/cmoc/files/cmoc-0.1.93.tar.gz
tar -xzf cmoc-0.1.93.tar.gz
cd cmoc-0.1.93
```

Compilation et installation :

```bash
./configure
make
sudo make install
```

Vérification :

```bash
cmoc --version
```

---

## Installation de LWTOOLS

Téléchargement :

```bash
wget http://www.lwtools.ca/releases/lwtools/lwtools-4.24.tar.gz
tar -xzf lwtools-4.24.tar.gz
cd lwtools-4.24
```

Compilation et installation :

```bash
make
sudo make install
```

---

## Développement à distance avec VS Code

Pour travailler confortablement, j’ai installé l’extension **Remote - SSH** dans Visual Studio Code.  
Elle me permet de me connecter directement à mon Raspberry Pi et de lancer les commandes de compilation sans quitter mon éditeur.

Un autre avantage de Visual Studio Code, c’est la possibilité d’y activer des agents de développement comme GitHub Copilot ou d’autres assistants IA.

Pour un projet aussi atypique que le développement sur Thomson MO5, ces outils modernes peuvent s’avérer précieux, que ce soit pour générer du code C, comprendre d’anciens concepts, ou m’aider à optimiser des routines pour le processeur 6809.

---

## Tests et émulateurs

Avant d’essayer sur le vrai MO5, mieux vaut tester les programmes dans un **émulateur**.

- Sous Linux, il existe plusieurs émulateurs MO5.  
- Sous Windows (mon cas), j’ai choisi **DCMOTO** ([dcmoto.free.fr](http://dcmoto.free.fr/emulateur/index.html)).

⚠️ La plupart de ces sites sont en HTTP (non sécurisé), ce qui déclenche quelques alertes sur les navigateurs modernes.  
Mais pas d’inquiétude : ce sont des **références historiques fiables** dans le monde du rétro-computing.

---

## Résumé de la configuration

### 🖥️ Windows :
- Visual Studio Code + extension **Remote - SSH**
- SourceTree (je préfère éviter la ligne de commande Git 😄)

### 🍓 Raspberry Pi 4 (Raspbian) :
- CMOC  
- LWTOOLS  

---

> 💬 En résumé : pour retrouver l’esprit du développement 8 bits, j’ai recréé un environnement minimaliste, mais fonctionnel, basé sur un Raspberry Pi.  
> Ce setup me servira pour compiler et tester mes futurs programmes destinés au Thomson MO5.
