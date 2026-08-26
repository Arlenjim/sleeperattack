# SEO-PROCESS.md — process d'indexation des pages de référence

But : les règles SEO vivent dans le repo, pas dans une tête. À lire AVANT toute
création ou modification de page.

**Fichier identique dans les deux repos** (skillinjection, sleeperattack). Toute
modification se répercute dans les deux dans le même geste : maintenir deux
copies qui divergent vaut moins que pas de copie du tout.

## Méthode — attribution
- Ne jamais présenter comme une consigne de Damien quelque chose qu'il n'a pas
  écrit. Une déduction se formule « je propose » / « je recommande », jamais
  « ta règle » ni « comme tu l'as demandé ».
- Sur une session longue, attribuer ses propres déductions à Damien finit par
  lui faire valider ce qu'il croit avoir demandé. Toujours séparer fait et déduction.

## Règles (non négociables)

### Sitemap
- `sitemap.xml` mis à jour à CHAQUE publication ou changement d'URL, `lastmod` =
  date du jour. C'est le point qui casse en silence quand on publie en dur.
- Aujourd'hui manuel (1 URL/domaine). Dès que le nombre de pages grandit :
  générer le sitemap au build et documenter la commande ici.

### Maillage interne
- ≥ 3 liens internes entrants vers toute nouvelle page, depuis le **corps** de
  pages thématiquement proches (pas un menu, pas un footer).
- Ancre = mot-clé cible **exact** de la page qui reçoit le lien.
- La cible de ≥ 3 s'applique **dès qu'il y a assez de pages proches** pour
  l'atteindre naturellement. Sur un domaine à 1-2 pages : poser le lien le plus
  pertinent, **ne jamais fabriquer** de liens artificiels pour cocher la règle,
  compléter au fur et à mesure que le cluster grandit.

### Données structurées (JSON-LD)
- Systématique, adapté au contenu **réel** : `DefinedTerm` (définition),
  `TechArticle` (article), `FAQPage` **uniquement si** une vraie FAQ est visible.
- Réponses `FAQPage` **identiques au texte visible, caractère par caractère** —
  une apostrophe typographique ou une espace insécable différente casse tout le
  balisage. Vérifier **programmatiquement** avant commit, jamais à l'œil.
- Vérifier que le JSON parse avant commit.

### Canonical & en-tête
- `<link rel="canonical">` **absolu** vers le domaine custom, sur chaque page.
- `<title>` < 60 caractères, `meta description` < 155, **un seul** `<h1>`.
- Dates de publication et de mise à jour **visibles**, cohérentes avec
  `datePublished` / `dateModified` du JSON-LD.

### Anti-cannibalisation (décidé le 2026-08-17)
- Deux pages d'un même domaine ne visent jamais le même mot-clé nu.
- Si une réponse de FAQ recoupe une page « X vs Y » dédiée : la FAQ reste
  **courte** (1–2 phrases) et **lie** vers la page dédiée (ancre = mot-clé) ; la
  page dédiée est propriétaire de la requête. Voir « Notes par page ».

### Après chaque publication
- ⚠️ Demander l'indexation de l'URL dans **Google Search Console** (propriété
  « Préfixe d'URL »). Claude ne peut pas le faire → à rappeler en fin de rapport
  à chaque publication.

## Checklist avant commit
- [ ] title < 60 · meta description < 155 · un seul `<h1>`
- [ ] canonical absolu présent
- [ ] JSON-LD parse + types adaptés au contenu réel
- [ ] si FAQ : réponses `FAQPage` == visible (comparaison **caractère par caractère**)
- [ ] ≥ 3 liens internes entrants (ancre = mot-clé) vers toute nouvelle page
- [ ] `sitemap.xml` à jour (URL + `lastmod` du jour)
- [ ] rendu vérifié (capture ou navigateur), pas seulement le code
- [ ] rappel Search Console noté pour Damien

## Template de page (structure de référence)

Une nouvelle page reprend la structure ci-dessous et remplit les mêmes
emplacements, dans cet ordre. **Design partagé via `/style.css`** — extraction
faite dans les deux domaines : lier `/style.css`, plus un `<style>` d'override
minimal propre à la page si elle en a besoin. Le `<style>` interne d'un schéma
SVG reste dans le SVG. Toute extraction de CSS se fait par **déplacement pur**,
vérifié par script (le bloc extrait doit être identique caractère par
caractère) ; les règles neuves s'ajoutent en fin de fichier, sous un commentaire
qui les délimite. Pas de `TEMPLATE.html` séparé : la structure ci-dessous fait
foi, `index.html` / `style.css` en sont l'instance vivante.

**HEAD**
- `charset` / `viewport`, `<title>`, `meta description`
- `<link rel="canonical">` absolu
- Open Graph (`og:title/description/type/url/site_name` + `og:image` 1200×630 avec
  `width`/`height`/`alt`) + Twitter Card
- JSON-LD `@graph` : `DefinedTerm` (+ `TechArticle`, + `FAQPage` si FAQ visible)

**BODY** (hiérarchie Hn)
- `.bar` : bandeau statut
- `<header>` : `.eyebrow`, `<h1>` (contient le terme), `.lede`
- `.def` : définition en une phrase
- Sections `<h2>` numérotées. Tronc commun, obligatoire :
  `01` What it is · `02` Mechanism / lifecycle (schéma SVG) · `03` How it differs /
  why it's hard · `04` Reducing the risk · `05` FAQ (visible) · `06` Related attack
  classes (carte `.ref` vers le domaine frère) · `07` Research & disclosures
- Deux emplacements **optionnels** s'insèrent quand le sujet les justifie et
  décalent la numérotation : « pourquoi la défense évidente échoue » après `03`,
  et un mapping vers un référentiel (OWASP) après « Reducing the risk ». Ne pas
  les créer pour remplir : ils ne valent que si la page a quelque chose que la
  littérature ne dit pas déjà mieux ailleurs.
- `<footer>` : mainteneur, lien **GitHub Issues**, dates publiée / mise à jour, disclaimer

Cible : **1200–2000 mots**, anglais, ton technique neutre. Références arXiv
**vérifiées** (page existe + titre correspond) — jamais inventées.

## Notes par page
- **skillinjection (home)** : va de `01` à `09` depuis le 2026-08-26 — elle
  utilise les deux emplacements optionnels (`04` why scanners miss it, `06`
  OWASP AST10). Les CVE Claude Code qu'elle cite portent sur la configuration
  de l'agent, pas sur des skills : elles sont présentées comme la même
  frontière de confiance, jamais comme des CVE de skill injection.
- **sleeper attack vs sleeper agents** : page dédiée construite le 2026-08-17 à
  `/sleeper-attack-vs-sleeper-agents/`. La réponse FAQ Q2 de la home a été
  raccourcie et **liée** vers elle (réponse courte + lien) — modèle à répliquer
  pour toute future FAQ qui recoupe une page « vs ».

## Constats de terrain (2026-08-17)
- **Statut d'indexation** : ne jamais le déduire d'une recherche web du modèle
  (elle renvoie des sosies, pas un vrai `site:`). Source de vérité = Search
  Console (Inspection d'URL / Couverture) ou un `site:` exécuté à la main.
- **sleeperattack.com** : était **déjà indexée** par Google avant les corrections
  (vérifié `site:` manuel — titre + description affichés). Le « aucune page
  indexée » de l'audit du 2026-08-17 était faux, dû à un `site:` mal exécuté par
  l'outil de recherche.
- **skillinjection.com** : explorée le 2026-07-23 puis « Explorée, actuellement
  non indexée » — **pas un problème de découverte mais de valeur perçue**. En file
  de réévaluation depuis le 2026-08-17, sur la version enrichie.

## Références arXiv (vérifiées)

Vérifier **à la source**, jamais depuis la mémoire du modèle ni depuis un
agrégateur : recouper la page `arxiv.org/abs/<id>` **et** l'API brute
(`export.arxiv.org/api/query?id_list=<id>`), qui renvoie le titre et la date
sans passer par un résumé. Même exigence pour une CVE (NVD) et pour un
référentiel (dépôt officiel du projet).

- skillinjection — vérifiées le **2026-08-26** : `2602.06547`, `2602.14211`,
  `2602.20156`, `2603.00195`, `2604.03081`, `2606.07943` (POISE),
  `2608.09732` (ColluSkill), `2608.21929` (SkillBloat)
- sleeperattack — vérifiées le **2026-08-17** : `2605.28201`, `2605.15338`,
  `2604.16548`, `2401.05566` (Sleeper Agents, Anthropic 2024)

Un identifiant arXiv porte le mois d'**annonce** (`2603.00195` = mars 2026),
qui peut différer du mois de soumission renvoyé par l'API (27 février 2026).
Afficher le mois de l'identifiant : cohérent avec ce que le lecteur voit.
