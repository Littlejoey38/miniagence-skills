# Integrations

## 1. Base `astro.config.mjs` du starter

```js
import { defineConfig } from 'astro/config';
import tailwindcss from '@tailwindcss/vite';

export default defineConfig({
  devToolbar: {
    enabled: false,
  },
  server: { host: '::', port: 4321 },
  vite: {
    plugins: [tailwindcss()],
  },
});
```

- C'est la base par defaut pour un site statique MiniAgence.
- Ne pas ajouter un adapter ou un framework UI sans besoin explicite.

## 2. Ajouter du rendu serveur

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

Si la majorite du site devient dynamique :

```js
export default defineConfig({
  output: 'server',
  adapter: vercel(),
});
```

## 3. Integrations optionnelles

- React : `npx astro add react`
- Vue : `npx astro add vue`
- Svelte : `npx astro add svelte`
- MDX : `npx astro add mdx`
- Sitemap : `npx astro add sitemap`
- Vercel : `npx astro add vercel`

Regle : n'installer une integration UI que si une vraie island en depend.

## 4. Frameworks UI

- Le starter ne fournit aucun framework UI par defaut.
- Si tu ajoutes un framework, limite-le aux composants interactifs.
- Ne convertis pas des sections statiques en composants framework.

## Anti-patterns

- Importer un composant React/Vue/Svelte sans integration installee.
- Ajouter un adapter alors que tout le site reste prerender.
- Faire grossir `astro.config.mjs` avec des integrations non utilisees.
