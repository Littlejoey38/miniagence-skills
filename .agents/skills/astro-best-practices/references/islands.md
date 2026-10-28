# Islands

## Principe

Astro n'envoie pas de JavaScript par defaut. Une island doit rester l'exception.

## Directives client

| Directive | Quand l'utiliser |
|---|---|
| `client:load` | UI critique visible immediatement |
| `client:idle` | Widget non critique |
| `client:visible` | Composant sous la ligne de flottaison |
| `client:media="(...)"` | UI active sur un breakpoint precis |
| `client:only="<framework>"` | Composant impossible a rendre cote serveur |

## Regles

- Si le composant n'a pas d'etat, il reste en `.astro`.
- Une animation decorative ne justifie pas une island.
- Un formulaire simple peut souvent rester en HTML natif + endpoint Astro.
- Ne suppose jamais que React, Vue ou Svelte sont deja installes.

## Exemple minimal

```astro
---
import NewsletterWidget from '../components/NewsletterWidget';
---

<NewsletterWidget client:idle />
```

Le composant interactif ne doit apparaitre que si l'integration UI necessaire a deja ete
ajoutee au projet.

## Etat partage

- Eviter l'etat global si une prop suffit.
- Si plusieurs islands doivent partager un petit etat, utiliser un store leger.
- Ajouter ce store seulement apres avoir confirme que plusieurs islands en dependent.

## Anti-patterns

- `client:load` par habitude.
- Ajouter un framework pour une simple grille de cartes.
- Utiliser `client:only` sans avoir d'abord tente un rendu serveur.
