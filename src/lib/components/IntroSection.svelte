<script lang="ts">
	import { onMount } from 'svelte';
	import { gsap } from 'gsap';
	import { ScrollTrigger } from 'gsap/ScrollTrigger';

	gsap.registerPlugin(ScrollTrigger);

	let introSection: HTMLElement;
	let bloc1: HTMLElement;
	let dateElement: HTMLElement;
	let bloc2Intro: HTMLElement;
	let inoubliableWord: HTMLElement;
	let bloc2Outro: HTMLElement;
	let isMobile = $state(false);

	onMount(() => {
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

		// Animation scroll avec ScrollTrigger - animations séquencées plus rapides
		const timeline = gsap.timeline({
			scrollTrigger: {
				trigger: introSection,
				start: 'top 75%',
				end: 'bottom 20%',
				toggleActions: 'play none none reverse',
				// markers: true, // Décommenter pour débugger
			}
		});

		// Bloc 1 - texte principal avec fade-in-up
		timeline.fromTo(
			bloc1,
			{
				opacity: 0,
				y: 70
			},
			{
				opacity: 1,
				y: 0,
				duration: 0.7,
				ease: 'power3.out'
			}
		);

		// Pause courte avant la date
		timeline.to({}, { duration: 0.15 });

		// Date "28 mars" - animation spéciale avec scale
		timeline.fromTo(
			dateElement,
			{
				opacity: 0,
				scale: 0.75
			},
			{
				opacity: 1,
				scale: 1,
				duration: 0.8,
				ease: 'back.out(1.7)'
			}
		);

		// Pause courte avant le build up
		timeline.to({}, { duration: 0.1 });

		// Bloc 2 intro - fade normal
		timeline.fromTo(
			bloc2Intro,
			{
				opacity: 0,
				y: 40
			},
			{
				opacity: 1,
				y: 0,
				duration: 0.6,
				ease: 'power2.out'
			}
		);

		// Très courte pause avant "inoubliable"
		timeline.to({}, { duration: 0.05 });

		// "inoubliable" - animation dramatique séparée
		timeline.fromTo(
			inoubliableWord,
			{
				opacity: 0,
				scale: 0.65,
				y: 35
			},
			{
				opacity: 1,
				scale: 1,
				y: 0,
				duration: 0.75,
				ease: 'back.out(1.4)'
			}
		);

		// Très courte pause avant conclusion
		timeline.to({}, { duration: 0.05 });

		// Bloc 2 outro - fade normal
		timeline.fromTo(
			bloc2Outro,
			{
				opacity: 0,
				y: 30
			},
			{
				opacity: 1,
				y: 0,
				duration: 0.5,
				ease: 'power2.out'
			}
		);

		// Effet parallaxe sur les orbes
		gsap.to('.intro-orb-1', {
			scrollTrigger: {
				trigger: introSection,
				start: 'top bottom',
				end: 'bottom top',
				scrub: 1
			},
			y: -100,
			ease: 'none'
		});

		gsap.to('.intro-orb-2', {
			scrollTrigger: {
				trigger: introSection,
				start: 'top bottom',
				end: 'bottom top',
				scrub: 1.5
			},
			y: 100,
			ease: 'none'
		});

		// Pause animations orbes quand section hors viewport (desktop uniquement)
		if (!isMobile && orb1Tween && orb2Tween) {
			ScrollTrigger.create({
				trigger: introSection,
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
	id="intro-section"
	bind:this={introSection}
	class="relative pt-20 sm:pt-24 pb-24 sm:pb-32 px-6 sm:px-10"
	style="background: rgb(15 23 42); overflow-x: clip; overflow-y: visible;"
>
	<!-- Orbes lumineux -->
	<div
		class="intro-orb-1 absolute -right-20 -top-20 w-96 h-96 lg:w-[500px] lg:h-[500px] rounded-full bg-purple-500/15 blur-xl md:blur-3xl pointer-events-none"
		style="will-change: transform, opacity; z-index: 5;"
	></div>
	<div
		class="intro-orb-2 absolute -bottom-20 -left-20 w-96 h-96 lg:w-[500px] lg:h-[500px] rounded-full bg-blue-500/15 blur-xl md:blur-3xl pointer-events-none"
		style="will-change: transform, opacity; z-index: 5;"
	></div>


	<!-- Contenu avec agencement créatif -->
	<div class="relative z-10 max-w-5xl mx-auto px-4">
		<!-- Bloc 1 - Introduction alignée à gauche -->
		<div bind:this={bloc1} class="opacity-0 mb-16 sm:mb-20 max-w-3xl" style="will-change: transform, opacity; transform: translateZ(0);">
			<h2 class="text-2xl sm:text-3xl md:text-4xl lg:text-5xl text-white font-light leading-tight mb-4">
				On est <span class="font-bold text-white">très heureux</span><br class="hidden sm:block" />
				de vous inviter à célébrer notre<br />
				<span class="font-bold text-white">anniversaire commun</span>
			</h2>
		</div>

		<!-- Date - centré, grand, avec emphase -->
		<div bind:this={dateElement} class="opacity-0 text-center mb-12 sm:mb-16" style="will-change: transform, opacity;">
			<p class="text-sm sm:text-base md:text-lg text-white/70 font-light mb-3 tracking-wider uppercase">
				le week-end du
			</p>
			<p
				class="text-5xl sm:text-6xl md:text-7xl lg:text-8xl font-black inline-block transition-transform duration-300 hover:scale-105 mb-4"
				style="font-family: var(--font-serif); background: linear-gradient(135deg, rgb(216 180 254) 0%, rgb(221 214 254) 50%, rgb(147 197 253) 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text; line-height: 1;"
			>
				28 mars
			</p>
			<p class="text-xl sm:text-2xl md:text-3xl text-white font-light mt-4">
				dans un
				<span class="font-semibold text-white relative inline-block mx-1">
					lieu privatisé
					<span
						class="absolute bottom-0 left-0 right-0 h-[2px] bg-gradient-to-r from-purple-400/50 via-purple-300/50 to-purple-400/50"
					></span>
				</span>
				<br class="sm:hidden" />
				à l'occasion !
			</p>
		</div>

		<!-- Bloc 2 - Build up aligné à droite -->
		<div bind:this={bloc2Intro} class="opacity-0 text-right mb-8 sm:mb-12 ml-auto max-w-2xl" style="will-change: transform, opacity; transform: translateZ(0);">
			<p class="text-lg sm:text-xl md:text-2xl lg:text-3xl text-white/90 font-light leading-relaxed">
				On a vu les choses
				<span class="font-bold text-white relative inline-block">
					très grand
					<span
						class="absolute bottom-0 left-0 right-0 h-[2px] bg-gradient-to-r from-purple-400/60 via-purple-300/60 to-purple-400/60"
					></span>
				</span>
				<br />
				pour que ce week-end soit
			</p>
		</div>

		<!-- Mot clé central - ÉNORME et centré -->
		<div bind:this={inoubliableWord} class="opacity-0 text-center my-12 sm:my-16 md:my-20" style="will-change: transform, opacity;">
			<p
				class="text-6xl sm:text-7xl md:text-8xl lg:text-9xl font-black transition-transform duration-300 hover:scale-105"
				style="font-family: var(--font-serif); background: linear-gradient(135deg, rgb(221 214 254) 0%, rgb(186 230 253) 50%, rgb(221 214 254) 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text; will-change: transform; line-height: 0.95; letter-spacing: -0.02em;"
			>
				inoubliable
			</p>
		</div>
	</div>
</section>

<style>
	/* Optimisations GPU */
	.intro-orb-1,
	.intro-orb-2 {
		transform: translate3d(0, 0, 0);
	}
</style>
