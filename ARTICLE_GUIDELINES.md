# Guide éditorial — Guide et Comparatif Air Fryer (À LIRE INTÉGRALEMENT AVANT DE PUBLIER UN ARTICLE)

Ce document est la référence unique pour générer et publier un nouvel article sur ce site.
Il est conçu pour être suivi par une tâche automatisée quotidienne sans contexte préalable :
tout ce qui est nécessaire est décrit ici ou dans les fichiers référencés.

## 0. Fichiers du projet à connaître

- `data/config.json` — nom du site, domaine, marketplace Amazon (`amazon.fr`), tag Associates.
- `data/products.json` — catalogue des produits Amazon utilisables dans les articles (ASIN, prix, specs, avis).
- `data/content-queue.json` — file d'attente éditoriale : la liste des sujets à publier, dans l'ordre, avec leur statut.
- `css/style.css` — feuille de style unique, ne pas dupliquer de CSS inline sauf cas exceptionnel.
- `articles/{slug}.html` — chaque article est un fichier HTML autonome dans ce dossier.
- `index.html`, `guides-comparatifs.html`, `guides-complets.html`, `articles.html` — pages de listing à mettre à jour à chaque publication (voir section 6).
- `sitemap.xml` — à mettre à jour à chaque publication (voir section 7).

**IMPORTANT — Garde-fou affiliation** : si `data/config.json` a encore `"status": "PLACEHOLDER_DO_NOT_PUBLISH..."`
ou si `amazon_associate_tag` vaut `REPLACE_WITH_REAL_TAG`, ou si les produits utilisés dans
`data/products.json` ont `"status": "PLACEHOLDER_DO_NOT_PUBLISH"` : NE PAS publier de liens
affiliés réels. Générer l'article normalement mais avertir l'utilisateur qu'il manque encore
les vraies informations Amazon.

## 1. Choisir le sujet du jour

1. Ouvrir `data/content-queue.json`.
2. Prendre la première entrée avec `"status": "pending"`.
3. Le champ `type` indique le type d'article à produire : `comparatif`, `guide` ou `article`.
4. Une fois l'article publié, repasser cette entrée à `"status": "published"` et ajouter
   `"published_date": "YYYY-MM-DD"`.
5. S'il ne reste plus d'entrées `pending`, générer 20 nouveaux sujets cohérents avec la ligne
   éditoriale (voir section 2) et les ajouter à la queue avant de continuer.

## 2. Les 3 types de contenu (rotation)

| Type | Objectif | Mots-clés типiques |
|---|---|---|
| `comparatif` | Comparer 2 à 5 modèles précis pour aider à trancher | "X vs Y", "meilleur airfryer pour...", "comparatif airfryer 2026" |
| `guide` | Guide complet, exhaustif, evergreen (utilisation, entretien, achat, sécurité) | "comment...", "guide d'achat", "tout savoir sur..." |
| `article` | Conseil pratique, astuce, recette, actualité produit | "recette airfryer...", "astuce...", "pourquoi..." |

Ne jamais publier deux fois le même sujet ou la même requête cible (vérifier `content-queue.json`
et le dossier `articles/` existant).

## 3. Structure SEO obligatoire (les 3 types)

- **Longueur** : 2500 mots minimum dans le corps de l'article (hors header/footer/nav). Compter le texte réel, pas le HTML.
- **Title tag** (`<title>`) : 50-60 caractères, mot-clé principal en début de titre.
- **Meta description** : 140-160 caractères, incite au clic, contient le mot-clé principal.
- **URL/slug** : `articles/{slug-en-minuscules-avec-tirets}.html`, dérivé du mot-clé principal, sans mots vides inutiles.
- **H1** : un seul par page, contient le mot-clé principal, différent du title tag (variation naturelle).
- **Structure Hn** : H2 pour chaque section majeure, H3 pour les sous-sections. Ne jamais sauter un niveau.
- **Table des matières** (`.toc`) juste après l'intro, avec ancres vers chaque H2.
- **Introduction** (100-150 mots) : répond immédiatement à l'intention de recherche, contient le mot-clé principal dans les 2 premières phrases.
- **Maillage interne** : au moins 3 liens vers d'autres articles du site (existants dans `articles/`) ou vers les pages de catégorie.
- **FAQ** en fin d'article (3 à 6 questions) avec balisage `FAQPage` JSON-LD — cible les featured snippets et les "People Also Ask".
- **Images** : si une image est utilisée, `alt` descriptif contenant le mot-clé associé (pas de bourrage). Ne jamais inventer des images produit — utiliser seulement des `image_url` déjà présentes dans `data/products.json`, sinon omettre l'image et garder uniquement les blocs texte/tableau.
- **Balise canonical** vers l'URL finale du domaine (voir `data/config.json`).
- **JSON-LD** : `Article` (ou `Product`/`FAQPage` en complément) + `BreadcrumbList` sur chaque page article.
- **Disclosure d'affiliation** : bloc `.disclosure` obligatoire juste après l'intro, AVANT le premier lien affilié, avec le texte standard (section 5).

## 4. Gabarit HTML d'un article

Reprendre exactement le header/nav et le footer utilisés dans `index.html` (même markup,
mêmes liens). Le corps suit ce squelette :

```html
<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>{{TITLE_50_60_CHARS}}</title>
<meta name="description" content="{{META_DESCRIPTION_140_160}}">
<link rel="canonical" href="https://{{DOMAIN}}/articles/{{SLUG}}.html">
<meta property="og:title" content="{{TITLE}}">
<meta property="og:description" content="{{META_DESCRIPTION}}">
<meta property="og:type" content="article">
<link rel="stylesheet" href="/css/style.css">
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Article",
  "headline": "{{H1}}",
  "datePublished": "{{ISO_DATE}}",
  "author": { "@type": "Organization", "name": "Guide et Comparatif Air Fryer" }
}
</script>
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "BreadcrumbList",
  "itemListElement": [
    {"@type":"ListItem","position":1,"name":"Accueil","item":"https://{{DOMAIN}}/index.html"},
    {"@type":"ListItem","position":2,"name":"{{CATEGORY_LABEL}}","item":"https://{{DOMAIN}}/{{CATEGORY_PAGE}}.html"},
    {"@type":"ListItem","position":3,"name":"{{H1}}","item":"https://{{DOMAIN}}/articles/{{SLUG}}.html"}
  ]
}
</script>
<!-- Ajouter un 3e bloc JSON-LD FAQPage si une FAQ est présente -->
</head>
<body>

<!-- même header que index.html -->

<div class="article-header">
  <div class="container">
    <p class="breadcrumb"><a href="/index.html">Accueil</a> / <a href="/{{CATEGORY_PAGE}}.html">{{CATEGORY_LABEL}}</a> / {{H1}}</p>
    <h1>{{H1}}</h1>
    <p class="article-meta">Publié le {{DATE_LISIBLE}} · {{READING_TIME}} min de lecture</p>
  </div>
</div>

<article class="article-body">
  <div class="disclosure">
    Cet article contient des liens affiliés Amazon. En tant que Partenaire Amazon, nous réalisons un
    bénéfice sur les achats remplissant les conditions requises, sans coût supplémentaire pour vous.
    <a href="/divulgation-affiliation.html">En savoir plus</a>.
  </div>

  <p>{{INTRO_100_150_MOTS}}</p>

  <div class="toc">
    <div class="toc-title">Sommaire</div>
    <ul>
      <li><a href="#section-1">{{TITRE_H2_1}}</a></li>
      <!-- ... -->
    </ul>
  </div>

  <!-- Sections H2/H3, tableaux comparatifs (.compare-table), blocs produits (.product-card), CTA (.cta-banner) -->

  <h2>Foire aux questions</h2>
  <div class="faq-item"><h3>{{QUESTION}}</h3><p>{{REPONSE}}</p></div>
  <!-- ... -->

  <h2>Articles liés</h2>
  <ul>
    <li><a href="/articles/{{AUTRE_SLUG}}.html">{{AUTRE_TITRE}}</a></li>
  </ul>
</article>

<!-- même footer que index.html -->

</body>
</html>
```

## 5. Texte de disclosure standard (obligatoire, ne pas reformuler librement)

> Cet article contient des liens affiliés Amazon. En tant que Partenaire Amazon, nous réalisons un
> bénéfice sur les achats remplissant les conditions requises, sans coût supplémentaire pour vous.

Ce bloc doit apparaître **avant le premier lien affilié** de la page (juste après l'intro, voir gabarit).

## 6. Intégration des liens affiliés Amazon

1. Choisir les produits pertinents dans `data/products.json` (filtrer par `category`/`best_for` selon le sujet).
2. Construire l'URL : `https://{{amazon_marketplace_domain}}/dp/{{asin}}?tag={{amazon_associate_tag}}`
   (valeurs dans `data/config.json`).
3. Chaque lien affilié DOIT avoir : `rel="sponsored nofollow noopener" target="_blank"`.
4. Présenter les produits via le bloc `.product-card` (voir `css/style.css`) : image, nom, prix,
   note, points forts/faibles, bouton `.btn-affiliate` avec le texte "Voir le prix sur Amazon".
5. Pour un comparatif, utiliser aussi un tableau `.compare-table` récapitulatif avec une colonne
   "Voir sur Amazon" contenant le même lien affilié.
6. Ne jamais inventer un ASIN, un prix ou une caractéristique produit qui n'est pas dans
   `data/products.json`. Si le catalogue ne contient pas assez de produits pertinents pour le
   sujet du jour, le signaler à l'utilisateur plutôt que d'inventer des données produit.
7. Ne pas dépasser 1 lien affilié pour ~300-400 mots de contenu (éviter le sur-referencement,
   mauvais pour l'UX et pénalisé par Google).

## 7. Mise à jour des pages de listing après publication

Après avoir créé `articles/{{slug}}.html` :

1. Dans `index.html`, ajouter une nouvelle `.article-card` en haut de la grille entre
   `<!-- ARTICLES_LATEST_START -->` et `<!-- ARTICLES_LATEST_END -->` (garder les 9 plus récentes,
   retirer la plus ancienne si on dépasse 9).
2. Dans la page de catégorie correspondante (`guides-comparatifs.html`, `guides-complets.html`
   ou `articles.html`), ajouter la carte entre les marqueurs `ARTICLES_{TYPE}_START/END` de cette page.
3. Retirer les cartes placeholder "Bientôt disponible" dès qu'au moins un vrai article existe.
4. Dans `sitemap.xml`, ajouter une entrée `<url>` pour le nouvel article
   (`<loc>`, `<lastmod>` = date du jour, `<changefreq>monthly</changefreq>`, `<priority>0.6</priority>`).

## 8. Workflow Git (fin de chaque génération)

```
git add -A
git commit -m "Ajout article: {{H1}}"
git push origin main
```

Le push déclenche automatiquement le redéploiement sur Vercel (connecté au repo GitHub).

## 9. Ce qu'il ne faut jamais faire

- Ne jamais publier un article de moins de 2500 mots réels.
- Ne jamais dupliquer un mot-clé principal déjà utilisé par un autre article publié.
- Ne jamais inventer des données produit (prix, ASIN, caractéristiques).
- Ne jamais publier de vrais liens affiliés tant que `data/config.json`/`data/products.json`
  sont encore au statut `PLACEHOLDER_DO_NOT_PUBLISH...`.
- Ne jamais retirer le bloc `.disclosure` ni les attributs `rel="sponsored nofollow noopener"`.
- Ne jamais faire de `git push --force`.
