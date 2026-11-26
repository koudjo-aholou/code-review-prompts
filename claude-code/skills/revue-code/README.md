# revue-code — ce qui est mesuré, et ce qu'il faut savoir

Documentation pour qui installe ou évalue le skill. Le modèle n'en a pas besoin pour relire :
elle vit ici, hors de `SKILL.md`, pour ne pas être rechargée à chaque revue.

## Ce qui est mesuré, et sur quel modèle

Ce prompt a été qualifié le 2026-09-21 sur `claude-sonnet-4-6`, deux corpus, sans juge :

| | résultat |
|---|---|
| gabarit rendu | 13/13 |
| détection de défauts plantés, après relecture | 7/8 |
| retenue sur cas-règles | 5/5 |
| 50 pull requests réelles **sans défaut** | commente **72 %** d'entre elles |

Les lignes viennent de rejeux propres. Sur les 50 PR réelles, le taux de commentaire est **le
même avec et sans une couche de compression des réponses** : elle ne change pas la propension
à commenter.

**72 % n'est pas un taux d'erreur.** SWR-Bench appelle « propre » une PR qui n'introduit aucun
défaut corrigé plus tard dans l'historique — pas une PR où il n'y a rien à dire. Douze rapports
de l'ancienne version, qui classait encore la gravité, ont été relus un par un : **aucune
remarque inventée**. Chacun nomme un défaut réel avec son extrait de code, dont une erreur de
précédence qui rendait une moyenne fausse en silence.

### Le relecteur ne classe plus la gravité

Depuis le 2026-10-06, toute remarque impose `request changes`, quel que soit son label :
**le modèle trouve, tu tranches.** Il classait mal dans les deux sens — deux défauts bloquants
de commits réels sortis en `question` et en `issue (non-blocking)`, sous un verdict `comment`
que personne n'était tenu de lire ; et des bloquants posés sur des fichiers de test.

| mesure de l'A/B du 2026-10-06 | avant | maintenant |
|---|---|---|
| verdict cohérent avec la règle, 50 PR propres | 26/50 | **49/50** |
| remarques encore décorées `(blocking)` / `(non-blocking)` | 47 | **0** |
| PR commentées | 36/50 | 35/50 |
| remarques par PR | 1,58 | 1,52 |
| détection par mot-clé, retenue (13 cas plantés) | 8/8, 5/5 | 8/8, 5/5 |
| PR propres renvoyées en `request changes` | 12/50 | **36/50** |

La dernière ligne est voulue : `request changes` veut désormais dire **« un humain doit lire »**,
plus « c'est cassé ». Sur un commit réel, un plafond vérifié puis consommé — rendu
`non-blocking` avant — sort maintenant dans `À trancher`.

### Avec les outils, sur du vrai code

Mesure du 2026-10-04 : quatre commits réels de deux dépôts, agent outillé (`Read`, `Bash`,
`Grep`, `Glob`), contre des défauts trouvés après coup, sans jamais nommer le défaut dans la
demande.

| | résultat |
|---|---|
| rapports trouvant leur défaut connu | **3 sur 4** |
| remarques rendues | 24, dont **0 fausse** et **0 hors diff** |
| défauts réels que personne ne connaissait | **4** |
| coût | ~77 000 tokens et 9 min par revue |

Le surcoût propre aux outils vaut environ **3** face à la mesure sans outils, estimé sur le
seul commit de taille comparable — ordre de grandeur, pas mesure. Le reste de l'écart vient du
volume à lire. Le rapport le moins cher, 5 appels d'outils, est le seul à avoir manqué son
défaut ; le plus cher, 17 appels, a trouvé le sien **plus** un second.

## Datasets

`datasets/corpus_v1.json` — 13 diffs courts, chacun avec ce que la revue doit trouver ou
taire, déclaré dans son champ `gate` :

- `rb01`–`rb05` : **retenue** — le relecteur doit se taire, ou ne pas déborder du diff ;
- `rb06`–`rb13` : **détection** — mutable par défaut, clé d'API journalisée, requête en boucle,
  promesse non attendue, politique IAM trop large…

Ses défauts sont plantés, donc plus visibles que dans du code réel. Il sert d'exemples, et de
premier contrôle sur un modèle qu'on n'a jamais essayé.

La retenue sur du code réel a été mesurée sur **SWR-Bench** (Zeng et al., arXiv 2509.01494) :
50 pull requests vérifiées sans défaut. Non embarqué ici : ni ces PR, ni les scorers, ni la
mesure complète ne sont distribués dans ce dépôt.

**Ces chiffres ne valent que pour Sonnet.**

**Six réserves à connaître.** Les deux premières sont les plus utiles au quotidien.

- **C'est toi qui classes.** `request changes` signifie qu'il y a au moins une remarque à
  trancher, pas que le code est cassé. Le label reste une indication — le modèle minimise
  encore : une clé d'API journalisée sort en `suggestion (security)`. Lis `À trancher` en
  entier ; le SPEC demande de l'ordonner par conséquence décroissante, et `(test)` signale
  ce qui ne touche pas la production.
- **La détection varie d'une revue à l'autre.** Un même défaut — un appel sans
  `try`/`except` qui ne laissait aucune trace — a été trouvé le 2026-10-04 et manqué deux fois
  le 2026-10-06, sous l'ancien et le nouveau SPEC. Un rapport sans remarque sur un point ne
  prouve pas que le point est sain. Une correction posée à un seul endroit est mieux couverte
  depuis le 2026-10-06 : le SPEC fait chercher les autres occurrences du motif, et un correctif
  oublié sur trois sites a été trouvé là où un témoin du même jour le ratait — un cas, pas une
  mesure.
- **Il suppose les versions récentes des bibliothèques**, pas celles que ton projet épingle.
  Sur des PR anciennes, trois remarques reposaient sur une API supprimée des années après
  (`unicode` en 2013, `np.int` en 2019, `mode.use_inf_as_null` en 2021). Si ton projet est
  bloqué sur une version, vérifie-la avant de traiter une remarque de dépréciation.
- **Aucun autre modèle n'est qualifié.** Le seul chiffre existant ailleurs est défavorable
  (`qwen2.5:14b` reste derrière un prompt libre). Ce SPEC pèse ~3 900 tokens : au-delà d'une
  fenêtre de 4 096, certains moteurs locaux le tronquent **sans erreur**.
- **La détection se mesure par mot-clé**, donc c'est un **indice, pas une preuve**. Le banc
  élimine de façon fiable un modèle qui ne voit rien ; il ne certifie pas celui qui passe. Un
  modèle peut écrire le mot sans avoir vu le défaut — c'est arrivé sur `slugify`, compté à
  tort parce qu'il n'apparaissait que dans `slugifyTag`.
- **Un seul run par mesure**, sur des diffs courts (quelques dizaines de lignes, plafond de
  9 000 caractères) pour la qualification. Les quatre revues outillées portaient sur de vrais
  commits, jusqu'à 43 fichiers — mais elles sont quatre.

## Provenance

`SPEC.md` est développé et mesuré hors de ce dépôt, avec ses corpus et ses bancs. **Ce
dossier en est la copie distribuable, et la copie fait foi pour qui l'installe.** Dans ce
dépôt, `SPEC.md` est identique à l'octet à `prompts/revue-backend.md`.
