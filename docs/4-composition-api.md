
[← Sommaire](0-index.md)

# API de composition

Voir : [Composition API FAQ](https://vuejs.org/guide/extras/composition-api-faq.html)

## Sommaire<!-- omit from toc -->

- [Présentation](#présentation)
- [Option `setup`](#option-setup)
- [Nouveau système de réactivité](#nouveau-système-de-réactivité)
  - [Fonction `ref()`](#fonction-ref)
  - [Fonction `reactive()`](#fonction-reactive)
- [Syntaxe](#syntaxe)
  - [Méthodes](#méthodes)
  - [Computed properties](#computed-properties)
  - [Watchers](#watchers)
  - [Lifecycle hooks](#lifecycle-hooks)
  - [Template refs](#template-refs)
- [Composants monofichiers et `<script setup>`](#composants-monofichiers-et-script-setup)
  - [Utilisation de composants](#utilisation-de-composants)
  - [Fontions `defineProps()` et `defineEmits()`](#fontions-defineprops-et-defineemits)
- [Composables et VueUse](#composables-et-vueuse)
  - [Composables](#composables)
  - [VueUse](#vueuse)

## Présentation

L'API de composition introduite dans Vue.js 3 offre une nouvelle syntaxe pour créer des composants, qui améliore la lisibilité et la réutilisation du code.

Les options de l'API d'options sont ici remplacées par une fonction de configuration qui renvoie des propriétés à utiliser dans le modèle.

La nouvelle API de composition est assez similaire aux [hooks de React](https://reactjs.org/docs/hooks-intro.html).

Voici un exemple de composant déclaré avec l'API de composition :

```html
<template>
  <div>
    <button @click="incrementCount">{{ count }}</button>
  </div>
</template>

<script>
import { ref } from 'vue'

export default {
  setup() {
    const count = ref(0)

    const incrementCount = () => { count.value++ }

    return { count, incrementCount }
  }
}
</script>
```

## Option `setup`

Pour utiliser l'API de composition, vous devez définir dans votre composant une option `setup` de type fonction. On peut y déclarer les mêmes fonctionnalités que propose l'API d'options :

- des propriétés de données,
- des méthodes,
- des computed properties,
- des watchers,
- des lifecycle hooks.

Exemple :

```html
<template>
  <div>
    <p>Spaces Left: {{ spacesLeft }} out of {{ capacity }}</p>
    <h2>Attending</h2>
    <ul>
      <li v-for="(name, index) in attending" :key="index">
        {{ name }}
      </li>
    </ul>
    <button @click="increaseCapacity()">Increase Capacity</button>
  </div>
</template>

<script>
import { ref, reactive, computed, watch, onMounted } from 'vue'

export default {
  setup() {

    // ref (works for every type)
    const capacity = ref(4)

    // reactive (only works for non-primitive types: Object, Array, ...)
    const attending = reactive(['Tim', 'Bob', 'Joe'])

    // computed
    const spacesLeft = computed(() => {
      return capacity.value - attending.length
    })

    // watch
    watch(spacesLeft, async (newSpacesLeft, oldSpacesLeft) => {
      console.log(`spacesLeft has changed from ${oldSpacesLeft} to ${newSpacesLeft}.`)
    })

    // watch getter
    watch(
      () => spacesLeft > 0,
      (hasSpacesLeft) => {
        console.log(hasSpacesLeft ? 'There is space left.' : 'There is no space left.' )
      }
    )

    // method
    function increaseCapacity() {
      capacity.value++
    }

    // lifecycle hook
    onMounted(() => {
      console.log(`the component is now mounted.`)
    })

    // Gives our template access to these objects & functions
    return { capacity, attending, spacesLeft, increaseCapacity }
  }
}
</script>
```

Attention, la fonction `setup` doit retourner un objet avec les éléments qui doivent être accessibles par le template et les autres options de l'API d'options.

## Nouveau système de réactivité

Avec l'API de composition, la gestion de la réactivité a été complètement revue et améliorée. Au lieu d'utiliser un objet `data` qui est automatiquement rendu réactif, l'API de composition propose deux nouvelles fonctions : `ref` et `reactive`.

### Fonction `ref()`

La fonction `ref()` est une fonction qui rend une variable réactive. Pour l'utiliser, vous devez l'importer en haut de la section script : `import { ref } from 'vue'`.

Elle peut être utilisée avec des primitives, telles que des nombres/chaînes de caractères (contraiement à reactive `reactive()`).

Exemple :

```html
<template>
  <div>
    <button @click="incrementCount">{{ count }}</button>
  </div>
</template>

<script>
import { ref } from 'vue'

export default {
  setup() {
    const count = ref(0)

    const incrementCount = () => { count.value++ }

    return { count, incrementCount }
  }
}
</script>
```

Vous pouvez vous représenter `count` structuré comme ceci : `{ value: 0 }`.

C'est pourquoi, lorsque la fonction `incrementCount()` veut incrémenter la valeur de `count`, elle doit exécuter `count.value++`. Mettre à jour juste `count++` ne fonctionne pas.

Par contre, vous pouvez accéder directement à `count` dans la section `<template>`. Vous n'avez pas besoin d'écrire `{{ count.value }}`, vous pouvez écrire simplement `{{ count }}`, cela est géré automatiquement par le système de template de Vue.

**Pour modifier les valeurs de `ref()`, souvenez-vous toujours d'utiliser l'attribut `.value`, sinon ça ne fonctionnera pas.**

### Fonction `reactive()`

La fonction `reactive()` permet comme la fonction `ref()` de rendre des variables réactives, mais contrairement à `ref()`, **la fonction `reactive()` ne peut pas être utilisée avec les variables primitives**. Elle fonctionne uniquement avec les variables complexes telles que les objets et les tableaux.

De plus, contrairement à `ref()`, vous pouvez modifier une variable `reactive()` directement (sans utiliser l'attribut `.value`).

```html
<template>
  <div>
    {{ user.name }}
    <button @click="changeName">Change Name</button>
  </div>
</template>

<script>
import { reactive } from 'vue'

export default {
  setup() {
    const user = reactive({ name: 'John' })
    const changeName = () => { user.name = 'Jane' }
    return { user, changeName }
  }
}
</script>
```

## Syntaxe

### Méthodes

Avec l'API d'options :

```js
export default {
  data() {
    return {
      count: 0
    }
  },
  methods: {
    incrementCount() {
      this.count++
    }
  }
}
```

Avec l'API de composition :

```js
import { ref } from 'vue'

export default {
  setup() {
    const count = ref(0)
    const incrementCount = () => { count.value++ }
    return { count, incrementCount }
  }
}
```

### Computed properties

Avec l'API d'options :

```js
export default {
  data() {
    return {
      price: 100,
      quantity: 2
    }
  },
  computed: {
    totalPrice() {
      return this.price * this.quantity
    },
    printTotalPrice() {
      console.log(`The total price is ${this.totalPrice}.`)
    }
  }
}
```

Avec l'API de composition :

```js
import { ref, computed } from 'vue'

export default {
  setup() {
    const price = ref(100)
    const quantity = ref(2)

    const totalPrice = computed(() => {
      return price.value * quantity.value
    })

    const printTotalPrice = () => {
      console.log(`The total price is ${totalPrice.value}.`)
    }

    return { price, quantity, totalPrice, printTotalPrice }
  }
}

```

Pour accéder à la valeur d'une variable `computed()`, vous devez utiliser l'attribut `.value` (de la même manière que pour une variable `ref()`).

### Watchers

#### Fonction `watch()`

Avec l'API d'options :

```js
export default {
  data() {
    return {
      count: 0
    }
  },
  methods: {
    incrementCount() {
      this.count++
    }
  },
  watch: {
    count(newCount, oldCount) {
      console.log(`count has changed from ${oldCount} to ${newCount}.`)
    }
  }
}
```

Avec l'API de composition :

```js
import { ref, watch } from 'vue'

export default {
  setup() {
    const count = ref(0)

    const incrementCount = () => { count.value++ }

    watch(count, (newCount, oldCount) => {
      console.log(`count has changed from ${oldCount} to ${newCount}.`)
    })

    return { count, incrementCount }
  }
}
```

##### Options `immediate` et `deep`

Avec l'API d'options :

```js
watch: {
  source: {
    immediate: true,
    deep: true,
    handler(newValue, oldValue) {
      // ...
    }
  }
}
```

Avec l'API de composition :

```js
watch(source, (newValue, oldValue) => {
  // ...
}, { immediate: true, deep: true })
```

#### Fonction `watchEffect()`

La fonction `watchEffect()` exécute immédiatement ue première fois le code. De plus, il observe automatiquement les dépendances réactives.

```js
const todoId = ref(1)
const data = ref(null)

watchEffect(async () => {
  const response = await fetch(
    `https://jsonplaceholder.typicode.com/todos/${todoId.value}`
  )
  data.value = await response.json()
})
```

Quand utiliser `watchEffect` vs `watch` ?

- Utilisez `watchEffect` quand vous voulez observer plusieurs propriétés réactives et vous n'avez pas besoin des anciennes valeurs,
- Utilisez `watch` quand vous voulez observer une (ou plusieurs) propriété réactive spécifique et vous avez pas besoin des anciennes valeurs.

### Lifecycle hooks

Vou avez accès à la plupart des lifecycle hooks dans `setup()`. Voici un exemple :

```js
import {
  onActivated, onBeforeMount, onBeforeUnmount, onBeforeUpdate,
  onDeactivated, onErrorCaptured, onMounted, onUnmounted, onUpdated,
} from 'vue'

export default {
  setup() {
    console.log('created')
    onBeforeMount(() => console.log('before mount'))
    onMounted(() => console.log('mounted'))
    onBeforeUpdate(() => console.log('before update'))
    onUpdated(() => console.log('updated'))
    onBeforeUnmount(() => console.log('before unmount'))
    onUnmounted(() => console.log('unmounted'))
    onActivated(() => console.log('activated'))
    onDeactivated(() => console.log('deactivated'))
    onErrorCaptured(() => console.error('error captured'))
    onRenderTracked(() => console.log('render tracked'))
    onRenderTriggered(() => console.log('render triggered'))
  }
}
```

**NB:** `setup()` est exécuté entre `beforeCreate()` et `created()`, ces hooks sont donc inutiles. Placez le code que vous souhaitez exécuter à cet instant dans `setup()` lui-même.

### Template refs

Pour obtenir la référence d'un élément du template avec l'API de composition, vous devez déclarer une `ref` avec le même nom.

Exemple :

```html
<template>
  <Child ref="child" />
</template>

<script setup>
import { ref, onMounted } from 'vue'
import Child from './Child.vue'

const child = ref(null)

onMounted(() => {
  // child.value aura pour valeur une instance de <Child />
})
</script>
```

#### Dans une boucle `v-for`

Quand `ref` est utilisé à l'intérieur de `v-for`, la référence correspondante devrait contenir une valeur de type `Array`, qui sera remplie avec les éléments après le `mount` :

```html
<template>
  <ul>
    <li v-for="item in list" ref="itemRefs">
      {{ item }}
    </li>
  </ul>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const list = ref([
  /* ... */
])

const itemRefs = ref([])

onMounted(() => console.log(itemRefs.value))
</script>
```

#### Fonctions `refs`

L'attribut `ref` peut également être lié à une fonction, qui sera appelée à chaque mise à jour du composant et qui vous donne une flexibilité totale pour stocker la référence de l'élément. La fonction reçoit la référence de l'élément en premier argument :

```html
<template>
  <ul>
    <li
      v-for="item in list"
      :key="item.id"
      :ref="(el) => { itemRefs[item.id] = el }"
    >
      {{ item }}
    </li>
  </ul>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const list = ref([
  /* ... */
])

const itemRefs = ref({})

onMounted(() => console.log(itemRefs.value))
</script>
```

## Composants monofichiers et `<script setup>`

La syntaxe `<script setup>` accessible dans les composants monofichiers `*.vue` est un sucre syntaxique qui facilite l'écriture de code utilisant l'API de composition. A la compilation, le code est  transformé en code Vue.js 3 standard.

Avec la syntaxe `<script setup>` :

- vous écrivez uniquement le code de la fonction `setup()`,
- toutes les variables et méthodes sont exportées automatiquement.

Exemple :

```html
<script setup>
import { ref } from 'vue'

const capacity = ref(4)
const increaseCapacity = () => { capacity.value++ }
</script>
```

Est équivalent à :

```html
<script>
import { ref } from 'vue'

export default {
  setup() {
    const capacity = ref(4)
    const increaseCapacity = () => { capacity.value++ }acity.value++

    return { capacity, increaseCapacity }
  }
}
</script>
```

### Utilisation de composants

Avec `<script setup>`, vous pouvez importer des composants et les utiliser directement dans le template.

```html
<script setup>
import MyComponent from './MyComponent.vue'
</script>

<template>
  <MyComponent />
</template>
```

### Fontions `defineProps()` et `defineEmits()`

La syntaxe `<script setup>` s'accompagne des fonctions `defineProps()` et `defineEmits()` qui permettent de déclarer des `props` et `emits`.

Elle sont automatiquement accessibles (sans import) dans le bloc `<script setup>`.

Exemple :

```html
<script setup>
const props = defineProps({
  foo: String
})

console.log(`foo: ${props.foo}`)

const emit = defineEmits(['change'])

function onChange(value) {
  emit('change', value)
}

// setup code
</script>
```

Notez que nous utilisons un lien de référence dynamique `:ref` pour pouvoir passer une fonction au lieu d'une chaîne de nom de référence. Lorsque l'élément est `unmounted`, l'argument sera `null`. Vous pouvez bien sûr utiliser une méthode au lieu d'une fonction en ligne.

## Composables et VueUse

### Composables

L'API de composition permet de créer des fonctions réutilisables qui retournent des objets de valeurs réactives, appelés `fonctions de composition` ou `composables`.

Les composables sont utiles pour centraliser et réutiliser de la logique dans différents composants de l'application, ce qui peut améliorer la lisibilité et la maintenance du code.

Voici un exemple simple de composable qui retourne un compteur :

```js
import { ref } from 'vue'

export const useCounter = () => {
  const count = ref(0)
  const increment = () => count.value++
  const decrement = () => count.value--

  return { count, increment, decrement }
}
```

Ce composable peut être utilisé dans un composant en utilisant la fonction setup de l'API de composition :

```js
import { useCounter } from './useCounter'

export default {
  setup() {
    const { count, increment, decrement } = useCounter()

    return { count, increment, decrement }
  }
}
```

### VueUse

[VueUse](https://vueuse.org/) est une librairie qui propose une grande collection de composables.

#### Installation

```bash
npm add @vueuse/core
```

#### Utilisation

Exemple avec `useMouse` qui traque la position de la souris :

```html
<script setup>
import { useMouse } from '@vueuse/core'

const { x, y } = useMouse()
</script>

<template>Mouse position is at: {{ x }}, {{ y }}</template>
```
