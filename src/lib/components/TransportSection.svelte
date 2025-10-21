<script lang="ts">
	import { onMount } from 'svelte';
	import { gsap } from 'gsap';
	import { ScrollTrigger } from 'gsap/ScrollTrigger';


	let transportSection: HTMLElement;
	let titleElement: HTMLElement;
	let textContentElement: HTMLElement;
	let imageElement: HTMLElement;
	let bloc1Element: HTMLElement;
	let divider1Element: HTMLElement;
	let bloc2Element: HTMLElement;
	let divider2Element: HTMLElement;
	let bloc3Element: HTMLElement;
	let isMobile = $state(false);

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
			orb1Tween = gsap.to('.transport-orb-1', {
				scale: 1.1,
				opacity: 0.24,
				duration: 8,
				repeat: -1,
				yoyo: true,
				ease: 'sine.inOut'
			});

			orb2Tween = gsap.to('.transport-orb-2', {
				scale: 1.08,
				opacity: 0.22,
				duration: 10,
				delay: 1,
				repeat: -1,
				yoyo: true,
				ease: 'sine.inOut'
			});
		}

		// Timeline principale
		const timeline = gsap.timeline({
			scrollTrigger: {
				trigger: transportSection,
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

		// Image
		timeline.fromTo(
			imageElement,
			{ opacity: 0, x: 40 },
			{ opacity: 1, x: 0, duration: 0.8, ease: 'power2.out' },
			'-=0.3'
		);

		// Bloc 1
		timeline.fromTo(
			bloc1Element,
			{ opacity: 0, y: 30 },
			{ opacity: 1, y: 0, duration: 0.6, ease: 'power2.out' },
			'-=0.5'
		);

		// Divider 1
		timeline.fromTo(
			divider1Element.querySelector('div'),
			{ scaleX: 0 },
			{ scaleX: 1, duration: 0.5, ease: 'power2.out' },
			'-=0.2'
		);

		// Bloc 2
		timeline.fromTo(
			bloc2Element,
			{ opacity: 0, y: 30 },
			{ opacity: 1, y: 0, duration: 0.6, ease: 'power2.out' },
			'-=0.2'
		);

		// Divider 2
		timeline.fromTo(
			divider2Element.querySelector('div'),
			{ scaleX: 0 },
			{ scaleX: 1, duration: 0.5, ease: 'power2.out' },
			'-=0.2'
		);

		// Bloc 3
		timeline.fromTo(
			bloc3Element,
			{ opacity: 0, y: 30 },
			{ opacity: 1, y: 0, duration: 0.6, ease: 'power2.out' },
			'-=0.2'
		);

		// Parallaxe sur les orbes
		gsap.to('.transport-orb-1', {
			scrollTrigger: {
				trigger: transportSection,
				start: 'top bottom',
				end: 'bottom top',
				scrub: 1
			},
			y: -90,
			ease: 'none'
		});

		gsap.to('.transport-orb-2', {
			scrollTrigger: {
				trigger: transportSection,
				start: 'top bottom',
				end: 'bottom top',
				scrub: 1.3
			},
			y: 90,
			ease: 'none'
		});

		// Pause animations orbes hors viewport
		if (!isMobile && orb1Tween && orb2Tween) {
			ScrollTrigger.create({
				trigger: transportSection,
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
	id="transport-section"
	bind:this={transportSection}
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
		class="transport-orb-1 absolute left-0 top-40 w-80 h-80 lg:w-96 lg:h-96 rounded-full bg-blue-500/20 blur-xl md:blur-3xl pointer-events-none"
		style="will-change: transform, opacity; z-index: 1;"
	></div>
	<div
		class="transport-orb-2 absolute right-0 bottom-40 w-80 h-80 lg:w-96 lg:h-96 rounded-full bg-purple-500/20 blur-xl md:blur-3xl pointer-events-none"
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
				Transport
			</h2>
		</div>

		<!-- Layout principal : Texte (gauche) + Image (droite) -->
		<div class="grid grid-cols-1 lg:grid-cols-2 gap-10 lg:gap-16 items-start">
			<!-- Contenu texte -->
			<div bind:this={textContentElement} class="space-y-8 sm:space-y-10">
				<!-- Bloc 1 - Localisation -->
				<div bind:this={bloc1Element} class="opacity-0">
					<p class="text-lg sm:text-xl md:text-2xl text-white/90 font-light leading-relaxed">
						Le village se trouve à environ <span class="font-semibold text-white">1h30 de route au sud de Paris</span>. Le plus simple, c'est donc de venir en voiture.
					</p>
				</div>

				<!-- Divider 1 -->
				<div bind:this={divider1Element}>
					<div class="h-px bg-gradient-to-r from-transparent via-blue-500/40 to-transparent" style="transform-origin: left;"></div>
				</div>

				<!-- Bloc 2 - Organisation covoiturage -->
				<div bind:this={bloc2Element} class="opacity-0">
					<p class="text-lg sm:text-xl md:text-2xl text-white/90 font-light leading-relaxed">
						On vous laisse vous organiser entre vous pour les groupes de voiture : pensez à en parler assez vite pour savoir qui part avec qui !
					</p>
				</div>

				<!-- Divider 2 -->
				<div bind:this={divider2Element}>
					<div class="h-px bg-gradient-to-r from-transparent via-purple-500/40 to-transparent" style="transform-origin: left;"></div>
				</div>

				<!-- Bloc 3 - Parking -->
				<div bind:this={bloc3Element} class="opacity-0 space-y-4">
					<p class="text-lg sm:text-xl md:text-2xl text-white/90 font-light leading-relaxed">
						Une fois sur place, pas de galère on a un grand
						<span
							class="font-semibold"
							style="background: linear-gradient(135deg, rgb(167 139 250) 0%, rgb(147 197 253) 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text;"
						>
							parking privé
						</span>, juste à côté des chalets.
					</p>

					<p class="text-lg sm:text-xl text-white/70 font-light leading-relaxed">
						Il y a donc pas de stationnement à payer, et vos voitures seront en sécurité tout le week-end.
					</p>
				</div>
			</div>

			<!-- Image -->
			<div bind:this={imageElement} class="opacity-0 order-first lg:order-last">
				<div class="relative">
					<!-- Glow -->
					<div class="absolute -inset-2 bg-gradient-to-r from-purple-500/20 via-blue-500/20 to-purple-500/20 rounded-3xl blur-2xl opacity-70"></div>

					<div class="relative">
						<img
							src="/parking.png"
							alt="Parking"
							class="w-full aspect-[4/3] object-cover rounded-3xl border-2 border-white/10 shadow-2xl"
							loading="lazy"
						/>
					</div>
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
	.transport-orb-1,
	.transport-orb-2 {
		transform: translate3d(0, 0, 0);
	}
</style>
