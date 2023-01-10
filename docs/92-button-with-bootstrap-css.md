[← Sommaire](0-index.md)

# Créer un bouton personnalisé avec les styles de Bootstrap

Ici, nous créons un composant `MyButton.vue` qui s'utilise comme un bouton HTML standard mais avec des propriétés qui permettent de personnaliser son apparence et son comportement :

| Propriété  | Type    | Par défaut | Description                                                                               |
| ---------- | ------- | ---------- | ----------------------------------------------------------------------------------------- |
| `theme`    | String  | `primary`  | définit la couleur du bouton : `primary`(default), `success`, `warning`, `danger`, `link` |
| `size`     | String  | `md`       | définit la taille du bouton : `sm`, `md`(default), `lg`                                   |
| `disabled` | Boolean | `false`    | désactive le bouton                                                                       |
| `loading`  | Boolean | `false`    | affiche un loader                                                                         |

```html
<!-- MyButton.vue -->
<template>
  <button
    type="button"
    class="btn"
    :class="[`btn-${theme}`, `btn-${size}`]"
    :disabled="disabled"
  >
    <!-- loader -->
    <div
      v-if="loading"
      class="spinner-border spinner-border-sm"
      style="width: 1em; height: 1em; margin-right: 0.5em"
    ></div>
    <!-- slot content -->
    <slot />
  </button>
</template>

<script>
export default {
  props: {
    theme: {
      type: String,
      default: 'primary',
      validator: (val) => {
        return ['primary', 'success', 'warning', 'danger', 'link'].includes(val)
      }
    },
    size: {
      type: String,
      default: 'md',
      validator: (val) => {
        return ['md', 'sm', 'lg'].includes(val)
      }
    },
    disabled: {
      type: Boolean,
      default: false
    },
    loading: {
      type: Boolean,
      default: false
    }
  }
}
</script>
```

NB : Il est important de rappeler que pour utiliser les styles de Bootstrap, il faut avoir inclut les fichiers CSS de Bootstrap dans votre application.

Exemples d'utilisation:

```html
<!-- default -->
<MyButton>Click</MyButton>

<!-- custom theme and size -->
<MyButton theme="danger" size="sm">Delete</MyButton>

<!-- disabled with loader -->
<MyButton disabled loading>Loading...</MyButton>
```
