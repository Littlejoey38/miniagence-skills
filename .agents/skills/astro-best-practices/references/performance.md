# Performance

## Core Web Vitals

| Metrique | Cause frequente | Levier Astro |
|---|---|---|
| LCP | Image hero trop lourde ou trop tardive | `astro:assets`, preload cible, `fetchpriority="high"` |
| CLS | Medias sans dimensions | `Image`/`Picture` avec dimensions |
| INP | Trop de JS | Hydration minimale |

## 1. Prefetch

```js
export default defineConfig({
  prefetch: {
    prefetchAll: true,
    defaultStrategy: 'hover',
  },
});
```

- Bien pour un petit site vitrine.
- A desactiver sur une page lourde ou sensible si besoin.

## 2. Client-side navigation avec `ClientRouter`

```astro
---
import { ClientRouter } from 'astro:transitions';
---

<head>
  <ClientRouter />
</head>
```

- Utiliser `ClientRouter` seulement si la navigation enrichie apporte une vraie valeur.
- Reinitialiser les scripts dependants du DOM avec les evenements de navigation Astro
  quand c'est necessaire.

## 3. Scripts

```astro
<script>
  import { initMenu } from '../lib/menu.ts';
  initMenu();
</script>
```

- Les petits scripts Astro sont bundles et differes automatiquement.
- Utiliser `is:inline` seulement pour un script qui doit s'executer immediatement,
  par exemple un theme toggle anti-flash.

## 4. Fonts cote perf

- Preferer une police locale si le projet en dispose.
- Preload uniquement la police critique.
- Les choix esthetiques de fonts restent documentes dans `frontend-design`.

## Checklist

- L'image hero est optimisee.
- Pas de JS envoye pour un composant statique.
- Les fonts ne sont importees qu'a un seul endroit.
