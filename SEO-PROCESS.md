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

Une nouvelle page **copie le `index.html` du domaine cible** (pour hériter de ses
tokens de design : voir `:root` dans `index.html`) et remplit les mêmes
emplacements, dans cet ordre. Pas de fichier `TEMPLATE.html` séparé : ce serait
un doublon du `index.html` qui divergerait — la structure ci-dessous fait foi,
le `index.html` en est l'instance vivante.

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
- Sections `<h2>` numérotées :
  `01` What it is · `02` Mechanism / lifecycle (schéma SVG) · `03` How it differs /
  why it's hard · `04` Reducing the risk · `05` FAQ (visible) · `06` Related attack
  classes (carte `.ref` vers le domaine frère) · `07` Research & disclosures
- `<footer>` : mainteneur, lien **GitHub Issues**, dates publiée / mise à jour, disclaimer

Cible : **1200–2000 mots**, anglais, ton technique neutre. Références arXiv
**vérifiées** (page existe + titre correspond) — jamais inventées.

## Notes par page
- **sleeper attack vs sleeper agents** : la FAQ de sleeperattack.com contient déjà
  cette disambiguation (réponse développée, faute de page dédiée). Quand la page
  dédiée « sleeper attack vs sleeper agents » sera construite : **raccourcir** la
  réponse FAQ à 1–2 phrases et la **lier** vers la page dédiée (anti-cannibalisation).

## Références arXiv (vérifiées le 2026-08-17)
- skillinjection : `2602.20156`, `2604.03081`, `2603.00195`
- sleeperattack : `2605.28201`, `2605.15338`, `2604.16548`
