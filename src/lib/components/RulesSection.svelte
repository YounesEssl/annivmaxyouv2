<script lang="ts">
	import { onMount } from 'svelte';
	import { gsap } from 'gsap';
	import { ScrollTrigger } from 'gsap/ScrollTrigger';

	let rulesSection: HTMLElement;
	let titleElement: HTMLElement;
	let subtitleElement: HTMLElement;
	let introElement: HTMLElement;
	let rulesContainer: HTMLElement;
	let isMobile = $state(false);

	// Les 4 règles avec couleurs vives
	const rules = [
		{
			number: 1,
			title: 'Tu casses → tu payes',
			description: 'Ce serait con que vous ayez à repayer une porte ou un baby-foot, alors on fait gaffe. Et en cas de casse, prévenez tout de suite Maxence ou Younes.',
			color: '#ef4444', // red
			bgGradient: 'from-red-500/20 to-orange-500/10'
		},
		{
			number: 2,
			title: 'On est là pour faire la fête, pas pour repeindre les murs.',
			description: 'On évite de vomir partout, c\'est mieux pour tout le monde',
			color: '#10b981', // green
			bgGradient: 'from-emerald-500/20 to-green-500/10'
		},
		{
			number: 3,
			title: 'Mélangez-vous !',
			description: 'Tout le monde ne se connaît pas → soyez ouverts et mélangez-vous. On veut que de la bonne humeur et de la bonne ambiance.',
			color: '#06b6d4', // cyan
			bgGradient: 'from-cyan-500/20 to-blue-500/10'
		},
		{
			number: 4,
			title: 'Amusez-vous (obligatoire)',
			description: 'Pas de place pour les endormis ou les blasés → ce week-end est fait pour kiffer à fond.',
			color: '#a855f7', // purple
			bgGradient: 'from-purple-500/20 to-pink-500/10'
		}
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
			orb1Tween = gsap.to('.rules-orb-1', {
				scale: 1.1,
				opacity: 0.25,
				duration: 6,
				repeat: -1,
				yoyo: true,
				ease: 'sine.inOut'
			});

			orb2Tween = gsap.to('.rules-orb-2', {
				scale: 1.1,
				opacity: 0.2,
				duration: 7,
				delay: 1,
				repeat: -1,
				yoyo: true,
				ease: 'sine.inOut'
			});
		}

		// Titre principal
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

		// Sous-titre
		gsap.fromTo(
			subtitleElement,
			{ opacity: 0, y: 30 },
			{
				opacity: 1,
				y: 0,
				duration: 0.7,
				ease: 'power2.out',
				scrollTrigger: {
					trigger: subtitleElement,
					start: 'top 70%',
					toggleActions: 'play none none reverse'
				}
			}
		);

		// Introduction
		gsap.fromTo(
			introElement,
			{ opacity: 0, y: 30 },
			{
				opacity: 1,
				y: 0,
				duration: 0.7,
				ease: 'power2.out',
				scrollTrigger: {
					trigger: introElement,
					start: 'top 70%',
					toggleActions: 'play none none reverse'
				}
			}
		);

		// Règles en cascade
		const ruleItems = gsap.utils.toArray<HTMLElement>('.rule-item');
		gsap.fromTo(
			ruleItems,
			{ opacity: 0, x: -40 },
			{
				opacity: 1,
				x: 0,
				duration: 0.7,
				stagger: 0.15,
				ease: 'power2.out',
				scrollTrigger: {
					trigger: rulesContainer,
					start: 'top 65%',
					toggleActions: 'play none none reverse'
				}
			}
		);

		// Pause animations orbes hors viewport
		if (!isMobile && orb1Tween && orb2Tween) {
			ScrollTrigger.create({
				trigger: rulesSection,
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
	id="rules-section"
	bind:this={rulesSection}
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
		class="rules-orb-1 absolute left-0 top-40 w-96 h-96 lg:w-[500px] lg:h-[500px] rounded-full bg-purple-500/20 blur-3xl pointer-events-none"
		style="will-change: transform, opacity; z-index: 1;"
	></div>
	<div
		class="rules-orb-2 absolute right-0 bottom-40 w-96 h-96 lg:w-[500px] lg:h-[500px] rounded-full bg-blue-500/20 blur-3xl pointer-events-none"
		style="will-change: transform, opacity; z-index: 1;"
	></div>

	<!-- Contenu -->
	<div class="relative z-10 max-w-5xl mx-auto">
		<!-- Titres -->
		<div class="mb-12 sm:mb-16 text-center">
			<!-- Titre principal -->
			<div bind:this={titleElement} class="opacity-0 mb-3">
				<h2
					class="text-5xl sm:text-6xl md:text-7xl lg:text-8xl font-black"
					style="font-family: var(--font-serif); background: linear-gradient(135deg, rgb(251 191 36) 0%, rgb(245 158 11) 50%, rgb(251 191 36) 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text; line-height: 1.1;"
				>
					Les rappels logiques
				</h2>
			</div>

			<!-- Sous-titre -->
			<div bind:this={subtitleElement} class="opacity-0">
				<p
					class="text-3xl sm:text-4xl md:text-5xl font-bold text-white/60"
					style="font-family: var(--font-serif);"
				>
					(mais importants)
				</p>
			</div>
		</div>

		<!-- Introduction -->
		<div bind:this={introElement} class="opacity-0 mb-12 sm:mb-16 text-center max-w-3xl mx-auto space-y-3">
			<p class="text-xl sm:text-2xl text-white/80 font-light leading-relaxed">
				On part du principe que tout le monde est grand et responsable de soi.
			</p>
			<p class="text-xl sm:text-2xl font-semibold" style="background: linear-gradient(135deg, rgb(251 191 36) 0%, rgb(245 158 11) 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text;">
				Donc, quelques règles simples :
			</p>
		</div>

		<!-- Liste des règles -->
		<div bind:this={rulesContainer} class="space-y-6 sm:space-y-8">
			{#each rules as rule}
				<div class="rule-item opacity-0 relative group">
					<!-- Numéro badge (en dehors pour rester visible) -->
					<div class="absolute -left-4 sm:-left-6 top-6 sm:top-8 w-16 h-16 sm:w-20 sm:h-20 rounded-2xl flex items-center justify-center font-black text-3xl sm:text-4xl text-white shadow-2xl transition-transform duration-300 group-hover:scale-110 group-hover:rotate-3 z-10" style="background: linear-gradient(135deg, {rule.color} 0%, {rule.color}dd 100%);">
						{rule.number}
					</div>

					<div class="relative overflow-hidden p-6 sm:p-8 rounded-3xl border-2 border-white/10 backdrop-blur-sm bg-gradient-to-br {rule.bgGradient} transition-all duration-300 group-hover:scale-[1.02] group-hover:border-white/30" style="box-shadow: 0 10px 40px rgba(0, 0, 0, 0.3);">
						<!-- Contenu -->
						<div class="ml-14 sm:ml-16">
							<!-- Titre de la règle -->
							<h3 class="text-2xl sm:text-3xl md:text-4xl font-black text-white mb-4 leading-tight" style="font-family: var(--font-serif);">
								{rule.title}
							</h3>

							<!-- Description -->
							<p class="text-lg sm:text-xl text-white/80 font-light leading-relaxed">
								{rule.description}
							</p>
						</div>

						<!-- Ligne décorative bottom -->
						<div class="absolute bottom-0 left-0 right-0 h-1 transition-all duration-300 group-hover:h-2" style="background: {rule.color};"></div>
					</div>
				</div>
			{/each}
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
	.rules-orb-1,
	.rules-orb-2 {
		transform: translate3d(0, 0, 0);
	}

	.rule-item {
		will-change: opacity, transform;
		transform: translateZ(0);
	}
</style>
