# Styling

## 1. CSS scope par defaut

```astro
<section class="card">
  <slot />
</section>

<style>
  .card {
    border-radius: 1rem;
  }
</style>
```

- Les styles d'un composant `.astro` sont scopes par defaut.
- `is:global` reste reserve aux tokens, reset et contenu rendu dynamiquement.

## 2. CSS global et tokens

```astro
---
import '../styles/global.css';
---
```

```css
:root {
  --color-text: oklch(0.2 0.02 260);
  --color-bg: oklch(0.98 0.01 95);
  --radius-lg: 1rem;
}
```

## 3. Tailwind v4 dans le starter MiniAgence

Le starter Astro fourni ici utilise Tailwind v4 via Vite, pas l'ancienne integration Tailwind.

```js
import { defineConfig } from 'astro/config';
import tailwindcss from '@tailwindcss/vite';

export default defineConfig({
  vite: {
    plugins: [tailwindcss()],
  },
});
```

```css
@import "tailwindcss";
```

- Garder les tokens de design dans les variables CSS et les classes utilitaires.
- Utiliser du CSS scope quand Tailwind devient verbeux ou peu lisible.

## 4. Fonts

- Les choix typo et pairings vivent dans `frontend-design`.
- Cote Astro, on se concentre sur la livraison : une seule source d'import, preload si
  necessaire, et pas de `@import url(...)` ajoute dans le CSS.
- Si des Google Fonts sont requises, les charger depuis le `<head>`.

## Anti-patterns

- Ajouter `@import url(...)` dans `global.css`.
- Reintroduire une ancienne integration Tailwind dans ce starter.
- Mettre tous les tokens directement dans les classes HTML.
