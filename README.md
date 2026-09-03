# BUT3 MMI — Parcours WDI S5 2026

## Sujet de projet : "Ce que le prof ne vous a pas dit !" — Les nouvelles fonctionnalités CSS

**Débusquez-vous des fonctionnalités que même votre prof ne connaît pas encore !?**

---

### Contexte

CSS évolue vite. Entre les _container queries_, `:has()`, l'imbrication native, les _cascade layers_, `color-mix()`, le _scroll-driven animation_ ou encore l'_anchor positioning_, le langage a beaucoup changé ces trois dernières années — bien plus que ce qui a pu être vu en cours.

Votre mission : **construire une page web de référence** ("cheat sheet" interactive) qui recense les nouvelles fonctionnalités CSS, avec pour chacune :

- une explication claire (à quoi ça sert, quel problème ça résout),
- un exemple de code **et** une démonstration visuelle fonctionnelle,
- un tableau de compatibilité navigateurs,
- une solution de repli (_fallback_) ou une astuce de _progressive enhancement_ si la fonctionnalité n'est pas supportée partout.

---

### Travail demandé

#### 1. Recherche (veille)

Recensez **au minimum 10 fonctionnalités CSS récentes** (grosso modo post-2022). Quelques pistes pour démarrer votre veille — libre à vous d'en trouver d'autres :

| Thème                   | Exemples de pistes                                                                     |
| ----------------------- | -------------------------------------------------------------------------------------- |
| Mise en page            | Container Queries (`@container`), Subgrid, `:has()`, `text-wrap: balance`              |
| Couleurs & thèmes       | `color-mix()`, espaces colorimétriques (`oklch`, `lab`), `light-dark()`                |
| Architecture CSS        | Cascade Layers (`@layer`), imbrication native (nesting), `@scope`                      |
| Animation & interaction | Scroll-driven animations, `@starting-style`, transitions de vue (View Transitions API) |
| Positionnement          | Anchor Positioning (`anchor()`, `position-anchor`)                                     |
| Unités & fonctions      | `clamp()`, unités `dvh`/`svh`/`lvh`, `min()`/`max()`                                   |
| Sélecteurs              | `:is()`, `:where()`, `:has()`, sélecteurs de sous-grille                               |
| ...                     | ...                                                                                    |

Pour chaque fonctionnalité, indiquez vos sources (documentation officielle, spec W3C/WHATWG, articles).

#### 2. Réalisation de la page web

La page doit être **codée en HTML / CSS / JS**

1. **Une page d'accueil / sommaire** avec navigation vers chaque fiche fonctionnalité (ancre ou menu).
2. **Une fiche par fonctionnalité**, structurée ainsi :
   - Nom de la propriété/fonction CSS et date d'introduction approximative.
   - Explication en français, claire et synthétique (5–10 lignes).
   - Un bloc de code (utilisez `<pre><code>`) montrant la syntaxe.
   - **Une démo live** : la fonctionnalité doit être visible et testée en vrai dans la page (pas juste une capture d'écran).
   - Un tableau de compatibilité (navigateur / version minimale / support partiel ou total). Vous pouvez vous appuyer sur les données de [Can I Use](https://caniuse.com) ou du [MDN Browser Compatibility Data].
   - Une note sur le _fallback_ : que se passe-t-il si le navigateur ne supporte pas la fonctionnalité ? Comment sécuriser l'affichage (`@supports`, valeurs de repli, etc.) ?

---

### Livrables

- Le code source complet (dépôt Git : lien à fournir).
- La page déployée et accessible en ligne (GitHub Pages).
- Un court README expliquant vos choix techniques et vos éventuelles limites/bugs connus.

---

### Modalités

- **Travail** : individuel ou en binôme.
- **Durée indicative** : 2 séances de TP + travail personnel.
- **Format de rendu** : dépôt du lien du repo + lien de la page en ligne sur la plateforme du cours.

---

### Grille d'évaluation indicative

Critère

- Pertinence et diversité des fonctionnalités choisies (≥10, variées)
- Qualité des explications (clarté, justesse technique)
- Démos fonctionnelles et code exemple propre
- Tableaux de compatibilité navigateurs (exactitude, sourcing)
- Gestion des fallbacks / `@supports`
- Qualité du HTML/CSS (sémantique, responsive, accessibilité)
- Bonus : filtre/recherche JS, design soigné, originalité

### Pour bien démarrer

- [MDN Web Docs — CSS](https://developer.mozilla.org/fr/docs/Web/CSS)
- [Can I Use](https://caniuse.com)
- [Chrome for Developers — nouveautés CSS](https://developer.chrome.com/blog)
- [web.dev — CSS](https://web.dev/learn/css)
- Baseline (indicateur de support consolidé multi-navigateurs) sur MDN
- [State of CSS 2026](https://2026.stateofcss.com/en-US) — pour voir les tendances et l'adoption des nouvelles fonctionnalités.
- web ...
