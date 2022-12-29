# Fondamentaux avec l'API d'options

Sources :

- [https://vuejs.org/guide/essentials/application.html](https://vuejs.org/guide/essentials/application.html)


## Syntaxe de template

### Interpolation de texte

La syntaxe double-accolades `{{ }}` est utilisée pour l'interpolation de texte.

```html
<span>Message: {{ msg }}</span>
```

Il est possible d'utiliser des expresions javascript:

```html
{{ number + 1 }}
{{ ok ? 'YES' : 'NO' }}
{{ message.split('').reverse().join('') }}
```

<div :id="`list-${id}`"></div>


### Directive `v-html`

La directive `v-html` permet d'insérer du code html de façon dynamique.

```html
<div v-html="rawHtml"></div>
```

### Directive `v-bind`

La directive `v-bind` est utilisée pour l'interpolation des attributs.

```html
<div v-bind:id="dynamicId"></div>

<!-- syntaxe courte -->
<div :id="dynamicId"></div>
```

Attributs booléens :

```html
<button :disabled="isButtonDisabled">Button</button>
```

### Directives `v-if`, `v-else-if` et `v-else`

Les directives `v-if`, `v-else-if` et `v-else` permettent d'insérer dans le DOM des blocks de façon conditionnelle.

```html
<div v-if="type === 'A'">A</div>
<div v-else-if="type === 'B'">B</div>
<div v-else-if="type === 'C'">C</div>
<div v-else>Not A/B/C</div>
```

### Directive `v-show`

La directive `v-show` permet comme `v-if` de contrôler si un élément doit être affiché ou non en fonction d'une condition. Cependant, au lieu de retirer complètement l'élément du DOM, `v-show` modifie simplement la propriété `display` de l'élément pour le masquer ou le afficher. Cela signifie que l'élément reste toujours présent dans le DOM, mais est simplement caché de la vue de l'utilisateur. `v-show` est donc plus performant que `v-if` dans les cas où vous devez fréquemment masquer et afficher des éléments.

```html
<div v-show="type === 'A'">A</div>
<div v-show="type === 'B'">B</div>
<div v-show="type === 'C'">C</div>
```

### Directive `v-for`

La directive `v-for` est utilisée pour l'interpolation des listes.

```html
<div v-for="item in items" :key="item.id">
  {{ item.text }}
</div>
```

Il est fortement recommandé d'ajouter un attribut `:key` en spécifiant une valeur unique pour chaque élément, afin d'aider Vuejs à distinguer les éléments lors de modifications.

La directive `v-for` peut aussi être utilisée avec le type `Object` :

```html
<div v-for="(item, index) in items"></div>
<div v-for="(value, key) in object"></div>
<div v-for="(value, name, index) in object"></div>
```

### Directive `v-on`

La directive `v-on` est utilisée pour écouter des événements.

```html
<a v-on:click="doSomething"> ... </a>

<!-- syntaxe courte -->
<a @click="doSomething"> ... </a>
```

### Directive `v-model`

La directive `v-model` est utilisée pour interpoler une variable avec la valeur d'un champs de saisie html. L'interpolation se fait dans les 2 sens: si vous modifiez la valeur de l'élément en utilisant le champs de saisie, la variable sera mise à jour en conséquence, et inversement, si vous modifiez la valeur de la variable dans le code JavaScript, l'élément HTML sera mis à jour.

La directive `v-model` support plusieurs type d'éléments de formulaire.

#### Champs texte

```html
<input v-model="message">
```

#### Cases à cocher

Simple - la variable `checked` est un `Boolean` :

```html
<input type="checkbox" v-model="checked" />
</html>
```

Multiple - la variable `checkedFruits` est un `Array` :

```html
<label>
  <input type="checkbox" value="apple" v-model="checkedFruits" />
  apple
</label>
<label>
  <input type="checkbox" value="orange" v-model="checkedFruits" />
  orange
</label>
<label>
  <input type="checkbox" value="grape" v-model="checkedFruits" />
  grape
</label>
```

#### Radio boutons

```html
<label>
  <input type="radio" value="apple" v-model="selected" />
  apple
</label>
<label>
  <input type="radio" value="orange" v-model="selected" />
  orange
</label>
<label>
  <input type="radio" value="grape" v-model="selected" />
  grape
</label>
```

#### Select

```html
<select v-model="selected">
  <option disabled value="">Please select one</option>
  <option>apple</option>
  <option>orange</option>
  <option>grape</option>
</select>
```

### Modificateurs de directives

Les modificateurs de directives sont des suffixes qui peuvent être ajoutés à une directive de Vue.js pour fournir des informations supplémentaires sur la manière dont la directive doit être utilisée. Ils sont généralement utilisés pour personnaliser le comportement d'une directive de manière plus précise.

Voici quelques exemples de modificateurs de directives couramment utilisés :

- `.prevent` : empêche la propagation de l'événement déclenché par la directive. Par exemple, si vous utilisez `v-on:submit.prevent` sur un formulaire, cela empêchera la soumission du formulaire et l'actualisation de la page.

- `.stop` : arrête la propagation de l'événement déclenché par la directive. Cela est similaire à `.prevent`, mais `.stop` est utilisé pour les événements qui ne peuvent pas être annulés (comme `click`).

- `.capture` : capture l'événement déclenché par la directive pendant la phase de capture de l'événement (au lieu de la phase de bulle). Cela signifie que la directive sera déclenchée avant que l'événement ne parvienne à l'élément cible.

- `.self` : limite la portée de la directive aux événements déclenchés par l'élément lui-même. Par exemple, si vous utilisez `v-on:click.self` sur un élément `div`, la directive ne sera déclenchée que si vous cliquez sur l'élément `div` lui-même, pas sur ses enfants.

Voici un exemple d'utilisation de modificateurs de directives :

```html
<template>
  <form @submit.prevent="submitForm">
    <input v-model="name">
    <button>Envoyer</button>
  </form>
</template>

<script>
export default {
  data() {
    return {
      name: ''
    }
  },
  methods: {
    submitForm() {
      console.log(`Bonjour, ${this.name}!`)
    }
  }
}
</script>
```

Voici un exemple d'utilisation de modificateurs avec la directive `v-on:keyup` pour détecter des combinaisons de touches au clavier:

```html
<template>
  <input
    v-on:keyup.enter="submitForm"
    v-on:keyup.esc="clearForm"
    v-on:keyup.shift.s="saveForm"
    v-on:keyup.alt.c="copyForm">
</template>

<script>
export default {
  methods: {
    submitForm() {
      console.log('Formulaire soumis!')
    },
    clearForm() {
      console.log('Formulaire vidé!')
    },
    saveForm() {
      console.log('Formulaire enregistré!')
    },
    copyForm() {
      console.log('Formulaire copié!')
    }
  }
}
</script>
```

Dans cet exemple, nous avons utilisé plusieurs modificateurs de clés pour déclencher différentes méthodes lorsque des combinaisons de touches sont appuyées sur l'input :

- La touche `Entrée` déclenche la méthode `submitForm`,
- La touche `Echap` déclenche la méthode `clearForm`,
- La conbinaison de touches `Shift + s` déclenche la méthode `saveForm`,
- La conbinaison de touches `Alt + c` déclenche la méthode `copyForm`.

## API d'options

Avec l'API d'options, nous définissons la logique d'un composant en utilisant un objet d'options, telles que `data`, `methods` et `mounted`. Les propriétés définies par les options sont exposées sur `this` à l'intérieur des fonctions, qui pointe vers l'instance du composant :

```html
<script>
export default {
  // Les propriétés renvoyées par data() deviennent des données réactives
  // et seront exposées sur `this`.
  data() {
    return {
      count: 0
    }
  },

  // Les méthodes sont des fonctions qui modifient l'état et déclenchent des mises à jour.
  // Elles peuvent être liées en tant qu'event listeners dans les templates.
  methods: {
    increment() {
      this.count++
    }
  },

  // Les lifecycle hooks sont appelés à différentes étapes du cycle de vie d'un composant.
  // Cette fonction sera appelée lorsque le composant sera monté.
  mounted() {
    console.log(`The initial count is ${this.count}.`)
  }
};
</script>

<template>
  <button @click="increment">Count is: {{ count }}</button>
</template>
}
```

### Option `data`

L'option `data` est utilisée dans pour définir les données du composant. Les données du composant sont des variables qui peuvent être utilisées dans le template du composant pour afficher des informations ou pour contrôler le comportement du composant.

```html
<template>
  <div>
    <p>Le compteur est actuellement à {{ counter }}.</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      counter: 0
    }
  }
}
</script>
```

### Option `computed`

L'option `computed` est utilisée pour définir des propriétés calculées. Les propriétés calculées sont des fonctions qui retournent une valeur en fonction des données du composant. Elles sont utiles lorsque vous avez besoin de calculer une valeur à partir de plusieurs autres données, ou lorsque vous avez besoin de formater une valeur avant de l'afficher dans le template.

```html
<template>
  <div>
    <p>Le prix total est de {{ totalPrice }} €.</p>
  </div>
</template>

<script>
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
    }
  }
}
</script>
```

### Option `methods`

L'option `methods` est utilisée pour définir des méthodes du composant. Les méthodes du composant sont des fonctions qui peuvent être utilisées pour modifier les données du composant et déclencher des mises à jour de l'interface utilisateur. Elles peuvent être liées à des événements du template en utilisant des directives comme `v-on:click` ou `v-on:submit`.

```html
<template>
  <div>
    <p>Le compteur est actuellement à {{ counter }}.</p>
    <button v-on:click="incrementCounter">Incrémenter</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      counter: 0
    }
  },
  methods: {
    incrementCounter() {
      this.counter++
    }
  }
}
</script>
```

### Option `watch`

L'option `watch` est utiliséepour définir des observateurs de propriété. Les observateurs de propriété sont des fonctions qui sont exécutées chaque fois que la valeur d'une propriété spécifique est modifiée. Ils sont utiles lorsque vous avez besoin de déclencher une action lorsque la valeur d'une propriété change, ou lorsque vous avez besoin de mettre à jour une autre propriété en fonction de la valeur de la propriété observée.

Voici un exemple :

```html
<template>
  <div>
    <p>La valeur en majuscules est : {{ uppercaseValue }}</p>
    <input v-model="userInput" />
  </div>
</template>

<script>
export default {
  data() {
    return {
      userInput: ''
    }
  },
  watch: {
    userInput(newValue, oldValue) {
      console.log(`La valeur a été modifiée de ${oldValue} à ${newValue}.`)
    }
  },
  computed: {
    uppercaseValue() {
      return this.userInput.toUpperCase()
    }
  }
}
</script>
```

Dans cet exemple, nous avons défini un observateur de propriété pour la variable de données `userInput`. Lorsque la valeur de `userInput` est modifiée, la fonction de l'observateur est déclenchée et affiche un message dans la console indiquant la valeur précédente et la valeur actuelle de `userInput`. Nous avons également défini une propriété calculée `uppercaseValue` qui retourne la valeur de `userInput` en majuscules.

Il est important de noter que les observateurs de propriété sont déclarés en tant qu'objet dans l'option `watch`. Chaque observateur est défini en tant que fonction à l'intérieur de cet objet, et prend en entrée la valeur actuelle et la valeur précédente de la propriété observée.

#### Initial

#### Deep

Il est possible de définir un observateur de propriété avec l'option `deep` pour surveiller les modifications dans les élément d'un `Array` ou les propriétés d'un `Object`. Lorsque l'option `deep` est activée, l'observateur sera déclenché non seulement lorsque la valeur de la propriété observée est modifiée directement, mais également lorsque la valeur de tout élément / propriété enfant est modifié.

```html
<template>
  <div>
    <p>La valeur de l'objet est : {{ obj }}</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      obj: {
        prop1: 'value1',
        prop2: 'value2'
      }
    }
  },
  watch: {
    obj: {
      handler(newValue, oldValue) {
        console.log(`La valeur de l'objet a été modifiée.`)
      },
      deep: true
    }
  }
}
</script>
```

### Lifecycle hooks

Les lifecycle hooks sont des fonctions qui sont appelées à différentes étapes du cycle de vie d'un composant Vue. Elles sont utiles lorsque vous avez besoin de déclencher une action lorsque le composant est créé, monté, mis à jour ou détruit.

Voici la liste des lifecycle hooks disponibles :

- `beforeCreate` : appelée juste avant que l'instance du composant ne soit créée,
- `created` : appelée immédiatement après que l'instance du composant a été créée,
- `beforeMount` : appelée juste avant que le composant ne soit rendu pour la première fois,
- `mounted` : appelée lorsque le composant a été rendu pour la première fois,
- `beforeUpdate` : appelée juste avant que le composant ne soit mis à jour,
- `updated` : appelée lorsque le composant a été mis à jour,
- `beforeDestroy` : appelée juste avant que l'instance du composant ne soit détruite,
- `destroyed` : appelée lorsque l'instance du composant a été détruite.

```html
<template>
  <div>
    <p>Le compteur est actuellement à {{ counter }}.</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      counter: 0
    }
  },
  mounted() {
    console.log(`Le composant a été rendu pour la première fois.`)
  }
}
</script>
```

## Liaisons de classes et de styles

Un besoin classique de la liaison de données est la manipulation de la liste des classes d’un élément, ainsi que ses styles en ligne. Puisque ce sont tous deux des attributs, il est possible d’utiliser `v-bind` pour les gérer : il faut simplement générer une chaine de caractère avec nos expressions. Cependant la concaténation de chaine de caractères est fastidieuse et source d’erreur. Pour cette raison, Vue fournit des améliorations spécifiques quand `v-bind` est utilisé avec `class` et `style. En plus des chaines de caractères, l’expression peut évaluer des objets ou des tableaux.

### Liaison de classes `v-bind:class`

#### Syntaxe Objet

Example :

```html
<template>
  <div
    class="static"
    :class="{
      active: isActive,
      'text-danger': hasError
    }"
  ></div>
</template>

<script>
export default {
  data() {
    return {
      isActive: true,
      hasError: false
    }
  }
}
</script>
```

Rendu :

```html
<div class="static active"></div>
```

#### Syntaxe Tableau

Example :

```html
<template>
  <div :class="[
    'static',
    isActive ? activeClass : '',
    hasError ? errorClass : ''
  ]"></div>
</template>

<script>
export default {
  data() {
    return {
      activeClass: 'active',
      errorClass: 'text-danger',
      isActive: true,
      hasError: false,
    }
  }
}
</script>
```

Rendu :

```html
<div class="static active"></div>
```

#### Syntaxes Objet et Tableau

Il est aussi possible d’utiliser la syntaxe objet dans la syntaxe tableau :

```html
<div :class="[{ active: isActive }, errorClass]"></div>
```

### Liaison de styles `v-bind:style`

#### Syntaxe Objet

La syntaxe objet pour `v-bind:style` est assez simple - cela ressemble presque à du CSS, sauf que c’est un objet JavaScript. Vous pouvez utiliser camelCase ou kebab-case (utilisez des apostrophes avec kebab-case) pour les noms des propriétés CSS :

```html
<template>
  <div :style="{
    color: activeColor,
    fontSize: fontSize + 'px'
  }"></div>
</template>

<script>
export default {
  data() {
    return {
      activeColor: 'red',
      fontSize: 30
    }
  }
}
</script>
```

C’est souvent une bonne idée de lier directement un objet de style, pour que le template soit plus propre :

```html
<template>
  <div :style="styleObj"></div>
</template>

<script>
export default {
  data() {
    return {
      styleObj: {
        color: 'red',
        fontSize: '30px'
      }
    }
  }
}
</script>
```

#### Syntaxe Tableau

La syntaxe tableau pour v-bind:style permet d’appliquer plusieurs objets de style à un même élément.

```html
<div v-bind:style="[baseStyles, overridingStyles]"></div>
```

## Attributs speciaux

- `key`: [https://vuejs.org/api/built-in-special-attributes.html#key](https://vuejs.org/api/built-in-special-attributes.html#key)
- `ref`: [https://vuejs.org/guide/essentials/template-refs.html](https://vuejs.org/guide/essentials/template-refs.html)