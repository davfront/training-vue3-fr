[← Sommaire](0-index.md)

# State management avec Pinia

Voir : [Pinia](https://pinia.vuejs.org/)

## Sommaire<!-- omit from toc -->

- [Présentation](#présentation)
- [Installation](#installation)
- [Définition d'un store](#définition-dun-store)
  - [API de Composition](#api-de-composition)
  - [API d'Options](#api-doptions)
- [Utilisation d'un store dans un composant](#utilisation-dun-store-dans-un-composant)
  - [Avec setup()](#avec-setup)
  - [Sans setup()](#sans-setup)
- [Modifier le state sans les actions](#modifier-le-state-sans-les-actions)
- [Observer les changements du state](#observer-les-changements-du-state)

## Présentation

Pinia est une nouvelle bibliothèque de gestion d'états pour Vue. Il s'agit d'un outil pour partager des données entre les composants de votre application. Pinia est maintenant également le choix recommandé par Vue (en remplacement de Vuex) et fait maintenant partie intégrante de l'écosystème Vue. Elle est maintenue et développée par les membres principaux de l'équipe Vue. Enfin, Pinia est extrèmement léger, ne pesant que 1ko.

L'état et la logique métier sont définis dans Pinia à l'aide de magasins ou `stores`.
Un `store` est une entité qui gère l'état et la logique métier qui n'est pas lié à votre arborescence de composants. En d'autres termes, il héberge un état global. C'est un peu comme un composant qui est toujours là et que tout le monde peut lire et modifier. Chaque `store` peut contenir `state`, `getters` et `actions` :

- le `state` définit les données gérées par un `store`,
- les `getters` renvoient une valeur calculée à partir du `state` et/ou d'autres `getters`,
- les `actions` sont des méthodes utilisées pour exécuter une logique métier ou des opérations asynchrones telles que des appels d'API.

Ces 3 concepts sont l'équivalent de `data`, `computed` et `methods` dans les composants (Options API).

## Installation

Installez Pinia avec `npm` :

```bash
npm install pinia
```

Dans `main.js`, créez une instance de Pinia et déclarez-la à l'application en tant que plugin :

```js
import { createApp } from 'vue'
import { createPinia } from 'pinia'
import App from './App.vue'

const app = createApp(App)
app.use(createPinia())
app.mount('#app')
```

## Définition d'un store

Un `store` est défini en utilisant `defineStore()` et nécessite un nom unique, passé en premier argument :

```js
import { defineStore } from 'pinia'

// You can name the return value of `defineStore()` anything you want, 
// but it's best to use the name of the store and surround it with `use` 
// and `Store` (e.g. `useUserStore`, `useCartStore`, `useProductStore`)
// the first argument is a unique id of the store across your application
export const useAlertsStore = defineStore('alerts', {
  // other options...
})
```

`defineStore()` accepte deux types distincts (avec deux syntaxes) pour son deuxième argument :

- une `fonction` (API de composition),
- ou un `objet d'options` (API d'options).

### API de Composition

De la même manière qu'avec l'API de Composition des composants, nous pouvons passer une fonction qui définit des propriétés réactives et des méthodes et qui renvoie un objet avec les propriétés et les méthodes que nous voulons exposer.

```js
// stores/counter.js
import { ref, computed } from 'vue'
import { defineStore } from 'pinia'

export const useCounterStore = defineStore('counter', () => {
  // state
  const count = ref(0)

  // getters
  const doubleCount = computed(() => count.value * 2)

  // actions
  function increment() {
    count.value++
  }

  return { count, doubleCount, increment }
})
```

### API d'Options

De la même manière qu'avec l'API d'Options des composants, nous pouvons passer un objet d'options avec des propriétés `state`, `getters` et `actions`.

```js
// stores/counter.js
export const useCounterStore = defineStore('counter', {
  state: () => ({
    count: 0,
    name: 'Eduardo'
  }),
  getters: {
    doubleCount: (state) => state.count * 2,
  },
  actions: {
    increment() {
      this.count++
    }
  }
})
```

## Utilisation d'un store dans un composant

### Avec setup()

Le `store` ne sera créé qu'à l'appel de la fonction `use...Store()` à l'intérieur de la fonction `setup()`.

```js
import { useCounterStore } from '@/stores/counter'

export default {
  setup() {
    const store = useCounterStore()

    // you can return the whole store instance to use it in the template
    return { store }
  },
}
```

Notez que le `store` est un objet encapsulé dans une `reactive()`, il n'est donc pas nécessaire d'écrire `.value` après les `getters`. Mais, comme les `props` dans la configuration, il ne peut pas être déstructuré.

Si vous souhaitez déstructurer les éléments du `store`, vous devez utiliser `storeToRefs` sur les éléments du `state` et des `getters` pour conserver leur réactivité :

```js
import { storeToRefs } from 'pinia'

export default {
  setup() {
    const store = useCounterStore()
    
    // `name` and `doubleCount` are reactive refs
    // This will also create refs for properties added by plugins
    // but skip any action or non reactive (non ref/reactive) property
    const { name, doubleCount } = storeToRefs(store)

    // the increment action can just be extracted
    const { increment } = store

    return {
      name,
      doubleCount,
      increment,
    }
  },
}

```

### Sans setup()

Pour utiliser un `store` avec l'API d'Options, Pinia fournit des fonctions de mapping :

- `mapStores` - pour accéder à tout le `store`,
- `mapState` - pour accéder à des propriété du `state` et des `getters` en lecture uniquement,
- `mapWritableState` - pour accéder à des propriété du `state` et pouvoir les modifier,
- `mapActions` - pour accéder à des `actions`.

Exemple :

```js
import { mapStores } from 'pinia'
import { useUserStore } from '@/stores/user'
import { useCartStore } from '@/stores/cart'

export default {
  computed: {
    ...mapStores(useCartStore, useUserStore)
  },
  methods: {
    async buyStuff() {
      if (this.userStore.isAuthenticated()) {
        await this.cartStore.buy()
        this.$router.push('/purchased')
      }
    }
  }
}
```

## Modifier le state sans les actions

Pour modifier le `state` d'un `store` depuis un composant sans utiliser les `actions`, Pinia fournit plusieurs méthodes :

- `$patch()` - pour appliquer plusieurs modifications à la fois au `state` avec une représentation partielle du `state`,
- `$state()` - pour remplacer tout le `state`,
- `$reset()` - pour réinitialiser le `state` avec les valeurs initiales.

```js
// modifier une donnée
store.x = 1

// modifier plusieurs données
store.$patch({ x: 1, y: 2})
// ou
store.$patch(state => {
  state.x = 1
  state.y = 2
})

// modifier tout le state
store.$state = { x: 1, y: 2, z: 3 }

// modifier tout le state avec les valeurs initiales
store.$reset()
```

## Observer les changements du state

Vous pouvez observer le `state` et ses changements avec la méthode `$subscribe()` d'un `store`. L'avantage d'utiliser `$subscribe()` plutôt qu'un classique `watch()` est que `$subscribe()` ne se déclenche qu'une seule fois après la méthode `$patch()`.

```js
store.$subscribe((mutation, state) => {
  // mutation: détails sur le changement
  // state: valeurs après le changement
})
```

---

Découvrez les toutes fonctionnalités sur le site officiel de [Pinia](https://pinia.vuejs.org/).
