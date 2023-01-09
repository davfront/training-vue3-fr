[← Sommaire](0-index.md)

# Structuration de l'application avec les composants Vue

## Sommaire<!-- omit from toc -->

- [Présentation](#présentation)
- [Composants monofichiers avec Vite](#composants-monofichiers-avec-vite)
  - [Avantages](#avantages)
  - [Fichier séparés](#fichier-séparés)
  - [Vite](#vite)
- [Déclaration de composants](#déclaration-de-composants)
  - [Déclaration globale](#déclaration-globale)
  - [Déclaration locale](#déclaration-locale)
- [Communication entre composants](#communication-entre-composants)
  - [Props](#props)
  - [Evénements](#evénements)
  - [Utilisation de `v-model` sur un composant](#utilisation-de-v-model-sur-un-composant)
  - [Héritage des attributs](#héritage-des-attributs)
  - [Slots](#slots)
  - [Provide / inject](#provide--inject)

## Présentation

Voir : [Components Basics](https://vuejs.org/guide/essentials/component-basics.html)

Les composants nous permettent de diviser l'interface utilisateur en éléments indépendants et réutilisables, et de considérer chaque élément de manière indépendante. Il est courant de structurer une application sous forme d'un arbre de composants imbriqués :

```bash
Root
├── Header
├── Main
│   ├── Article
│   └── Article
└── Aside
    ├── Item
    ├── Item
    └── Item
```

Cela ressemble beaucoup à la manière dont nous imbriquons les éléments HTML natifs, mais Vue met en place son propre modèle de composant qui permet d'encapsuler du contenu et une logique personnalisés dans chaque composant.

## Composants monofichiers avec Vite

Voir : [Single-File Components](https://vuejs.org/guide/scaling-up/sfc.html)

Le composant monofichier Vue (ou SFC pour Single-File Component) est un format de fichier spécial qui nous permet d'encapsuler le template, la logique et le style d'un composant Vue dans un seul fichier. Il utilise l'extension de fichier `*.vue`. Voici un exemple de SFC :

```html
<script>
export default {
  data() {
    return {
      greeting: 'Hello World!'
    }
  }
}
</script>

<template>
  <p class="greeting">{{ greeting }}</p>
</template>

<style>
.greeting {
  color: red;
  font-weight: bold;
}
</style>
```

Comme nous pouvons le voir, les SFC Vue sont une extension naturelle du trio classique HTML, CSS et JavaScript. Les blocs `<template>`, `<script>` et `<style>` réunissent la vue, la logique et le style d'un composant dans le même fichier.

### Avantages

Bien que les SFC nécessitent une étape de compilation, ils présentent de nombreux avantages en retour :

- Création de composants modulaires en utilisant la syntaxe HTML, CSS et JavaScript familière
- Regroupement des préoccupations intimement liées
- Templates précompilés sans coût de compilation en temps d'exécution
- CSS à portée de composant
- Syntaxe plus ergonomique lors de l'utilisation de l'API de composition
- Optimisations en temps de compilation par analyse croisée du template et du script
- Prise en charge de l'IDE avec l'auto-complétion et la vérification des types pour les expressions de template
- Support de Hot-Module Replacement (HMR) prêt à l'emploi

Les SFC sont une caractéristique définissante de Vue en tant que framework et sont recommandées pour l'utilisation de Vue dans les scénarios suivants :

- Applications de page unique (SPA)
- Génération de sites statiques (SSG)
- Toute interface utilisateur non triviale pour laquelle une étape de compilation peut être justifiée pour une meilleure expérience de développement.

### Fichier séparés

Il est toujours possible d'utiliser des fichiers séparés avec les imports src :

```html
<!-- Fichier .vue -->
<template src="./template.html"></template>
<style src="./style.css"></style>
<script src="./script.js"></script>
```

### Vite

Doc: [Vite](https://vitejs.dev/)

Vite est un outil de compilation léger et rapide avec un support natif des SFC Vue. Il est créé par Evan You, l'auteur de Vue.

Pour démarrer un projet Vue avec Vite, il suffit de lancer :

```bash
npm init vue@latest
```

Cette commande va installer et exécuter `create-vue`, l'outil officiel de scaffolding de projet Vue.

## Déclaration de composants

Voir : [Component Registration](https://vuejs.org/guide/components/registration.html)

Un composant Vue doit être déclaré pour que Vue sache où trouver son implémentation lorsqu'il est rencontré dans un template. Il existe deux façons de déclarer des composants : globalement et localement.

### Déclaration globale

Nous pouvons rendre les composants accessibles globalement dans l'application Vue à l'aide de la méthode `app.component()` :

```js
import { createApp } from 'vue'

const app = createApp({})

app.component(
  // the registered name
  'MyComponent',
  // the implementation
  {
    /* ... */
  }
)

```

Avec les fichiers SFC :

```js
import ComponentA from './ComponentA.vue'
import ComponentB from './ComponentB.vue'

app.component('ComponentA', ComponentA)
app.component('ComponentB', ComponentB)
```

### Déclaration locale

Bien que pratique, l'enregistrement global présente quelques inconvénients :

- Le composant sera toujours inclus dans le bundle final.
- Il est difficile de localiser l'implémentation d'un composant utilisé.

La déclaration locale limite l'accés des composants déclarés au composant actuel seulement. Il rend la relation de dépendance plus explicite et est plus compatible avec le tree-shaking.

La déclaration locale est réalisée avec l'option `components` :

```html
<script>
import ComponentA from './ComponentA.vue'
import ComponentB from './ComponentB.vue'

export default {
  components: {
    ComponentA,
    ComponentB
  }
}
</script>

<template>
  <ComponentA />
</template>
```

## Communication entre composants

### Props

Voir : [Component Props](https://vuejs.org/guide/components/props.html)

Il est possible de transmettre des information d'un composant parent à un composant enfant grâce aux `props` (propriétés).

Les `props` sont des attributs personnalisés que vous pouvez définir sur un composant :

```html
<!-- BlogPost.vue -->
<script>
export default {
  props: {
    title: {
      type: String,
      required: true
    }
  }
}
</script>

<template>
  <h4>{{ title }}</h4>
</template>
```

Attention, les `props` sont immutables, ils ne peuvent pas être modifiés dans le composant où ils sont déclarés.

Usage :

```html
<BlogPost title="My journey with Vue" />
<BlogPost title="Blogging with Vue" />
<BlogPost title="Why Vue is so fun" />
```

#### Prop obligatoire ou facultative

L'option `required` permet de définir si une prop est obligatoire ou facultative (`false` par défaut). Si une prop est facultative, il est nécessaire de préciser avec l'option `default` sa valeur par défaut.

```js
export default {
  props: {
    // prop obligatoire
    title: {
      type: String,
      required: true
    },
    // prop facultative
    disabled: {
      type: Boolean,
      default: false
    }
  }
}
```

#### Validition des props

Il est possible de vérifier la validité d'une valeur transmise à une prop en passant une fonction à l'option `validator`.

La fonction `validator` prend en entrée la valeur de la prop, et renvoie `true` si la validation est réussie, ou `false` sinon.

```js
export default {
  props: {
    theme: {
      type: String,
      default: 'default',
      validator(value) {
        return ['default', 'success', 'error'].includes(value)
      }
    }
  }
}
```

### Evénements

Voir : [Events](https://vuejs.org/guide/components/events.html)

Un composant peut émettre des événements personnalisés grâce à la méthode `$emit`.

Depuis le template :

```html
<!-- MyComponent -->
<button @click="$emit('someEvent')">click me</button>
```

Depuis l'instance du composant :

```js
export default {
  methods: {
    submit() {
      this.$emit('someEvent')
    }
  }
}
```

Ces événements pourront être écoutés par le composant parent avec la directive `v-on`.

```html
<MyComponent @some-event="callback" />
```

#### Argument

Il est possible de transmettre une donnée en second argument.

```js
<button @click="$emit('increaseBy', 1)">
  Increase by 1
</button>
```

A l'écoute avec `v-on`, la donnée est passé en argumente de la méthode :

```html
<MyButton @increase-by="(n) => count += n" />
```

ou encore :

```html
<template>
  <MyButton @increase-by="(n) => count += n" />
</template>

<script>
export default {
  methods: {
    increaseCount(n) {
      this.count += n
    }
  }
}
```

#### $event

Parfois, nous avons également besoin d'accéder à l'événement original du DOM depuis le template. Vous pouvez le passer à une méthode en utilisant la variable spéciale `$event`, ou utiliser une fonction fléchée :

```html
<template>
  <button @click="handleClick($event)">Click me</button>
  <button @click="e => handleClick(e)">Click me</button>
</template>
```

### Utilisation de `v-model` sur un composant

Les événements peuvent également être utilisés pour créer des champs de saisie personnalisés qui fonctionnent avec `v-model`.

La directive `v-model` est en fait un raccourci pour définir la valeur d'une prop, et écouter l'événement qui transmet la nouvelle valeur saisie.

Sur un composant :

```html
<CustomInput v-model="searchText" />

<!-- Est équivalent à -->
<CustomInput
  :modelValue="searchText"
  @update:modelValue="newValue => searchText = newValue"
/>
```

De façon similaire sur un input natif :

```html
<input v-model="searchText" />

<!-- Est équivalent à -->
<input
  :value="searchText"
  @input="searchText = $event.target.value"
/>
```

Implémentation de `CustomInput`

```html
<!-- CustomInput.vue -->
<script>
export default {
  props: ['modelValue'],
  emits: ['update:modelValue']
}
</script>

<template>
  <input
    :value="modelValue"
    @input="$emit('update:modelValue', $event.target.value)"
  />
</template>
```

### Héritage des attributs

Voir : [Fallthrough Attributes](https://vuejs.org/guide/components/attrs.html)

Les attributs d'un composant qui ne sont pas explicitement déclarés dans les props sont directement ajouter aux attributs de l'élément racine du composant.

Example :

```html
<!-- Template du composant parent -->
<MyButton class="large" />

<!-- Template du composant enfant <MyButton> -->
<button>click me</button>

<!-- Rendu -->
<button class="large">click me</button>
```

Ici, `<MyButton>` n'a pas déclaré `class` comme prop acceptée. Néanmoins, `class` est automatiquement ajouté à l'élément racine de `<MyButton>`.

#### Event listeners

Les mêmes règles s'appliquent aux event listeners :

```html
<MyButton @click="onClick" />
```

L'event listener de `click` sera ajouté à l'élément racine de `<MyButton>`, c'est-à-dire l'élément `<button>` natif. Lorsque le `<button>` natif est cliqué, il déclenchera la méthode `onClick` du composant parent. Si le `<button>` natif a déjà un event listener de `click` lié avec `v-on`, alors les deux gestionnaires seront déclenchés.

#### Fusion des attributs `class` et `style`

Si l'élément racine du composant enfant possède déjà des attributs `class` ou `style`, ils seront fusionnés avec les valeurs de `class` et de `style` héritées du parent.

```html
<!-- Template du composant parent -->
<MyButton class="large" />

<!-- Template du composant enfant <MyButton> -->
<button class="btn">click me</button>

<!-- Rendu -->
<button class="btn large">click me</button>
```

#### Désactivation l'héritage

Il est possible de désactiver l'héritage avec l'option `inheritAttrs` :

```js
export default {
  inheritAttrs: false,
  // ...
}
```

#### Héritage sur un autre élément

Les attributs et event listeners non déclarés sont exposés sur l'object `$attrs`. Il est possible de choisir l'élément qui en hérite avec `inheritAttrs: false` et `v-bind="$attrs"`.

```html
<script>
export default {
  inheritAttrs: false
}
</script>

<template>
  <div class="btn-wrapper">
    <button class="btn" v-bind="$attrs">click me</button>
  </div>
</template>
```

#### Plusieurs éléments racine

Si un composant à plusieurs éléments racine. L'héritage d'attributs est désactivé par défaut. Pour l'activer, `v-bind="$attrs"` doit appliqué sur un élément.

### Slots

Voir : [Slots](https://vuejs.org/guide/components/slots.html)

Il est possible d'insérer du contenu HTML ou des composants Vue en utilisant l'élément `slot` dans le template du composant parent.

```html
<!-- Template du composant parent -->
<MyButton>
  <IconPlus /> Add an item <!-- slot content -->
</MyButton>

<!-- Template du composant enfant <MyButton> -->
<button class="btn">
  <slot /> <!-- slot outlet -->
</button>

<!-- Rendu -->
<button class="btn">
  <i class="icon-plus"></i> Add an item
</button>
```

#### Rendu par défaut

Il est possible de définir un contenu par défaut lorsque le contenu du slot est vide.

```html
<!-- Template du composant parent -->
<MyButton />

<!-- Template du composant enfant <MyButton> -->
<button class="btn">
  <slot>Click</slot>
</button>

<!-- Rendu -->
<button class="btn">Click</button>
```

#### Slots nommés

Il est possible de définir plusieurs zones de contenu avec les slots nommés.

Exemple :

```html
<!-- <BaseLayout> -->
<div class="container">
  <header>
    <slot name="header" />
  </header>

  <main>
    <slot />
  </main>

  <footer>
    <slot name="footer" />
  </footer>
</div>
```

Usage :

```html
<BaseLayout>
  <template #header>
    <h1>Here might be a page title</h1>
  </template>

  <template #default>
    <p>A paragraph for the main content.</p>
    <p>And another one.</p>
  </template>

  <template #footer>
    <p>Here's some contact info</p>
  </template>
</BaseLayout>
```

Un `slot` non nommé prendra automatiquemant le nom `default`.

#### SlotProps

Le contenu d'un `slot` n'a pas accès aux données du composant enfant.

Il est possible de transmettre des données ou méthodes en définissant des slotProps :

```html
<!-- Template du composant enfant <MyComponent> -->
<div>
  <slot :text="greetingMessage" :submit="submitMethod"></slot>
</div>

<!-- Template du composant parent -->
<MyComponent v-slot="slotProps">
  <button @click="slotProps.submit">
    {{ slotProps.text }}
  </button>
</MyComponent>
```

Exemple en déstructurant `v-slot` :

```html
<MyComponent v-slot="{ text, count }">
  {{ text }} {{ count }}
</MyComponent>
```

Exemple avec des slots nommés :

```html
<MyComponent>
  <template #header="headerProps">
    {{ headerProps }}
  </template>

  <template #default="defaultProps">
    {{ defaultProps }}
  </template>

  <template #footer="footerProps">
    {{ footerProps }}
  </template>
</MyComponent>
```

Example d'usage :

```html
<SizeObserver v-slot="{ width }">
  <MyComponent :class="{ 'is-small': width < 500 }">
    <!-- content ... -->
  </MyComponent>
</SizeObserver>
```

### Provide / inject

Voir : [Provide / inject](https://vuejs.org/guide/components/provide-inject.html)

La fonctionnalité `provide / inject` permet de fournir des données ou des fonctions à des composants enfants dans l'arbre de composants qui ne sont pas forcément des enfants directs.

```js
Root
├── Component 1 // provide A
│   ├── Component 11            // can inject A
│   │   ├── Component 111       // can inject A
│   │   ├── Component 112       // can inject A
│   └── Component 12            // can inject A
│       └── Component 121       // can inject A
└── Component 2
    └── Component 21 // provide B
        └── Component 211       // can inject B
            └── Component 2111  // can inject B
```

#### Provide

Un composant parent peut utiliser l'option de données `provide` pour fournir des données ou des fonctions à ses composants enfants.

```js
export default {
  provide: {
    message: 'hello!'
  }
}
```

`Provide` une variable déclarée dans `data` :

```js
export default {
  data() {
    return {
      message: 'hello!'
    }
  },
  provide() {
    // use function syntax so that we can access `this`
    return {
      message: this.message // not reactive
    }
  }
}
```

`Provide` avec réactivité :

```js
import { computed } from 'vue'

export default {
  data() {
    return {
      message: 'hello!'
    }
  },
  provide() {
    return {
      // explicitly provide a computed property
      message: computed(() => this.message)
    }
  }
}

```

##### Provide au niveau de l'App

```js
import { createApp } from 'vue'

const app = createApp({})

app.provide(/* key */ 'message', /* value */ 'hello!')
```

#### Inject

Les composants enfants peuvent utiliser l'option de données `inject` pour accéder aux données ou aux fonctions fournies par le composant parent.

```js
export default {
  inject: ['message'],
  created() {
    console.log(this.message) // injected value
  }
}
```

Il est possible d'accéder aux données injectées depuis `data` :

```js
export default {
  inject: ['message'],
  data() {
    return {
      // initial data based on injected value
      fullMessage: this.message
    }
  }
}
```
