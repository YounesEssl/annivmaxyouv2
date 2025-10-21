<script lang="ts">
	import '../app.css';
	import favicon from '$lib/assets/favicon.svg';
	import { onMount } from 'svelte';
	import { afterNavigate } from '$app/navigation';
	import FloatingCTA from '$lib/components/FloatingCTA.svelte';

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
