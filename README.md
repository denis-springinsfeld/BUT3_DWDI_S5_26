# Motion (Ex Framer Motion)

[Docs motion pour React](https://motion.dev/docs/react)

## Exercice 0 : Les Fondamentaux

> [!TIPS]
> **Objectif** : Comprendre le composant `motion` et les propriétés de base.
> Consignes :
>
> - Créer un titre qui apparaît avec un fondu et un changement d'échelle.
> - Utiliser `initial`, `animate` et `transition`.
> - Définir des `variants` pour séparer la logique du design.

### Installation

```bash
npm install motion
```

```jsx
import { motion, type Variants } from "motion/react";
```

### Composant `motion.*`

Pour animer un élément HTML, on remplace la balise standard par sa version accessible via `motion.`.
Exemple : `<h1>` devient `<motion.h1>`, `<div>` devient `<motion.div>`...

### Propriétés fondamentales

- `initial` : État de départ au montage du composant.
- `animate` : État cible de l'animation.
- `transition` : Façon dont on passe de `initial` vers `animate`.
- `exit` : État avant le démontage du composant.

Options courantes de `transition` :

- `duration` : durée (en secondes)
- `delay` : délai de départ
- `ease` : courbe (`"easeInOut"`, `"linear"`, etc.)
- `type` : `"spring"` (physique) ou `"tween"` (temporel)

### Exemple (objet direct)

```jsx
<motion.h1
  initial={{ opacity: 0, scale: 0.5 }}
  animate={{ opacity: 1, scale: 1 }}
  transition={{ duration: 0.7 }}
  className="text-5xl font-bold"
>
  Hello World !
</motion.h1>
```

### Les Variants

Les **variants** permettent de définir des objets de styles nommés. C'est la méthode recommandée pour séparer la logique d'animation du rendu JSX.

```tsx
const myVariants: Variants = {
  hidden: { opacity: 0 },
  visible: { opacity: 1 },
};

<motion.h1
  variants={myVariants}
  initial="hidden"
  animate="visible"
  className="text-5xl font-bold"
>
  Hello World !
</motion.h1>;
```

Pourquoi utiliser les variants :

- Lisibilité : JSX plus propre
- Réutilisabilité : mêmes états sur plusieurs composants
- Propagation : parent/enfants coordonnés
- Orchestration : synchronisation des animations

---

## Exercice 1 : Orchestration (Stagger)

> [!TIPS]
> **Objectif** : Animer plusieurs éléments de manière séquentielle.
> Consignes :
>
> - Créer un conteneur parent et deux enfants.
> - Utiliser `staggerChildren` dans le variant parent pour décaler l'apparition des enfants.
> - Faire venir un enfant du haut et l'autre du bas.

### États standard

| Propriété     | Description                                                |
| :------------ | :--------------------------------------------------------- |
| `initial`     | État au montage du composant (ex: `hidden`).               |
| `animate`     | État cible immédiat (ex: `visible`).                       |
| `exit`        | État avant le démontage du DOM (avec `<AnimatePresence>`). |
| `whileHover`  | État actif au survol.                                      |
| `whileTap`    | État actif au clic / toucher.                              |
| `whileInView` | État actif quand l'élément entre dans le viewport.         |

### Propagation

Si le parent est en `animate="visible"`, les enfants `motion.*` cherchent automatiquement le variant `visible` dans leur propre objet `variants`.

### Orchestration

Dans la transition du parent, `staggerChildren` décale le démarrage des enfants.

```tsx
visible: {
  opacity: 1,
  transition: {
    staggerChildren: 0.3,
  },
}
```

---

## Exercice 2 : Keyframes et Boucles

> [!TIPS]
> **Objectif** : Créer des animations cycliques
> complexes.
> Consignes :
>
> - Utiliser des tableaux de valeurs (keyframes) pour `scale`, `rotate` et `borderRadius`.
> - Mettre en place une boucle infinie avec `repeat: Infinity` et `repeatType: "reverse"`.

### Keyframes

Au lieu d'une valeur unique, on peut passer un tableau pour faire une séquence d'états.

```tsx
animate={{
  scale: [1, 2, 2, 1],
  rotate: [0, 90, 180, 0],
  borderRadius: ["20%", "20%", "50%", "20%"],
}}
```

- Chaque étape partage la durée totale par défaut.
- Pratique pour des animations plus complexes.

### Boucles

- `repeat: Infinity` : boucle infinie
- `repeatDelay` : pause entre deux cycles
- `repeatType` :
  - `"loop"` : recommence au début
  - `"reverse"` : joue en sens inverse un cycle sur deux
  - `"mirror"` : alterne aller/retour

### Bonnes pratiques de nommage

- `hidden` / `visible`
- `open` / `closed`
- `offscreen` / `onscreen`
- `active` / `inactive`

---

## Exercice 3 : Interactions (Hover & Tap)

> [!TIPS]
> **Objectif** : Rendre l'interface réactive au curseur et au clic.
> Consignes :
>
> - Créer un bouton interactif utilisant `whileHover` et `whileTap`.
> - Configurer une transition de type `spring` avec `stiffness` et `damping`.
> - Utiliser des noms de variants sémantiques (`rest`, `hover`, `tap`).

### Interactions

- `whileHover` : animation au survol
- `whileTap` : animation à l'appui

### Réglages spring

- `stiffness` : tension du ressort (plus haut = plus nerveux)
- `damping` : amortissement (plus bas = plus de rebond)

```tsx
const buttonVariants: Variants = {
  rest: { scale: 1 },
  hover: {
    scale: 1.1,
    backgroundColor: "#d1d5db",
    color: "#000000",
    transition: { type: "spring", damping: 10, stiffness: 600 },
  },
  tap: { scale: 0.9 },
};

<motion.button
  variants={buttonVariants}
  initial="rest"
  whileHover="hover"
  whileTap="tap"
>
  Subscribe
</motion.button>;
```

---

## Exercice 4 : Le Drag (Glisser-Déposer)

> [!TIPS]
> **Objectif** : Manipuler des éléments à la souris.
> Consignes :
>
> - Rendre un carré déplaçable avec la propriété `drag`.
> - Limiter la zone de mouvement avec `dragConstraints`.
> - Ajouter un feedback visuel pendant le déplacement avec `whileDrag`.

### Propriétés drag

- `drag` : active le glisser-déposer (`true`, `"x"`, `"y"`)
- `dragConstraints` : limites de déplacement
- `dragTransition` : comportement inertie/rebond
- `whileDrag` : style actif pendant le déplacement

```tsx
const boxVariants: Variants = {
  hover: { scale: 1.1 },
  tap: { scale: 0.9 },
  drag: { scale: 1.2, boxShadow: "0px 10px 20px rgba(0,0,0,0.3)" },
};

<motion.div
  variants={boxVariants}
  drag
  dragConstraints={{ top: -125, right: 125, bottom: 125, left: -125 }}
  whileHover="hover"
  whileTap="tap"
  whileDrag="drag"
/>;
```

---

## Exercice 5 : Progression au Scroll

> [!TIPS]
> **Objectif** : Lier une animation au défilement de la page.
> Consignes :
>
> - Utiliser le hook `useScroll` pour récupérer la progression (`scrollYProgress`).
> - Mapper cette progression sur une barre de remplissage.
> - Utiliser `useSpring` pour lisser le mouvement et `offset` pour définir la zone d'activation.

### `useScroll`

Retourne des `MotionValue` de progression (0 -> 1), comme `scrollYProgress`.

- `target` : `ref` de l'élément suivi
- `offset` : règle le début/fin de la progression

Format offset : `"position_cible position_conteneur"`

| Type      | Valeurs possibles        | Description                        |
| :-------- | :----------------------- | :--------------------------------- |
| Mots-clés | `start`, `center`, `end` | Repères relatifs (0%, 50%, 100%).  |
| Nombres   | `0`, `0.5`, `1`          | Progression en pourcentage.        |
| Pixels    | `100px`, `-50px`         | Position fixe par rapport au bord. |

Combinaisons classiques :

- `["start end", "end start"]`
- `["start end", "start start"]`
- `["0 0.5", "1 0.5"]`

### Lissage avec `useSpring`

```tsx
const { scrollYProgress } = useScroll({ target: ref });
const smoothProgress = useSpring(scrollYProgress, {
  stiffness: 100,
  damping: 30,
});

<motion.div style={{ scaleY: smoothProgress }} />;
```

Pour les `MotionValue` issues du scroll, privilégier `style` plutôt que `animate` pour éviter les re-renders React.

### Débogage

- Repères visuels en CSS (`position: fixed`)
- `useMotionValueEvent` pour logger la progression

```tsx
import { useMotionValueEvent } from "motion/react";

useMotionValueEvent(scrollYProgress, "change", (latest) => {
  console.log("Progression:", latest);
});
```

---

## Exercice 6 : Animation SVG

> [!TIPS]
> **Objectif** : Animer des tracés vectoriels
> Consignes :
>
> - Utiliser `pathLength` pour faire se dessiner une icône SVG.
> - Définir des transitions spécifiques pour le tracé (`default`) et le remplissage (`fill`).
> - Utiliser `repeatType: "reverse"` pour un effet de va-et-vient.

### Tracé de chemin

- `pathLength: 0` : tracé invisible
- `pathLength: 1` : tracé complet

```tsx
transition: {
  default: { duration: 2, ease: "easeInOut" },
  fill: { duration: 2, ease: "easeIn", delay: 1 }
}
```

```tsx
const svgIconVariants: Variants = {
  hidden: { pathLength: 0, fill: "rgba(255, 255, 255, 0)" },
  visible: {
    pathLength: 1,
    fill: "rgba(255, 255, 255, 1)",
    transition: {
      default: { duration: 2, repeat: Infinity, repeatType: "reverse" },
    },
  },
};

<motion.svg viewBox="0 0 24 24">
  <motion.path
    d="..."
    variants={svgIconVariants}
    initial="hidden"
    animate="visible"
  />
</motion.svg>;
```

---

## Exercice 7 : Scroll Reveal

> [!TIPS]
> **Objectif** : Déclencher des animations à l'entrée dans l'écran.
> Consignes :
>
> - Utiliser `whileInView` au lieu de `animate`.
> - Configurer `viewport` avec `once: false` et `amount` pour contrôler le déclenchement.
> - Utiliser des noms sémantiques `offscreen` et `onscreen`.

### Rappels

- `whileInView` lance l'animation quand l'élément entre dans la zone visible.
- `viewport.once` : joue une seule fois si `true`.
- `viewport.amount` : proportion visible requise.
- `viewport.margin` : marge virtuelle de déclenchement.

```tsx
const variants = {
  offscreen: { opacity: 0, y: 50 },
  onscreen: {
    opacity: 1,
    y: 0,
    transition: { type: "spring", duration: 0.8 },
  },
};

<motion.div
  variants={variants}
  initial="offscreen"
  whileInView="onscreen"
  viewport={{ once: false, amount: 0.5 }}
/>;
```

---

## Exercice 8 : AnimatePresence (Sortie)

> [!TIPS]
> **Objectif** : Animer la disparition d'un élément.
> Consignes :
>
> - Utiliser le composant `<AnimatePresence>`.
> - Définir une propriété `exit` sur l'élément motion.
> - Créer un bouton pour masquer/afficher un élément avec une transition fluide à la fermeture.

### Rappels

Sans `AnimatePresence`, React retire le composant immédiatement. Avec `AnimatePresence`, le composant reste dans le DOM le temps de jouer `exit`.

- `initial` : état d'entrée
- `animate` : état stable
- `exit` : état de sortie
- `mode="popLayout"` : évite les sauts de layout

---

## Exercice 9 : Layout Animations

> [!TIPS]
> **Objectif** : Animer les changements de structure CSS.
> Consignes :
>
> - Utiliser la prop `layout`.
> - Créer un carré qui s'agrandit pour remplir son conteneur au clic.
> - Observer comment Motion gère automatiquement la transition de taille et de `borderRadius`.

### Rappels

La prop `layout` anime automatiquement les changements de taille/position/rayon lorsque la mise en page change.

- Idéal pour accordéons et cartes expansibles
- Basé sur les transforms pour rester fluide

---

## Exercice 10 : Shared Layout (`layoutId`)

> [!TIPS]
> **Objectif** : Créer des transitions fluides entre composants distincts.
> Consignes :
>
> - Créer un système d'onglets (tabs).
> - Utiliser `layoutId` pour faire voyager l'indicateur de sélection (la pilule) d'un bouton à l'autre.

### Rappels

Deux éléments avec le même `layoutId` sont interprétés comme le même objet visuel au travers d'états différents du DOM.

- Très utile pour un indicateur actif (tabs, menus)
- Crée une transition continue entre deux composants distincts

---

## Exercice 11 : Transformations de valeurs (`useTransform`)

> [!TIPS]
> **Objectif** : Créer des effets de parallaxe ou de liaison complexe.
> Consignes :
>
> - Utiliser `useTransform` pour lier la progression du scroll à la rotation et à l'opacité d'une carte.
> - Créer un effet d'entrée dynamique où l'élément se redresse en scrollant.

### Rappels

`useTransform(source, [inMin, inMax], [outMin, outMax])`

- Exemple : mapper le scroll `0..1` vers une rotation `0..360`
- Utilisé avec `style`

---

## Exercice 12 : Listes réordonnables (`Reorder`)

> [!TIPS]
> **Objectif** : Créer une liste drag-and-drop fonctionnelle.
> Consignes :
>
> - Utiliser `<Reorder.Group>` et `<Reorder.Item>`.
> - Lier l'état d'un tableau React à la liste pour permettre la réorganisation réelle des éléments.

### Rappels

`<Reorder.Group>` et `<Reorder.Item>` simplifient le drag-and-drop d'une liste.

- Gestion native du réordonnancement
- Animations automatiques des items qui se déplacent
- Compatible avec `whileDrag` pour le feedback visuel

---

## Exercice 13 : Text Animation (Par caractère)

> [!TIPS]
> **Objectif** : Animer un texte lettre par lettre.
> Consignes :
>
> - Découper un texte en tableau de caractères.
> - Utiliser `staggerChildren` pour un effet de vague ou de révélation séquentielle.
> - Ajouter des transformations 3D (`rotateX`, `perspective`) pour un rendu premium.

### Méthode

Découper le texte en lettres (`Array.from` ou `split`) puis animer chaque lettre comme enfant motion.

Points clés :

- Chaque lettre en `inline-block`
- `staggerChildren` sur le conteneur
- `perspective` possible pour les effets 3D

```tsx
const letters = Array.from("Hello");

<motion.h1 variants={container} initial="hidden" animate="visible">
  {letters.map((l) => (
    <motion.span variants={letter}>{l}</motion.span>
  ))}
</motion.h1>;
```

---

## Bonnes pratiques transversales

### Accessibilité

- Respecter `prefers-reduced-motion` avec le hook `useReducedMotion` pour proposer une animation raccourcie ou désactivée.
- Ne pas communiquer une information uniquement par le mouvement : le texte, la couleur ou l'état doivent rester compréhensibles sans animation.
- Conserver un élément interactif natif (`button`, `a`) plutôt qu'un `div` cliquable.

```tsx
import { motion, useReducedMotion } from "motion/react";

const shouldReduceMotion = useReducedMotion();

<motion.div
  animate={{ x: shouldReduceMotion ? 0 : 100 }}
  transition={{ duration: shouldReduceMotion ? 0 : 0.5 }}
/>;
```

### Tactile et performance

- Pour un élément déplaçable sur mobile, prévoir `touch-action: none` sur la zone de drag.
- Préférer `transform` et `opacity`, généralement plus fluides que des changements fréquents de dimensions ou de position CSS.
- Utiliser `style` avec une `MotionValue` pour les valeurs liées au scroll afin d'éviter des re-renders React inutiles.

### Vérification

Pour chaque exercice, vérifier au minimum :

- l'état initial et l'état final ;
- le comportement clavier des contrôles ;
- le comportement avec une réduction des animations activée ;
- l'absence de débordement sur petit écran.
