<script lang="ts">
	import '../app.css';
	import favicon from '$lib/assets/favicon.png';
	import { onMount } from 'svelte';
	import { afterNavigate } from '$app/navigation';
	import FloatingCTA from '$lib/components/FloatingCTA.svelte';
	import Lenis from 'lenis';
	import { gsap } from 'gsap';
	import { ScrollTrigger } from 'gsap/ScrollTrigger';

	let { children } = $props();

	// Reset scroll on navigation (including page refresh)
	afterNavigate(() => {
		window.scrollTo(0, 0);
	});

	onMount(() => {
		// Ensure scroll is at top on initial mount
		window.scrollTo(0, 0);

		// Also handle browser refresh
		if ('scrollRestoration' in history) {
			history.scrollRestoration = 'manual';
		}

		// Initialize Lenis smooth scroll
		const lenis = new Lenis({
			duration: 1.2,
			easing: (t) => Math.min(1, 1.001 - Math.pow(2, -10 * t)),
			orientation: 'vertical',
			smoothWheel: true,
			wheelMultiplier: 1,
			touchMultiplier: 2
		});

		// Connect Lenis with GSAP ScrollTrigger
		lenis.on('scroll', ScrollTrigger.update);

		gsap.ticker.add((time) => {
			lenis.raf(time * 1000);
		});

		gsap.ticker.lagSmoothing(0);

		return () => {
			lenis.destroy();
		};
	});
</script>

<svelte:head>
	<link rel="icon" href={favicon} />
	<link rel="preconnect" href="https://fonts.googleapis.com" />
	<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin="anonymous" />
	<link
		href="https://fonts.googleapis.com/css2?family=Playfair+Display:wght@400;700;900&display=swap"
		rel="stylesheet"
	/>
</svelte:head>

{@render children?.()}

<FloatingCTA />
