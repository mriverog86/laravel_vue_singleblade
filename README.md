# Laravel 11 + Vue 3: Single Blade Hosting a Vue.js App
Example of an API-driven integration method between Laravel 11 and Vue 3. A single Laravel blade file acts as the host for the entire Vue.js application. Both frameworks coexist within the same project directory, facilitated by the power of Vite’s official plugin for Vue. This approach adopts separation of concerns principle, allowing Vue.js to function as a client-side rendered application while consuming Laravel APIs. Moreover, Vue Router, the official routing solution for Vue, will manage client-side navigation.

### Instalation Guide

1. Clone the repository to your workspace:

```shell
git clone https://github.com/mriverog86/laravel_vue_singleblade.git
```

2. Move inside the project folder:

```shell
cd laravel_vue_singleblade
```

3. Install the project Laravel dependencies:

```shell
composer install
```

4. Install the project Vue dependencies:

```shell
npm install
```

5. Generate Application Key:

```shell
php artisan key:generate 
```

6. Start Laravel development server:

```shell
php artisan serve
```

6. Start Vue development server:

```shell
npm run dev
```

## Integration Guide

1. The first step is to create a new Laravel project.
```bash
composer create-project laravel/laravel your_app_name
```

2. Install the project Laravel dependencies:

```shell
composer install
```

3. Next, install Laravel related npm dependencies.

```bash
npm install
```

4. Now, add Vue.

```bash
npm install vue-router@4 @vitejs/plugin-vue
```

5. Set up `vite.config.js`.

```javascript
import { defineConfig } from "vite";
import laravel from "laravel-vite-plugin";
import vue from "@vitejs/plugin-vue";

export default defineConfig({
    plugins: [
        vue(),
        laravel({
            input: ["resources/css/app.css", "resources/js/app.js"],
            refresh: true,
        }),
    ],
    resolve: {
        alias: {
            vue: "vue/dist/vue.esm-bundler.js",
        },
    },
});
```

6. Go to `./resources/js` and create a new file `App.vue`. This file will serve as the entry point for the application.

```html
<template>
    <h1>
        This is an example
    </h1>
</template>
```

7. Create a new Vue app instance, modify `./resources/js/app.js`.

```javascript
import "./bootstrap";
import { createApp } from "vue";
import App from "./App.vue";

createApp(App).mount("#app");
```

8. Create an element with the id of “app” within the `<body>` of `./resources/views/welcome.blade.php` file. This Blade file containing our Vue app, is the only Laravel file that will be exposed to the browser.

```html
<body>
    <div id="app"></div>
</body>
```

9. Import the `./resources/js/app.js` file using the @vite() Blade directive.

```html
<head>
    @vite(['resources/js/app.js'])
</head>

<body>
    <div id="app"></div>
</body>
```

10. At this point, Vue is working with Laravel. You can test it by running:

```bash
npm run dev
php artisan serve
```

11. Create a new file `./resources/js/router.js` to define the router:

```javascript
import { createRouter, createWebHistory } from "vue-router";

const routes = [];

export default createRouter({
    history: createWebHistory(),
    routes,
});
```

12. Integrate the router with the Vue instance inside `./resources/js/app.js` file:

```javascript
import router from "./router";
import { createApp } from "vue";
import App from "./App.vue";

createApp(App).use(router).mount("#app");
```

13. Create a `./resources/js/pages/` directory that will contain the route components. 

14. Create `./resources/js/pages/HomeRoute.vue` with a router-link to test internal navigation:

```html
<template>
    <div>
        <h1>HOME</h1>
        <router-link to="/test"> Take me to Test page </router-link>
    </div>
</template>
```

15. Create `./resources/js/pages/TestRoute.vue`

```html
<template>
    <h1>Just a test page!</h1>
</template>
```

16. Next, Lazy load the new route components in the router’s file `./resources/js/router.js`

```javascript
import { createRouter, createWebHistory } from "vue-router";

const routes = [
    {
        path: "/",
        component: () => import("./pages/HomeRoute.vue"),
    },
    {
        path: "/test",
        component: () => import("./pages/TestRoute.vue"),
    },
];

export default createRouter({
    history: createWebHistory(),
    routes,
});
```

17. Modify the Vue entry file to make it dynamic. Vue Router provides RouterView built-in component, which exposes slots that can be used to dynamically render route components. Go to `./resources/js/App.vue` and modify it as follows:

```jsx
<template>
    <router-view v-slot="{ Component, route }">
        <div :key="route.name">
            <Component :is="Component" />
        </div>
    </router-view>
</template>
```

18. Solve 404 error when refreshing client routes that don't exist in Laravel. Go to `./routes/web.php` and define a single route that will catch all valid routes and render the Blade entry file which contains the Vue application.

```php
Route::get('/{vue_capture?}', function () {
    return view('welcome');
})->where('vue_capture', '[\/\w\.-]*');
```

19. Expose API routes:

```bash
php artisan install:api
```

20. Let’s Create a testing API endpoint in Laravel and consume it from a Vue component. Add a new API endpoint Inside `./routes/api.php`:

```php
Route::get("/test-me", function () {
    return 'Hello from Laravel!';
});
```

21. Consume this new API endpoint from `./resources/js/pages/HomeRoute.vue` component using axios. Let’s prepare a button that will get the response.

```html
<template>
    <div>
        <h1>HOME</h1>
        <router-link to="/test"> Take me to Test page </router-link>
        <button @click.prevent="tiggerEndpoint">Trigger Endpoint</button>
        <p v-if="response">{{ response.data }}</p>
    </div>
</template>
```

22. Finally, add the script setup to create the function and variable ref in `./resources/js/Pages/HomeRoute.vue`.

```javascript
<script setup>
    import axios from "axios";
    import { ref } from "vue";

    const response = ref();

    const getValue = async () => {
        try {
            response.value = await axios.get("/api/test-me");
        } catch (error) {
            // Do something with the error
            console.log(error);
        }
    };
</script>
```

## Recomentations

You can send any recomendations to [mriverog86@gmail.com](mailto:mriverog86@gmail.com).
