[← Sommaire](0-index.md)

# Configurer ESLint et Prettier dans VScode (pour un projet Vue)

## Qu'est-ce que ESLint et Prettier ?

- [**ESLint**](https://eslint.org) est un **linter javascript**.
  Il trouve les bugs, erreurs de syntaxe et d'autres problèmes. Des règles spécifiques peuvent être configurées pour personnaliser les conventions de code.

- [**Prettier**](https://prettier.io) est un *formateur de code** qui prend en charge plusieurs langages.

## Prérequis

Tout d'abord, vous devez avoir
[VSCode](https://code.visualstudio.com),
[Node.js](https://nodejs.org) et
[NPM](https://www.npmjs.com/) installés.

## Création du projet Vue

Créer un projet Vue3 avec la commande suivante :

```bash
npm init vue@3
```

N'oubliez pas de sélectionner les options `ESLint` et `Prettier`.

## Configuration

### 1. Installer les extensions VSCode

Ouvrez `VSCode` et installez les extensions pour
[ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) et
[Prettier](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode).

### 2. Mettre à jour les paramètres VSCode

Appuyez sur `Ctrl+Maj+P` (sur Windows) et tapez `Ouvrir les paramètres d'espace de travail (JSON)`.
Dans le fichier JSON ouvert, configurez `Prettier` en tant que formateur par défaut, et activez la mise en forme de code lors de la sauvegarde :

```json
"editor.defaultFormatter":"esbenp.prettier-vscode",
"editor.formatOnSave":true,
```

### 3. Configurer Prettier

Ouvrez le fichier `.prettierrc.json` et ajoutez les règles de formatage désirées. Par exemple:

```json
{
  "semi": false,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "none"
}
```

Voir la documentation [Prettier](https://prettier.io/docs/en/options.html).
