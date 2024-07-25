# Laravel 11 + Vue 3: Single Blade Hosting a Vue.js App
Example of an API-driven integration method between Laravel 11 and Vue 3. A single Laravel blade file acts as the host for the entire Vue.js application. Both frameworks coexist within the same project directory, facilitated by the power of Vite’s official plugin for Vue. This approach adopts separation of concerns principle, allowing Vue.js to function as a client-side rendered application while consuming Laravel APIs. Moreover, Vue Router, the official routing solution for Vue, will manage client-side navigation.

## Main steps

### Expose API routes:
```bash
php artisan install:api
```

### Enabled CORS policy to allow the frontend access to the API endpoints:

* Publish CORS config:
```bash
php artisan config:publish cors
```
* Setting up an environment variable in `.env` to stablish the frontend app url in order to grant CORS access:
```yml
CORS_ALLOWED_ORIGINS="http://localhost:5173"
```
* Loading the frontend url from `.env` and adding it to `allowed_origins` in the CORS config file located in `config/cors.php`:
  
```php
'allowed_origins' => explode(',', env('CORS_ALLOWED_ORIGINS', '*')),
```


## Recomentations

You can send any recomendations to [mriverog86@gmail.com](mailto:mriverog86@gmail.com).
