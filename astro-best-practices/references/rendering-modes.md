# Rendering Modes

## Regle de choix

```text
Le rendu depend-il de la requete, des cookies, de l'auth ou d'un secret serveur ?
  ├── Non -> rester en static
  └── Oui -> seulement quelques routes ?
            ├── Oui -> garder le projet static + `prerender = false` sur ces routes
            └── Non -> passer le projet en `output: 'server'`
```

## 1. Static par defaut

```js
import { defineConfig } from 'astro/config';
import tailwindcss from '@tailwindcss/vite';

export default defineConfig({
  vite: {
    plugins: [tailwindcss()],
  },
});
```

- Toutes les pages sont prerender au build.
- C'est le mode prefere pour un site vitrine, du contenu editorial, une landing page,
  une doc, un portfolio.

## 2. Route on-demand dans un projet majoritairement statique

Ajouter un adapter, puis desactiver le prerender uniquement sur les routes qui en ont besoin.

```js
import { defineConfig } from 'astro/config';
import vercel from '@astrojs/vercel';
import tailwindcss from '@tailwindcss/vite';

export default defineConfig({
  adapter: vercel(),
  vite: {
    plugins: [tailwindcss()],
  },
});
```

```astro
---
export const prerender = false;

const user = Astro.locals.user;
if (!user) {
  return Astro.redirect('/connexion');
}
---
```

```ts
import type { APIRoute } from 'astro';

export const prerender = false;

export const POST: APIRoute = async ({ request }) => {
  const body = await request.json();
  return new Response(JSON.stringify(body), {
    headers: { 'Content-Type': 'application/json' },
  });
};
```

## 3. Projet majoritairement dynamique

```js
import { defineConfig } from 'astro/config';
import vercel from '@astrojs/vercel';
import tailwindcss from '@tailwindcss/vite';

export default defineConfig({
  output: 'server',
  adapter: vercel(),
  vite: {
    plugins: [tailwindcss()],
  },
});
```

- Utiliser `output: 'server'` seulement si la majorite du site depend de la requete.
- Les pages qui peuvent rester statiques declarent alors `export const prerender = true`.

## 4. Middleware et headers

```ts
import { defineMiddleware } from 'astro:middleware';

export const onRequest = defineMiddleware(async (_context, next) => {
  const response = await next();
  response.headers.set('X-Frame-Options', 'DENY');
  return response;
});
```

## Anti-patterns

- Activer le rendu serveur sans besoin explicite.
- Mettre `prerender = false` sur des pages purement marketing.
- Ajouter un adapter "au cas ou" sans route server concrete.
