# miniagence-skills

Depot central des skills partages par MiniAgence.

## Structure

Le format canonique compatible Open Agents est :

```text
.agents/skills/<skill>/SKILL.md
```

Exemples :

```text
.agents/skills/impeccable/SKILL.md
.agents/skills/astro-best-practices/SKILL.md
```

## Compatibilite

- `Open Agents` decouvre directement les skills depuis `.agents/skills/`.
- `MiniAgence` synchronise les skills depuis `.agents/skills/` vers la sandbox.
- Les slash commands de MiniAgence sont des invocations directes de skills.

La source de verite est `.agents/skills/`.

## Slash commands

Les slash commands ne vivent plus dans un dossier dedie.

- `/audit` invoque le skill `audit`
- `/polish` invoque le skill `polish`
- `/impeccable` invoque le skill `impeccable`

Le nom de la slash command est donc directement le nom du skill.

## Contrat d'un skill

Chaque skill doit etre dans son propre dossier avec un `SKILL.md` ou `skill.md`
contenant au minimum :

```md
---
name: my-skill
description: Short description
---

Instructions here.
```
