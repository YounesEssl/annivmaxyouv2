<script lang="ts">
	import { onMount } from 'svelte';
	import { gsap } from 'gsap';
	import { ScrollTrigger } from 'gsap/ScrollTrigger';


	let accommodationSection: HTMLElement;
	let titleElement: HTMLElement;
	let imageElement: HTMLElement;
	let introElement: HTMLElement;
	let subtitleElement: HTMLElement;
	let bloc1Element: HTMLElement;
	let bloc2Element: HTMLElement;
	let note1Element: HTMLElement;
	let note2Element: HTMLElement;
	let conclusionElement: HTMLElement;
	let isMobile = $state(false);

	onMount(() => {
		gsap.registerPlugin(ScrollTrigger);

		// Detect mobile
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
			orb1Tween = gsap.to('.accom-orb-1', {
				scale: 1.08,
				opacity: 0.2,
				duration: 8,
				repeat: -1,
				yoyo: true,
				ease: 'sine.inOut'
			});

			orb2Tween = gsap.to('.accom-orb-2', {
				scale: 1.06,
				opacity: 0.18,
				duration: 10,
				delay: 1.5,
				repeat: -1,
				yoyo: true,
				ease: 'sine.inOut'
			});
		}

		// Timeline principale avec ScrollTrigger
		const timeline = gsap.timeline({
			scrollTrigger: {
				trigger: accommodationSection,
				start: 'top 45%',
				end: 'bottom 20%',
				toggleActions: 'play none none reverse'
			}
		});

		// Titre principal - fade-in-up
		timeline.fromTo(
			titleElement,
			{
				opacity: 0,
				y: 60
			},
			{
				opacity: 1,
				y: 0,
				duration: 0.8,
				ease: 'power3.out'
			}
		);

		// Image - slide from left
		timeline.fromTo(
			imageElement,
			{
				opacity: 0,
				x: -50,
				scale: 0.95
			},
			{
				opacity: 1,
				x: 0,
				scale: 1,
				duration: 0.8,
				ease: 'power3.out'
			},
			'-=0.4'
		);

		// Intro text - slide from right
		timeline.fromTo(
			introElement,
			{
				opacity: 0,
				x: 50
			},
			{
				opacity: 1,
				x: 0,
				duration: 0.7,
				ease: 'power2.out'
			},
			'-=0.5'
		);

		// Sous-titre
		timeline.fromTo(
			subtitleElement,
			{
				opacity: 0,
				y: 30
			},
			{
				opacity: 1,
				y: 0,
				duration: 0.6,
				ease: 'power2.out'
			},
			'+=0.5'
		);

		// Bloc 1 avec gros chiffre "3"
		timeline.fromTo(
			bloc1Element,
			{
				opacity: 0,
				scale: 0.8,
				y: 40
			},
			{
				opacity: 1,
				scale: 1,
				y: 0,
				duration: 0.7,
				ease: 'back.out(1.5)'
			},
			'+=0.4'
		);

		// Bloc 2 avec gros chiffre "12"
		timeline.fromTo(
			bloc2Element,
			{
				opacity: 0,
				scale: 0.8,
				y: 40
			},
			{
				opacity: 1,
				scale: 1,
				y: 0,
				duration: 0.7,
				ease: 'back.out(1.5)'
			},
			'-=0.3'
		);

		// Note 1 - bounce léger
		timeline.fromTo(
			note1Element,
			{
				opacity: 0,
				y: 20
			},
			{
				opacity: 1,
				y: 0,
				duration: 0.6,
				ease: 'power2.out'
			},
			'+=0.5'
		);

		// Note 2 - bounce léger
		timeline.fromTo(
			note2Element,
			{
				opacity: 0,
				y: 20
			},
			{
				opacity: 1,
				y: 0,
				duration: 0.6,
				ease: 'power2.out'
			},
			'-=0.4'
		);

		// Conclusion
		timeline.fromTo(
			conclusionElement,
			{
				opacity: 0,
				y: 30
			},
			{
				opacity: 1,
				y: 0,
				duration: 0.7,
				ease: 'power3.out'
			},
			'+=0.5'
		);

		// Effet parallaxe sur les orbes
		gsap.to('.accom-orb-1', {
			scrollTrigger: {
				trigger: accommodationSection,
				start: 'top 80%',
				end: 'bottom top',
				scrub: 1
			},
			y: -100,
			ease: 'none'
		});

		gsap.to('.accom-orb-2', {
			scrollTrigger: {
				trigger: accommodationSection,
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
				trigger: accommodationSection,
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
	id="accommodation-section"
	bind:this={accommodationSection}
	class="relative min-h-screen pt-20 sm:pt-24 pb-20 sm:pb-24 px-6 sm:px-10"
	style="background: linear-gradient(to bottom,
		rgb(15 23 42) 0%,
		rgb(12 18 35) 50%,
		rgb(15 23 42) 100%
	); overflow-x: clip; overflow-y: visible;"
>
	<!-- Orbes lumineux -->
	<div
		class="accom-orb-1 absolute left-0 top-40 w-80 h-80 lg:w-96 lg:h-96 rounded-full bg-purple-500/15 blur-xl md:blur-3xl pointer-events-none"
		style="will-change: transform, opacity; z-index: 1;"
	></div>
	<div
		class="accom-orb-2 absolute right-0 bottom-40 w-80 h-80 lg:w-96 lg:h-96 rounded-full bg-blue-500/15 blur-xl md:blur-3xl pointer-events-none"
		style="will-change: transform, opacity; z-index: 1;"
	></div>

	<!-- Contenu -->
	<div class="relative z-10 max-w-7xl mx-auto">
		<!-- Titre principal -->
		<div bind:this={titleElement} class="opacity-0 mb-12 sm:mb-16 text-center">
			<h2
				class="text-5xl sm:text-6xl md:text-7xl lg:text-8xl font-black transition-all duration-500 hover:tracking-wide"
				style="font-family: var(--font-serif); background: linear-gradient(135deg, rgb(221 214 254) 0%, rgb(186 230 253) 50%, rgb(221 214 254) 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text; line-height: 1.1;"
			>
				Pour bien dormir
			</h2>
		</div>

		<!-- Image + Intro -->
		<div class="grid grid-cols-1 lg:grid-cols-2 gap-12 lg:gap-16 items-center mb-16 sm:mb-20">
			<!-- Image -->
			<div bind:this={imageElement} class="opacity-0 order-2 lg:order-1">
				<div class="relative group" style="perspective: 1000px;">
					<!-- Glow effect -->
					<div
						class="absolute -inset-1 bg-gradient-to-r from-purple-500/20 via-blue-500/20 to-purple-500/20 rounded-2xl blur-xl opacity-60 group-hover:opacity-100 transition-opacity duration-500 animate-pulse-slow"
					></div>

					<!-- Image container -->
					<div class="relative transition-transform duration-500 group-hover:scale-[1.02]">
						<img
							src="/chalets.png"
							alt="Les chalets du domaine"
							class="w-full aspect-[4/3] object-cover rounded-2xl border border-white/10 shadow-2xl transition-all duration-500"
							loading="lazy"
							style="will-change: transform;"
						/>

						<!-- Overlay au hover -->
						<div
							class="absolute inset-0 bg-gradient-to-t from-purple-500/10 to-transparent opacity-0 group-hover:opacity-100 transition-opacity duration-500 rounded-2xl"
						></div>
					</div>
				</div>
			</div>

			<!-- Intro text -->
			<div bind:this={introElement} class="opacity-0 space-y-4 order-1 lg:order-2">
				<p class="text-lg sm:text-xl md:text-2xl text-white/90 font-light leading-relaxed">
					On aura la chance de profiter de
					<span class="font-semibold text-white">15 chalets tout confort</span>, pouvant
					accueillir jusqu'à
					<span class="font-semibold text-white">62 personnes</span>, avec un lit individuel
					réservé pour chaque invité.
				</p>
				<p class="text-base sm:text-lg text-white/60 font-light italic">
					(donc pas de matelas gonflable ni de bataille pour le canapé)
				</p>
			</div>
		</div>

		<!-- Sous-titre -->
		<div bind:this={subtitleElement} class="opacity-0 mb-12 sm:mb-14">
			<h3
				class="text-3xl sm:text-4xl md:text-5xl font-bold text-white/90 text-center"
				style="font-family: var(--font-serif);"
			>
				Voici la répartition :
			</h3>
		</div>

		<!-- Répartition - 2 blocs -->
		<div class="grid grid-cols-1 lg:grid-cols-2 gap-8 lg:gap-12 mb-16 sm:mb-20">
			<!-- BLOC 1 - Grands chalets -->
			<div
				bind:this={bloc1Element}
				class="opacity-0 relative group p-8 sm:p-10 rounded-3xl border border-white/10 bg-gradient-to-br from-purple-900/10 to-transparent backdrop-blur-sm hover:border-purple-500/30 transition-all duration-500"
			>
				<!-- Glow effect -->
				<div
					class="absolute -inset-0.5 bg-gradient-to-r from-purple-500/0 via-purple-500/10 to-purple-500/0 rounded-3xl opacity-0 group-hover:opacity-100 transition-opacity duration-500 blur-xl"
				></div>

				<div class="relative">
					<!-- Gros chiffre -->
					<div class="mb-4">
						<span
							class="text-8xl sm:text-9xl md:text-[10rem] font-black leading-none"
							style="font-family: var(--font-serif); background: linear-gradient(135deg, rgb(216 180 254) 0%, rgb(147 197 253) 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text;"
						>
							3
						</span>
					</div>

					<!-- Titre -->
					<h4 class="text-2xl sm:text-3xl font-bold text-white mb-2">
						Grands chalets partagés
					</h4>

					<!-- Capacité -->
					<p class="text-lg sm:text-xl text-purple-300/80 font-semibold mb-4">
						pour 15, 15 et 8 personnes
					</p>

					<!-- Description -->
					<p class="text-base sm:text-lg text-white/80 font-light leading-relaxed">
						avec plusieurs chambres, dortoirs, séjours, salles de bain, terrasses… de quoi
						loger confortablement en mode coloc' de week-end.
					</p>
				</div>
			</div>

			<!-- BLOC 2 - Chalets duo -->
			<div
				bind:this={bloc2Element}
				class="opacity-0 relative group p-8 sm:p-10 rounded-3xl border border-white/10 bg-gradient-to-br from-blue-900/10 to-transparent backdrop-blur-sm hover:border-blue-500/30 transition-all duration-500"
			>
				<!-- Glow effect -->
				<div
					class="absolute -inset-0.5 bg-gradient-to-r from-blue-500/0 via-blue-500/10 to-blue-500/0 rounded-3xl opacity-0 group-hover:opacity-100 transition-opacity duration-500 blur-xl"
				></div>

				<div class="relative">
					<!-- Gros chiffre -->
					<div class="mb-4">
						<span
							class="text-8xl sm:text-9xl md:text-[10rem] font-black leading-none"
							style="font-family: var(--font-serif); background: linear-gradient(135deg, rgb(147 197 253) 0%, rgb(216 180 254) 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text;"
						>
							12
						</span>
					</div>

					<!-- Titre -->
					<h4 class="text-2xl sm:text-3xl font-bold text-white mb-2">Chalets duo</h4>

					<!-- Capacité -->
					<p class="text-lg sm:text-xl text-blue-300/80 font-semibold mb-4">
						pour 2 personnes chacun
					</p>

					<!-- Description -->
					<p class="text-base sm:text-lg text-white/80 font-light leading-relaxed">
						avec lit individuel, salle de bain et terrasse pour un peu plus d'intimité.
					</p>
				</div>
			</div>
		</div>

		<!-- Notes importantes -->
		<div class="space-y-6 mb-12 sm:mb-14">
			<!-- Note 1 -->
			<div
				bind:this={note1Element}
				class="opacity-0 flex gap-4 p-6 sm:p-8 rounded-2xl bg-purple-900/10 border border-purple-500/20 backdrop-blur-sm"
			>
				<div class="text-3xl sm:text-4xl flex-shrink-0">🧡</div>
				<p class="text-base sm:text-lg text-white/90 font-light leading-relaxed">
					<span class="font-semibold text-white">Priorité aux couples</span> pour les chalets
					duo : si vous venez en couple et souhaitez dormir ensemble, on fera le maximum pour vous
					réserver un de ces chalets.
				</p>
			</div>

			<!-- Note 2 -->
			<div
				bind:this={note2Element}
				class="opacity-0 flex gap-4 p-6 sm:p-8 rounded-2xl bg-orange-900/10 border border-orange-500/20 backdrop-blur-sm"
			>
				<div class="text-3xl sm:text-4xl flex-shrink-0">⚠️</div>
				<p class="text-base sm:text-lg text-white/90 font-light leading-relaxed">
					En revanche, s'il y a plus de 12 couples, certains devront partager un grand chalet
					– mais toujours avec un lit à soi.
				</p>
			</div>
		</div>

		<!-- Conclusion -->
		<div bind:this={conclusionElement} class="opacity-0 text-center">
			<p class="text-xl sm:text-2xl md:text-3xl text-white/90 font-light leading-relaxed">
				Bref,
				<span class="font-semibold text-white">personne ne dormira par terre</span>, et on a
				tout organisé pour que tout le monde soit à l'aise tout le week-end !
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
	/* Optimisations GPU */
	.accom-orb-1,
	.accom-orb-2 {
		transform: translate3d(0, 0, 0);
	}

	@keyframes pulse-slow {
		0%,
		100% {
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
