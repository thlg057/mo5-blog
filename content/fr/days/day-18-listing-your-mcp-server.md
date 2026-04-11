---
title: "Day 18 : Référencer son serveur MCP, du formulaire web au registre officiel"
date: 2026-04-11
tags: ["publish", "mcp", "MO5", "ai", "npmjs", "registry"]
description: "Référencer son serveur MCP, du formulaire web au registre officiel."
draft: false
weight: 19
translationKey: day-18
---

Après avoir développé mon serveur MCP, je me suis dit qu'il serait dommage de le laisser dans son coin.

Un serveur MCP qui n'est pas référencé, c'est un peu comme un restaurant sans enseigne. Ça existe, mais personne ne le trouve. 😄

Alors j'ai décidé de le publier dans les registres MCP. Et comme d'habitude… c'était "simple" 😅

## C'est quoi un registre MCP ?

Un registre MCP, c'est un annuaire de serveurs MCP. Les coding agents (Claude Desktop, Cursor, Augment…) peuvent s'y connecter pour découvrir et installer des serveurs.

Il en existe plusieurs :
- **mcp.so** : le plus accessible, formulaire web
- **registry.modelcontextprotocol.io** : le registre officiel, géré par l'équipe MCP
- **GitHub MCP Registry** (github.com/mcp) : alimenté automatiquement par le registre officiel

L'idée : publier une fois sur le registre officiel, et les autres suivent automatiquement.

## Étape 0 : publier sur npmjs

Avant de pouvoir référencer le serveur MCP dans le registre officiel, il faut d'abord que le package soit publié sur npm. Le registre vérifie en effet que vous êtes bien le propriétaire du package.

J'avais déjà un `package.json` bien structuré, Claude m'avait aidé à le créer, donc pas de galère particulière à ce niveau.

Enfin presque. 😄

À ma première publication, npm m'a sorti ce warning :

```
npm warn publish npm auto-corrected some errors in your package.json when publishing.
npm warn publish errors corrected:
npm warn publish "bin[mo5-rag-mcp]" script name index.js was invalid and removed
```

Pourtant la config `bin` était bien présente dans mon fichier. Probablement un souci d'encodage. Un coup de :

```bash
npm pkg fix
npm publish --access public
```

…et c'était réglé. ✅

## Étape 1 : mcp.so — le plus simple

mcp.so propose un formulaire web. Nom, URL GitHub, config JSON, description. Rien de compliqué.

La seule subtilité : la description doit donner envie, pas juste décrire. J'ai mis en avant la toolchain complète, le SDK, le template, le serveur MCP, parce que c'est ça la vraie valeur ajoutée.

## Étape 2 : le registre officiel — un poil plus technique

Là, pas de formulaire web. Ça passe par un CLI : `mcp-publisher`.

**Ce qu'il faut préparer :**

### 1. Ajouter `mcpName` dans `package.json`

```json
"mcpName": "io.github.thlg057/mo5-mcp-server"
```

### 2. Bumper la version et republier sur npm

Petite erreur de ma part : j'ai changé la version à la main dans `package.json` au lieu d'utiliser la commande dédiée. Ça marche, mais c'est le genre de chose qui peut créer des incohérences. La bonne façon de faire :

```bash
npm version patch   # gère automatiquement package.json ET le tag git
npm publish --access public
```

### 3. Créer un fichier `server.json` à la racine du repo

```json
{
  "$schema": "https://static.modelcontextprotocol.io/schemas/2025-12-11/server.schema.json",
  "name": "io.github.thlg057/mo5-mcp-server",
  "description": "MCP server for Thomson MO5 C development — 6809, CMOC, SDK, toolchain.",
  "version": "1.0.2",
  "repository": {
    "url": "https://github.com/thlg057/mo5-mcp-server",
    "source": "github"
  },
  "packages": [
    {
      "registryType": "npm",
      "registryBaseUrl": "https://registry.npmjs.org",
      "identifier": "@thlg057/mo5-rag-mcp",
      "version": "1.0.2",
      "transport": { "type": "stdio" },
      "environmentVariables": [
        {
          "name": "RAG_BASE_URL",
          "description": "URL of the MO5 RAG server",
          "isRequired": true,
          "isSecret": false,
          "default": "https://retrocomputing-ai.cloud"
        }
      ]
    }
  ]
}
```

**Quelques pièges rencontrés :**

- La description est limitée à **100 caractères**. J'avais écrit un roman. 😄
- Le schéma évolue vite, j'avais la version `2025-07-09`, la bonne était `2025-12-11`
- Les noms de champs changent entre versions (`registry_type` → `registryType`)

Bref, la commande `mcp-publisher validate` est votre amie. Elle dit exactement ce qui cloche.

### 4. Installer le CLI, s'authentifier et publier

```bash
# Authentification interactive avec GitHub (code à saisir sur github.com/login/device)
mcp-publisher login github

mcp-publisher validate  # toujours valider avant !
mcp-publisher publish
```

### 5. Vérifier que c'est bien en ligne

```bash
curl "https://registry.modelcontextprotocol.io/v0.1/servers?search=io.github.thlg057"
```

Réponse 200 avec vos métadonnées → c'est bon. ✅


## Ce qui se passe ensuite

Une fois publié sur le registre officiel, les sous-registres (GitHub MCP Registry, Smithery, PulseMCP, Glama…) récupèrent automatiquement les infos. Pas besoin de soumettre partout.

En pratique, ça veut dire que n'importe quel utilisateur de Claude Desktop ou Cursor peut maintenant trouver et installer mon serveur MCP sans connaître mon GitHub.

C'est quand même sympa pour un projet qui au départ ne devait être qu'un outil perso. 😄


## Bilan

Au final, référencer un serveur MCP n'est pas compliqué, mais il faut connaître les étapes dans le bon ordre. Et comme souvent dans ce projet, les pièges sont dans les détails — un champ mal nommé, un schéma déprécié, une description trop longue.

Rien d'insurmontable, mais autant le documenter pour les suivants. 😉