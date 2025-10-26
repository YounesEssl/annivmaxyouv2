<script lang="ts">
	import { onMount } from 'svelte';
	import { gsap } from 'gsap';
	import { ScrollTrigger } from 'gsap/ScrollTrigger';

	let activitiesSection: HTMLElement;
	let titleElement: HTMLElement;
	let activitiesGrid: HTMLElement;
	let finalMessageElement: HTMLElement;
	let isMobile = $state(false);

	// Activités avec emojis et couleurs simples
	const activities = [
		{ emoji: '🏊', text: 'Piscine', color: '#06b6d4' },
		{ emoji: '⚽', text: 'Foot', color: '#10b981' },
		{ emoji: '🏐', text: 'Volley', color: '#f97316' },
		{ emoji: '🏀', text: 'Basket', color: '#ef4444' },
		{ emoji: '🎯', text: 'Pétanque', color: '#64748b' },
		{ emoji: '🏓', text: 'Ping-pong', color: '#fbbf24' },
		{ emoji: '🕹️', text: 'Bornes d\'arcade', color: '#a855f7' },
		{ emoji: '🎲', text: 'Jeux de société', color: '#6366f1' },
		{ emoji: '⚽', text: 'Babyfoot', color: '#14b8a6' }
	];

	onMount(() => {
		gsap.registerPlugin(ScrollTrigger);

		const mediaQuery = window.matchMedia('(max-width: 767px)');
		isMobile = mediaQuery.matches;

		const handleResize = () => {
			isMobile = mediaQuery.matches;
		};
		mediaQuery.addEventListener('change', handleResize);

		// Animation des orbes (desktop uniquement)
		let orb1Tween: gsap.core.Tween | null = null;
		let orb2Tween: gsap.core.Tween | null = null;

		if (!isMobile) {
			orb1Tween = gsap.to('.activities-orb-1', {
				scale: 1.1,
				opacity: 0.25,
				duration: 6,
				repeat: -1,
				yoyo: true,
				ease: 'sine.inOut'
			});

			orb2Tween = gsap.to('.activities-orb-2', {
				scale: 1.1,
				opacity: 0.2,
				duration: 7,
				delay: 1,
				repeat: -1,
				yoyo: true,
				ease: 'sine.inOut'
			});
		}

		// Titre - animation simple fade-in
		gsap.fromTo(
			titleElement,
			{ opacity: 0, y: 40 },
			{
				opacity: 1,
				y: 0,
				duration: 0.8,
				ease: 'power2.out',
				scrollTrigger: {
					trigger: titleElement,
					start: 'top 70%',
					toggleActions: 'play none none reverse'
				}
			}
		);

		// Activités - animation simple en stagger
		const activityItems = gsap.utils.toArray<HTMLElement>('.activity-item');
		gsap.fromTo(
			activityItems,
			{ opacity: 0, y: 30 },
			{
				opacity: 1,
				y: 0,
				duration: 0.6,
				stagger: 0.1,
				ease: 'power2.out',
				scrollTrigger: {
					trigger: activitiesGrid,
					start: 'top 65%',
					toggleActions: 'play none none reverse'
				}
			}
		);

		// Message final - animation simple
		gsap.fromTo(
			finalMessageElement,
			{ opacity: 0, y: 30 },
			{
				opacity: 1,
				y: 0,
				duration: 0.8,
				ease: 'power2.out',
				scrollTrigger: {
					trigger: finalMessageElement,
					start: 'top 70%',
					toggleActions: 'play none none reverse'
				}
			}
		);

		// Pause animations orbes hors viewport
		if (!isMobile && orb1Tween && orb2Tween) {
			ScrollTrigger.create({
				trigger: activitiesSection,
				start: 'top 80%',
				end: 'bottom top',
				onEnter: () => {
					orb1Tween?.play();
					orb2Tween?.play();
				},
				onLeave: () => {
					orb1Tween?.pause();
					orb2Tween?.pause();
				},
				onEnterBack: () => {
					orb1Tween?.play();
					orb2Tween?.play();
				},
				onLeaveBack: () => {
					orb1Tween?.pause();
					orb2Tween?.pause();
				}
			});
		}

		return () => {
			mediaQuery.removeEventListener('change', handleResize);
			ScrollTrigger.getAll().forEach((trigger) => trigger.kill());
			orb1Tween?.kill();
			orb2Tween?.kill();
		};
	});
</script>

<section
	id="activities-section"
	bind:this={activitiesSection}
	class="relative min-h-screen pt-20 sm:pt-24 pb-20 sm:pb-24 px-6 sm:px-10"
	style="background: linear-gradient(to bottom, rgb(15 23 42) 0%, rgb(10 15 35) 50%, rgb(15 23 42) 100%);"
>
	<!-- Dégradé de transition depuis section précédente -->
	<div
		class="absolute top-0 left-0 right-0 h-48 pointer-events-none z-20"
		style="background: linear-gradient(to bottom,
			rgb(15 23 42) 0%,
			rgb(15 23 42 / 0.8) 30%,
			rgb(15 23 42 / 0.4) 60%,
			transparent 100%
		);"
	></div>

	<!-- Orbes lumineux -->
	<div
		class="activities-orb-1 absolute left-0 top-40 w-96 h-96 lg:w-[500px] lg:h-[500px] rounded-full bg-purple-500/20 blur-3xl pointer-events-none"
		style="will-change: transform, opacity; z-index: 1;"
	></div>
	<div
		class="activities-orb-2 absolute right-0 bottom-40 w-96 h-96 lg:w-[500px] lg:h-[500px] rounded-full bg-blue-500/20 blur-3xl pointer-events-none"
		style="will-change: transform, opacity; z-index: 1;"
	></div>

	<!-- Contenu -->
	<div class="relative z-10 max-w-7xl mx-auto">
		<!-- Titre principal -->
		<div bind:this={titleElement} class="opacity-0 mb-16 sm:mb-20 text-center">
			<h2
				class="text-5xl sm:text-6xl md:text-7xl lg:text-8xl font-black pb-2"
				style="font-family: var(--font-serif); background: linear-gradient(135deg, rgb(168 85 247) 0%, rgb(59 130 246) 50%, rgb(139 92 246) 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text; line-height: 1.2;"
			>
				Ce qu'il y a à faire…
			</h2>
		</div>

		<!-- Grille d'activités -->
		<div bind:this={activitiesGrid} class="mb-16 sm:mb-20">
			<div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-6 lg:gap-8">
				{#each activities as activity}
					<div class="activity-item opacity-0">
						<div class="group h-full p-8 rounded-3xl border-2 border-white/10 backdrop-blur-sm transition-all duration-300 hover:scale-105 hover:border-white/30" style="background: linear-gradient(135deg, {activity.color}15 0%, {activity.color}05 100%);">
							<div class="flex flex-col items-center text-center gap-4">
								<!-- Emoji -->
								<div class="text-7xl sm:text-8xl transition-transform duration-300 group-hover:scale-110" style="filter: drop-shadow(0 0 20px {activity.color}80);">
									{activity.emoji}
								</div>

								<!-- Texte -->
								<p class="text-2xl sm:text-3xl font-bold text-white/90 transition-colors duration-300 group-hover:text-white">
									{activity.text}
								</p>

								<!-- Ligne colorée -->
								<div class="w-16 h-1 rounded-full transition-all duration-300 group-hover:w-24" style="background: {activity.color};"></div>
							</div>
						</div>
					</div>
				{/each}
			</div>
		</div>

		<!-- Message final -->
		<div bind:this={finalMessageElement} class="opacity-0 text-center max-w-4xl mx-auto">
			<div class="relative p-8 sm:p-10 rounded-3xl bg-gradient-to-br from-purple-500/10 via-blue-500/10 to-purple-500/10 border border-white/20 backdrop-blur-sm">
				<p
					class="text-3xl sm:text-4xl md:text-5xl font-bold leading-relaxed"
					style="font-family: var(--font-serif); background: linear-gradient(135deg, rgb(196 181 253) 0%, rgb(147 197 253) 50%, rgb(196 181 253) 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text;"
				>
					et encore plein de surprises pour que personne ne s'ennuie !
				</p>
			</div>
		</div>
	</div>

	<!-- Dégradé de transition vers section suivante -->
	<div
		class="absolute bottom-0 left-0 right-0 h-48 pointer-events-none z-20"
		style="background: linear-gradient(to top,
			rgb(15 23 42) 0%,
			rgb(15 23 42 / 0.8) 30%,
			rgb(15 23 42 / 0.4) 60%,
			transparent 100%
		);"
	></div>
</section>

<style>
	/* Optimisations GPU */
	.activities-orb-1,
	.activities-orb-2 {
		transform: translate3d(0, 0, 0);
	}

	.activity-item {
		will-change: opacity, transform;
		transform: translateZ(0);
	}
</style>
