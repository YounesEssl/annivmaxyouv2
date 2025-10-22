<script lang="ts">
	import { onMount } from 'svelte';
	import { gsap } from 'gsap';
	import { ScrollTrigger } from 'gsap/ScrollTrigger';


	let drinkSection: HTMLElement;
	let titleElement: HTMLElement;
	let announcementElement: HTMLElement;
	let imageElement: HTMLElement;
	let alcoholCardsElement: HTMLElement;
	let dividerElement: HTMLElement;
	let softsElement: HTMLElement;
	let waterNoteElement: HTMLElement;
	let isMobile = $state(false);

	// Alcools avec emojis pour cartes visuelles
	const alcohols = [
		{ emoji: '🍺', text: '60L de bière', color: 'from-amber-500/20 to-yellow-500/20' },
		{ emoji: '🍷', text: 'Vin rouge & blanc', color: 'from-red-500/20 to-purple-500/20' },
		{ emoji: '🥃', text: 'Vodka, rhum, gin, JET 27, Jäger…', color: 'from-blue-500/20 to-cyan-500/20' },
		{ emoji: '🍾', text: 'Champagne', color: 'from-yellow-500/20 to-amber-500/20' }
	];

	// Softs
	const softs = ['Crazy', 'Coca', 'Oasis', 'Orangina', 'Ice Tea', 'Fanta', 'etc…'];

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
			orb1Tween = gsap.to('.drink-orb-1', {
				scale: 1.15,
				opacity: 0.3,
				duration: 5,
				repeat: -1,
				yoyo: true,
				ease: 'sine.inOut'
			});

			orb2Tween = gsap.to('.drink-orb-2', {
				scale: 1.12,
				opacity: 0.25,
				duration: 7,
				delay: 1,
				repeat: -1,
				yoyo: true,
				ease: 'sine.inOut'
			});
		}

		// Titre - animation individuelle
		gsap.fromTo(
			titleElement,
			{ opacity: 0, y: 40 },
			{
				opacity: 1,
				y: 0,
				duration: 0.7,
				ease: 'power2.out',
				scrollTrigger: {
					trigger: titleElement,
					start: 'top 70%',
					toggleActions: 'play none none reverse'
				}
			}
		);

		// Annonce - lignes progressives avec ScrollTrigger individuel
		const announcementLines = announcementElement.querySelectorAll('.announcement-line');
		gsap.fromTo(
			announcementLines,
			{ opacity: 0, x: -40 },
			{
				opacity: 1,
				x: 0,
				duration: 0.6,
				stagger: 0.15,
				ease: 'power2.out',
				scrollTrigger: {
					trigger: announcementElement,
					start: 'top 65%',
					toggleActions: 'play none none reverse'
				}
			}
		);

		// Image - ScrollTrigger individuel
		gsap.fromTo(
			imageElement,
			{ opacity: 0, x: 40 },
			{
				opacity: 1,
				x: 0,
				duration: 0.8,
				ease: 'power2.out',
				scrollTrigger: {
					trigger: imageElement,
					start: 'top 65%',
					toggleActions: 'play none none reverse'
				}
			}
		);

		// Cartes alcools - ScrollTrigger individuel
		const alcoholCards = gsap.utils.toArray<HTMLElement>('.alcohol-card');
		gsap.fromTo(
			alcoholCards,
			{ opacity: 0, y: 40 },
			{
				opacity: 1,
				y: 0,
				duration: 0.6,
				stagger: 0.15,
				ease: 'power2.out',
				scrollTrigger: {
					trigger: alcoholCardsElement,
					start: 'top 65%',
					toggleActions: 'play none none reverse'
				}
			}
		);

		// Divider - ScrollTrigger individuel
		gsap.fromTo(
			dividerElement.querySelectorAll('.divider-line'),
			{ scaleX: 0 },
			{
				scaleX: 1,
				duration: 0.8,
				ease: 'power2.inOut',
				scrollTrigger: {
					trigger: dividerElement,
					start: 'top 70%',
					toggleActions: 'play none none reverse'
				}
			}
		);

		gsap.fromTo(
			dividerElement.querySelectorAll('.divider-dot'),
			{ scale: 0, opacity: 0 },
			{
				scale: 1,
				opacity: 1,
				duration: 0.4,
				stagger: 0.1,
				ease: 'back.out(3)',
				scrollTrigger: {
					trigger: dividerElement,
					start: 'top 70%',
					toggleActions: 'play none none reverse'
				}
			}
		);

		// Softs - ScrollTrigger individuel
		const softItems = gsap.utils.toArray<HTMLElement>('.soft-badge');
		gsap.fromTo(
			softItems,
			{ opacity: 0, y: 30 },
			{
				opacity: 1,
				y: 0,
				duration: 0.5,
				stagger: 0.1,
				ease: 'power2.out',
				scrollTrigger: {
					trigger: softsElement,
					start: 'top 65%',
					toggleActions: 'play none none reverse'
				}
			}
		);

		// Note eau - ScrollTrigger individuel
		gsap.fromTo(
			waterNoteElement,
			{ opacity: 0, y: 30 },
			{
				opacity: 1,
				y: 0,
				duration: 1,
				ease: 'power2.out',
				scrollTrigger: {
					trigger: waterNoteElement,
					start: 'top 70%',
					toggleActions: 'play none none reverse'
				}
			}
		);

		// Parallaxe sur les orbes seulement
		gsap.to('.drink-orb-1', {
			scrollTrigger: {
				trigger: drinkSection,
				start: 'top 80%',
				end: 'bottom top',
				scrub: 1
			},
			y: -100,
			ease: 'none'
		});

		gsap.to('.drink-orb-2', {
			scrollTrigger: {
				trigger: drinkSection,
				start: 'top 80%',
				end: 'bottom top',
				scrub: 1.2
			},
			y: 100,
			ease: 'none'
		});

		// Pause animations orbes hors viewport
		if (!isMobile && orb1Tween && orb2Tween) {
			ScrollTrigger.create({
				trigger: drinkSection,
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
	id="drink-section"
	bind:this={drinkSection}
	class="relative min-h-screen pt-20 sm:pt-24 pb-20 sm:pb-24 px-6 sm:px-10"
	style="background: linear-gradient(to bottom,
		rgb(15 23 42) 0%,
		rgb(8 12 30) 50%,
		rgb(15 23 42) 100%
	); overflow-x: clip; overflow-y: visible;"
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
		class="drink-orb-1 absolute left-0 top-20 w-96 h-96 lg:w-[500px] lg:h-[500px] rounded-full bg-blue-500/20 blur-3xl pointer-events-none"
		style="will-change: transform, opacity; z-index: 1;"
	></div>
	<div
		class="drink-orb-2 absolute right-0 bottom-40 w-96 h-96 lg:w-[500px] lg:h-[500px] rounded-full bg-purple-500/20 blur-3xl pointer-events-none"
		style="will-change: transform, opacity; z-index: 1;"
	></div>

	<!-- Contenu -->
	<div class="relative z-10 max-w-7xl mx-auto">
		<!-- Titre principal -->
		<div bind:this={titleElement} class="opacity-0 mb-12 sm:mb-16 text-center">
			<h2
				class="text-6xl sm:text-7xl md:text-8xl lg:text-9xl font-black pb-2"
				style="font-family: var(--font-serif); background: linear-gradient(135deg, rgb(251 191 36) 0%, rgb(245 158 11) 50%, rgb(251 191 36) 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text; line-height: 1.2; text-shadow: 0 0 60px rgba(251, 191, 36, 0.3);"
			>
				À boire !
			</h2>
		</div>

		<!-- Annonce GÉANTE + Image -->
		<div class="grid grid-cols-1 lg:grid-cols-2 gap-10 lg:gap-16 items-center mb-20 sm:mb-24">
			<!-- Annonce -->
			<div bind:this={announcementElement} class="order-2 lg:order-1">
				<div class="relative">
					<!-- Glow -->
					<div class="absolute -inset-8 bg-gradient-to-r from-purple-500/30 via-blue-500/30 to-purple-500/30 rounded-full blur-[80px] opacity-50 animate-pulse-slow"></div>

					<div class="relative text-center lg:text-left space-y-3">
						<div class="announcement-line opacity-0">
							<h3
								class="text-5xl sm:text-6xl md:text-7xl lg:text-8xl xl:text-9xl font-black leading-[0.9]"
								style="font-family: var(--font-serif); background: linear-gradient(135deg, rgb(139 92 246) 0%, rgb(59 130 246) 50%, rgb(139 92 246) 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text; letter-spacing: -0.02em;"
							>
								L'ALCOOL
							</h3>
						</div>

						<div class="announcement-line opacity-0">
							<h3
								class="text-5xl sm:text-6xl md:text-7xl lg:text-8xl xl:text-9xl font-black leading-[0.9]"
								style="font-family: var(--font-serif); background: linear-gradient(135deg, rgb(99 102 241) 0%, rgb(139 92 246) 50%, rgb(99 102 241) 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text; letter-spacing: -0.02em;"
							>
								SERA
							</h3>
						</div>

						<div class="announcement-line opacity-0">
							<h3
								class="text-5xl sm:text-6xl md:text-7xl lg:text-8xl xl:text-9xl font-black leading-[0.9]"
								style="font-family: var(--font-serif); background: linear-gradient(135deg, rgb(59 130 246) 0%, rgb(147 197 253) 50%, rgb(59 130 246) 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text; letter-spacing: -0.02em;"
							>
								EN ILLIMITÉ
							</h3>
						</div>

						<div class="announcement-line opacity-0 pt-4">
							<p
								class="text-3xl sm:text-4xl md:text-5xl lg:text-6xl font-bold"
								style="font-family: var(--font-serif); background: linear-gradient(135deg, rgb(167 139 250) 0%, rgb(196 181 253) 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text;"
							>
								TOUT LE WEEK-END !
							</p>
						</div>
					</div>
				</div>
			</div>

			<!-- Image -->
			<div bind:this={imageElement} class="opacity-0 order-1 lg:order-2">
				<div class="relative">
					<!-- Glow -->
					<div class="absolute -inset-2 bg-gradient-to-r from-purple-500/20 via-blue-500/20 to-purple-500/20 rounded-3xl blur-2xl opacity-70"></div>

					<div class="relative">
						<img
							src="/alcool.png"
							alt="Boissons"
							class="w-full aspect-[4/3] object-cover rounded-3xl border-2 border-white/20 shadow-2xl"
							loading="lazy"
						/>
					</div>
				</div>
			</div>
		</div>

		<!-- Cartes alcools -->
		<div bind:this={alcoholCardsElement} class="mb-16 sm:mb-20">
			<div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-5 lg:gap-6">
				{#each alcohols as alcohol}
					<div class="alcohol-card opacity-0">
						<div class="p-6 sm:p-8 rounded-3xl border border-white/10 bg-gradient-to-br {alcohol.color} backdrop-blur-sm">
							<div class="flex flex-col items-center text-center gap-4">
								<!-- Emoji -->
								<div class="text-6xl sm:text-7xl">
									{alcohol.emoji}
								</div>

								<!-- Texte -->
								<p class="text-lg sm:text-xl text-white/90 font-light leading-relaxed">
									{alcohol.text}
								</p>
							</div>
						</div>
					</div>
				{/each}
			</div>
		</div>

		<!-- Divider animé -->
		<div bind:this={dividerElement} class="mb-16 sm:mb-20">
			<div class="flex items-center justify-center gap-6">
				<div class="divider-line h-px w-full max-w-xs bg-gradient-to-r from-transparent via-purple-500/60 to-purple-500/60" style="transform-origin: left;"></div>

				<div class="flex gap-3">
					<div class="divider-dot w-3 h-3 rounded-full bg-gradient-to-r from-purple-500 to-blue-500"></div>
					<div class="divider-dot w-3 h-3 rounded-full bg-gradient-to-r from-blue-500 to-purple-500"></div>
					<div class="divider-dot w-3 h-3 rounded-full bg-gradient-to-r from-purple-500 to-blue-500"></div>
				</div>

				<div class="divider-line h-px w-full max-w-xs bg-gradient-to-r from-purple-500/60 to-transparent" style="transform-origin: right;"></div>
			</div>
		</div>

		<!-- Section Softs -->
		<div bind:this={softsElement} class="mb-12 sm:mb-16 text-center">
			<h3
				class="text-5xl sm:text-6xl md:text-7xl font-black mb-8 pb-2"
				style="font-family: var(--font-serif); background: linear-gradient(135deg, rgb(134 239 172) 0%, rgb(74 222 128) 50%, rgb(134 239 172) 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text; line-height: 1.2;"
			>
				Softs
			</h3>

			<!-- Softs en badges flottants -->
			<div class="flex flex-wrap justify-center items-center gap-4 sm:gap-6">
				{#each softs as soft}
					<div class="soft-badge opacity-0">
						{#if soft === 'etc…'}
							<span class="inline-block px-6 py-3 text-xl sm:text-2xl md:text-3xl font-light text-white/40">
								{soft}
							</span>
						{:else}
							<span
								class="inline-block px-6 py-3 rounded-full bg-gradient-to-r from-green-500/20 to-emerald-500/20 border border-green-500/40 text-xl sm:text-2xl md:text-3xl font-light text-white/90 transition-all duration-300"
								style="background-clip: padding-box; text-shadow: 0 0 20px rgba(134, 239, 172, 0.4);"
							>
								{soft}
							</span>
						{/if}
					</div>
				{/each}
			</div>
		</div>

		<!-- Note eau humoristique -->
		<div bind:this={waterNoteElement} class="opacity-0 text-center max-w-2xl mx-auto">
			<div class="p-6 rounded-2xl bg-white/5 border border-white/10 backdrop-blur-sm">
				<p class="text-lg sm:text-xl text-white/70 font-light italic mb-2">
					Pour ce qui est de l'eau, on n'en a pas prévu
				</p>
				<p class="text-base sm:text-lg text-white/50 font-light">
					(après il y a le robinet)
				</p>
			</div>
		</div>
	</div>

	<!-- Dégradé de transition -->
	<div
		class="absolute bottom-0 left-0 right-0 h-32 pointer-events-none z-20"
		style="background: linear-gradient(to top,
			rgb(15 23 42) 0%,
			rgb(15 23 42 / 0.5) 40%,
			transparent 100%
		);"
	></div>
</section>

<style>
	.drink-orb-1,
	.drink-orb-2 {
		transform: translate3d(0, 0, 0);
	}

	.alcohol-card,
	.soft-badge,
	.announcement-line {
		will-change: opacity, transform;
	}

	@keyframes pulse-slow {
		0%,
		100% {
			opacity: 0.6;
		}
		50% {
			opacity: 1;
		}
	}

	.animate-pulse-slow {
		animation: pulse-slow 4s ease-in-out infinite;
	}
</style>
