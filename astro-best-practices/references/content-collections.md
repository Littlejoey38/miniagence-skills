# Content Collections

## 1. Declarer un schema

```ts
import { defineCollection, reference, z } from 'astro:content';

const blog = defineCollection({
  type: 'content',
  schema: ({ image }) => z.object({
    title: z.string(),
    pubDate: z.coerce.date(),
    draft: z.boolean().default(false),
    cover: image().optional(),
    author: reference('authors').optional(),
    tags: z.array(z.string()).default([]),
  }),
});

const authors = defineCollection({
  type: 'data',
  schema: z.object({
    name: z.string(),
    role: z.string().optional(),
  }),
});

export const collections = { blog, authors };
```

## 2. Lire les entrees

```ts
import { getCollection, getEntry } from 'astro:content';

const posts = await getCollection('blog', ({ data }) => !data.draft);
const author = posts[0]?.data.author ? await getEntry(posts[0].data.author) : undefined;
```

- Filtrer les drafts explicitement.
- Utiliser `reference()` pour les relations plutot que des ids libres.

## 3. Generer des pages

```astro
---
import type { GetStaticPaths } from 'astro';
import { getCollection } from 'astro:content';

export const getStaticPaths: GetStaticPaths = async () => {
  const posts = await getCollection('blog', ({ data }) => !data.draft);

  return posts.map((post) => ({
    params: { slug: post.slug },
    props: { post },
  }));
};

const { post } = Astro.props;
const { Content } = await post.render();
---

<article>
  <h1>{post.data.title}</h1>
  <Content />
</article>
```

## Anti-patterns

- Parser du Markdown a la main avec `fs` alors qu'une collection suffit.
- Stocker les images de collection comme simple string locale.
