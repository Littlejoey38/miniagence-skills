# Routing

## 1. File-based routing

```text
src/pages/
├── index.astro        -> /
├── about.astro        -> /about
├── blog/[slug].astro  -> /blog/:slug
└── api/contact.ts     -> /api/contact
```

## 2. Route dynamique

```astro
---
import type { GetStaticPaths } from 'astro';

export const getStaticPaths: GetStaticPaths = async () => {
  return [
    { params: { slug: 'bonjour' } },
    { params: { slug: 'contact' } },
  ];
};

const { slug } = Astro.params;
---

<h1>{slug}</h1>
```

## 3. API route

```ts
import type { APIRoute } from 'astro';

export const prerender = false;

export const POST: APIRoute = async ({ request }) => {
  if (!request.headers.get('content-type')?.includes('application/json')) {
    return new Response('Unsupported Media Type', { status: 415 });
  }

  const body = await request.json();

  return new Response(JSON.stringify(body), {
    status: 200,
    headers: { 'Content-Type': 'application/json' },
  });
};

export const ALL: APIRoute = () => {
  return new Response('Method Not Allowed', { status: 405 });
};
```

## 4. Redirects

```js
export default defineConfig({
  redirects: {
    '/ancien-slug': '/nouveau-slug',
  },
});
```

## Regles

- Valider les entrees dans les API routes.
- Renvoyer des codes HTTP explicites.
- N'ajouter `prerender = false` que sur les routes qui le necessitent.
