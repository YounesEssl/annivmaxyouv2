<script lang="ts">
	import { onMount } from 'svelte';
	import { gsap } from 'gsap';
	import { ScrollTrigger } from 'gsap/ScrollTrigger';

	gsap.registerPlugin(ScrollTrigger);

	let luggageSection: HTMLElement;
	let titleElement: HTMLElement;
	let suitcaseElement: HTMLElement;
	let bloc1Element: HTMLElement;
	let divider1Element: HTMLElement;
	let bloc2Element: HTMLElement;
	let divider2Element: HTMLElement;
	let bloc3Element: HTMLElement;
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
			orb1Tween = gsap.to('.luggage-orb-1', {
				scale: 1.1,
				opacity: 0.25,
				duration: 7,
				repeat: -1,
				yoyo: true,
				ease: 'sine.inOut'
			});

			orb2Tween = gsap.to('.luggage-orb-2', {
				scale: 1.08,
				opacity: 0.22,
				duration: 9,
				delay: 1.5,
				repeat: -1,
				yoyo: true,
				ease: 'sine.inOut'
			});
		}

		// Timeline principale
		const timeline = gsap.timeline({
			scrollTrigger: {
				trigger: luggageSection,
				start: 'top 75%',
				end: 'bottom 20%',
				toggleActions: 'play none none reverse'
			}
		});

		// Titre
		timeline.fromTo(
			titleElement,
			{ opacity: 0, y: 40 },
			{ opacity: 1, y: 0, duration: 0.7, ease: 'power2.out' }
		);

		// Icône valise
		timeline.fromTo(
			suitcaseElement,
			{ opacity: 0, x: -50 },
			{ opacity: 1, x: 0, duration: 0.8, ease: 'power3.out' },
			'-=0.4'
		);

		// Bloc 1
		timeline.fromTo(
			bloc1Element,
			{ opacity: 0, x: 40 },
			{ opacity: 1, x: 0, duration: 0.7, ease: 'power2.out' },
			'-=0.5'
		);

		// Divider 1
		timeline.fromTo(
			divider1Element,
			{ opacity: 0, scaleX: 0 },
			{ opacity: 1, scaleX: 1, duration: 0.6, ease: 'power2.out' },
			'-=0.3'
		);

		// Bloc 2
		timeline.fromTo(
			bloc2Element,
			{ opacity: 0, y: 30 },
			{ opacity: 1, y: 0, duration: 0.7, ease: 'power2.out' },
			'-=0.3'
		);

		// Divider 2
		timeline.fromTo(
			divider2Element,
			{ opacity: 0, scaleX: 0 },
			{ opacity: 1, scaleX: 1, duration: 0.6, ease: 'power2.out' },
			'-=0.3'
		);

		// Bloc 3
		timeline.fromTo(
			bloc3Element,
			{ opacity: 0, y: 30 },
			{ opacity: 1, y: 0, duration: 0.7, ease: 'power2.out' },
			'-=0.3'
		);

		// Animation float sur la valise (desktop uniquement)
		if (!isMobile) {
			gsap.to(suitcaseElement, {
				y: -15,
				duration: 3,
				repeat: -1,
				yoyo: true,
				ease: 'sine.inOut'
			});
		}

		// Parallaxe sur les orbes
		gsap.to('.luggage-orb-1', {
			scrollTrigger: {
				trigger: luggageSection,
				start: 'top bottom',
				end: 'bottom top',
				scrub: 1
			},
			y: -80,
			ease: 'none'
		});

		gsap.to('.luggage-orb-2', {
			scrollTrigger: {
				trigger: luggageSection,
				start: 'top bottom',
				end: 'bottom top',
				scrub: 1.3
			},
			y: 80,
			ease: 'none'
		});

		// Pause animations orbes hors viewport
		if (!isMobile && orb1Tween && orb2Tween) {
			ScrollTrigger.create({
				trigger: luggageSection,
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
	id="luggage-section"
	bind:this={luggageSection}
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
		class="luggage-orb-1 absolute right-0 top-40 w-80 h-80 lg:w-96 lg:h-96 rounded-full bg-purple-500/20 blur-xl md:blur-3xl pointer-events-none"
		style="will-change: transform, opacity; z-index: 1;"
	></div>
	<div
		class="luggage-orb-2 absolute left-0 bottom-40 w-80 h-80 lg:w-96 lg:h-96 rounded-full bg-blue-500/20 blur-xl md:blur-3xl pointer-events-none"
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
				Dans ma valise j'ai…
			</h2>
		</div>

		<!-- Layout principal : Icône + Contenu -->
		<div class="grid grid-cols-1 lg:grid-cols-[300px_1fr] gap-12 lg:gap-16 items-start">
			<!-- Icône valise SVG -->
			<div bind:this={suitcaseElement} class="opacity-0 flex justify-center lg:justify-start">
				<svg
					class="w-48 h-48 sm:w-56 sm:h-56 lg:w-64 lg:h-64"
					viewBox="0 0 200 200"
					fill="none"
					xmlns="http://www.w3.org/2000/svg"
				>
					<!-- Définition des gradients -->
					<defs>
						<linearGradient id="suitcaseGradient" x1="0%" y1="0%" x2="100%" y2="100%">
							<stop offset="0%" style="stop-color:#7c3aed;stop-opacity:1" />
							<stop offset="50%" style="stop-color:#a78bfa;stop-opacity:1" />
							<stop offset="100%" style="stop-color:#3b82f6;stop-opacity:1" />
						</linearGradient>
						<linearGradient id="handleGradient" x1="0%" y1="0%" x2="0%" y2="100%">
							<stop offset="0%" style="stop-color:#60a5fa;stop-opacity:1" />
							<stop offset="100%" style="stop-color:#3b82f6;stop-opacity:1" />
						</linearGradient>
					</defs>

					<!-- Corps de la valise -->
					<rect
						x="40"
						y="60"
						width="120"
						height="100"
						rx="8"
						fill="url(#suitcaseGradient)"
						stroke="#a78bfa"
						stroke-width="2"
					/>

					<!-- Bande décorative 1 -->
					<rect x="40" y="90" width="120" height="3" fill="#60a5fa" opacity="0.6" />

					<!-- Bande décorative 2 -->
					<rect x="40" y="130" width="120" height="3" fill="#60a5fa" opacity="0.6" />

					<!-- Poignée -->
					<path
						d="M 70 60 Q 70 35, 100 35 Q 130 35, 130 60"
						stroke="url(#handleGradient)"
						stroke-width="6"
						fill="none"
						stroke-linecap="round"
					/>

					<!-- Serrure centrale -->
					<circle cx="100" cy="110" r="6" fill="#fbbf24" />
					<rect x="97" y="110" width="6" height="8" rx="1" fill="#f59e0b" />

					<!-- Coins renforcés -->
					<circle cx="48" cy="68" r="4" fill="#a78bfa" />
					<circle cx="152" cy="68" r="4" fill="#a78bfa" />
					<circle cx="48" cy="152" r="4" fill="#a78bfa" />
					<circle cx="152" cy="152" r="4" fill="#a78bfa" />

					<!-- Roulettes -->
					<circle cx="65" cy="170" r="6" fill="#334155" />
					<circle cx="135" cy="170" r="6" fill="#334155" />
					<circle cx="65" cy="170" r="3" fill="#64748b" />
					<circle cx="135" cy="170" r="3" fill="#64748b" />
				</svg>
			</div>

			<!-- Contenu texte -->
			<div class="space-y-10 sm:space-y-12">
				<!-- Bloc 1 - Soirée -->
				<div bind:this={bloc1Element} class="opacity-0">
					<p class="text-xl sm:text-2xl md:text-3xl text-white/90 font-light leading-relaxed mb-4">
						Pour la soirée du samedi, c'est
						<span
							class="font-semibold"
							style="background: linear-gradient(135deg, rgb(167 139 250) 0%, rgb(147 197 253) 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text;"
						>
							tenue correcte exigée
						</span>.
					</p>
					<p class="text-lg sm:text-xl text-white/80 font-light leading-relaxed">
						Néanmoins, l'important est que vous vous sentiez à l'aise, et la seule obligation est de vous habiller de manière élégante. Si le cœur vous en dit, une robe ou une chemise serait un excellent choix pour l'occasion.
					</p>
				</div>

				<!-- Divider 1 -->
				<div bind:this={divider1Element} class="opacity-0">
					<div class="h-px bg-gradient-to-r from-transparent via-purple-500/40 to-transparent"></div>
				</div>

				<!-- Bloc 2 - Sport -->
				<div bind:this={bloc2Element} class="opacity-0">
					<p class="text-lg sm:text-xl md:text-2xl text-white/90 font-light leading-relaxed">
						Pensez à prendre une <span class="font-semibold text-white">tenue de sport</span> si vous comptez en faire <span class="text-white/60">(on pense notamment à ceux qui veulent faire du foot)</span>.
					</p>
				</div>

				<!-- Divider 2 -->
				<div bind:this={divider2Element} class="opacity-0">
					<div class="h-px bg-gradient-to-r from-transparent via-blue-500/40 to-transparent"></div>
				</div>

				<!-- Bloc 3 - Maillot -->
				<div bind:this={bloc3Element} class="opacity-0">
					<p class="text-lg sm:text-xl md:text-2xl text-white/90 font-light leading-relaxed">
						Enfin, si le temps le permet, la piscine chauffée sera accessible. Pensez donc à glisser un <span class="font-semibold text-white">maillot de bain</span> dans votre valise, au cas où.
					</p>
				</div>
			</div>
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
	.luggage-orb-1,
	.luggage-orb-2 {
		transform: translate3d(0, 0, 0);
	}
</style>
