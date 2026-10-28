---
name: astro-best-practices
description: >
  Best practices Astro pour produire du code de qualite production. Utilise ce skill
  pour toute tache impliquant Astro : composants .astro, pages, layouts, routing,
  styles, images, Content Collections, islands, API routes, configuration,
  deploiement, ou optimisation de performances.
---

# Astro Best Practices

## Structure canonique

```text
src/
├── assets/          # Images optimisees pour astro:assets
├── components/      # Composants reutilisables
├── content/         # Content Collections
│   └── config.ts
├── layouts/         # BaseLayout.astro et layouts specialises
├── pages/           # Routing file-based
│   └── api/         # API routes
├── styles/          # global.css, tokens, overrides
├── lib/             # Helpers, clients, utilitaires
└── env.d.ts         # Types d'environnement
public/              # Fichiers copies tels quels, URL stables
astro.config.mjs
tsconfig.json
```

**Regle** : tout ce qui peut etre optimise doit vivre dans `src/`. `public/` est reserve
aux fichiers qui doivent garder une URL exacte.

## Regles fondamentales

### 1. Rendering
- Defaut : `output: 'static'`.
- Si seules quelques pages ou routes ont besoin de donnees de requete, garder le projet
  statique et passer ces routes en SSR avec `export const prerender = false`.
- Utiliser `output: 'server'` uniquement quand la majorite du site depend de la requete.
- Toute route on-demand implique un adapter configure dans `astro.config.mjs`.

### 2. Islands
| Directive | Usage |
|---|---|
| `client:load` | UI critique visible immediatement |
| `client:idle` | Widget non critique |
| `client:visible` | Composant sous la ligne de flottaison |
| `client:media="(...)"` | UI conditionnelle au viewport |
| `client:only="<framework>"` | Dernier recours si pas de rendu serveur possible |
| aucune directive | Composant purement statique |

- Pas d'hydration par defaut.
- Si un composant n'a ni etat ni evenement, il reste en `.astro`.
- Ne suppose jamais qu'un framework UI est deja installe.

### 3. Layouts
- Un seul `BaseLayout.astro` contient `<html>`, `<head>` et `<body>`.
- Les metas SEO arrivent en props typees.
- Les zones variables passent par des slots explicites.

```astro
---
interface Props {
  title: string;
  description?: string;
  ogImage?: string;
  noIndex?: boolean;
}
const { title, description = '', ogImage, noIndex = false } = Astro.props;
---
<html lang="fr">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>{title}</title>
    {description && <meta name="description" content={description} />}
    {noIndex && <meta name="robots" content="noindex" />}
    {ogImage && <meta property="og:image" content={ogImage} />}
    <slot name="head" />
  </head>
  <body>
    <slot />
  </body>
</html>
```

### 4. Environnement et types
- `PUBLIC_` uniquement pour les variables exposees au client.
- Ne jamais mettre un secret dans `PUBLIC_*`.
- Activer `strict: true` dans `tsconfig.json`.
- Typer `Astro.props`, `GetStaticPaths` et `APIRoute` dans les exemples.

---

## References detaillees

Lire les fichiers suivants selon le besoin :

| Fichier | Lire quand... |
|---|---|
| `references/rendering-modes.md` | Choix entre static, server et `prerender = false` |
| `references/images.md` | Images, LCP, responsive, remote images |
| `references/styling.md` | CSS scope, Tailwind v4, tokens, fonts cote livraison |
| `references/islands.md` | Hydration minimale et composants interactifs |
| `references/content-collections.md` | Schemas Zod, `getCollection`, relations |
| `references/routing.md` | Pages, routes dynamiques, API routes, redirects |
| `references/performance.md` | Core Web Vitals, prefetch, ClientRouter, scripts |
| `references/integrations.md` | `astro.config.mjs`, adapters et integrations optionnelles |

## Anti-patterns a eviter

| Anti-pattern | Correct |
|---|---|
| Assets d'image dans `public/` sans besoin d'URL stable | Images locales dans `src/assets/` |
| `client:load` partout | Directive la plus legere possible |
| Plusieurs layouts avec `<html>` | Un seul `BaseLayout.astro` |
| Framework UI ajoute pour du simple markup | `.astro` pur |
| CSS global partout | Styles scopes + tokens globaux limites |
| Props non typees | `interface Props` locale |
