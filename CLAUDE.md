# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A modern hero section website with GSAP animations built with SvelteKit. Features include a typewriter effect, floating particles, animated orbs, and scroll-triggered parallax effects.

**Stack**: SvelteKit + GSAP (with ScrollTrigger plugin) + Tailwind CSS 4.0 + TypeScript

## Development Commands

```bash
# Start development server
npm run dev

# Start dev server and open browser
npm run dev -- --open

# Build for production
npm run build

# Preview production build
npm run preview

# Type checking
npm run check

# Type checking in watch mode
npm run check:watch
```

## Architecture

### Component Structure

The app uses a page-based architecture where `+page.svelte` imports and renders multiple section components:

- **HeroSection.svelte**: Full-screen animated hero with typewriter effect, orbs, and particles
- **IntroSection.svelte**: Content section following the hero
- **VenueSection.svelte**: Additional content section

Each section is a standalone component in `src/lib/components/`.

### GSAP Animation System

**Key pattern**: All GSAP animations are initialized in `onMount()` and cleaned up on component destroy.

The HeroSection uses several GSAP animation types:
1. **Infinite animations**: Orb movement and particle floating (desktop only)
2. **Timeline animations**: Sequential typewriter effect with intelligent prefix detection
3. **ScrollTrigger animations**: Parallax effects on scroll for orbs and content fade-out

**Mobile optimization**: Detect viewport width (`< 768px`) to disable heavy animations (orbs movement, particles, arrow bounce) while keeping essential animations.

### Typewriter Effect Implementation

Located in `HeroSection.svelte:116-223`. The typewriter uses a smart algorithm that:
- Finds common prefixes between consecutive words (e.g., "unique" → "inoubliable" keeps "u")
- Only erases/retypes the differing portion for natural transitions
- Uses randomized speeds (`Math.random() * 0.3 + 0.85`) for human-like typing
- Runs once through all words (not in a loop)
- Triggers scroll button appearance after first word completes

### Svelte 5 Runes

This project uses Svelte 5 with modern runes syntax:
- `$state()` for reactive state (see `HeroSection.svelte:12-13`)
- `$props()` for component props (see `+layout.svelte:5`)

### Styling System

**Tailwind CSS 4.0** with custom theme configuration:
- Custom font defined in `app.css` using `@theme` directive: `--font-serif: 'Playfair Display'`
- Global styles include smooth scrolling and hidden overflow-x
- Inline styles used for complex gradients and z-index layering in HeroSection

**Performance optimizations** (in component `<style>` blocks):
- `will-change: transform` on animated elements
- `transform: translateZ(0)` for GPU acceleration

### SvelteKit Configuration

- Uses `adapter-auto` for automatic deployment adapter selection
- Vite preprocessor for TypeScript and style handling
- Tailwind integrated via `@tailwindcss/vite` plugin (see `vite.config.ts:6`)

## Important Implementation Details

### Mobile Detection Pattern

```typescript
let isMobile = $state(false);
onMount(() => {
  isMobile = window.innerWidth < 768;
  const handleResize = () => {
    isMobile = window.innerWidth < 768;
  };
  window.addEventListener('resize', handleResize);
  return () => window.removeEventListener('resize', handleResize);
});
```

### GSAP Cleanup Pattern

Always kill ScrollTrigger instances in the cleanup function:
```typescript
return () => {
  ScrollTrigger.getAll().forEach(trigger => trigger.kill());
};
```

### Full Viewport Height

Uses `100dvh` (dynamic viewport height) instead of `100vh` for proper mobile support: `style="height: 100dvh"` (see `HeroSection.svelte:251`).

### Font Loading

Playfair Display is loaded in `+layout.svelte` head with preconnect optimization and referenced via CSS variable `var(--font-serif)`.
