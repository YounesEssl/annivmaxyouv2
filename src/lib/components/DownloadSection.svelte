<script lang="ts">
	import { onMount } from 'svelte';
	import { gsap } from 'gsap';
	import { ScrollTrigger } from 'gsap/ScrollTrigger';

	gsap.registerPlugin(ScrollTrigger);

	let downloadSection: HTMLElement;
	let titleElement: HTMLElement;
	let descriptionElement: HTMLElement;
	let buttonElement: HTMLElement;
	let iconElement: HTMLElement;
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
			orb1Tween = gsap.to('.download-orb-1', {
				scale: 1.15,
				opacity: 0.28,
				duration: 6,
				repeat: -1,
				yoyo: true,
				ease: 'sine.inOut'
			});

			orb2Tween = gsap.to('.download-orb-2', {
				scale: 1.12,
				opacity: 0.25,
				duration: 7,
				delay: 1.5,
				repeat: -1,
				yoyo: true,
				ease: 'sine.inOut'
			});
		}

		// Timeline principale
		const timeline = gsap.timeline({
			scrollTrigger: {
				trigger: downloadSection,
				start: 'top 75%',
				end: 'bottom 20%',
				toggleActions: 'play none none reverse'
			}
		});

		// Titre
		timeline.fromTo(
			titleElement,
			{ opacity: 0, y: 30 },
			{ opacity: 1, y: 0, duration: 0.7, ease: 'power2.out' }
		);

		// Description
		timeline.fromTo(
			descriptionElement,
			{ opacity: 0, y: 25 },
			{ opacity: 1, y: 0, duration: 0.7, ease: 'power2.out' },
			'-=0.3'
		);

		// Bouton
		timeline.fromTo(
			buttonElement,
			{ opacity: 0, scale: 0.9 },
			{ opacity: 1, scale: 1, duration: 0.6, ease: 'back.out(1.3)' },
			'-=0.2'
		);

		// Float animation sur l'icône (desktop uniquement)
		if (!isMobile && iconElement) {
			gsap.to(iconElement, {
				y: -8,
				duration: 2,
				repeat: -1,
				yoyo: true,
				ease: 'sine.inOut'
			});
		}

		// Parallaxe sur les orbes
		gsap.to('.download-orb-1', {
			scrollTrigger: {
				trigger: downloadSection,
				start: 'top bottom',
				end: 'bottom top',
				scrub: 1
			},
			y: -80,
			ease: 'none'
		});

		gsap.to('.download-orb-2', {
			scrollTrigger: {
				trigger: downloadSection,
				start: 'top bottom',
				end: 'bottom top',
				scrub: 1.2
			},
			y: 80,
			ease: 'none'
		});

		// Pause animations orbes hors viewport
		if (!isMobile && orb1Tween && orb2Tween) {
			ScrollTrigger.create({
				trigger: downloadSection,
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
	id="download-section"
	bind:this={downloadSection}
	class="relative min-h-screen flex items-center justify-center pt-20 sm:pt-24 pb-20 sm:pb-24 px-6 sm:px-10"
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
		class="download-orb-1 absolute left-0 top-1/3 w-96 h-96 lg:w-[28rem] lg:h-[28rem] rounded-full bg-purple-500/20 blur-3xl pointer-events-none"
		style="will-change: transform, opacity; z-index: 1;"
	></div>
	<div
		class="download-orb-2 absolute right-0 bottom-1/3 w-96 h-96 lg:w-[28rem] lg:h-[28rem] rounded-full bg-blue-500/20 blur-3xl pointer-events-none"
		style="will-change: transform, opacity; z-index: 1;"
	></div>

	<!-- Contenu -->
	<div class="relative z-10 max-w-4xl mx-auto text-center space-y-10 sm:space-y-12">
		<!-- Titre principal -->
		<div bind:this={titleElement} class="opacity-0">
			<h2
				class="text-4xl sm:text-5xl md:text-6xl lg:text-7xl font-black"
				style="font-family: var(--font-serif); background: linear-gradient(135deg, rgb(221 214 254) 0%, rgb(186 230 253) 50%, rgb(221 214 254) 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text; line-height: 1.2;"
			>
				Besoin d'un récap ?
			</h2>
		</div>

		<!-- Description -->
		<div bind:this={descriptionElement} class="opacity-0">
			<p class="text-lg sm:text-xl md:text-2xl text-white/80 font-light leading-relaxed max-w-3xl mx-auto">
				Télécharge toutes les infos pratiques dans un PDF : dates, lieu, prix, programme... tout ce qu'il te faut pour ne rien oublier !
			</p>
		</div>

		<!-- Bouton Download Card -->
		<div bind:this={buttonElement} class="opacity-0 inline-block">
			<a
				href="/recap-anniv.pdf"
				download
				class="relative group flex items-center gap-6 px-8 sm:px-10 py-6 sm:py-8 rounded-2xl border-2 backdrop-blur-sm transition-all duration-300 active:scale-95"
				style="background: linear-gradient(135deg, rgb(88 28 135 / 0.4) 0%, rgb(30 58 138 / 0.4) 100%); border-color: rgb(147 51 234 / 0.4); will-change: transform; box-shadow: 0 20px 50px rgb(0 0 0 / 0.3);"
			>
				<!-- Glow effect -->
				<div
					class="absolute -inset-1 bg-gradient-to-r from-purple-600/30 to-blue-600/30 rounded-2xl blur-xl opacity-50 group-hover:opacity-70 transition-opacity duration-300"
				></div>

				<!-- Icône Download -->
				<div
					bind:this={iconElement}
					class="relative flex-shrink-0 w-16 h-16 sm:w-20 sm:h-20 rounded-xl flex items-center justify-center"
					style="background: linear-gradient(135deg, rgb(147 51 234 / 0.5) 0%, rgb(37 99 235 / 0.5) 100%); will-change: transform;"
				>
					<svg
						class="w-8 h-8 sm:w-10 sm:h-10 text-white"
						fill="none"
						stroke="currentColor"
						viewBox="0 0 24 24"
						xmlns="http://www.w3.org/2000/svg"
					>
						<!-- Cloud -->
						<path
							stroke-linecap="round"
							stroke-linejoin="round"
							stroke-width="2"
							d="M7 16a4 4 0 01-.88-7.903A5 5 0 1115.9 6L16 6a5 5 0 011 9.9M9 19l3 3m0 0l3-3m-3 3V10"
						/>
					</svg>
				</div>

				<!-- Texte -->
				<div class="relative flex flex-col items-start text-left">
					<span class="text-xl sm:text-2xl md:text-3xl font-bold text-white">
						Télécharger le récap
					</span>
					<span class="text-sm sm:text-base md:text-lg text-white/70 font-light mt-1">
						PDF - Toutes les infos
					</span>
				</div>
			</a>
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
	.download-orb-1,
	.download-orb-2 {
		transform: translate3d(0, 0, 0);
	}

	a:hover {
		transform: scale(1.05);
	}
</style>
