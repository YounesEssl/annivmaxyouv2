<script lang="ts">
	import { onMount } from 'svelte';
	import { gsap } from 'gsap';

	let buttonElement: HTMLElement;
	let arrowElement: HTMLElement;
	let glowElement: HTMLElement;
	let buttonLinkElement: HTMLElement;
	let isMobile = $state(false);

	onMount(() => {
		const mediaQuery = window.matchMedia('(max-width: 767px)');
		isMobile = mediaQuery.matches;

		const handleResize = () => {
			isMobile = mediaQuery.matches;
		};
		mediaQuery.addEventListener('change', handleResize);

		// Animation d'apparition initiale avec délai
		const appearDelay = isMobile ? 0.8 : 1.5;

		gsap.fromTo(
			buttonElement,
			{ opacity: 0, y: 20 },
			{
				opacity: 1,
				y: 0,
				duration: 0.6,
				delay: appearDelay,
				ease: 'power2.out'
			}
		);

		// Pulse scale très subtil sur le bouton entier (continu, mobile + desktop)
		gsap.to(buttonLinkElement, {
			scale: 1.02,
			duration: 2,
			repeat: -1,
			yoyo: true,
			ease: 'sine.inOut'
		});

		// Glow pulse très subtil (continu, mobile + desktop)
		gsap.to(glowElement, {
			opacity: 0.7,
			scale: 1.08,
			duration: 2,
			repeat: -1,
			yoyo: true,
			ease: 'sine.inOut'
		});

		// Float animation très subtile (mobile + desktop)
		gsap.to(buttonElement, {
			y: -3,
			duration: 3,
			repeat: -1,
			yoyo: true,
			ease: 'power1.inOut'
		});

		// Petite animation subtile de la flèche (desktop uniquement)
		if (!isMobile) {
			gsap.to(arrowElement, {
				x: 2,
				duration: 2,
				repeat: -1,
				yoyo: true,
				ease: 'sine.inOut'
			});
		}

		return () => {
			mediaQuery.removeEventListener('change', handleResize);
		};
	});
</script>

<div
	bind:this={buttonElement}
	class="fixed z-50 opacity-0"
	style="bottom: 2rem; right: 1rem; will-change: transform, opacity;"
	class:md:right-8={true}
>
	<!-- Glow effect pulsant -->
	<div
		bind:this={glowElement}
		class="absolute -inset-3 bg-gradient-to-r from-purple-600/60 to-blue-600/60 rounded-full blur-xl"
		style="will-change: transform, opacity;"
	></div>

	<!-- Bouton -->
	<a
		bind:this={buttonLinkElement}
		href="https://tally.so/r/mZqRao"
		target="_blank"
		rel="noopener noreferrer"
		class="relative group inline-flex items-center gap-2 px-5 py-3 sm:px-6 sm:py-3.5 text-sm sm:text-base font-semibold text-white rounded-full shadow-2xl transition-all duration-300 hover:shadow-2xl active:scale-95"
		style="background: linear-gradient(135deg, rgb(147 51 234) 0%, rgb(37 99 235) 100%); will-change: transform;"
	>
		<span class="relative">S'inscrire</span>

		<!-- Flèche -->
		<svg
			bind:this={arrowElement}
			class="relative w-4 h-4"
			fill="none"
			stroke="currentColor"
			viewBox="0 0 24 24"
			xmlns="http://www.w3.org/2000/svg"
			style="will-change: transform;"
		>
			<path
				stroke-linecap="round"
				stroke-linejoin="round"
				stroke-width="2"
				d="M13 7l5 5m0 0l-5 5m5-5H6"
			/>
		</svg>
	</a>
</div>

<style>
	a:hover {
		transform: scale(1.05);
		background: linear-gradient(135deg, rgb(168 85 247) 0%, rgb(59 130 246) 100%);
	}

	@media (min-width: 768px) {
		.md\:right-8 {
			right: 2rem;
		}
	}
</style>
