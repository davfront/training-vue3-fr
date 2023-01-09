[← Sommaire](0-index.md)

# Introduction à Vue.js

Voir : [Vue.js - Introduction](https://vuejs.org/guide/introduction.html)

## Sommaire<!-- omit from toc -->

- [Présentation](#présentation)
- [Un framework progressif](#un-framework-progressif)
- [Composants monofichiers](#composants-monofichiers)
- [API d'options et API de composition](#api-doptions-et-api-de-composition)
  - [API d'options](#api-doptions)
  - [API de composition](#api-de-composition)
- [Outils](#outils)

## Présentation

Vue.js est un framework JavaScript open-source pour la création d'applications web interactives. Il a été créé par Evan You et est devenu très populaire pour sa simplicité et sa flexibilité.

Vue.js utilise une syntaxe HTML spéciale appelée template qui permet de créer des composants réutilisables et de gérer l'état de l'application de manière simple. Vue.js fournit également un mécanisme de binding de données en temps réel, ce qui signifie que lorsque les données de l'application sont mises à jour, l'interface utilisateur est automatiquement mise à jour en conséquence.

Vue.js est souvent utilisé comme une alternative légère et facile à utiliser aux frameworks plus complexes tels que Angular ou React. C'est un outil populaire pour la création de prototypes rapides et de petites applications, mais peut également être utilisé pour de plus grandes applications.

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Mon application Vue.js</title>
  </head>
  <body>
    <div id="app">
      {{ message }}
    </div>

    <script src="https://unpkg.com/vue@3"></script>
    <script>
      const app = Vue.createApp({
        data() {
          return {
            message: 'Bonjour le monde !'
          }
        }
      })

      app.mount('#app')
    </script>
  </body>
</html>

```

Dans cet exemple, nous avons inclus le script CDN de Vue.js dans notre page HTML et créé une instance de Vue.js en utilisant la méthode `Vue.createApp()`. Nous avons défini une variable de données appelée `message` qui contient une chaîne de caractères.

Pour afficher l'application Vue.js, nous avons utilisé la méthode `app.mount()` en spécifiant l'élément HTML #app comme l'élément de racine. Dans le template HTML, nous avons utilisé la syntaxe double accolades pour afficher la valeur de message. Lorsque l'application est chargée, le texte `"Bonjour le monde !"` sera affiché à l'intérieur de l'élément #app. Si nous mettons à jour la valeur de message, l'interface utilisateur sera automatiquement mise à jour en conséquence.

## Un framework progressif

Vue.js est un framework progressif car il été pensé pour être utilisé de différentes manières selon les besoins de votre projet. Vous pouvez utiliser toutes les fonctionnalités de Vue.js pour créer des applications Web complètes, ou vous pouvez simplement inclure certaines parties de Vue.js dans votre projet pour ajouter une interaction à une page Web existante.

Voici quelques exemples de la façon dont Vue.js peut être utilisé de manière progressive :

- Vous pouvez utiliser Vue.js comme une `simple bibliothèque JavaScript` en incluant un fichier script dans votre page HTML. Vous pouvez alors utiliser Vue.js pour ajouter de l'interaction à une page Web existante en utilisant des directives et des composants.

- Vous pouvez utiliser Vue.js avec une architecture de type `"Single-Page Application" (SPA)` en utilisant une bibliothèque de routage telle que `vue-router` pour gérer les différentes routes de votre application et une bibliothèque de gestion d'état telle que vuex pour gérer l'état global de votre application.

- Vue.js peut être utilisé avec une architecture de type `"Server-Side Rendering" (SSR)` pour améliorer les performances de votre application en générant le HTML côté serveur. Cela peut être particulièrement utile si vous souhaitez améliorer le référencement de votre site Web ou si vous souhaitez que votre application soit accessible à des utilisateurs qui utilisent des navigateurs qui ne prennent pas en charge JavaScript.

En résumé, Vue.js est un framework progressif parce qu'il peut être utilisé de différentes manières selon les besoins de votre projet, et vous pouvez commencer par utiliser simplement une partie de ses fonctionnalités et étendre votre utilisation de Vue.js au fur et à mesure que votre projet grandit.

## Composants monofichiers

Les composants monofichiers (fichiers avec l'extension `.vue`) sont une façon de définir des composants Vue.js dans un fichier unique, qui contient à la fois le code HTML, CSS et JavaScript associé à un composant.

Cela peut être pratique pour maintenir votre code organisé et facile à maintenir, surtout si vous avez de nombreux composants dans votre projet.

La compilation est nécessaire pour transformer le code des composants monofichiers Vue.js en code JavaScript exécutable par un navigateur.

Exemple de monofichier `.vue` :

```html
<!-- code JavaScript du composant -->
<script>
export default {
  data() {
    return {
      count: 0
    }
  }
}
</script>

<!-- code HTML du composant -->
<template>
  <button @click="count++">Count is: {{ count }}</button>
</template>

<!-- code CSS du composant -->
<style scoped>
button {
  font-weight: bold;
}
</style>
```

Vous pouvez réécrire le code du fichier `.vue` en un fichier `.js` comme ceci :

```js
export default {
  data() {
    return {
      count: 0
    }
  },
  template: `
    <button @click="count++">Count is: {{ count }}</button>
  `
}
```

## API d'options et API de composition

Vue.js 3 offre deux styles d'API différents pour créer et gérer les composants : l'**API d'options** (ou options API) et l'**API de composition** (ou composition API).

### API d'options

L'API d'options est le style d'API le plus utilisé dans les versions précédentes de Vue.js et consiste à définir les propriétés et les méthodes d'un composant en utilisant des options déclarées dans l'objet `component`. Voici un exemple de composant Vue déclaré avec l'API d'options :

```js
export default {
  data() {
    return {
      count: 0
    }
  },
  methods: {
    increment() {
      this.count++
    }
  },
  template: `
    <button @click="increment">Count is: {{ count }}</button>
  `
}
```

### API de composition

L'API de composition est une nouvelle API introduite dans Vue.js 3 qui offre une approche différente pour déclarer et gérer les composants. Elle permet de décomposer les différentes parties d'un composant en fonctions distinctes et de les combiner pour créer le composant final. Voici un exemple de composant Vue déclaré avec l'API de composition :

```js
import { ref, onMounted } from 'vue'

export default function MyComponent() {
  const count = ref(0)

  function increment() {
    count.value++
  }

  onMounted(() => {
    console.log('Mounted!')
  })

  return {
    count,
    increment
  }
}
```

L'API de composition permet une meilleure flexibilité et une meilleure lisibilité du code, mais peut être un peu plus complexe à apprendre pour les débutants. Vous pouvez utiliser l'une ou l'autre de ces APIs en fonction de vos préférences et de vos besoins. Toutefois, il est recommandé d'utiliser l'API de composition pour les nouveaux projets, car elle offre de nombreux avantages sur l'API d'options.

## Outils

Voir : [Vue.js - Tooling](https://vuejs.org/guide/scaling-up/tooling.html)

Les outils préconisés pour le développement d'une application Vue.js sont :

- Un environnement avec [**Node.js**](https://nodejs.org) installé (version 16 ou supérieure),
  
- L'outil de build [**Vite**](https://vitejs.dev/) :
  - Serveur de développement rapide
  - Compilation automatique
  - Optimisation des ressources
  - Remplace `vue-cli` utilisé pour la version 2 de Vue.js

- L'éditeur de logiciel [**VSCode**](https://code.visualstudio.com/) avec le plugin [**Volar**](https://github.com/johnsoncodehk/volar) :
  - Coloration syntaxique
  - Prise en charge de TypeScript
  - IntelliSense

- Un navigateur avec l'extension [**Vue Devtools**](https://devtools.vuejs.org/):
  - [Pour Chrome](https://chrome.google.com/webstore/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd)
  - [Pour firefox](https://addons.mozilla.org/en-US/firefox/addon/vue-js-devtools/)

Pour démarrer un projet `Vue.js` avec `Vite` :

```bash
npm init vue@3
```
