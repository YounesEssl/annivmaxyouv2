<script lang="ts">
	import { onMount } from 'svelte';
	import { gsap } from 'gsap';
	import { ScrollTrigger } from 'gsap/ScrollTrigger';

	gsap.registerPlugin(ScrollTrigger);

	let priceSection: HTMLElement;
	let titleElement: HTMLElement;
	let introElement: HTMLElement;
	let priceLineElement: HTMLElement;
	let priceHeroElement: HTMLElement;
	let priceSubElement: HTMLElement;
	let priceNoteElement: HTMLElement;
	let subtitleElement: HTMLElement;
	let card1Element: HTMLElement;
	let card2Element: HTMLElement;
	let card3Element: HTMLElement;
	let card4Element: HTMLElement;
	let card5Element: HTMLElement;
	let dividerElement: HTMLElement;
	let finalMessageElement: HTMLElement;
	let alcoholEmojiElement: HTMLElement;
	let isMobile = $state(false);

	onMount(() => {
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
			orb1Tween = gsap.to('.price-orb-1', {
				scale: 1.15,
				opacity: 0.3,
				duration: 6,
				repeat: -1,
				yoyo: true,
				ease: 'sine.inOut'
			});

			orb2Tween = gsap.to('.price-orb-2', {
				scale: 1.12,
				opacity: 0.25,
				duration: 8,
				delay: 2,
				repeat: -1,
				yoyo: true,
				ease: 'sine.inOut'
			});
		}

		// Timeline principale
		const timeline = gsap.timeline({
			scrollTrigger: {
				trigger: priceSection,
				start: 'top 75%',
				end: 'bottom 20%',
				toggleActions: 'play none none reverse'
			}
		});

		// Titre
		timeline.fromTo(
			titleElement,
			{ opacity: 0, y: 50 },
			{ opacity: 1, y: 0, duration: 0.8, ease: 'power2.out' }
		);

		// Introduction
		timeline.fromTo(
			introElement,
			{ opacity: 0, y: 30 },
			{ opacity: 1, y: 0, duration: 0.7, ease: 'power2.out' },
			'-=0.3'
		);

		// Prix - "Le tarif est de"
		timeline.fromTo(
			priceLineElement,
			{ opacity: 0 },
			{ opacity: 1, duration: 0.5, ease: 'power2.out' },
			'-=0.2'
		);

		// Prix - "70€" HERO avec scale dramatique
		timeline.fromTo(
			priceHeroElement,
			{ opacity: 0, scale: 0.5 },
			{ opacity: 1, scale: 1, duration: 1, ease: 'back.out(1.7)' },
			'-=0.1'
		);

		// Prix - "PAR PERSONNE"
		timeline.fromTo(
			priceSubElement,
			{ opacity: 0 },
			{ opacity: 1, duration: 0.5, ease: 'power2.out' },
			'-=0.3'
		);

		// Prix - "pour tout le week-end"
		timeline.fromTo(
			priceNoteElement,
			{ opacity: 0 },
			{ opacity: 1, duration: 0.5, ease: 'power2.out' },
			'-=0.2'
		);

		// Sous-titre "Ce que ça comprend"
		timeline.fromTo(
			subtitleElement,
			{ opacity: 0, y: 20 },
			{ opacity: 1, y: 0, duration: 0.6, ease: 'power2.out' },
			'-=0.1'
		);

		// Carte 1
		timeline.fromTo(
			card1Element,
			{ opacity: 0, scale: 0.8, y: 30 },
			{ opacity: 1, scale: 1, y: 0, duration: 0.6, ease: 'back.out(1.5)' },
			'-=0.2'
		);

		// Carte 2
		timeline.fromTo(
			card2Element,
			{ opacity: 0, scale: 0.8, y: 30 },
			{ opacity: 1, scale: 1, y: 0, duration: 0.6, ease: 'back.out(1.5)' },
			'-=0.4'
		);

		// Carte 3
		timeline.fromTo(
			card3Element,
			{ opacity: 0, scale: 0.8, y: 30 },
			{ opacity: 1, scale: 1, y: 0, duration: 0.6, ease: 'back.out(1.5)' },
			'-=0.4'
		);

		// Carte 4 ALCOOL - Plus d'impact
		timeline.fromTo(
			card4Element,
			{ opacity: 0, scale: 0.7, y: 40 },
			{ opacity: 1, scale: 1, y: 0, duration: 0.8, ease: 'back.out(1.7)' },
			'-=0.4'
		);

		// Carte 5
		timeline.fromTo(
			card5Element,
			{ opacity: 0, scale: 0.8, y: 30 },
			{ opacity: 1, scale: 1, y: 0, duration: 0.6, ease: 'back.out(1.5)' },
			'-=0.5'
		);

		// Divider
		timeline.fromTo(
			dividerElement,
			{ opacity: 0, scaleX: 0 },
			{ opacity: 1, scaleX: 1, duration: 0.6, ease: 'power2.out' },
			'-=0.2'
		);

		// Message final
		timeline.fromTo(
			finalMessageElement,
			{ opacity: 0, y: 30 },
			{ opacity: 1, y: 0, duration: 0.8, ease: 'power2.out' },
			'-=0.2'
		);

		// Rotation continue sur emoji alcool (desktop uniquement)
		if (!isMobile && alcoholEmojiElement) {
			gsap.to(alcoholEmojiElement, {
				rotation: 360,
				duration: 4,
				repeat: -1,
				ease: 'none'
			});
		}

		// Parallaxe sur les orbes
		gsap.to('.price-orb-1', {
			scrollTrigger: {
				trigger: priceSection,
				start: 'top bottom',
				end: 'bottom top',
				scrub: 1
			},
			y: -100,
			ease: 'none'
		});

		gsap.to('.price-orb-2', {
			scrollTrigger: {
				trigger: priceSection,
				start: 'top bottom',
				end: 'bottom top',
				scrub: 1.2
			},
			y: 100,
			ease: 'none'
		});

		// Pause animations orbes hors viewport
		if (!isMobile && orb1Tween && orb2Tween) {
			ScrollTrigger.create({
				trigger: priceSection,
				start: 'top bottom',
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
	id="price-section"
	bind:this={priceSection}
	class="relative min-h-screen pt-20 sm:pt-24 pb-20 sm:pb-24 px-6 sm:px-10"
	style="background: linear-gradient(to bottom,
		rgb(15 23 42) 0%,
		rgb(10 15 35) 50%,
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
		class="price-orb-1 absolute left-0 top-60 w-96 h-96 lg:w-[32rem] lg:h-[32rem] rounded-full bg-purple-500/20 blur-3xl pointer-events-none"
		style="will-change: transform, opacity; z-index: 1;"
	></div>
	<div
		class="price-orb-2 absolute right-0 bottom-60 w-96 h-96 lg:w-[32rem] lg:h-[32rem] rounded-full bg-blue-500/20 blur-3xl pointer-events-none"
		style="will-change: transform, opacity; z-index: 1;"
	></div>

	<!-- Contenu -->
	<div class="relative z-10 max-w-7xl mx-auto">
		<!-- Titre principal -->
		<div bind:this={titleElement} class="opacity-0 mb-12 sm:mb-16 text-center">
			<h2
				class="text-5xl sm:text-6xl md:text-7xl lg:text-8xl font-black pb-2"
				style="font-family: var(--font-serif); background: linear-gradient(135deg, rgb(221 214 254) 0%, rgb(186 230 253) 50%, rgb(221 214 254) 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text; line-height: 1.2;"
			>
				Le prix
			</h2>
		</div>

		<!-- Introduction -->
		<div bind:this={introElement} class="opacity-0 max-w-4xl mx-auto mb-16 sm:mb-20">
			<p class="text-lg sm:text-xl md:text-2xl text-white/80 font-light leading-relaxed text-center">
				Pour que cet événement ait lieu dans ce lieu de rêve, avec tout ce qu'il faut pour manger, boire et faire la fête, on a besoin que chacun mette un petit coup de pouce.
			</p>
		</div>

		<!-- Prix HERO -->
		<div class="mb-20 sm:mb-24 text-center space-y-4">
			<p
				bind:this={priceLineElement}
				class="opacity-0 text-2xl sm:text-3xl md:text-4xl text-white/70 font-light"
			>
				Le tarif est de
			</p>
			<div
				bind:this={priceHeroElement}
				class="opacity-0 relative inline-block"
			>
				<!-- Glow effect -->
				<div
					class="absolute -inset-8 bg-gradient-to-r from-purple-500/30 via-blue-500/30 to-purple-500/30 rounded-full blur-3xl opacity-60"
				></div>
				<h3
					class="relative text-8xl sm:text-9xl md:text-[12rem] lg:text-[14rem] font-black"
					style="font-family: var(--font-serif); background: linear-gradient(135deg, rgb(167 139 250) 0%, rgb(147 197 253) 50%, rgb(167 139 250) 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text; line-height: 1;"
				>
					70€
				</h3>
			</div>
			<p
				bind:this={priceSubElement}
				class="opacity-0 text-3xl sm:text-4xl md:text-5xl text-white font-bold"
			>
				PAR PERSONNE
			</p>
			<p
				bind:this={priceNoteElement}
				class="opacity-0 text-xl sm:text-2xl text-white/60 font-light"
			>
				pour tout le week-end
			</p>
		</div>

		<!-- Sous-titre -->
		<div bind:this={subtitleElement} class="opacity-0 mb-12 text-center">
			<h3
				class="text-3xl sm:text-4xl md:text-5xl font-bold text-white/90"
				style="font-family: var(--font-serif);"
			>
				Ce que ça comprend
			</h3>
		</div>

		<!-- Grid des cartes -->
		<div class="grid grid-cols-1 lg:grid-cols-3 gap-6 lg:gap-8 mb-16 sm:mb-20">
			<!-- Carte 1 - 2 nuits -->
			<div
				bind:this={card1Element}
				class="opacity-0 relative rounded-3xl p-8 sm:p-10 backdrop-blur-sm border border-white/10 overflow-hidden"
				style="background: linear-gradient(135deg, rgb(88 28 135 / 0.4) 0%, rgb(30 58 138 / 0.4) 100%);"
			>
				<!-- Glow -->
				<div
					class="absolute -inset-1 bg-gradient-to-r from-purple-500/30 to-blue-500/30 rounded-3xl blur-xl opacity-60"
				></div>

				<!-- Emojis décoratifs en arrière-plan -->
				<div class="absolute inset-0 overflow-hidden pointer-events-none opacity-25">
					<div class="absolute top-4 left-6 text-5xl">🌙</div>
					<div class="absolute top-12 right-8 text-4xl">⭐</div>
					<div class="absolute bottom-8 left-8 text-6xl">💤</div>
					<div class="absolute bottom-12 right-6 text-4xl">✨</div>
				</div>

				<div class="relative flex flex-col items-center justify-center text-center space-y-4 min-h-[240px]">
					<div class="text-7xl sm:text-8xl">🌙</div>
					<h4 class="text-3xl sm:text-4xl font-bold text-white">2 nuits</h4>
					<p class="text-xl sm:text-2xl text-white/70 font-light">Sur place</p>
				</div>
			</div>

			<!-- Carte 2 - Repas -->
			<div
				bind:this={card2Element}
				class="opacity-0 relative rounded-3xl p-8 sm:p-10 backdrop-blur-sm border border-white/10 overflow-hidden"
				style="background: linear-gradient(135deg, rgb(30 58 138 / 0.4) 0%, rgb(88 28 135 / 0.4) 100%);"
			>
				<!-- Glow -->
				<div
					class="absolute -inset-1 bg-gradient-to-r from-blue-500/30 to-purple-500/30 rounded-3xl blur-xl opacity-60"
				></div>

				<!-- Emojis décoratifs en arrière-plan -->
				<div class="absolute inset-0 overflow-hidden pointer-events-none opacity-25">
					<div class="absolute top-4 left-6 text-5xl">🍽️</div>
					<div class="absolute top-12 right-8 text-4xl">🥐</div>
					<div class="absolute bottom-8 left-8 text-6xl">🍕</div>
					<div class="absolute bottom-12 right-6 text-5xl">☕</div>
				</div>

				<div class="relative flex flex-col items-center justify-center text-center space-y-4 min-h-[240px]">
					<div class="text-7xl sm:text-8xl">🍽️</div>
					<h4 class="text-3xl sm:text-4xl font-bold text-white">Tous les repas</h4>
					<p class="text-xl sm:text-2xl text-white/70 font-light">Matin, midi & soir</p>
				</div>
			</div>

			<!-- Carte 3 - Cadre -->
			<div
				bind:this={card3Element}
				class="opacity-0 relative rounded-3xl p-8 sm:p-10 backdrop-blur-sm border border-white/10 overflow-hidden"
				style="background: linear-gradient(135deg, rgb(88 28 135 / 0.4) 0%, rgb(30 58 138 / 0.4) 100%);"
			>
				<!-- Glow -->
				<div
					class="absolute -inset-1 bg-gradient-to-r from-purple-500/30 to-blue-500/30 rounded-3xl blur-xl opacity-60"
				></div>

				<!-- Emojis décoratifs en arrière-plan -->
				<div class="absolute inset-0 overflow-hidden pointer-events-none opacity-25">
					<div class="absolute top-4 left-6 text-5xl">🏰</div>
					<div class="absolute top-12 right-8 text-4xl">🌳</div>
					<div class="absolute bottom-8 left-8 text-6xl">🎪</div>
					<div class="absolute bottom-12 right-6 text-5xl">🏡</div>
				</div>

				<div class="relative flex flex-col items-center justify-center text-center space-y-4 min-h-[240px]">
					<div class="text-7xl sm:text-8xl">🏰</div>
					<h4 class="text-3xl sm:text-4xl font-bold text-white">Cadre unique</h4>
					<p class="text-xl sm:text-2xl text-white/70 font-light">100% privatisé</p>
				</div>
			</div>

			<!-- Carte 4 - ALCOOL HERO (2 colonnes) -->
			<div
				bind:this={card4Element}
				class="opacity-0 relative rounded-3xl p-10 sm:p-12 backdrop-blur-sm border-2 border-orange-500/30 overflow-hidden lg:col-span-2"
				style="background: linear-gradient(135deg, rgb(180 83 9 / 0.4) 0%, rgb(217 119 6 / 0.4) 50%, rgb(239 68 68 / 0.3) 100%);"
			>
				<!-- Glow intense -->
				<div
					class="absolute -inset-2 bg-gradient-to-r from-orange-500/30 via-amber-500/30 to-red-500/30 rounded-3xl blur-2xl opacity-70"
				></div>

				<!-- Emojis décoratifs en arrière-plan -->
				<div class="absolute inset-0 overflow-hidden pointer-events-none opacity-20">
					<div class="absolute top-4 left-8 text-6xl">🍺</div>
					<div class="absolute top-12 right-12 text-5xl">🥂</div>
					<div class="absolute bottom-8 left-16 text-7xl">🍾</div>
					<div class="absolute bottom-16 right-8 text-6xl">🍻</div>
				</div>

				<div class="relative flex flex-col items-center justify-center text-center space-y-6 min-h-[280px]">
					<div bind:this={alcoholEmojiElement} class="text-9xl sm:text-[10rem]">🍺</div>
					<h4
						class="text-4xl sm:text-5xl md:text-6xl font-black"
						style="background: linear-gradient(135deg, rgb(251 191 36) 0%, rgb(245 158 11) 50%, rgb(251 191 36) 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text;"
					>
						Alcool illimité
					</h4>
					<p class="text-2xl sm:text-3xl text-orange-200/90 font-light">Tout le week-end</p>
				</div>
			</div>

			<!-- Carte 5 - Week-end inoubliable -->
			<div
				bind:this={card5Element}
				class="opacity-0 relative rounded-3xl p-8 sm:p-10 backdrop-blur-sm border border-white/10 overflow-hidden"
				style="background: linear-gradient(135deg, rgb(30 58 138 / 0.4) 0%, rgb(88 28 135 / 0.4) 100%);"
			>
				<!-- Glow -->
				<div
					class="absolute -inset-1 bg-gradient-to-r from-blue-500/30 to-purple-500/30 rounded-3xl blur-xl opacity-60"
				></div>

				<!-- Emojis décoratifs en arrière-plan -->
				<div class="absolute inset-0 overflow-hidden pointer-events-none opacity-25">
					<div class="absolute top-4 left-6 text-5xl">✨</div>
					<div class="absolute top-12 right-8 text-4xl">⭐</div>
					<div class="absolute bottom-8 left-8 text-6xl">💫</div>
					<div class="absolute bottom-12 right-6 text-5xl">🎊</div>
				</div>

				<div class="relative flex flex-col items-center justify-center text-center space-y-4 min-h-[240px]">
					<div class="text-7xl sm:text-8xl">🎉</div>
					<h4 class="text-3xl sm:text-4xl font-bold text-white">
						Week-end<br />inoubliable
					</h4>
					<p class="text-xl sm:text-2xl text-white/70 font-light">On l'espère !</p>
				</div>
			</div>
		</div>

		<!-- Divider -->
		<div bind:this={dividerElement} class="opacity-0 mb-12 sm:mb-16">
			<div
				class="h-px bg-gradient-to-r from-transparent via-purple-500/50 to-transparent"
				style="transform-origin: center;"
			></div>
		</div>

		<!-- Message final -->
		<div bind:this={finalMessageElement} class="opacity-0 max-w-4xl mx-auto text-center">
			<p class="text-xl sm:text-2xl md:text-3xl text-white/90 font-light leading-relaxed">
				Franchement, on ne fait pas ça tous les ans, c'est peut-être même une fois dans une vie, alors on espère que vous serez au rendez-vous 🔥
			</p>
		</div>
	</div>

	<!-- Dégradé de transition vers section suivante -->
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
	.price-orb-1,
	.price-orb-2 {
		transform: translate3d(0, 0, 0);
	}
</style>
