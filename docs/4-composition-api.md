
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
import { ref, computed } from 'vue'

export default {
  setup() {

    // ref
    const capacity = ref(4)
    const attending = ref(['Tim', 'Bob', 'Joe'])

    // computed
    const spacesLeft = computed(() => {
      return capacity.value - attending.value.length
    })

    // method
    function increaseCapacity() {
      capacity.value++
    }

    // Gives our template access to these objects & functions
    return { capacity, attending, spacesLeft, increaseCapacity }
  }
}
</script>
```

## Nouveau système de réactivité

## Composants monofichiers et `<script setup>`

## Composables et VueUse
