# artist-toolkit

Boîte à outils Claude pour **artistes musicaux indépendants**. Cinq skills qui couvrent le
workflow de carrière d'un artiste autoproduit — du pitch streaming à la campagne curateurs,
en passant par le contenu social, le coaching créatif et l'identité visuelle.

**Principe fondateur : méthode pure, zéro donnée d'artiste.** Aucune skill ne code en dur un
univers, un titre, une couleur ou une plateforme. Chacune lit l'identité de l'artiste, son
positionnement et ses trackers **dans le projet actif** où elle est invoquée. Le même plugin
sert donc n'importe quel projet musical sans modification.

## Les 5 skills

| Skill | Ce qu'elle fait |
|---|---|
| `streaming-pitch` | Rédige les pitchs éditoriaux Spotify / Apple Music / Amazon (genres, styles, moods, instruments, description), ancrés dans les paroles et la place du morceau dans l'album. |
| `social-content` | Produit en batch hooks, légendes et CTA pour Instagram/TikTok, adaptés au format et à l'objectif, dans la voix de l'artiste. |
| `creative-coach` | Critique une proposition, teste sa cohérence avec l'univers, propose des angles de storytelling. Conseille, ne produit pas. |
| `curators-groover` | Gère les campagnes Groover : pitch général, pitchs personnalisés en batch, scoring d'un curateur, scoring en batch depuis un export JSON + allocation de budget par EV. |
| `visual-brief` | Codifie l'identité visuelle d'un artiste en document de référence, le maintient, et en dérive des briefs (Brand Kit, skill visuelle, livrable ponctuel). |

Une fois le plugin installé, les skills s'appellent avec le préfixe du plugin, p. ex.
`artist-toolkit:curators-groover`, et se déclenchent automatiquement selon le contexte.

## Pré-requis côté projet

Les skills sont des **méthodes** : elles attendent que l'univers de l'artiste vive dans le
projet où elles tournent. Concrètement, le projet doit fournir :

- L'**identité artistique** (voix, positionnement, marqueurs d'identité) dans ses instructions.
- Les **paroles** des morceaux (pour les pitchs et le storytelling).
- Un **tracker de contenu** (Notion ou fichier) pour `social-content` et `creative-coach`.
- Un **registre curateurs** + templates de pitchs pour `curators-groover`.
- Les **assets visuels** (EPK, pochettes, feed) pour `visual-brief`.

Si une ressource manque, la skill la demande au lieu d'inventer.

## Installation

```bash
# Ajouter le marketplace qui référence ce plugin
claude plugin marketplace add <url-ou-repo-du-marketplace>

# Installer le plugin
claude plugin install artist-toolkit@<nom-du-marketplace>
```

## Statut

Version `0.1.0` — premier bundle. Les champs `author` et `license` du manifeste sont des
valeurs par défaut à confirmer avant toute distribution publique.
