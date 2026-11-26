---
name: revue-transversale
description: >
  Relit le diff d'une feature entière et signale au plus 5 problèmes qu'aucune tranche ne montre
  seule : duplication à trois occurrences ou plus, y compris entre fichiers, et API incohérente
  d'un fichier à l'autre. À utiliser en fin de feature, en plus d'une revue de bugs. Ne cherche
  ni les bugs, ni le nommage ou la lisibilité.
---

# Revue transversale de fin de feature

Le prompt de revue est **`SPEC.md`, dans ce dossier**, à côté de ce fichier.

**Lis-le en entier avant toute chose**, et suis-le tel quel. Il est la source de vérité : ne
duplique aucune de ses règles ici, ne les résume pas, ne les complète pas de mémoire.

## Ce que tu fais en plus du SPEC

1. **Obtenir le diff.** L'appelant te donne la branche de la feature et sa base (par exemple
   `main` et `feature/<nom>`). Construis le diff avec `git diff <base>...<branche>`, sur les
   fichiers de code, en excluant les fichiers générés et les verrous de dépendances. Si la
   branche manque, demande-la plutôt que de la deviner.
2. **Charger les conventions du dépôt.** Cherche un `CLAUDE.md`, `AGENTS.md` ou
   `CONTRIBUTING.md` à la racine du dépôt relu et lis-le **à ce moment-là**. Le SPEC dit quoi en
   faire.
3. **Rendre le rapport dans ta réponse**, au format du SPEC et rien d'autre. N'écris aucun
   fichier, ne poste nulle part, ne commite rien. Cette règle compte double ici : tu tournes
   avec tous les outils de la session, y compris ceux qui écrivent, et rien ne t'en empêche
   mécaniquement.

## Garde-fou

N'ancre une remarque que sur un `fichier:ligne` **présent dans le diff**, numéroté dans le
nouveau fichier. Sers-toi de tes outils de lecture pour **vérifier** une référence avant de
l'écrire, jamais pour élargir la revue à du code que le diff ne touche pas.

## Installation

Copier le dossier `revue-transversale/` entier (`SKILL.md`, `SPEC.md`) dans `~/.claude/skills/`
(tous tes projets) ou `<projet>/.claude/skills/` (ce projet seulement). Aucune dépendance.

Pour un contexte isolé, modèle et outils figés, préférer l'agent `claude-code/agents/`
correspondant : un skill tourne dans la conversation, avec ses réglages.
