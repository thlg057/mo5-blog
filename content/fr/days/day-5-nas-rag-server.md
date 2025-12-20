---
title: "Day 5 – Un NAS maison pour hoster mon RAG server"
date: 2025-12-11
tags: ["AI", "nas", "openmediavault", "raspberry pi"]
description: "Cinquième jour de vibe coding : comment hoster localement mon serveur RAG dédié au MO5."
draft: false
weight: 6
translationKey: "day-5"
---


L’objectif de ce **day 5**, c’était de résoudre un problème assez simple sur le papier, mais bloquant dans la pratique :  **où hoster localement mon RAG server dédié au MO5**.

### Petit rappel : c’est quoi un RAG server ?

Un **RAG (Retrieval-Augmented Generation)** server, c’est un backend qui combine :

- un moteur de recherche (souvent basé sur des embeddings / vecteurs)
- une base de connaissances (documents, sources, archives…)
- un LLM (local ou distant)

L’idée est simple :  
👉 au lieu de poser une question “dans le vide” à un modèle, on va **aller chercher des infos pertinentes dans une base de données**, puis **les injecter dans le prompt**.

Dans mon cas, ce RAG server est dédié au **MO5** :  il sert à interroger de la doc, des sources, des notes techniques, etc., de manière contextuelle.

### Le problème : mon environnement de dev

Mon environnement de dev principal tourne sur un **Raspberry Pi**.  
Si je fais tourner le RAG server sur mon laptop, il n’est tout simplement **pas accessible** depuis cet environnement.

J’ai bien un **NAS Synology** à la maison… mais :
- c’est un **DS110J**
- il est ancien
- très peu customisable
- ne sais pas hoster des services docker

Je me suis donc dit que c’était peut-être le bon moment pour **tourner la page du NAS Synology**  et le remplacer par quelque chose de plus... flexible.

## Un NAS à base de Raspberry Pi 4

L’idée :  
👉 monter un **NAS maison**, basé sur un **Raspberry Pi 4** (que j'ai déjà), qui puisse à la fois :
- servir de stockage
- héberger des services / images docker

### Mes besoins (très raisonnables)

Rien d’exotique :

- 📁 Un serveur de fichiers pour partager documents et sources
- 👥 Gérer des utilisateurs et des droits
- 🎬 Un service de streaming vidéo  (oui, l’UPnP c’est dépassé, je sais… mais je trouve ça pratique)
- 🐳 Pouvoir hoster des images Docker  (mon RAG server sera déployé comme ça)

Et surtout :
- quelque chose de **simple à configurer**
- avec une **interface graphique** (ça doit venir de mon background Windows 😄)
- pas trop prise de tête  

### OpenMediaVault

Après quelques recherches, **OpenMediaVault** cochait pas mal de cases.

L’installation est plutôt simple :

- partir d’une image **Raspberry Pi OS Lite**  (pas besoin d’interface graphique, il sera utilisé comme serveur)
- installer OpenMediaVault via le script officiel

```bash
wget -O - https://github.com/OpenMediaVault-Plugin-Developers/installScript/raw/master/install | sudo bash
```

## Le disque dur : détail important

J’avais déjà un disque dur externe de **1 To**, mais alimenté uniquement par USB

Pour un usage NAS, ce n’est **pas une super idée**.

👉 Le Raspberry Pi ne fournit pas toujours assez de puissance sur l’USB  
👉 Risque de déconnexions  et de corruption des données

Un disque dur **auto-alimenté** est bien plus adapté :
- alimentation dédiée
- stabilité
- fiabilité pour un service 24/7

### Upgrade matériel

Direction **Leboncoin**.  
Résultat :

- 💾 Disque dur Western Digital
- 📦 2 To
- 🔌 Auto-alimenté
- 💰 35,49 € frais de port inclus

Parfait.

## Configuration du NAS

Globalement, la configuration d’OpenMediaVault s’est faite sans gros souci.

Là où j’ai un peu plus galéré, c’est quand j’ai voulu recompiler à distance depuis **Visual Studio Code** sur mon laptop

### Problème SSH avec OpenMediaVault

Sur le NAS, j’ai créé **le même utilisateur que sur mon laptop** pour simplifier les accès.

Mais les utilisateurs créés via OpenMediaVault **ne font pas partie du groupe autorisé à se connecter en SSH**

Il faut donc ajouter l’utilisateur au bon groupe :

```bash
sudo usermod -aG _ssh myuser
```

⚠️ **Très important** :  
ne pas oublier le `_` devant `ssh`

Puis appliquer le changement :

```bash
sudo systemctl restart ssh
```

### VS Code refuse toujours de se connecter

Connexion SSH OK via **Putty**, mais **VS Code Remote-SSH** refusait de fonctionner

Dans les logs :

```
channel 3: open failed: administratively prohibited
ERROR: TCP port forwarding appears to be disabled on the remote host.
Ensure that the sshd_config has `AllowTcpForwarding yes`.
```

Et là, le déclic.

👉 Ce n’est pas un bug VS Code  
👉 C’est une **restriction SSH côté OpenMediaVault**

### Pourquoi VS Code a besoin de ça ?

VS Code Remote-SSH utilise :
- du **port forwarding**
- des tunnels TCP
- notamment `-D` (dynamic port forwarding / SOCKS)

OpenMediaVault désactive ça par défaut.

⚠️ Important : il faut ne pas modifier `sshd_config` à la main, OMV pourrait écraser vos changements. Il faut passer par l’interface OMV.

### La solution (propre)

Dans l’interface web OpenMediaVault :

- **Services → SSH**
- Onglet **Paramètres**

Activer / vérifier :

- ✅ Autoriser le transfert TCP
- ✅ Autoriser le tunneling
- ✅ Autoriser l’authentification par mot de passe (si utilisée)

Appliquer.

🎉 Nickel.  
VS Code peut maintenant se connecter en remote au Raspberry Pi sans problème.
![OPenMediaVault SSH configuration](/assets/openmediavault-ssh.jpg "OpenMediaVault SSH configuration")

## Et la suite ?

Il ne me reste plus qu’à :
- activer **Docker** dans OpenMediaVault
- déployer l’image de mon **RAG server dédié au MO5**

### Day 5 bis…

Une fois l’image buildée et lancée, mon API est accessible via le **port 8080**

En clair :
- `http://nas` → interface du NAS  (oui, pas encore de certificat 😅)
- `http://nas:8080` → API du RAG server

Ce n’est pas très cool de devoir retenir des numéros de ports, surtout si je veux héberger d’autres services plus tard.

👉 `https://nas/mo5-knowledges-api`  serait idéal comme adresse pour mon serveur !

Je me suis donc mis en tête d’installer un **reverse proxy**... Mais ça…  ce sera pour un autre day 😉