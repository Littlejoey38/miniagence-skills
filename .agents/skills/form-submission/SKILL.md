---
name: form-submission
description: >
  Regles pour creer des formulaires de contact fonctionnels sur les sites.
  Utilise ce skill des qu'un formulaire de contact, devis, ou inscription
  doit etre ajoute a un site.
---

# Formulaires de contact — Mini Agence Leads API

## Endpoint

Les formulaires envoient les donnees vers l'API publique Mini Agence :

```
POST {LEADS_ENDPOINT}
Content-Type: application/json
```

> `{LEADS_ENDPOINT}` est fourni dans le contexte du site. Ne jamais hardcoder d'URL.

## Body JSON

```json
{
  "data": {
    "Nom": "Marie Dupont",
    "Email": "marie@exemple.fr",
    "Message": "Je souhaite un devis..."
  },
  "formName": "contact",
  "_hp": ""
}
```

| Champ | Type | Requis | Description |
|-------|------|--------|-------------|
| `data` | `Record<string, string>` | Oui | Paires cle-valeur des champs du formulaire. Au moins un champ non-vide. |
| `formName` | `string` | Non | Nom du formulaire (defaut : `"contact"`). Utile si le site a plusieurs formulaires. |
| `_hp` | `string` | Oui | Champ honeypot anti-spam. Toujours envoyer vide `""`. |

## Honeypot anti-spam

Ajouter un champ invisible dans le HTML :

```html
<input
  name="_hp"
  type="text"
  class="hidden"
  tabindex="-1"
  autocomplete="off"
  aria-hidden="true"
/>
```

- Le champ est masque avec `class="hidden"` (Tailwind) ou `display:none`
- Les bots remplissent les champs caches → le serveur ignore silencieusement la soumission
- Les vrais utilisateurs ne le voient pas → `_hp` reste vide

## Implementation JavaScript

Le formulaire est soumis via `fetch()` en JavaScript inline dans le composant Astro :

```astro
<form id="contact-form" class="space-y-4">
  <input name="_hp" type="text" class="hidden" tabindex="-1" autocomplete="off" aria-hidden="true" />

  <div>
    <label for="name" class="block text-sm font-medium">Nom</label>
    <input id="name" name="Nom" type="text" required class="..." />
  </div>

  <div>
    <label for="email" class="block text-sm font-medium">Email</label>
    <input id="email" name="Email" type="email" required class="..." />
  </div>

  <div>
    <label for="message" class="block text-sm font-medium">Message</label>
    <textarea id="message" name="Message" required class="..."></textarea>
  </div>

  <button type="submit" class="...">Envoyer</button>
  <p id="form-status" class="text-sm hidden"></p>
</form>

<script>
  const form = document.getElementById("contact-form");
  const status = document.getElementById("form-status");
  const ENDPOINT = "LEADS_ENDPOINT_PLACEHOLDER";

  form.addEventListener("submit", async (e) => {
    e.preventDefault();
    const fd = new FormData(form);
    const data = {};
    fd.forEach((val, key) => {
      if (key !== "_hp") data[key] = val;
    });

    try {
      const res = await fetch(ENDPOINT, {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({
          data,
          formName: "contact",
          _hp: fd.get("_hp") || "",
        }),
      });

      if (res.ok) {
        form.classList.add("hidden");
        status.textContent = "Merci ! Votre message a bien ete envoye.";
        status.classList.remove("hidden");
        status.classList.add("text-green-600");
      } else {
        throw new Error("Erreur serveur");
      }
    } catch {
      status.textContent = "Une erreur est survenue. Veuillez reessayer.";
      status.classList.remove("hidden");
      status.classList.add("text-red-600");
    }
  });
</script>
```

## Regles strictes

1. **Toujours utiliser `fetch()` avec JSON** — jamais de `action=""` classique, jamais de `mailto:`, jamais de Formspree/Netlify Forms/service tiers.
2. **Toujours inclure le champ `_hp`** (honeypot) dans le HTML et dans le body JSON.
3. **L'endpoint est dans le contexte** — utiliser la variable `{LEADS_ENDPOINT}` fournie, ne jamais inventer d'URL.
4. **Feedback utilisateur** — en cas de succes, masquer le formulaire et afficher un message de confirmation. En cas d'erreur, afficher un message generique.
5. **Pas de redirection** — tout se passe en inline sur la meme page.
6. **Accessibilite** — labels associes aux inputs, attributs `required`, `type` corrects.
7. **Cles en francais** — les cles dans `data` doivent correspondre aux labels visibles (ex: `"Nom"`, `"Email"`, `"Message"`).
