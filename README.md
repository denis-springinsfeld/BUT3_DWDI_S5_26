# Sujet de TD : Prise en main de React + StyleX

**Documentation de référence :** <https://stylexjs.com/docs/learn/>

> **Versions :** StyleX est en version 0.x et son API évolue vite. Ce sujet a été vérifié avec `@stylexjs/stylex`, `@stylexjs/unplugin` et `@stylexjs/eslint-plugin` en **0.19.1**. Les commandes ci-dessous épinglent ces versions afin de rendre le résultat reproductible. En cas de comportement différent, vérifiez votre installation avec `npm ls @stylexjs/stylex @stylexjs/unplugin @stylexjs/eslint-plugin`.

# Partie A : Installation

## Exercice A1 : Installer et configurer StyleX avec Vite

**Concept :** StyleX est un **compilateur** : un plugin Vite lit vos styles au build et génère un CSS statique. Il faut donc **deux paquets** : le _runtime_ `@stylexjs/stylex` (utilisé dans votre code) et le _plugin_ `@stylexjs/unplugin` (utilisé par Vite).

### Consignes

1. Créez un projet React + TypeScript avec Vite, puis installez StyleX :

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm install @stylexjs/stylex
npm install -D @stylexjs/unplugin
```

2. Ajoutez le plugin dans `vite.config.ts` :

```typescript
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import stylex from "@stylexjs/unplugin/vite";

export default defineConfig({
  plugins: [
    // StyleX doit être le premier plugin, sinon le CSS généré est vide
    stylex({
      useCSSLayers: { before: ["reset"] }, // le layer "reset" passe AVANT ceux de StyleX
      runtimeInjection: false, // le CSS est extrait au build, pas injecté à l'exécution
    }),
    react(),
  ],
});
```

3. Vérifiez `tsconfig.node.json`. Si le template contient `module: "nodenext"`, remplacez cette configuration par :

```diff
-    "module": "nodenext",
+    "module": "esnext",
+    "moduleResolution": "bundler",
```

> Certains templates Vite récents utilisent déjà `esnext` et `bundler` : dans ce cas, ne changez rien. Cette adaptation évite un problème de configuration rencontré avec certains templates et versions de TypeScript.

4. Dans `src/App.tsx`, retirez l'import de `./App.css`, puis supprimez `src/App.css`. Remplacez le contenu de `src/index.css` par ce reset :

```css
@layer reset {
  * {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
  }
  body {
    font-family: system-ui, sans-serif;
  }
}
```

Vérifiez que `src/main.tsx` importe toujours `./index.css` : le reset doit être chargé en développement comme en production.

5. Lancez `npm run dev`.

> **Pourquoi `@layer reset` et `before: ["reset"]` ?** Un CSS écrit hors de tout `@layer` écrase toujours un CSS en layer : un reset classique annulerait tous les `margin` et `padding` de StyleX. Avec `@layer reset`, c'est réglé après un build, mais en dev il faut aussi `before: ["reset"]`, qui déclare le layer `reset` avant ceux de StyleX.

### Points de contrôle

- `npx tsc -b` ne signale aucune erreur.
- `npm run dev` démarre sans erreur.

### Documentation

- [Installation avec Vite](https://stylexjs.com/docs/learn/installation/vite/)
- [Options du plugin (unplugin)](https://stylexjs.com/docs/api/configuration/unplugin)

---

## Exercice A2 : ESLint et validation des styles

**Concept :** le compilateur StyleX compile même des styles invalides. Le plugin ESLint les détecte avant la mise en production.

### Consignes

1. Installez le plugin :

```bash
npm install -D @stylexjs/eslint-plugin
```

2. Dans le `eslint.config.js` existant, importez le plugin en haut du fichier, puis ajoutez ces deux entrées à l'objet de configuration qui cible vos fichiers `.ts` / `.tsx` :

```js
import stylex from "@stylexjs/eslint-plugin";

// ... dans l'objet de configuration existant :
plugins: { "@stylexjs": stylex },
rules: { "@stylexjs/valid-styles": "error" },
```

> Si cet objet contient déjà `plugins` ou `rules`, ajoutez simplement ces entrées à l'intérieur.

3. Lancez `npm run lint` : aucune erreur n'est attendue pour l'instant.

---

# Partie B : Création des styles

## Les 5 règles de StyleX à connaître

1. **Les styles sont statiques.** Le compilateur doit pouvoir lire les valeurs sans exécuter votre code. Pas de `stylex.create` dans une fonction ou une condition. Une valeur qui dépend d'une prop se déclare avec une _fonction_ dans `stylex.create` (voir le bonus B5).
2. **Pas de raccourcis CSS ambigus.** `border` est refusé : utilisez `borderWidth`, `borderStyle` et `borderColor`. Pour les espacements, préférez `marginBlock`, `marginInline`, `paddingBlock`, `paddingInline`.
3. **Les conditions s'écrivent dans la valeur** (pseudo-classes, media queries), pas comme clé de premier niveau de l'objet de style.
4. **L'ordre dans `stylex.props()` décide de la priorité** : le dernier gagne.
5. **Les variables et constantes (`defineVars`, `defineConsts`) vivent dans un fichier `.stylex.ts`**, avec des exports nommés uniquement.

---

## Exercice B1 : Premiers styles, le « Hello World »

**Concept :** `stylex.create()` déclare des styles, `stylex.props()` les applique. Le compilateur génère une classe CSS par déclaration (CSS « atomique »).

### Notions : `create` et `props`

```tsx
import * as stylex from "@stylexjs/stylex";

// 1. On déclare les styles : les clés (ici "box") sont libres
const styles = stylex.create({
  box: {
    padding: 16, // un nombre = des pixels ou "1rem"
    //Attention on est en JS donc camelCase, pas kebab-case
    backgroundColor: "#fff",
  },
});

// 2. On les applique : props() renvoie { className, style }
<div {...stylex.props(styles.box)} />;
```

### Notions : une pseudo-classe se place dans la valeur

```tsx
const styles = stylex.create({
  link: {
    color: {
      default: "#0000dd",
      ":hover": "#dd0000",
    },
  },
});
```

### Consignes

1. Créez un composant `HelloWorld` centré dans la page (flexbox, hauteur de la fenêtre), avec un fond clair.
2. Donnez au conteneur un `padding` de 24px.
3. Ajoutez un titre dont la couleur change au survol.
4. Affichez le composant dans `App`.
5. Écrivez volontairement le raccourci `border` dans un de vos styles, lancez `npm run lint`, lisez le message puis corrigez.
6. Lancez `npm run build` et ouvrez le fichier CSS généré dans `dist/assets/`.

### Points de contrôle

- Inspectez le `<h1>` dans les DevTools : vous devez voir des classes courtes du type `x1abc23` (une par propriété CSS).
- Dans l'onglet « Computed » des DevTools, le `padding` du conteneur vaut bien 24px (et non 0). C'est le test du reset de l'exercice A1.
- Dans le CSS généré, retrouvez le bloc `@layer reset`, puis les `@layer priority…`.
- **Question :** que se passe-t-il si vous retirez `@layer reset` du reset (en gardant `useCSSLayers`) ? Pourquoi ?

### Documentation

- [Définir des styles](https://stylexjs.com/docs/learn/styling-ui/defining-styles/)
- [Utiliser des styles](https://stylexjs.com/docs/learn/styling-ui/using-styles/)
- [`stylex.create`](https://stylexjs.com/docs/api/javascript/create)
- [`stylex.props`](https://stylexjs.com/docs/api/javascript/props)

---

## Exercice B2 : Le composant à variantes `Button`

**Concept :** StyleX fusionne les styles de façon **prédictible** : le dernier argument de `stylex.props()` l'emporte en cas de conflit. Les styles conditionnels se choisissent en JavaScript, les styles d'état (`:hover`...) en CSS.

### Rappel : plusieurs groupes de styles et variantes

Exemple avec un composant `Badge` :

```tsx
const base = stylex.create({
  badge: { borderRadius: 4, paddingInline: 8 },
});

const tones = stylex.create({
  info: { backgroundColor: "#add8e6" },
  warn: { backgroundColor: "#ffa500" },
});

interface BadgeProps {
  tone?: keyof typeof tones;
}

export function Badge({ tone = "info" }: BadgeProps) {
  // tones[tone] choisit un style parmi ceux déclarés ci-dessus
  return <span {...stylex.props(base.badge, tones[tone])}>Nouveau</span>;
}
```

`keyof typeof tones` donne le type `"info" | "warn"` sans le réécrire.

### Style conditionnel

```tsx
// Un style est ajouté seulement si la condition est vraie
<div {...stylex.props(base.badge, isActive && styles.active)} />
```

### Plusieurs états sur une même propriété

```tsx
const styles = stylex.create({
  field: {
    opacity: {
      default: 1,
      ":disabled": 0.5,
    },
    transform: {
      default: null, // null = pas de valeur dans l'état normal
      ":active:not(:disabled)": "scale(0.98)",
    },
  },
});
```

### Notions : combiner deux pseudo-classes

Un sélecteur peut en combiner plusieurs. Ici, le survol n'agit que sur un élément **non désactivé** :

```tsx
backgroundColor: {
  default: "#0000dd",
  ":hover:not(:disabled)": "#00008b",
},
```

### Consignes

Créez un composant `Button` qui accepte :

- une `variant` : `primary`, `secondary` ou `danger` ;
- une `size` : `small`, `medium` ou `large` ;
- les props `disabled`, `onClick` et `children`.

Comportements attendus :

| État             | Comportement attendu                                                                                 |
| ---------------- | ---------------------------------------------------------------------------------------------------- |
| Normal           | Couleur de fond selon la variante                                                                    |
| `:hover`         | Couleur de fond plus foncée                                                                          |
| `:active`        | Léger effet de réduction (`scale`), uniquement si le bouton n'est pas désactivé                      |
| `:focus-visible` | Indicateur de focus visible pour la navigation au clavier                                            |
| `:disabled`      | Opacité réduite, curseur `not-allowed`, aucun effet `:active`, aucun changement de couleur au survol |

Contraintes :

- Séparez les styles en 3 groupes : base, variantes, tailles.
- Typez les props en TypeScript (pas de `any`).
- Ajoutez `type="button"` sur le `<button>`, pour éviter l'envoi involontaire d'un formulaire parent.
- Affichez dans `App` toutes les combinaisons variante × taille, plus un bouton désactivé.

### Points de contrôle

- Survolez le bouton désactivé : sa couleur ne doit pas changer.
- Naviguez entre les boutons avec la touche Tab : le bouton ayant le focus doit être clairement identifiable.

### Documentation

- [Définir des styles](https://stylexjs.com/docs/learn/styling-ui/defining-styles/)
- [Utiliser des styles](https://stylexjs.com/docs/learn/styling-ui/using-styles/)
- [`stylex.create`](https://stylexjs.com/docs/api/javascript/create)
- [`stylex.props`](https://stylexjs.com/docs/api/javascript/props)

---

## Exercice B3 : Design tokens et thèmes

**Concept :** StyleX définit des **variables CSS typées** avec `stylex.defineVars`. Un **thème** (`stylex.createTheme`) fournit de nouvelles valeurs pour ces variables : il suffit d'appliquer le thème sur un élément pour que tous ses descendants changent.

### Définir des variables

Dans un fichier `tokens.stylex.ts` :

```ts
import * as stylex from "@stylexjs/stylex";

// Exports nommés uniquement, et uniquement des defineVars (ou defineConsts)
export const palette = stylex.defineVars({
  surface: "#fff",
  text: "#1f2937",
  accent: "teal",
});
```

### Créer un thème (dans un fichier normal)

```ts
import * as stylex from "@stylexjs/stylex";
import { palette } from "./tokens.stylex";

export const night = stylex.createTheme(palette, {
  surface: "#111827",
  text: "#f9fafb",
  accent: "cyan",
});
```

### Utiliser les variables et appliquer un thème

```tsx
import * as stylex from "@stylexjs/stylex";
import { palette } from "./tokens.stylex";
import { night } from "./night";
import { useState } from "react";

const styles = stylex.create({
  panel: {
    backgroundColor: palette.surface,
    color: palette.text,
    transitionProperty: "background-color, color",
    transitionDuration: "200ms",
  },
});

export function App() {
  const [isNight, setIsNight] = useState(false);

  // Le thème appliqué à section est hérité par tous ses descendants.
  return (
    <section {...stylex.props(styles.panel, isNight && night)}>
      <button
        type="button"
        aria-pressed={isNight}
        onClick={() => setIsNight((current) => !current)}
      >
        {isNight ? "Activer le thème clair" : "Activer le thème sombre"}
      </button>
    </section>
  );
}
```

Les valeurs `palette.surface` se compilent en `var(--...)` : elles ne contiennent pas la couleur, mais une référence. Leur nom est hashé en production (ex : `--x14q1ub`) : on ne l'écrit jamais à la main.

### Consignes

1. Créez un fichier de tokens pour les couleurs (fond, texte, marque) et un autre pour les espacements.
2. Créez un thème sombre qui surcharge les couleurs.
3. Utilisez les tokens dans `App`, à la place de valeurs écrites en dur.
4. Ajoutez un bouton qui bascule entre le thème clair et le thème sombre.
5. Ajoutez une transition douce sur les couleurs de fond et de texte lors du changement de thème, sans animer toutes les propriétés.
6. Utilisez au moins un token dans votre `Button` de l'exercice B2 (par exemple la couleur de marque).

### Points de contrôle

- Basculez le thème et observez, dans les DevTools, la classe ajoutée sur l'élément racine : elle redéfinit les variables `--x…`.

### Documentation

- [Définir des variables](https://stylexjs.com/docs/learn/theming/defining-variables)
- [Créer des thèmes](https://stylexjs.com/docs/learn/theming/creating-themes)
- [`stylex.defineVars`](https://stylexjs.com/docs/api/javascript/defineVars)
- [`stylex.createTheme`](https://stylexjs.com/docs/api/javascript/createTheme)

---

## Exercice B4 : Composition et surcharge depuis le parent

**Concept :** passer une `className` à un enfant est risqué, car la priorité dépend de l'ordre des CSS dans le bundle (un `className="mt-4"` peut gagner ou perdre selon l'ordre des imports). StyleX passe plutôt des **styles compilés** en prop, et `stylex.props()` les fusionne avec une priorité garantie.

### Notions : accepter des styles du parent

Exemple avec un composant `Alert` :

```tsx
import type { ReactNode } from "react";
import * as stylex from "@stylexjs/stylex";
import type { StyleXStyles } from "@stylexjs/stylex";

// 1. On déclare les styles du composant
const styles = stylex.create({
  alert: { padding: 12, color: "#8b0000" },
});

interface AlertProps {
  children: ReactNode;
  xstyle?: StyleXStyles; // n'accepte QUE des styles créés par stylex.create
}

export function Alert({ children, xstyle }: AlertProps) {
  // xstyle est en dernier : il gagne en cas de conflit
  return <div {...stylex.props(styles.alert, xstyle)}>{children}</div>;
}
```

### Notions : le parent passe un style

```tsx
const parentStyles = stylex.create({
  wide: { width: "100%" },
});

<Alert xstyle={parentStyles.wide}>Attention</Alert>;
```

### Notions : reprendre les props natives d'un élément

Pour que le composant accepte toutes les props d'un `<div>` (sauf `style` et `className`, qu'on ne veut pas laisser passer) :

```tsx
import type { ComponentProps } from "react";

interface AlertProps extends Omit<
  ComponentProps<"div">,
  "style" | "className"
> {
  xstyle?: StyleXStyles;
}

export function Alert({ xstyle, ...rest }: AlertProps) {
  return <div {...rest} {...stylex.props(styles.alert, xstyle)} />;
}
```

### Rappel : restreindre les propriétés autorisées

`StyleXStyles` accepte un paramètre pour limiter ce que le parent peut changer (**liste blanche**) :

```tsx
xstyle?: StyleXStyles<{ width?: string | number }>
```

Le parent ne peut alors modifier que `width`. Toute autre propriété provoque une erreur TypeScript.

`StyleXStylesWithout` fait l'inverse (**liste noire**) : le parent peut tout modifier sauf ce qui est listé.

```tsx
xstyle?: StyleXStylesWithout<{ position: null; display: null }>
```

Les propriétés interdites s'écrivent avec le type `null` (le type `unknown` provoque une erreur de contrainte).

### Consignes

1. Ajoutez au `Button` une prop `xstyle` qui accepte uniquement des styles StyleX. Placez-la en dernier dans `stylex.props()`.
2. Faites en sorte que le `Button` reprenne toutes les props natives d'un `<button>`, sauf `style` et `className` (indice : `ComponentProps<"button">` et `Omit`).
3. Créez un composant `Card` qui contient un `Button`.
4. Depuis `Card`, forcez le bouton à prendre toute la largeur, avec une marge au-dessus et une taille de police de 20px.
5. Vérifiez que le style du parent l'emporte sur celui de la `size` du bouton. Placez ensuite `xstyle` en premier dans `stylex.props()`, constatez que le parent ne gagne plus, puis remettez-le en dernier.
6. Restreignez `xstyle` avec `StyleXStylesWithout` pour interdire `position` et `display`. Vérifiez que TypeScript refuse un style du parent contenant `position: "absolute"`.
7. **Bonus :** passez à une liste blanche qui n'autorise que `width` et `marginTop`. TypeScript refuse alors le `fontSize` de votre `Card` : constatez l'erreur, puis retirez-le. Vérifiez qu'un style `color` est lui aussi refusé.

### Points de contrôle

- Le bouton occupe toute la largeur de la carte, avec une marge au-dessus, et sa police est à 20px.
- **Question :** pourquoi n'y a-t-il pas de `className` dans les props du `Button` ?

### Documentation

- [Type `StyleXStyles`](https://stylexjs.com/docs/api/types/StyleXStyles)
- [Type `StyleXStylesWithout`](https://stylexjs.com/docs/api/types/StyleXStylesWithout)
- [Utiliser des styles](https://stylexjs.com/docs/learn/styling-ui/using-styles/)

---

## Exercice B5 (bonus) : Pour aller plus loin

Choisissez un ou plusieurs défis. Les extraits servent de point de départ.

**A. Styles dynamiques.** Quand une valeur dépend d'une prop à l'exécution, `stylex.create` accepte une **fonction** (c'est la seule façon de rester « statique » pour le compilateur). Défi : une barre de progression dont la largeur vient d'une prop `progress`.

```tsx
const styles = stylex.create({
  bar: (progress: number) => ({ width: `${progress}%` }),
});

<div {...stylex.props(styles.bar(progress))} />;
```

**B. Constantes et media queries.** Défi : un `padding` horizontal plus petit sur mobile. Créez `breakpoints.stylex.ts`, puis utilisez la constante comme clé conditionnelle.

```ts
import * as stylex from "@stylexjs/stylex";

export const breakpoints = stylex.defineConsts({
  mobile: "@media (max-width: 640px)",
  reduceMotion: "@media (prefers-reduced-motion: reduce)",
});
```

```tsx
card: {
  paddingInline: { default: 24, [breakpoints.mobile]: 12 },
},
```

**C. Animations.** Défi : une icône qui tourne, sans animation pour les personnes qui ont activé « réduire les animations ».

```tsx
const spin = stylex.keyframes({
  from: { transform: "rotate(0deg)" },
  to: { transform: "rotate(360deg)" },
});

const styles = stylex.create({
  icon: {
    animationName: spin,
    animationDuration: {
      default: "1s",
      [breakpoints.reduceMotion]: "0s",
    },
  },
});
```

**D. Réagir au survol du parent, sans JavaScript.** Défi : une icône qui n'apparaît que lorsque la carte est survolée.

```tsx
const marker = stylex.defaultMarker();

const styles = stylex.create({
  card: { borderColor: "gray" },
  icon: {
    opacity: {
      default: 0,
      [stylex.when.ancestor(":hover")]: 1,
    },
  },
});

<div {...stylex.props(styles.card, marker)}>
  <span {...stylex.props(styles.icon)}>★</span>
</div>;
```

**E. Thème automatique.** Défi : une variable qui change selon les préférences du système, sans JavaScript.

```ts
export const autoPalette = stylex.defineVars({
  surface: {
    default: "white",
    "@media (prefers-color-scheme: dark)": "black",
  },
});
```

### Documentation

- [`stylex.when`](https://stylexjs.com/docs/api/javascript/when)
- [`stylex.keyframes`](https://stylexjs.com/docs/api/javascript/keyframes)
- [`stylex.defineConsts`](https://stylexjs.com/docs/api/javascript/defineConsts)

---

## Erreurs fréquentes

| Symptôme                                                   | Cause probable                                                                               |
| ---------------------------------------------------------- | -------------------------------------------------------------------------------------------- |
| Tous les `padding` / `margin` sont à 0                     | Reset CSS hors `@layer` alors que `useCSSLayers` est activé (A1)                             |
| Marges correctes en `build` mais à 0 en dev (ou l'inverse) | Reset dans un `@layer` mais sans `before: ["reset"]` dans `useCSSLayers` (A1)                |
| `npm run build` échoue sur `vite.config.ts`                | Avec certains templates, configuration `"module": "nodenext"` dans `tsconfig.node.json` (A1) |
| Erreur à la compilation sur `defineVars`                   | Fichier non nommé `*.stylex.ts`, ou export autre qu'un export nommé                          |
| Erreur « valeur non statique » dans `stylex.create`        | Valeur calculée à l'exécution : utilisez une fonction (B5-A)                                 |
| Le survol change la couleur d'un bouton désactivé          | `:hover` au lieu de `:hover:not(:disabled)` (B2)                                             |

---

## Vérification

- [ ] `npx tsc -b` ne signale aucune erreur ;
- [ ] `npm run lint` ne signale aucune erreur ;
- [ ] `npm run build` réussit ;
- [ ] les `padding` et `margin` écrits avec StyleX sont bien appliqués, en dev (`npm run dev`) **et** en production (`npm run preview` après un build) ;
- [ ] tous les états du bouton (survol, clic, désactivé) fonctionnent dans le navigateur ;
- [ ] l'indicateur de focus du bouton est visible lors de la navigation au clavier ;
- [ ] le thème sombre fonctionne sans recharger la page ;
- [ ] TypeScript refuse un `xstyle` qui modifie une propriété interdite.
