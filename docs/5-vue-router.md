[← Sommaire](0-index.md)

# Routage avec Vue Router

Voir : [Vue Router](https://router.vuejs.org/)

## Sommaire<!-- omit from toc -->

- [Présentation](#présentation)
- [Installation](#installation)
  - [Sans compilation (CDN)](#sans-compilation-cdn)
  - [Avec compilation (Vite)](#avec-compilation-vite)
- [Composants `router-link` et `router-view`](#composants-router-link-et-router-view)
  - [`router-link`](#router-link)
  - [`router-view`](#router-view)
- [Configuration](#configuration)
  - [Routes dynamiques](#routes-dynamiques)
  - [Routes imbriquées](#routes-imbriquées)
  - [Props](#props)
- [Navigation programmatique](#navigation-programmatique)
  - [Option API](#option-api)
  - [Composition API](#composition-api)

## Présentation

`Vue Router` est le routeur officiel pour Vue.js. Il s'intègre profondément à Vue.js pour rendre la création d'applications Single-PAge (SPA) avec Vue.js très facile.

Les fonctionnalités incluent :

- Mapping de routes imbriquées
- Routing dynamique
- Modulaire - Configuration de routeur basée sur les composants
- Route params, query, wildcards
- Effets de transition basé sur le système de transition de Vue.js
- Contrôle de navigation fine
- Liens avec classes CSS actives automatiques
- Mode historique HTML5 ou mode hash
- Comportement du scroll personnalisable
- Encodage correct des URLs

## Installation

### Sans compilation (CDN)

```html
<script src="https://unpkg.com/vue-router@4"></script>
```

```html
<script>
// 1. Define route components.
// These can be imported from other files
const Home = { template: '<div>Home</div>' };
const About = { template: '<div>About</div>' };

// 2. Define some routes
// Each route should map to a component.
// We'll talk about nested routes later.
const routes = [
  { path: '/', component: Home },
  { path: '/about', component: About },
];

// 3. Create the router instance and pass the `routes` option
// You can pass in additional options here, but let's
// keep it simple for now.
const router = VueRouter.createRouter({
  history: VueRouter.createWebHashHistory(),
  routes,
});

// 5. Create and mount the root instance.
const app = Vue.createApp({});
app.use(router);
app.mount('#app');
</script>
```

```html
<div id="app">
  <h1>Hello App!</h1>
  <nav>
    <!-- use the router-link component for navigation. -->
    <!-- specify the link by passing the `to` prop. -->
    <!-- `<router-link>` will render an `<a>` tag with the correct `href` attribute -->
    <router-link to="/">Go to Home</router-link>
    <router-link to="/about">Go to About</router-link>
  </nav>
  <!-- route outlet -->
  <!-- component matched by the route will render here -->
  <router-view></router-view>
</div>
```

### Avec compilation (Vite)

```bash
npm install vue-router@4
```

```js
// router/index.js
import { createRouter, createWebHistory } from 'vue-router'
import Home from '@/components/Home.vue'
import About from '@/components/About.vue'

const router = createRouter({
  history: createWebHistory(import.meta.env.BASE_URL),
  routes: [
    {
      path: '/',
      name: 'home',
      component: Home
    },
    {
      path: '/about',
      name: 'about',
      component: About
    }
  ]
})

export default router
```

```js
// main.js
import { createApp } from 'vue'
import App from './App.vue'
import router from './router'

const app = createApp(App)
app.use(router)
app.mount('#app')
```

```html
<!-- App.vue -->
<template>
  <h1>Hello App!</h1>
  <nav>
    <RouterLink :to="{ name: 'home'}">Home</RouterLink>
    <RouterLink :to="{ name: 'about'}">About</RouterLink>
  </nav>
  <RouterView />
</template>
```

## Composants `router-link` et `router-view`

Les composants `router-link` et `router-view` sont deux composants clés de `Vue Router` qui permettent de créer des liens de navigation et d'afficher les composants de route correspondants.

```html
<template>
  <nav>
    <router-link to="/">Go to Home</router-link>
    <router-link to="/about">Go to About</router-link>
  </nav>
  <router-view></router-view>
</template>
```

### `router-link`

Au lieu d'utiliser des balises `a` normales, nous utilisons un composant personnalisé `router-link` pour créer des liens. Cela permet à `Vue Router` de changer l'URL sans recharger la page, de gérer la génération et l'encodage de l'URL.

### `router-view`

`router-view` affichera le composant qui correspond à l'URL. Vous pouvez le mettre n'importe où pour l'adapter à votre mise en page.

## Configuration

### Routes dynamiques

Vous allez très souvent associer des routes avec un motif donné à un même composant. Par exemple nous pourrions avoir le composant `User` qui devrait être rendu pour tous les utilisateurs mais avec différents identifiants. Avec vue-router nous pouvons utiliser des segments dynamiques dans le chemin de la route pour réaliser cela :

```js
import User from '@/components/User.vue'

const routes = [
  {
    path: '/users/:id',
    name: 'user',
    component: User
  }
]
```

```html
<!-- User.vue -->
<template>
  <div>User {{ $route.params.id }}</div>
</template>
```

Maintenant des URL comme `/users/12` et `/users/569` seront chacun associé à la même route.

### Routes imbriquées

Les vraies interfaces utilisateurs d'application sont faites de composants imbriqués à de multiples niveaux de profondeur. Il est aussi très commun que les segments d'URL correspondent à une certaine structure de composants imbriqués, par exemple :

```bash
/utilisateur/foo/profil                  /utilisateur/foo/billets
+---------------------+                  +--------------------+
| User                |                  | User               |
| +-----------------+ |                  | +----------------+ |
| | Profile         | |  +------------>  | | Posts          | |
| |                 | |                  | |                | |
| +-----------------+ |                  | +----------------+ |
+---------------------+                  +--------------------+
```

Avec `vue-router`, il est vraiment très simple d'exprimer cette relation en utilisant des configurations de route imbriquées.

```js
const routes = [
  {
    path: '/user/:id',
    name: 'user',
    component: User,
    children: [
      {
        // when /user/:id/profile is matched
        path: 'profile',
        name: 'user-profile',
        component: UserProfile
      },
      {
        // when /user/:id/posts is matched
        path: 'posts',
        name: 'user-posts',
        component: UserPosts
      }
    ]
  }
]
```

Le composant `User.vue` sera rendu dans une balise `<router-view>` de 1er niveau lorsque la route actuelle concorde (`/user/:id/*`).

```html
<!-- App.vue -->
<template>
  <RouterView />
</template>
```

Le composant de rendu `User.vue` contiendra sa propre balise `<router-view>` :

```html
<!-- User.vue -->
<template>
  <h1>User {{ $route.params.id }}</h1>
  <nav>
    <RouterLink :to="{ name: 'user-profile'}">
      Profile
    </RouterLink>
    <RouterLink :to="{ name: 'user-posts'}">
      Posts
    </RouterLink>
  </nav>
  <RouterView />
</template>
```

L'option `children` dans la configuration du router permet alors de déterminer quel composant utiliser pour le rendu à l'intérieur de la balise imbriquée.

### Props

Utiliser `$route` dans vos composants crée un couplage fort à la route qui va limiter la flexibilité du composant qui ne pourra être utilisé que par certains URL.

Pour découpler un composant de son routeur, utilisez les `props` :

On peut remplacer :

```js
const User = {
  template: '<div>User {{ $route.params.id }}</div>'
}
const routes = [{ path: '/user/:id', component: User }]
```

par :

```js
const User = {
  // make sure to add a prop named exactly like the route param
  props: ['id'],
  template: '<div>User {{ id }}</div>'
}
const routes = [{ path: '/user/:id', component: User, props: true }]
```

L'option `props` accepte plusieurs types : Booléen, Objet, Fonction.

Exemple :

```js
const routes = [
  // Mode Booleen
  // Quand props est mis à true, le route.params est remplis en tant que props du composant.
  {
    path: '/user/:id',
    component: User,
    props: true
  },

  // Mode Objet
  // Quand props est mis à true, le route.params est remplis en tant que props du composant.
  {
    path: '/promotion/from-newsletter',
    component: Promotion,
    props: { newsletterPopup: false }
  },

  // Mode Fonction
  // Vous pouvez créer une fonction qui va retourner les props
  // Danc cet exemple, l'URL "/search?q=vue" passerait {query: 'vue'} comme props au composant SearchUser.
  {
    path: '/search',
    component: SearchUser,
    props: (route) => ({ query: route.query.q })
  }
]
```

## Navigation programmatique

En plus de l'utilisation de `router-link` pour créer des liens de navigation pour le template, vou pouvez naviguer de manière programmatique en utilisant la méthode `push` de l'instance du routeur.

L'argument peut être une chaine de caractère représentant un chemin, ou un objet de description de destination. Des exemples :

```js
// literal string path
router.push('/users/eduardo')

// object with path
router.push({ path: '/users/eduardo' })

// named route with params to let the router build the url
router.push({ name: 'user', params: { username: 'eduardo' } })

// with query, resulting in /register?plan=private
router.push({ path: '/register', query: { plan: 'private' } })

// with hash, resulting in /about#team
router.push({ path: '/about', hash: '#team' })

```

### Option API

Dans l'API d'options, vous accédez au router et à la route active en utilisant respectivement `$router` et `$route` :

```js
export default {
  methods {
    pushWithQuery(query) {
      this.$router.push({
        name: 'search',
        query: {
          ...this.$route.query,
          ...query,
        },
      })
    }
  }
}
```

`$router` et `$route` sont aussi accessibles depuis le template.

### Composition API

Dans l'API de composition, vous accédez au router et à la route active en important `useRouter` et `useRoute` :

```js
import { useRouter, useRoute } from 'vue-router'

export default {
  setup() {
    const router = useRouter()
    const route = useRoute()

    function pushWithQuery(query) {
      router.push({
        name: 'search',
        query: {
          ...route.query,
          ...query,
        },
      })
    }
  }
}
```

---

Découvrez les toutes fonctionnalités sur le site officiel de [VueRouter](https://router.vuejs.org/).
