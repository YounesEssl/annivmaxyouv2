<script lang="ts">
	import { onMount } from 'svelte';
	import { gsap } from 'gsap';
	import { ScrollTrigger } from 'gsap/ScrollTrigger';


	let ctaSection: HTMLElement;
	let messageElement: HTMLElement;
	let buttonElement: HTMLElement;
	let arrowElement: HTMLElement;
	let buttonGlowElement: HTMLElement;
	let isMobile = $state(false);

	onMount(() => {
		gsap.registerPlugin(ScrollTrigger);

		const mediaQuery = window.matchMedia('(max-width: 767px)');
		isMobile = mediaQuery.matches;

		const handleResize = () => {
			isMobile = mediaQuery.matches;
		};
		mediaQuery.addEventListener('change', handleResize);

		// Animation de l'orbe (desktop uniquement)
		let orbTween: gsap.core.Tween | null = null;

		if (!isMobile) {
			orbTween = gsap.to('.cta-orb', {
				scale: 1.2,
				opacity: 0.35,
				duration: 5,
				repeat: -1,
				yoyo: true,
				ease: 'sine.inOut'
			});
		}

		// Timeline principale
		const timeline = gsap.timeline({
			scrollTrigger: {
				trigger: ctaSection,
				start: 'top 60%',
				end: 'bottom 20%',
				toggleActions: 'play none none reverse'
			}
		});

		// Message
		timeline.fromTo(
			messageElement,
			{ opacity: 0, y: 40 },
			{ opacity: 1, y: 0, duration: 0.8, ease: 'power2.out' }
		);

		// Bouton avec scale-in
		timeline.fromTo(
			buttonElement,
			{ opacity: 0, scale: 0.8 },
			{ opacity: 1, scale: 1, duration: 0.6, ease: 'back.out(1.5)' },
			'-=0.2'
		);

		// Pulse continu du glow du bouton
		if (buttonGlowElement) {
			gsap.to(buttonGlowElement, {
				scale: 1.15,
				opacity: 0.6,
				duration: 2,
				repeat: -1,
				yoyo: true,
				ease: 'sine.inOut'
			});
		}

		// Bounce de la flèche (desktop uniquement)
		if (!isMobile && arrowElement) {
			gsap.to(arrowElement, {
				x: 8,
				duration: 0.8,
				repeat: -1,
				yoyo: true,
				ease: 'sine.inOut'
			});
		}

		// Parallaxe sur l'orbe
		gsap.to('.cta-orb', {
			scrollTrigger: {
				trigger: ctaSection,
				start: 'top 80%',
				end: 'bottom top',
				scrub: 1
			},
			y: -50,
			ease: 'none'
		});

		// Pause animation orbe hors viewport
		if (!isMobile && orbTween) {
			ScrollTrigger.create({
				trigger: ctaSection,
				start: 'top 80%',
				end: 'bottom top',
				onEnter: () => {
					orbTween?.play();
				},
				onLeave: () => {
					orbTween?.pause();
				},
				onEnterBack: () => {
					orbTween?.play();
				},
				onLeaveBack: () => {
					orbTween?.pause();
				}
			});
		}

		return () => {
			mediaQuery.removeEventListener('change', handleResize);
			ScrollTrigger.getAll().forEach((trigger) => trigger.kill());
			orbTween?.kill();
		};
	});
</script>

<section
	id="cta-section"
	bind:this={ctaSection}
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

	<!-- Orbe central spotlight -->
	<div
		class="cta-orb absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-[600px] h-[600px] lg:w-[800px] lg:h-[800px] rounded-full bg-gradient-to-r from-purple-500/25 to-blue-500/25 blur-3xl pointer-events-none"
		style="will-change: transform, opacity; z-index: 1;"
	></div>

	<!-- Contenu -->
	<div class="relative z-10 max-w-4xl mx-auto text-center space-y-12 sm:space-y-16">
		<!-- Message principal -->
		<div bind:this={messageElement} class="opacity-0">
			<p class="text-2xl sm:text-3xl md:text-4xl lg:text-5xl text-white/90 font-light leading-relaxed">
				Si tu es prêt à t'inscrire définitivement et à régler, tu peux tout de suite cliquer sur le bouton <span class="font-semibold text-white">S'inscrire</span> et remplir le formulaire
			</p>
		</div>

		<!-- Bouton CTA -->
		<div bind:this={buttonElement} class="opacity-0 relative inline-block">
			<!-- Glow du bouton -->
			<div
				bind:this={buttonGlowElement}
				class="absolute -inset-4 bg-gradient-to-r from-purple-600/40 to-blue-600/40 rounded-full blur-2xl"
				style="will-change: transform, opacity;"
			></div>

			<!-- Bouton -->
			<a
				href="https://tally.so/r/mZqRao"
				target="_blank"
				rel="noopener noreferrer"
				class="relative inline-flex items-center gap-4 px-10 sm:px-14 py-5 sm:py-7 text-xl sm:text-2xl md:text-3xl font-bold text-white rounded-full shadow-2xl transition-all duration-300 active:scale-95"
				style="background: linear-gradient(135deg, rgb(147 51 234) 0%, rgb(37 99 235) 100%); will-change: transform;"
			>
				<span>S'inscrire maintenant</span>
				<span bind:this={arrowElement} class="text-2xl sm:text-3xl" style="will-change: transform;">→</span>
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
	.cta-orb {
		transform: translate3d(0, 0, 0);
	}

	a:hover {
		transform: scale(1.05);
	}
</style>
