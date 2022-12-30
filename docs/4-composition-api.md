
[← Sommaire](0-index.md)

# [Draft] API de composition

Voir : [Composition API FAQ](https://vuejs.org/guide/extras/composition-api-faq.html)

L'API de composition est un ensemble de fonctionnalités qui nous permet de créer des composants Vue en utilisant des fonctions importées plutôt que de déclarer des options. Elle offre une meilleure flexibilité dans l'organisation de votre code en permettant une approche plus orientée métier.

## Option `setup`

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

## Nouveau système de réactivité

```js
import { ref, reactive } from 'vue'

export default {
  setup(props, context) {
    // ref
    const val = ref('example')

    // reactive
    const obj = reactive({ count: 0 })

    return {
      val, obj
    }
  }
}
```

## Composants monofichiers et `<script setup>`

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
    <MyButton @click="increaseCapacity()">Increase Capacity</MyButton>
  </div>
</template>

<script setup>
import MyButton from '@/components/MyButton.vue'
import { ref, reactive, computed } from 'vue'

const capacity = ref(4)
const attending = ref(['Tim', 'Bob', 'Joe'])

const spacesLeft = computed(() => {
  return capacity.value - attending.value.length
})

function increaseCapacity() {
  capacity.value++
}
</script>
```

## Template refs

```html
<script setup>
import { ref, onMounted } from 'vue'
import Child from './Child.vue'

const child = ref(null)

onMounted(() => {
  // child.value will hold an instance of <Child />
})
</script>

<template>
  <Child ref="child" />
</template>
```

## Composables et VueUse

Mouse Tracker Example :

```html
<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const x = ref(0)
const y = ref(0)

function update(event) {
  x.value = event.pageX
  y.value = event.pageY
}

onMounted(() => window.addEventListener('mousemove', update))
onUnmounted(() => window.removeEventListener('mousemove', update))
</script>

<template>Mouse position is at: {{ x }}, {{ y }}</template>
```

En créant le composable :

```js
// mouse.js
import { ref, onMounted, onUnmounted } from 'vue'

// by convention, composable function names start with "use"
export function useMouse() {
  // state encapsulated and managed by the composable
  const x = ref(0)
  const y = ref(0)

  // a composable can update its managed state over time.
  function update(event) {
    x.value = event.pageX
    y.value = event.pageY
  }

  // a composable can also hook into its owner component's
  // lifecycle to setup and teardown side effects.
  onMounted(() => window.addEventListener('mousemove', update))
  onUnmounted(() => window.removeEventListener('mousemove', update))

  // expose managed state as return value
  return { x, y }
}
```

Le code précédent devient :

```html
<script setup>
import { useMouse } from './mouse.js'

const { x, y } = useMouse()
</script>

<template>Mouse position is at: {{ x }}, {{ y }}</template>
```

### VueUse

On peut trouver notre bonheur dans la librairie de composables [VueUse](https://vueuse.org/) :

#### Installation

```bash
npm i @vueuse/core
```

#### Usage

```html
<script setup>
import { useMouse } from '@vueuse/core'

const { x, y } = useMouse()
</script>

<template>Mouse position is at: {{ x }}, {{ y }}</template>
```
