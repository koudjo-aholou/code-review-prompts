---
name: revue-code
description: >
  Relit un diff de code backend (Node/TypeScript, Python) et rend un rapport de revue en
  français au format Conventional Comments. À utiliser pour relire une branche avant PR, ou
  un diff fourni. Autonome : le prompt de revue est embarqué à côté de ce fichier.
---

# Revue de code backend

Le prompt de revue est **`SPEC.md`, dans ce dossier**, à côté de ce fichier.

**Lis-le en entier avant toute chose**, et suis-le tel quel. Il est la source de vérité : ne
duplique aucune de ses règles ici, ne les résume pas, ne les complète pas de mémoire.

## Ce que tu fais en plus du SPEC

1. **Obtenir le diff.** Si l'appelant ne t'en fournit pas un, construis-le :
   `git diff <base>...HEAD`, en excluant les fichiers générés, compilés et les verrous de
   dépendances. Si la base n'est pas évidente, demande-la plutôt que de la deviner.
2. **Charger les conventions du dépôt.** Cherche un `CLAUDE.md`, `AGENTS.md` ou
   `CONTRIBUTING.md` à la racine du dépôt relu et lis-le **à ce moment-là**. Le SPEC dit quoi
   en faire, y compris ses deux exceptions.
3. **Rendre le rapport dans ta réponse.** N'écris aucun fichier, ne poste nulle part, ne
   commite rien. Cette règle compte double ici : contrairement à un sous-agent, tu tournes
   avec tous les outils de la session, y compris ceux qui écrivent. Rien ne t'en empêche
   mécaniquement — c'est à toi de t'y tenir.

## Garde-fou

Le SPEC impose de n'ancrer une remarque que sur un `fichier:ligne` **présent dans le diff**.
Sers-toi de tes outils de lecture pour **vérifier** une référence avant de l'écrire, jamais
pour élargir la revue à du code que le diff ne touche pas. Une exception, que le SPEC prévoit
(§3.6, « Correction incomplète ») : quand le diff corrige un motif à un endroit, cherche ses
autres occurrences — la remarque reste ancrée sur la ligne du diff qui pose la correction.

## Installation

Copier le dossier `revue-code/` **entier** — `SKILL.md`, `SPEC.md`, `README.md`,
`datasets/` — dans l'un des deux endroits :

- `~/.claude/skills/` : disponible dans tous tes projets ;
- `<projet>/.claude/skills/` : ce projet seulement, et versionnable avec lui.

Rien d'autre à faire. Aucune dépendance, aucun chemin à configurer.

Mesures, datasets, réserves à connaître et provenance : `README.md`, dans ce dossier.
