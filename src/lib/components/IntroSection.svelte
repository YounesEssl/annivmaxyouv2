<script lang="ts">
	import { onMount } from 'svelte';
	import { gsap } from 'gsap';
	import { ScrollTrigger } from 'gsap/ScrollTrigger';

	let introSection: HTMLElement;
	let titleElement: HTMLElement;
	let dateCardElement: HTMLElement;
	let descriptionElement: HTMLElement;
	let highlightWordElement: HTMLElement;
	let isMobile = $state(false);

	onMount(() => {
		gsap.registerPlugin(ScrollTrigger);

		// Detect mobile avec matchMedia
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
			orb1Tween = gsap.to('.intro-orb-1', {
				scale: 1.05,
				opacity: 0.2,
				duration: 6,
				repeat: -1,
				yoyo: true,
				ease: 'power1.inOut'
			});

			orb2Tween = gsap.to('.intro-orb-2', {
				scale: 1.05,
				opacity: 0.2,
				duration: 8,
				delay: 1.5,
				repeat: -1,
				yoyo: true,
				ease: 'power1.inOut'
			});
		}

		// Animation avec ScrollTrigger - tout centré et fluide
		const timeline = gsap.timeline({
			scrollTrigger: {
				trigger: introSection,
				start: 'top 60%',
				toggleActions: 'play none none reverse'
			}
		});

		// Titre
		timeline.fromTo(
			titleElement,
			{ opacity: 0, y: 50 },
			{ opacity: 1, y: 0, duration: 0.8, ease: 'power3.out' }
		);

		// Date card avec un petit délai
		timeline.fromTo(
			dateCardElement,
			{ opacity: 0, scale: 0.9, y: 30 },
			{ opacity: 1, scale: 1, y: 0, duration: 0.8, ease: 'back.out(1.5)' },
			'-=0.4'
		);

		// Description
		timeline.fromTo(
			descriptionElement,
			{ opacity: 0, y: 30 },
			{ opacity: 1, y: 0, duration: 0.7, ease: 'power2.out' },
			'-=0.3'
		);

		// Mot highlight - animation lettre par lettre
		const letters = gsap.utils.toArray<HTMLElement>('.inoubliable-letter');
		letters.forEach((letter, i) => {
			timeline.fromTo(
				letter,
				{
					opacity: 0,
					y: 50,
					rotationX: -90,
					scale: 0.5
				},
				{
					opacity: 1,
					y: 0,
					rotationX: 0,
					scale: 1,
					duration: 0.5,
					ease: 'back.out(2)',
					onComplete: () => {
						// Animation de rebond subtile après apparition de la dernière lettre
						if (i === letters.length - 1) {
							gsap.to(letters, {
								y: -10,
								duration: 0.3,
								stagger: 0.03,
								yoyo: true,
								repeat: 1,
								ease: 'power2.inOut'
							});
						}
					}
				},
				i === 0 ? '-=0.2' : '-=0.35' // Stagger entre les lettres
			);
		});

		// Animation continue de shimmer sur le mot complet
		if (!isMobile) {
			gsap.to('.inoubliable-shimmer', {
				backgroundPosition: '200% center',
				duration: 3,
				repeat: -1,
				ease: 'none',
				delay: 1
			});
		}

		// Effet parallaxe sur les orbes
		gsap.to('.intro-orb-1', {
			scrollTrigger: {
				trigger: introSection,
				start: 'top 80%',
				end: 'bottom top',
				scrub: 1
			},
			y: -100,
			ease: 'none'
		});

		gsap.to('.intro-orb-2', {
			scrollTrigger: {
				trigger: introSection,
				start: 'top 80%',
				end: 'bottom top',
				scrub: 1.5
			},
			y: 100,
			ease: 'none'
		});

		// Pause animations orbes quand section hors viewport
		if (!isMobile && orb1Tween && orb2Tween) {
			ScrollTrigger.create({
				trigger: introSection,
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
	id="intro-section"
	bind:this={introSection}
	class="relative min-h-screen flex items-center justify-center pt-20 sm:pt-24 pb-24 sm:pb-32 px-6 sm:px-10"
	style="background: rgb(15 23 42); "
>
	<!-- Orbes lumineux -->
	<div
		class="intro-orb-1 absolute -right-20 -top-20 w-96 h-96 lg:w-[500px] lg:h-[500px] rounded-full bg-rose-500/18 blur-xl md:blur-3xl pointer-events-none"
		style="will-change: transform, opacity; z-index: 5;"
	></div>
	<div
		class="intro-orb-2 absolute -bottom-20 -left-20 w-96 h-96 lg:w-[500px] lg:h-[500px] rounded-full bg-teal-500/18 blur-xl md:blur-3xl pointer-events-none"
		style="will-change: transform, opacity; z-index: 5;"
	></div>

	<!-- Contenu centré et unifié -->
	<div class="relative z-10 max-w-4xl mx-auto text-center space-y-12 sm:space-y-16">
		<!-- Titre principal -->
		<div bind:this={titleElement} class="opacity-0">
			<h2 class="text-3xl sm:text-4xl md:text-5xl lg:text-6xl text-white font-light leading-tight">
				Nous sommes <span class="font-bold text-white">très heureux</span>
				<br />
				de vous inviter à célébrer notre
				<br />
				<span class="font-bold text-white">anniversaire commun</span>
			</h2>
		</div>

		<!-- Card de la date - design élégant et unifié -->
		<div bind:this={dateCardElement} class="opacity-0">
			<div class="relative inline-block">
				<!-- Glow effect -->
				<div class="absolute -inset-6 bg-gradient-to-r from-rose-500/30 via-pink-500/30 to-teal-500/30 rounded-3xl blur-3xl opacity-60 animate-pulse-slow"></div>

				<div class="relative bg-gradient-to-br from-white/5 to-white/[0.02] backdrop-blur-sm rounded-3xl border border-white/10 p-8 sm:p-10 md:p-12">
					<!-- Date principale -->
					<h3
						class="text-4xl sm:text-5xl md:text-6xl lg:text-7xl font-black mb-6"
						style="font-family: var(--font-serif); background: linear-gradient(135deg, rgb(244 114 182) 0%, rgb(251 207 232) 50%, rgb(94 234 212) 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text; line-height: 1.1;"
					>
						27 - 29 Mars
					</h3>

					<!-- Horaires détaillés -->
					<div class="space-y-3 mb-6">
						<div class="flex items-center justify-center gap-3">
							<svg class="w-5 h-5 text-rose-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
								<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z" />
							</svg>
							<p class="text-lg sm:text-xl text-white/90 font-light">
								<span class="font-semibold text-white">Vendredi 19h</span> → <span class="font-semibold text-white">Dimanche 14h</span>
							</p>
						</div>
					</div>

					<!-- Lieu -->
					<div class="inline-flex items-center gap-3 px-6 py-3 rounded-full bg-white/5 border border-white/10">
						<svg class="w-5 h-5 text-teal-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
							<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17.657 16.657L13.414 20.9a1.998 1.998 0 01-2.827 0l-4.244-4.243a8 8 0 1111.314 0z" />
							<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 11a3 3 0 11-6 0 3 3 0 016 0z" />
						</svg>
						<span class="text-base sm:text-lg text-white/90 font-medium">
							Lieu privatisé
						</span>
					</div>
				</div>
			</div>
		</div>

		<!-- Description -->
		<div bind:this={descriptionElement} class="opacity-0">
			<p class="text-2xl sm:text-3xl md:text-4xl text-white/80 font-light leading-relaxed">
				On a vu les choses <span class="font-semibold text-white">très grand</span>
				<br />
				pour que ce week-end soit
			</p>
		</div>

		<!-- Mot highlight final -->
		<div bind:this={highlightWordElement}>
			<div class="relative inline-block" style="perspective: 1000px;">
				<!-- Glow effect -->
				<div class="absolute -inset-8 bg-gradient-to-r from-violet-500/30 via-fuchsia-500/30 to-orange-500/30 rounded-full blur-3xl opacity-70 animate-pulse-slow"></div>

				<h3
					class="relative text-6xl sm:text-7xl md:text-8xl lg:text-9xl font-black"
					style="font-family: var(--font-serif); line-height: 1; letter-spacing: -0.02em;"
				>
					{#each 'Inoubliable'.split('') as letter, i}
						<span
							class="inline-block inoubliable-letter inoubliable-shimmer"
							data-index={i}
							style="opacity: 0; background: linear-gradient(135deg, rgb(139 92 246) 0%, rgb(217 70 239) 25%, rgb(236 72 153) 50%, rgb(251 146 60) 75%, rgb(251 191 36) 100%); background-size: 200% 100%; -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text; color: transparent;"
						>
							{letter}
						</span>
					{/each}
				</h3>
			</div>
		</div>
	</div>
</section>

<style>
	/* Optimisations GPU */
	.intro-orb-1,
	.intro-orb-2 {
		transform: translate3d(0, 0, 0);
	}

	.inoubliable-letter {
		transform-style: preserve-3d;
		will-change: transform, opacity;
		display: inline-block;
		transform: translateZ(0);
	}

	.inoubliable-shimmer {
		will-change: background-position;
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
