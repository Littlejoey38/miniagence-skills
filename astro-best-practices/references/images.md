# Images

## Regles de base

- Pour un asset local, utiliser `astro:assets`.
- Les images optimisees vivent dans `src/assets/`, pas dans `public/`.
- Garder `public/` pour les fichiers qui doivent conserver une URL exacte.

## 1. `<Image />` pour un asset local

```astro
---
import { Image } from 'astro:assets';
import heroImage from '../assets/hero.jpg';
---

<Image
  src={heroImage}
  alt="Facade de l'atelier"
  width={1200}
  height={800}
  loading="eager"
  fetchpriority="high"
/>
```

- Pour l'image LCP, utiliser `loading="eager"` et `fetchpriority="high"`.
- Pour le reste, conserver `loading="lazy"`.

## 2. `<Picture />` pour le responsive

```astro
---
import { Picture } from 'astro:assets';
import heroImage from '../assets/hero.jpg';
---

<Picture
  src={heroImage}
  alt="Showroom"
  widths={[480, 768, 1200, 1600]}
  sizes="(max-width: 768px) 100vw, 1200px"
  formats={['avif', 'webp']}
/>
```

- Utiliser `<Picture />` quand la taille utile varie fortement selon le viewport.
- Toujours renseigner `sizes` sur un vrai usage responsive.

## 3. Images distantes

```js
export default defineConfig({
  image: {
    remotePatterns: [
      { protocol: 'https', hostname: 'images.example.com' },
    ],
  },
});
```

```astro
---
import { Image } from 'astro:assets';
---

<Image
  src="https://images.example.com/photo.jpg"
  alt="Produit"
  width={1200}
  height={800}
/>
```

- Autoriser uniquement les domaines necessaires.
- Si l'URL n'est connue qu'au runtime, revenir a `<img>` avec dimensions explicites.

## 4. Backgrounds

- Eviter `background-image` CSS pour une image LCP.
- Preferer une vraie image positionnee dans la section hero pour garder l'optimisation.

## Checklist

- Alt descriptif, ou `alt=""` si decoratif.
- Dimensions explicites pour eviter le CLS.
- Pas de GIF lourds quand une video legere suffit.
