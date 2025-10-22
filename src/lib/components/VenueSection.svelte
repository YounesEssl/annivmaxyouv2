<script lang="ts">
	import { onMount } from 'svelte';
	import { gsap } from 'gsap';
	import { ScrollTrigger } from 'gsap/ScrollTrigger';


	let venueSection: HTMLElement;
	let titleElement: HTMLElement;
	let paragraph1: HTMLElement;
	let paragraph2: HTMLElement;
	let paragraph3: HTMLElement;
	let incroyableWord: HTMLElement;
	let imageElement: HTMLElement;
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
			orb1Tween = gsap.to('.venue-orb-1', {
				scale: 1.05,
				opacity: 0.18,
				duration: 7,
				repeat: -1,
				yoyo: true,
				ease: 'power1.inOut'
			});

			orb2Tween = gsap.to('.venue-orb-2', {
				scale: 1.05,
				opacity: 0.18,
				duration: 9,
				delay: 1,
				repeat: -1,
				yoyo: true,
				ease: 'power1.inOut'
			});
		}

		// Animation scroll avec ScrollTrigger - animations staggerées
		const timeline = gsap.timeline({
			scrollTrigger: {
				trigger: venueSection,
				start: 'top 60%',
				end: 'bottom 20%',
				toggleActions: 'play none none reverse',
				// markers: true, // Décommenter pour débugger
			}
		});

		// Titre - fade-in-up
		timeline.fromTo(
			titleElement,
			{
				opacity: 0,
				y: 60
			},
			{
				opacity: 1,
				y: 0,
				duration: 0.7,
				ease: 'power3.out'
			}
		);

		// Paragraphe 1
		timeline.fromTo(
			paragraph1,
			{
				opacity: 0,
				x: -40
			},
			{
				opacity: 1,
				x: 0,
				duration: 0.6,
				ease: 'power2.out'
			},
			'-=0.3'
		);

		// Paragraphe 2
		timeline.fromTo(
			paragraph2,
			{
				opacity: 0,
				x: -40
			},
			{
				opacity: 1,
				x: 0,
				duration: 0.6,
				ease: 'power2.out'
			},
			'-=0.4'
		);

		// Paragraphe 3
		timeline.fromTo(
			paragraph3,
			{
				opacity: 0,
				x: -40
			},
			{
				opacity: 1,
				x: 0,
				duration: 0.6,
				ease: 'power2.out'
			},
			'-=0.4'
		);

		// Mot "INCROYABLE" - animation lettre par lettre
		const letters = gsap.utils.toArray<HTMLElement>('.incroyable-letter');
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

		// Image - animation créative avec rotation et scale
		timeline.fromTo(
			imageElement,
			{
				opacity: 0,
				scale: 0.85,
				rotationY: 15,
				x: 60
			},
			{
				opacity: 1,
				scale: 1,
				rotationY: 0,
				x: 0,
				duration: 1,
				ease: 'power3.out'
			},
			'-=1.5'
		);

		// Effet parallaxe subtil sur l'image
		gsap.to(imageElement, {
			scrollTrigger: {
				trigger: venueSection,
				start: 'top 80%',
				end: 'bottom top',
				scrub: 1.5
			},
			y: -40,
			ease: 'none'
		});

		// Effet parallaxe sur les orbes
		gsap.to('.venue-orb-1', {
			scrollTrigger: {
				trigger: venueSection,
				start: 'top 80%',
				end: 'bottom top',
				scrub: 1
			},
			y: -80,
			ease: 'none'
		});

		gsap.to('.venue-orb-2', {
			scrollTrigger: {
				trigger: venueSection,
				start: 'top 80%',
				end: 'bottom top',
				scrub: 1.2
			},
			y: 80,
			ease: 'none'
		});

		// Pause animations orbes quand section hors viewport (desktop uniquement)
		if (!isMobile && orb1Tween && orb2Tween) {
			ScrollTrigger.create({
				trigger: venueSection,
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
	id="venue-section"
	bind:this={venueSection}
	class="relative min-h-screen pt-20 sm:pt-24 pb-20 sm:pb-24 px-6 sm:px-10"
	style="background: linear-gradient(to bottom,
		rgb(15 23 42) 0%,
		rgb(12 18 38) 50%,
		rgb(15 23 42) 100%
	); overflow-x: clip; overflow-y: visible;"
>
	<!-- Orbes lumineux -->
	<div
		class="venue-orb-1 absolute right-0 top-20 w-80 h-80 lg:w-96 lg:h-96 rounded-full bg-purple-500/15 blur-xl md:blur-3xl pointer-events-none"
		style="will-change: transform, opacity; z-index: 1;"
	></div>
	<div
		class="venue-orb-2 absolute left-0 bottom-40 w-80 h-80 lg:w-96 lg:h-96 rounded-full bg-blue-500/15 blur-xl md:blur-3xl pointer-events-none"
		style="will-change: transform, opacity; z-index: 1;"
	></div>

	<!-- Contenu -->
	<div class="relative z-10 max-w-7xl mx-auto">
		<!-- Grid layout : texte à gauche, image à droite (inversé sur mobile) -->
		<div class="grid grid-cols-1 lg:grid-cols-2 gap-16 lg:gap-20 items-start">
			<!-- Colonne texte -->
			<div class="space-y-6">
				<!-- Titre -->
				<div bind:this={titleElement} class="opacity-0">
					<h2
						class="text-5xl sm:text-6xl md:text-7xl lg:text-8xl font-black mb-8 transition-all duration-500 hover:tracking-wide"
						style="font-family: var(--font-serif); background: linear-gradient(135deg, rgb(221 214 254) 0%, rgb(186 230 253) 50%, rgb(221 214 254) 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text; line-height: 1.1;"
					>
						Le domaine
					</h2>
				</div>

				<!-- Paragraphes -->
				<div class="space-y-5">
					<p
						bind:this={paragraph1}
						class="opacity-0 text-lg sm:text-xl md:text-2xl text-white/90 font-light leading-relaxed"
					>
						À seulement
						<span class="font-semibold text-white relative inline-block">
							1h30 de Paris
							<span
								class="absolute bottom-0 left-0 right-0 h-[1.5px] bg-gradient-to-r from-purple-400/50 via-purple-300/50 to-purple-400/50"
							></span>
						</span>, on vous invite dans un lieu de rêve, privatisé rien que pour nous :
					</p>

					<p
						bind:this={paragraph2}
						class="opacity-0 text-lg sm:text-xl md:text-2xl text-white/90 font-light leading-relaxed"
					>
						un grand domaine façon
						<span class="font-semibold text-white">village de vacances</span>, avec tout ce
						qu'il faut pour faire la fête, faire du sport, manger, danser, boire et quelques
						surprises…
					</p>

					<p
						bind:this={paragraph3}
						class="opacity-0 text-lg sm:text-xl md:text-2xl text-white/90 font-light leading-relaxed"
					>
						on a tout prévu pour que ce week-end soit
					</p>
				</div>

				<!-- Mot d'impact "INCROYABLE" - avec animation lettre par lettre -->
				<div bind:this={incroyableWord} class="pt-3 sm:pt-4">
					<p
						class="text-5xl sm:text-6xl md:text-7xl lg:text-8xl font-black"
						style="font-family: var(--font-serif); will-change: transform; line-height: 0.95; letter-spacing: -0.02em;"
					>
						{#each 'INCROYABLE'.split('') as letter, i}
							<span
								class="inline-block opacity-0 incroyable-letter"
								data-index={i}
								style="background: linear-gradient(135deg, rgb(216 180 254) 0%, rgb(147 197 253) 50%, rgb(216 180 254) 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text;"
							>
								{letter}
							</span>
						{/each}
					</p>
				</div>
			</div>

			<!-- Colonne image -->
			<div bind:this={imageElement} class="opacity-0 lg:pt-8">
				<div class="relative group" style="perspective: 1000px;">
					<!-- Glow effect animé -->
					<div
						class="absolute -inset-1 bg-gradient-to-r from-purple-500/20 via-blue-500/20 to-purple-500/20 rounded-2xl blur-xl opacity-60 group-hover:opacity-100 transition-opacity duration-500 animate-pulse-slow"
					></div>

					<!-- Image container avec effet 3D subtil -->
					<div class="relative transition-transform duration-500 group-hover:scale-[1.02]">
						<img
							src="/domaine.png"
							alt="Le domaine privatisé"
							class="w-full aspect-[4/3] object-cover rounded-2xl border border-white/10 shadow-2xl transition-all duration-500"
							loading="lazy"
							style="will-change: transform;"
						/>

						<!-- Overlay subtil au hover -->
						<div class="absolute inset-0 bg-gradient-to-t from-purple-500/10 to-transparent opacity-0 group-hover:opacity-100 transition-opacity duration-500 rounded-2xl"></div>
					</div>
				</div>
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
	.venue-orb-1,
	.venue-orb-2 {
		transform: translate3d(0, 0, 0);
	}

	.incroyable-letter {
		transform-style: preserve-3d;
		will-change: transform, opacity;
		display: inline-block;
		transform: translateZ(0);
	}

	@keyframes pulse-slow {
		0%, 100% {
			opacity: 0.6;
		}
		50% {
			opacity: 0.8;
		}
	}

	.animate-pulse-slow {
		animation: pulse-slow 3s ease-in-out infinite;
	}
</style>
