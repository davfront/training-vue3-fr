[← Sommaire](0-index.md)

# [Draft] State management avec Pinia

Voir : [Pinia](https://pinia.vuejs.org/)

## Installation

```bash
npm install pinia
```

```js
import { createApp } from 'vue'
import { createPinia } from 'pinia'
import App from './App.vue'

const app = createApp(App)
app.use(createPinia())
app.mount('#app')
```

## Création d'un store

### Options API

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

### Composition API

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

## Utilisation d'un store dans un composant

### Avec setup()

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

Pour déstructurer les éléments du state, il faut utiliser `storeToRefs` :

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

```js
store.$subscribe((mutation, state) => {
  // mutation: détails sur le changement
  // state: valeurs après le changement
})
```