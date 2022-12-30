[← Sommaire](0-index.md)

# [Draft] Routage avec Vue Router

Voir : [Vue Router](https://router.vuejs.org/)

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

## configuration

### Routes dynamiques

```js
const routes = [
  {
    path: '/users/:id',
    name: 'user',
    component: User
  }
]
```

```html
<!-- User component -->
<template>
  <div>User {{ $route.params.id }}</div>
</template>
```

### Routes imbriquées

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

### Props

## Navigation programmatique

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

Vous pouvez utiliser `$router` et `$route` :

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

### Composition API

Vous devez importer `useRouter` et `useRoute` :

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
