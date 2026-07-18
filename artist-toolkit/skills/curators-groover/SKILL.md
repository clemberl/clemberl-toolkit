---
name: curators-groover
description: "Méthode pour gérer les campagnes Groover d'un artiste. Cinq fonctions : (0) ouverture de campagne — checklist, intégration du bilan précédent au registre, vérification des recos, export/scoring des nouveaux curateurs, routage vers les autres fonctions ; (1) pitch général d'un single ; (2) pitchs personnalisés en batch selon l'historique de chaque curateur ; (3) scoring manuel d'un curateur (bio + genres) ; (4) scoring en batch depuis un export JSON Groover + plan de campagne et répartition du budget Grooviz. Déclencheurs : «nouvelle campagne Groover», «on lance la campagne [single]», «intègre le bilan de campagne», CSV Bilan Campagne fourni, «pitch général», «génère les pitchs», «score ce curateur», «score mes curateurs», «plan de campagne Groover», «répartis mon budget Grooviz». Sans fonction précisée, toujours démarrer par la Fonction 0. Registre curateurs, templates et identité artistique vivent dans le projet actif."
---

# Curators Groover – Méthode

## Rôle de la skill

Cette skill couvre **cinq tâches précises** liées à une campagne Groover, et rien d'autre. Elle s'appuie sur :

- L'**identité artistique** du projet où elle est invoquée (instructions du projet).
- Les **templates de pitchs** : les *pitchs généraux de référence* (Fonction 1) et le *kit de pitch personnalisé* — 3 cas × tutoiement/vouvoiement (Fonction 2). Ils vivent dans le registre du projet (Notion, fichier joint ou autre base) ou, à défaut, dans les instructions du projet.
- Le **registre curateurs** du projet (Notion, fichier joint ou autre base). Colonnes attendues :
  - **Curateur** — titre.
  - **Type** — champ multi-valeurs (ex. Playlist / Radio / Media). *N'intervient pas dans le choix du message.*
  - **Genres** — champ multi-valeurs, ~2 tags par curateur.
  - **Une colonne de statut par single passé**, nommée d'après le single (ex. `Single A (24)`, `Single B (26)`). Valeurs : `✅ Accepté` / `❌ Refusé` / `➕ Silence` / `– Non contacté` / `Veut en savoir plus`.
  - **Reco prochain single** — texte. Valeurs : `🎲 Renvoyer` / `🎱 Test` / `🎱 Retenter` / `🔴 Stop` / `🚨 Ne pas prioriser` / `⚠️ Exception 🔥`. Une campagne « parenthèse » peut avoir sa propre colonne de reco (ex. `Reco parenthèse`) — voir Fonction 2.
  - **Tutoiement** — texte ; `✅` = répondre en tu.
  - **Note** — texte ; **c'est ici que se lit la chaleur d'un refus** (encouragement explicite à revenir, « pas un refus définitif », etc.). **Format** : une entrée par single, taguée et ajoutée à la suite — jamais d'écrasement. Ex. : `[SGA-24] refus mais encourage à revenir · [SGB-26] accepté, cite une influence du projet`. **Contenu** : uniquement du qualitatif non déductible des colonnes de statut (chaleur d'un refus, éléments cités par le curateur, contexte). **Jamais de donnée dérivée** (« double acceptation », compte de refus…) : les compteurs se calculent depuis les colonnes de statut, les stocker dans Note créerait une seconde source de vérité qui divergerait.

La skill **ne reformule pas** les templates. Elle remplit les trous. Le registre est en **lecture seule pour toutes les fonctions sauf la Fonction 0**, qui peut y écrire sous un protocole strict : montrer le **diff complet** (curateur → ancien statut → nouveau statut → ajout Note), obtenir la **validation explicite** de l'utilisateur, écrire, puis confirmer ce qui a été écrit. **Aucune écriture sans diff validé, jamais.**

---

## Fonction 0 — Ouverture de campagne (checklist + intégration du bilan)

**Déclencheur** : « nouvelle campagne Groover pour [single] », « on lance la campagne [single] », « par où je commence ? », ou l'utilisateur fournit directement un CSV « Bilan Campagne ». **C'est la porte d'entrée par défaut** : si l'utilisateur démarre une campagne sans désigner une fonction précise, passer par ici.

**Règle transverse — quand une ligne entre au registre.** Deux portes d'entrée, pas plus : (1) **à l'ouverture de campagne** (Étape 5) — les nouveaux curateurs retenus au plan entrent avec reco `🎱 Test` et statut `– Non contacté` dans la colonne du single courant ; (2) **au fil de l'eau** (Fonction 3) — un curateur découvert et scoré à la main entre au moment où l'utilisateur décide de le contacter. Filet de sécurité : le CSV de bilan **fait foi pour l'exhaustivité** — à l'Étape 3, tout curateur du CSV absent du registre remonte automatiquement dans le diff comme ligne à créer.

### Étape 1 — Cadrage

Identifier : (a) le **single de la nouvelle campagne**, (b) le **single de la campagne précédente** (dernière colonne de single du registre). Confirmer les deux en une phrase avant de continuer. Si ambigu, demander.

### Étape 2 — État du registre

Une seule question : **« Les statuts de la campagne [single précédent] sont-ils reportés dans le registre ? »**

- **Non / doute** → Étape 3.
- **Oui** → proposer un **contrôle éclair optionnel** : fournir quand même le CSV pour vérifier qu'aucune ligne ne manque (curateurs contactés au fil de l'eau via la Fonction 3 et jamais ajoutés — c'est la seule fuite possible si la règle transverse est respectée). Accepté → Étape 3 en mode vérification (le diff ne devrait contenir que d'éventuelles lignes manquantes). Décliné → Étape 4.

### Étape 3 — Intégration du bilan (seul cas d'écriture de la skill)

1. **Demander le CSV « Bilan Campagne »** du single précédent (exporté depuis sa page Groover ; archivé à l'emplacement défini dans les instructions du projet).
2. **Lire le CSV et identifier les colonnes utiles** : nom du curateur, décision/réponse, commentaire. Si le mapping des colonnes n'est pas évident, montrer le mapping déduit et le faire valider — ne jamais deviner en silence.
3. **Traduire les décisions en statuts du registre** : acceptation/partage → `✅ Accepté` ; refus → `❌ Refusé` ; aucune réponse → `➕ Silence` ; demande d'informations → `Veut en savoir plus`. Les curateurs du registre non contactés sur cette campagne restent `– Non contacté`.
4. **Préparer les ajouts de Note** : extraire de chaque commentaire uniquement le qualitatif non déductible (chaleur du refus, éléments cités, contexte), au format d'ajout tagué défini dans la description du registre. Ne rien inventer : pas de commentaire → pas d'ajout.
5. **Construire le diff complet** — une table : `Curateur | Statut actuel | Nouveau statut | Ajout Note`. Lister **à part** les curateurs présents dans le CSV mais absents du registre : l'utilisateur tranche (ajouter au registre ou ignorer) ; s'ils sont ajoutés, demander leurs `Genres` et `Tutoiement` ou les récupérer via la Fonction 3.
6. **Validation puis écriture.** Aucune écriture avant validation explicite du diff. Après validation, écrire dans le registre (colonne du single + Note), puis confirmer le nombre de lignes modifiées. **Filet de secours** : si l'écriture échoue (source non connectée, erreur API), fournir le diff dans un format directement copiable et laisser l'utilisateur l'appliquer à la main.

### Étape 4 — Vérification des recos

Signaler les curateurs dont le nouveau statut **change mécaniquement la reco** : typiquement le passage à **3 `❌ Refusé`** → proposer `🔴 Stop` (cohérent avec la règle Stop de la Fonction 2). Pour les autres cas (acceptation, refus chaud, silence répété), **proposer** une reco par curateur mais ne jamais l'écrire sans validation — la colonne de reco appartient à l'utilisateur. Écriture des recos validées selon le même protocole de diff que l'Étape 3.

### Étape 5 — Nouveaux curateurs (pont vers la Fonction 4)

Plusieurs semaines se sont écoulées depuis la dernière campagne : **de nouveaux curateurs ont pu arriver sur Groover** dans les genres/pays cibles du projet. Rappeler systématiquement cette possibilité — ne jamais attendre que l'utilisateur y pense — et guider :

1. **Rappeler la procédure d'export** en une ligne (extraction navigateur F12 + script Console → fichiers JSON) et **pointer vers le runbook du projet** (lien dans les instructions du projet).
2. L'utilisateur fournit le JSON → **comparer d'abord à la table scorée de la campagne précédente** (archivée avec les artefacts de campagne du projet) pour signaler explicitement les curateurs **réellement nouveaux** depuis la dernière campagne, puis dérouler la **Fonction 4** : scoring EV, plan de campagne, répartition du budget Grooviz. Le JSON brut n'a pas besoin d'être archivé : la table scorée contient déjà toute la population de l'export.
3. **Destination des artefacts** : le scoring / plan de campagne s'archive à l'emplacement des artefacts de campagne défini dans le projet, sur la ligne du single de la campagne en cours. **La table scorée doit rester exhaustive** — une ligne par curateur de l'export, jamais tronquée aux seuls retenus : c'est elle qui sert de photo de la plateforme au jour J pour la comparaison de la campagne suivante.
4. **Les nouveaux curateurs retenus au plan sont ajoutés au registre** — même protocole de diff validé qu'en Étape 3, conformément à la règle transverse : `Genres` remplis depuis le scoring, `Tutoiement` vide (vouvoiement par défaut), reco `🎱 Test`, statut `– Non contacté` dans la colonne du single courant. C'est ce qui permet à la Fonction 2 de les traiter ensuite en **Cas 1 — premier envoi**.

Étape **sautable** (pas de budget nouveaux curateurs cette fois) : le noter et passer au routage — on peut y revenir depuis l'Étape 6.

### Étape 6 — Routage guidé

L'utilisateur lance une campagne toutes les quelques semaines : **il ne se souvient pas des numéros de fonctions**. Ne jamais router par numéro seul — présenter un menu en langage naturel, dans l'ordre type d'une campagne, avec une ligne de rappel par option. Exemple :

> Registre à jour ✅, recos vérifiées ✅, nouveaux curateurs [scorés ✅ / étape sautée]. Prochaines étapes possibles :
> 1. **Écrire le pitch général du single** — le texte unique, sans mention de curateur, à coller dans le champ « pitch » de Groover. (Fonction 1)
> 2. **Générer les pitchs personnalisés** — un message par curateur retenu, choisi automatiquement selon son historique. (Fonction 2)
> 3. **Revenir aux nouveaux curateurs** — si l'étape a été sautée et que tu changes d'avis. (Étape 5 / Fonction 4)
> Par où tu veux commencer ?

Adapter l'ordre au contexte : si le pitch général existe déjà, aller directement aux pitchs personnalisés.

---

## Fonction 1 — Générer le pitch général d'un nouveau single

**Déclencheur** : « écris le pitch général pour [nouveau single] », « pitch global Groover pour [single] », ou demande équivalente.

**Inputs nécessaires** :
- Le titre du single.
- Sa date de sortie.
- Sa position dans l'album (1er, 2ème, 3ème… extrait).
- Ce que raconte le morceau (1-2 phrases que l'utilisateur fournit).
- Toute particularité de registre (ex. langue différente, genre différent du reste de l'album).

**Action** : générer **un pitch général unique**, sans mention de curateur, calibré sur le ton et la structure des pitchs généraux des singles précédents (lire les pitchs de référence dans le registre). Conserver la signature, le lien d'écoute et la mention de l'album.

**Output** : un pitch unique, prêt à coller dans le champ « pitch général » de Groover.

---

## Fonction 2 — Générer en batch les pitchs personnalisés

**Déclencheur** : « génère les pitchs personnalisés pour la campagne [single] », « écris les messages pour ma campagne », ou demande équivalente.

Le traitement se fait sur **deux couches indépendantes** : *qui* on contacte (piloté par la reco de l'utilisateur), puis *quel des 3 messages* on envoie (dérivé automatiquement de l'historique). Aucune étiquette manuelle n'est nécessaire pour le choix du message.

### Couche 1 — Qui contacter (colonne de reco)

- Lire **`Reco prochain single`** par défaut. Si l'utilisateur désigne explicitement une colonne de reco propre à la campagne (ex. `Reco parenthèse` pour un single « parenthèse »), lire **celle-là** à la place. Ne jamais la deviner ni la modifier.
- **Inclure** dans le batch tout curateur dont la reco n'est ni `🔴 Stop` ni `🚨 Ne pas prioriser`.
- **`⚠️ Exception 🔥`** : ne pas inclure d'office. Lister à part pour que l'utilisateur tranche.

### Couche 2 — Quel des 3 messages (dérivé de l'historique)

Pour chaque curateur retenu, lire ses colonnes de statut par single passé + la colonne `Note`, puis appliquer dans l'ordre. **Le premier cas qui s'applique gagne.**

1. **Cas 2 — Relance après acceptation.** Le curateur a au moins un `✅ Accepté` **ou** un `Veut en savoir plus` sur un single passé. → Remplir `[dernier single accepté]` = le single **le plus récemment sorti** parmi ceux au statut `✅ Accepté` (ou `Veut en savoir plus`). **Prioritaire même si le curateur a aussi un `❌ Refusé`** sur un autre single.
2. **Hors batch (Stop).** Le curateur cumule **3 `❌ Refusé` ou plus**. Ne pas générer de message.
3. **Cas 3 — Relance après refus chaud.** **1 ou 2 `❌ Refusé`**, avec un **encouragement à revenir repérable dans la `Note`**. → Remplir `[single de la campagne actuelle]`.
4. **Hors batch (refus froid).** **1 ou 2 `❌ Refusé`** sans aucun encouragement dans la `Note`. Ne pas re-pitcher. Les regrouper dans un bloc **« Exclus — refus froid (repêchage manuel possible) »** : l'utilisateur peut en faire remonter manuellement vers le Cas 1 si le single change de registre (ex. une parenthèse pop qui rouvre des curateurs refusés sur du rap).
5. **Cas 1 — Premier envoi.** Aucun des cas ci-dessus : curateur **jamais contacté**, ou contacté mais **uniquement des `➕ Silence`**. → Remplir `[genre 1]` et `[genre 2]` depuis la colonne `Genres`.

**Liste « À vérifier » — ne jamais deviner.** Sortir à part, **sans générer de message**, tout curateur dont l'historique est ambigu : typiquement une **`Note` de refus dont on ne peut pas juger si elle est chaude ou froide**. L'utilisateur tranche, puis relance si besoin.

### Remplissage du template

Lire les 6 gabarits du *kit de pitch personnalisé* à l'emplacement désigné dans les instructions du projet, puis :

- **Tu / Vous** : si la colonne `Tutoiement` vaut `✅`, prendre la version **tu** ; sinon la version **vous**.
- `[Nom curateur]` : colonne `Curateur`.
- `[genre 1] / [genre 2]` (Cas 1) : les 2 tags de `Genres`, formulés naturellement.
- `[dernier single accepté]` (Cas 2) : règle ci-dessus.
- `[single de la campagne actuelle]` (Cas 2 et 3) : le single de la campagne en cours.
- Ne **rien** reformuler d'autre. Les gabarits font foi.

### Output

Une liste de blocs, un par curateur retenu :

```
### [Nom curateur] — Cas [1/2/3] — Tu/Vous
[message complet, prêt à copier-coller dans Groover]
---
```

…puis, en bas, trois petites listes (uniquement si non vides), sans messages générés :
- **« ⚠️ Exception 🔥 — à trancher »**
- **« À vérifier — Note ambiguë »**
- **« Exclus — refus froid (repêchage manuel possible) »**

**Si le registre n'est pas lisible** (source du registre non connectée dans la session) : demander à l'utilisateur de coller la sélection des curateurs avec, pour chacun, les colonnes `Curateur`, `Genres`, `Tutoiement`, le statut par single passé et la `Note`.

---

## Fonction 3 — Scorer un curateur à la main (1 curateur)

**Déclencheur** : l'utilisateur colle le profil / la bio d'un curateur Groover qu'il vient de
découvrir, avec ses genres affichés.

**Inputs nécessaires** :
- Bio / présentation du curateur (texte libre).
- Genres affichés sur Groover.
- Le single courant (ou un rappel rapide de son registre).

**Inputs optionnels — à utiliser dès qu'ils sont visibles sur le profil** :
- **Coût** en Grooviz.
- **Taux de partage / d'acceptation** affiché.
- **Artistes de référence** que le curateur cite (artistes types, « pour fans de… »).

**Action** : évaluer le fit sans inventer d'information manquante.

- **Alignement genre / sous-genre** (0–1) : les genres recoupent-ils le registre du single ?
- **Langue / culture** (0–1) : la langue du morceau est-elle compatible avec le territoire ?
- **Activité / sérieux** (0–1) : la bio signale-t-elle une activité réelle (playlists alimentées,
  ligne édito claire) ou un compte vague ?
- **Fit éditorial via artistes de référence** (0–1) : **axe prioritaire**. Si le curateur cite des
  artistes qui recoupent l'univers du single ou les influences du projet, c'est le signal le plus
  fort. Un recoupement net (ex. une influence directe du projet) → bascule quasi automatique en
  « à shooter », quels que soient les autres axes. *C'est le seul endroit où ce signal existe : il
  n'est pas dans l'export JSON (Fonction 4).*

**Si coût + taux de partage sont fournis** : ne pas se limiter à la note qualitative — calculer le
**mini-EV = taux de partage ÷ coût**, cohérent avec la Fonction 4, et faire porter la reco dessus
(un partage élevé à coût faible prime ; un partage faible à coût élevé se passe). La note
qualitative /4 reste le **fallback** quand ces chiffres ne sont pas disponibles.

**Output** :
- **Verdict** : note /4 (mode qualitatif) **ou** lecture EV (« bon rapport partage/coût » /
  « trop cher pour ce qu'il partage ») quand le coût + le partage sont connus.
- **Recommandation** : ≥ 3 (ou EV favorable) = à shooter ; intermédiaire = borderline, à shooter si
  le budget le permet ; faible = passer.
- **Angle de pitch** : une phrase concrète — quel élément de la bio (idéalement l'**artiste de
  référence** qui matche) mentionner dans la personnalisation, et quels genres copier dans la
  colonne `Genres` si l'utilisateur l'ajoute au registre.

---

## Fonction 4 — Scoring en batch depuis un export JSON Groover

**Déclencheur** : l'utilisateur fournit un ou plusieurs **exports JSON** de curateurs (endpoint
`statsv3`) pour une campagne, ou demande « score mes curateurs Groover », « fais-moi le plan de
campagne Groover », « répartis mon budget Grooviz ».

**Ressources projet (lues dans le projet, jamais inventées)** — vivent dans le registre / les
instructions du projet (Notion ou autre), **pas dans la skill** :
- Les **IDs des genres cibles** et des **pays** (table de correspondance ID → nom).
- Le **budget** en Grooviz.
- Le **lien du runbook d'extraction** (procédure F12 / Console + script), indiqué dans les
  instructions du projet.

**Inputs nécessaires** :
- Le ou les fichiers **JSON** exportés. **S'ils manquent** : renvoyer l'utilisateur vers le
  runbook d'extraction du projet (ne pas tenter de scraper soi-même).

**Action — logique de scoring.** Pour chaque curateur, **restreint aux genres cibles du projet** :

- **Taux de partage 90 j** = `partagés ÷ reçus` dans les genres cibles
  (`shared_stats_by_subgenre_90d`), **lissé** par un prior (le partage moyen du lot) + pseudo-comptes,
  pour neutraliser les petits échantillons (« 1/1 = 100 % »).
- **EV par Grooviz** = `partage × valeur d'opportunité × qualité × reach ÷ coût`.
  - *valeur d'opportunité* (`primary_opportunities`) : playlist Spotify > partage social permanent
    > radio > article > feedback seul.
  - *qualité* : `satisfaction_rate` + `stars_rate` → **élimine les playlists-poubelles** (partage
    élevé mais artistes mécontents). Prudence si trop peu de notes.
  - *reach* : `followers` (léger ; souvent absent pour radios/blogs, ne pas surpondérer).
- **Éligibilité streaming** : actif (`feedback_count_monthly` ≥ 3), qualité suffisante, **hors**
  « feedback seul » et **hors** contacts A&R/label (record/management/sync/publishing-deal) — ces
  derniers **listés à part**.
- **Décodage** des IDs de genre → noms via la table du projet.
- **Allocation du budget** : dépenser le budget sur les meilleurs EV, tous pays confondus
  (problème du sac à dos / greedy par EV). **La répartition par pays est un résultat, pas un point
  de départ.**

**Output** :
- **Table scorée complète** (CSV, une ligne par curateur) : `pays`, `curateur`, `id`,
  `genres_principaux`, `cout_grooviz`, `partage_pct`, `recus`, `fit`, `followers`,
  `satisfaction_pct`, `etoiles`, `qualite`, `actif`, `opportunite`, `pertinence_projet`, `ev`,
  `retenu_campagne`, `deal`, `feedback_only`.
- **Plan de campagne** : les curateurs retenus sous budget, groupés par pays, avec coût et lien
  profil (`groover.co/fr/influencer/{id}/`).
- **Contacts A&R / deals** listés séparément (hors budget streaming).

**Colonnes clés à expliquer à l'utilisateur** :
- **`ev`** = quoi acheter en premier (meilleur rapport résultat/prix). Trier par EV = ordre d'achat,
  y compris la réserve (curateurs non retenus = budget futur).
- **`pertinence_projet`** = qualité du match **indépendamment du prix** (EV sans diviser par le
  coût) → repérer un excellent curateur même cher, à garder pour un budget confortable.
- **`retenu_campagne`** = la sélection sous budget. **Non déductible d'un simple tri** : le sac à
  dos saute parfois un curateur cher pour en caser deux moins chers.

**Hors-périmètre** : la Fonction 4 **lit** le JSON fourni par l'utilisateur — elle ne scrape pas
(l'extraction se fait côté utilisateur, via le runbook du projet). Les IDs et le budget ne sont
jamais inventés : s'ils manquent, les demander.

---

## Hors-périmètre

Cette skill ne fait **pas** :
- Du scraping Groover automatique (le site est derrière login).
- De la création ou modification de templates de pitch (les templates appartiennent à l'utilisateur, vivent dans son projet).
- De l'écriture dans le registre **en dehors de la Fonction 0** — et en Fonction 0, jamais sans diff validé. Les Fonctions 1 à 4 restent en lecture seule.
- De la décision sur les exceptions ⚠️ 🔥, les notes de refus ambiguës ou autres cas qualitatifs : elle surface, l'utilisateur tranche.
- De la fabrication de données manquantes (genres, bio, historique) : si une info manque, elle la demande.
