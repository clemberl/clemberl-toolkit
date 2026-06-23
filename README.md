# Artist Toolkit

Une boîte à outils de *skills* pour artistes musicaux indépendants : campagnes de curateurs, contenu social, pitchs streaming, coaching créatif, identité visuelle.

Le plugin porte la **méthode** (le *comment*). Il ne contient **aucune donnée d'artiste** : la voix, le positionnement, l'univers et le contexte de la sortie en cours sont lus dans le **projet actif** où la skill est invoquée. Chaque skill est ainsi réutilisable d'un projet musical à l'autre.

## Skills incluses

| Skill | Rôle |
|---|---|
| `curators-groover` | Campagnes Groover : pitch général, pitchs personnalisés en batch, scoring d'un curateur, scoring en batch + plan de campagne / budget. |
| `social-content` | Posts Instagram/TikTok : hooks, légendes, CTA, adaptés au format et à l'objectif. |
| `streaming-pitch` | Pitchs éditoriaux Spotify / Apple Music / Amazon Music, ancrés dans les paroles et la place du morceau dans l'album. |
| `creative-coach` | Critique d'une proposition, test de cohérence avec l'univers, angles de storytelling. |
| `visual-brief` | Codification et maintien de l'identité visuelle d'un artiste. |

## Principe : méthode, pas infrastructure

Les skills ne codent en dur **ni un artiste, ni un outil**. Elles lisent les données dont elles ont besoin (registres, trackers, templates) depuis une source que **tu désignes dans les instructions de ton projet**.

**Notion est l'implémentation de référence**, mais ce n'est pas une dépendance : n'importe quelle source tabulaire convient (Airtable, Coda, Google Sheets, un fichier joint), du moment qu'elle respecte la **forme attendue** décrite ci-dessous. Si aucune source n'est connectée dans la session, la skill demande de coller les données utiles.

## Setup de référence

Cette section décrit **la forme** des données à mettre en place. Le **contenu** (textes des templates, table d'IDs, budget…) t'appartient et vit dans les instructions de ton projet, pas dans le plugin.

### 1. Registre curateurs — pour `curators-groover`

Une table (base Notion, Airtable, ou feuille) avec ces colonnes :

- **Curateur** — titre / nom.
- **Type** — champ multi-valeurs (ex. Playlist / Radio / Media).
- **Genres** — champ multi-valeurs, ~2 tags par curateur.
- **Une colonne de statut par single passé**, nommée d'après le single (ex. `Single A (24)`). Valeurs : `✅ Accepté` / `❌ Refusé` / `⬜ Silence` / `– Non contacté` / `Veut en savoir plus`.
- **Reco prochain single** — texte. Valeurs : `🟢 Renvoyer` / `🟡 Test` / `🟡 Retenter` / `🔴 Stop` / `🟠 Ne pas prioriser` / `⚠️ Exception 🔥`. (Une campagne « parenthèse » peut avoir sa propre colonne de reco.)
- **Tutoiement** — texte ; `✅` = répondre en tu.
- **Note** — texte ; c'est ici que se lit la **chaleur d'un refus** (encouragement à revenir, etc.).

La skill **lit** ce registre ; elle ne l'écrit jamais. Désigne son emplacement dans les instructions du projet.

### 2. Kit de pitch — pour `curators-groover` (Fonctions 1 & 2)

Un emplacement qui contient :

- **Pitchs généraux de référence** : les pitchs généraux des singles précédents, qui servent de calibrage de ton.
- **Kit de pitch personnalisé** : **6 gabarits** = 3 cas (premier envoi / relance après acceptation / relance après refus chaud) × tutoiement / vouvoiement.

> Dans le setup Notion d'origine, ceci vivait dans la page **« Curateurs Groover › 🧰 Kit de pitch personnalisé »**. Reproduis-le où tu veux et indique l'emplacement dans les instructions du projet.

### 3. Ressources de campagne — pour `curators-groover` (Fonction 4)

Vivent dans les instructions du projet (jamais dans le plugin) :

- La table de correspondance **IDs de genres / pays → noms**.
- Le **budget** en Grooviz.
- Le **lien du runbook d'extraction** (procédure d'export JSON).

### 4. Tracker de contenu — pour `social-content` et `creative-coach`

Un registre des posts passés : angles déjà publiés, hooks/légendes/CTA qui ont performé, avec les signaux de performance (rétention, saves/partages, engagement rapporté au reach). Lecture seule pour les skills.

## Connecteurs (optionnel)

Si tu utilises Notion, tu peux connecter le **connecteur Notion** pour que les skills lisent ton registre directement. C'est **proposé, pas imposé** : sans connecteur, colle les lignes pertinentes quand la skill le demande. Tout autre outil reste valable tant que la forme ci-dessus est respectée.

## Versioning

Le plugin suit le **semver** (`MAJEUR.MINEUR.PATCH`), actuellement en pré-1.0 (`0.x.y`) : corrections → patch, nouvelle capacité → mineur, changement cassant → majeur.
