# Hero Section avec GSAP et SvelteKit

Une Hero Section moderne et animée avec effet typewriter, particules flottantes et animations GSAP.

## Stack Technique

- **SvelteKit** - Framework Svelte moderne
- **GSAP** - Animations fluides 60 FPS
- **ScrollTrigger** - Plugin GSAP pour animations au scroll
- **TailwindCSS** - Styling utilitaire
- **TypeScript** - Type safety

## Caractéristiques

### Design
- Plein écran (h-screen)
- Background avec gradient de couleurs (slate-950 → purple-950 → slate-900)
- 2 orbes lumineux animés en arrière-plan avec effet pulse
- 20 particules flottantes (desktop uniquement)
- Dégradé de transition vers la section suivante

### Animations
- **Date** : Fade-in-down au chargement
- **Typewriter** : Animation intelligente avec effacement partiel
  - Garde le préfixe commun entre les mots (ex: "un" reste pour unique → inoubliable)
  - Curseur clignotant
- **Bouton scroll** : Apparaît après l'animation typewriter avec bounce sur la flèche

### Responsive
- **Mobile** (< 768px) :
  - Pas de particules flottantes
  - Animations infinies désactivées (orbes statiques, pas de bounce)
  - Blur réduit (blur-xl au lieu de blur-3xl)
  - Texte plus petit
  - Animation typewriter plus rapide

### Performance
- Animations optimisées GPU avec `will-change` et `transform`
- Détection mobile pour désactiver les animations lourdes
- Utilisation de GSAP pour animations fluides 60 FPS

## Démarrage rapide

```bash
# Lancer le serveur de développement
npm run dev

# Ouvrir dans le navigateur
npm run dev -- --open

# Build pour production
npm run build

# Prévisualiser le build de production
npm run preview
```

## Structure

```
src/
├── lib/
│   └── components/
│       └── HeroSection.svelte  # Composant principal de la hero section
├── routes/
│   ├── +layout.svelte          # Layout principal avec import CSS
│   └── +page.svelte            # Page d'accueil
├── app.css                     # Styles globaux avec Tailwind
└── ...
```

## Personnalisation

### Modifier les mots du typewriter

Dans `HeroSection.svelte`, modifiez le tableau `words` :

```typescript
const words = ["unique...", "inoubliable...", "immanquable..."];
```

### Ajuster la vitesse de l'animation

Modifiez les variables `typeSpeed` et `eraseSpeed` dans le composant :

```typescript
const typeSpeed = isMobile ? 50 : 70;  // ms par caractère
const eraseSpeed = isMobile ? 40 : 60; // ms pour l'effacement
```

### Changer les couleurs des orbes

Modifiez les classes Tailwind des orbes :

```html
<div class="orb-1 ... bg-purple-500/20 ..."></div>
<div class="orb-2 ... bg-blue-500/20 ..."></div>
```

## Performance

Toutes les animations sont optimisées pour GPU :
- Utilisation de `transform` au lieu de propriétés layout
- `will-change` pour prévenir le navigateur
- Désactivation des animations lourdes sur mobile
- Utilisation de GSAP pour des animations fluides
