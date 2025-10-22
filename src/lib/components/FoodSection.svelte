<script lang="ts">
	import { onMount } from 'svelte';
	import { gsap } from 'gsap';
	import { ScrollTrigger } from 'gsap/ScrollTrigger';


	let foodSection: HTMLElement;
	let titleElement: HTMLElement;
	let introElement: HTMLElement;
	let solutionElement: HTMLElement;
	let maxiSaladElement: HTMLElement;
	let ideaElement: HTMLElement;
	let optionElement: HTMLElement;
	let encouragementElement: HTMLElement;
	let noteElement: HTMLElement;
	let fridayDetailsElement: HTMLElement;
	let ideasListElement: HTMLElement;
	let chipsNoteElement: HTMLElement;
	let importantElement: HTMLElement;
	let isMobile = $state(false);

	// Idées de ce qu'on peut apporter avec emojis
	const bringIdeas = [
		{ icon: '🥧', label: 'quiches' },
		{ icon: '🍰', label: 'cakes' },
		{ icon: '🥖', label: 'tartes' },
		{ icon: '🥓', label: 'saucisson' },
		{ icon: '🥗', label: 'salade' },
		{ icon: '🥑', label: 'guacamole' },
		{ icon: '🍖', label: 'pâté' },
		{ icon: '🍅', label: 'tomates cerises' },
		{ icon: '🍿', label: 'chips' },
		{ icon: '🍇', label: 'fruits' },
		{ icon: '🍪', label: 'gâteaux' }
	];

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
			orb1Tween = gsap.to('.food-orb-1', {
				scale: 1.1,
				opacity: 0.22,
				duration: 7,
				repeat: -1,
				yoyo: true,
				ease: 'sine.inOut'
			});

			orb2Tween = gsap.to('.food-orb-2', {
				scale: 1.08,
				opacity: 0.2,
				duration: 9,
				delay: 1,
				repeat: -1,
				yoyo: true,
				ease: 'sine.inOut'
			});
		}

		// Timeline principale avec ScrollTrigger
		const timeline = gsap.timeline({
			scrollTrigger: {
				trigger: foodSection,
				start: 'top 60%',
				end: 'bottom 20%',
				toggleActions: 'play none none reverse'
			}
		});

		// Titre principal
		timeline.fromTo(
			titleElement,
			{ opacity: 0, y: 60 },
			{ opacity: 1, y: 0, duration: 0.8, ease: 'power3.out' }
		);

		// Intro
		timeline.fromTo(
			introElement,
			{ opacity: 0, y: 30 },
			{ opacity: 1, y: 0, duration: 0.6, ease: 'power2.out' },
			'-=0.3'
		);

		// Solution
		timeline.fromTo(
			solutionElement,
			{ opacity: 0, y: 30 },
			{ opacity: 1, y: 0, duration: 0.6, ease: 'power2.out' },
			'-=0.3'
		);

		// MAXI salad avec scale dramatique
		timeline.fromTo(
			maxiSaladElement,
			{ opacity: 0, scale: 0.7, rotationX: -20 },
			{ opacity: 1, scale: 1, rotationX: 0, duration: 0.9, ease: 'back.out(2)' },
			'+=0.2'
		);

		// Idea
		timeline.fromTo(
			ideaElement,
			{ opacity: 0, y: 30 },
			{ opacity: 1, y: 0, duration: 0.6, ease: 'power2.out' },
			'-=0.3'
		);

		// Option
		timeline.fromTo(
			optionElement,
			{ opacity: 0, y: 30 },
			{ opacity: 1, y: 0, duration: 0.6, ease: 'power2.out' },
			'-=0.2'
		);

		// Encouragement
		timeline.fromTo(
			encouragementElement,
			{ opacity: 0, y: 30 },
			{ opacity: 1, y: 0, duration: 0.6, ease: 'power2.out' },
			'-=0.2'
		);

		// Note (À noter)
		timeline.fromTo(
			noteElement,
			{ opacity: 0, scale: 0.9 },
			{ opacity: 1, scale: 1, duration: 0.7, ease: 'back.out(1.5)' },
			'+=0.5'
		);

		// Friday details
		timeline.fromTo(
			fridayDetailsElement,
			{ opacity: 0, x: -30 },
			{ opacity: 1, x: 0, duration: 0.6, ease: 'power2.out' },
			'+=0.2'
		);

		// Ideas list
		timeline.fromTo(
			ideasListElement,
			{ opacity: 0, y: 30 },
			{ opacity: 1, y: 0, duration: 0.6, ease: 'power2.out' },
			'+=0.4'
		);

		// Animer les items de la grille avec stagger from center
		const allItems = gsap.utils.toArray<HTMLElement>('.idea-item');
		timeline.fromTo(
			allItems,
			{ opacity: 0, scale: 0.8, y: 20 },
			{
				opacity: 1,
				scale: 1,
				y: 0,
				duration: 0.5,
				stagger: {
					amount: 0.8,
					from: 'center',
					ease: 'power2.out'
				},
				ease: 'back.out(1.5)'
			},
			'+=0.2'
		);

		// Chips note
		timeline.fromTo(
			chipsNoteElement,
			{ opacity: 0, scale: 0.9 },
			{ opacity: 1, scale: 1, duration: 0.5, ease: 'back.out(1.5)' },
			'+=0.3'
		);

		// Important
		timeline.fromTo(
			importantElement,
			{ opacity: 0, y: 30, scale: 0.95 },
			{ opacity: 1, y: 0, scale: 1, duration: 0.7, ease: 'back.out(1.5)' },
			'+=0.5'
		);

		// Effet parallaxe sur les orbes
		gsap.to('.food-orb-1', {
			scrollTrigger: {
				trigger: foodSection,
				start: 'top 80%',
				end: 'bottom top',
				scrub: 1
			},
			y: -120,
			ease: 'none'
		});

		gsap.to('.food-orb-2', {
			scrollTrigger: {
				trigger: foodSection,
				start: 'top 80%',
				end: 'bottom top',
				scrub: 1.3
			},
			y: 120,
			ease: 'none'
		});

		// Parallaxe sur le titre MAXI salades
		gsap.to(maxiSaladElement, {
			scrollTrigger: {
				trigger: maxiSaladElement,
				start: 'top 80%',
				end: 'bottom top',
				scrub: 2
			},
			y: -30,
			ease: 'none'
		});

		// Animation de rotation subtile sur le MAXI salades au scroll
		gsap.to(maxiSaladElement.querySelector('h3'), {
			scrollTrigger: {
				trigger: maxiSaladElement,
				start: 'top center',
				end: 'bottom center',
				scrub: 3
			},
			rotationY: 5,
			ease: 'none'
		});

		// Parallaxe sur la grille des idées
		gsap.to(ideasListElement.querySelector('.grid'), {
			scrollTrigger: {
				trigger: ideasListElement,
				start: 'top 80%',
				end: 'bottom top',
				scrub: 1.5
			},
			y: -30,
			ease: 'none'
		});

		// Scale effect sur les encadrés au scroll
		gsap.fromTo(
			noteElement,
			{ scale: 0.98 },
			{
				scale: 1,
				scrollTrigger: {
					trigger: noteElement,
					start: 'top 80%',
					end: 'center center',
					scrub: 1
				},
				ease: 'none'
			}
		);

		gsap.fromTo(
			importantElement,
			{ scale: 0.98 },
			{
				scale: 1,
				scrollTrigger: {
					trigger: importantElement,
					start: 'top 80%',
					end: 'center center',
					scrub: 1
				},
				ease: 'none'
			}
		);

		// Animation de glow pulsant sur le MAXI salades quand visible
		ScrollTrigger.create({
			trigger: maxiSaladElement,
			start: 'top center',
			end: 'bottom center',
			onEnter: () => {
				gsap.to(maxiSaladElement.querySelector('.absolute'), {
					opacity: 0.9,
					scale: 1.05,
					duration: 1.5,
					repeat: -1,
					yoyo: true,
					ease: 'sine.inOut'
				});
			},
			onLeave: () => {
				gsap.killTweensOf(maxiSaladElement.querySelector('.absolute'));
			},
			onEnterBack: () => {
				gsap.to(maxiSaladElement.querySelector('.absolute'), {
					opacity: 0.9,
					scale: 1.05,
					duration: 1.5,
					repeat: -1,
					yoyo: true,
					ease: 'sine.inOut'
				});
			},
			onLeaveBack: () => {
				gsap.killTweensOf(maxiSaladElement.querySelector('.absolute'));
			}
		});

		// Pause animations orbes quand section hors viewport
		if (!isMobile && orb1Tween && orb2Tween) {
			ScrollTrigger.create({
				trigger: foodSection,
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
	id="food-section"
	bind:this={foodSection}
	class="relative min-h-screen pt-20 sm:pt-24 pb-20 sm:pb-24 px-6 sm:px-10"
	style="background: linear-gradient(to bottom,
		rgb(15 23 42) 0%,
		rgb(10 16 35) 50%,
		rgb(15 23 42) 100%
	); overflow-x: clip; overflow-y: visible;"
>
	<!-- Orbes lumineux -->
	<div
		class="food-orb-1 absolute right-0 top-20 w-80 h-80 lg:w-96 lg:h-96 rounded-full bg-orange-500/15 blur-xl md:blur-3xl pointer-events-none"
		style="will-change: transform, opacity; z-index: 1;"
	></div>
	<div
		class="food-orb-2 absolute left-0 bottom-40 w-80 h-80 lg:w-96 lg:h-96 rounded-full bg-purple-500/15 blur-xl md:blur-3xl pointer-events-none"
		style="will-change: transform, opacity; z-index: 1;"
	></div>

	<!-- Contenu -->
	<div class="relative z-10 max-w-6xl mx-auto">
		<!-- Titre principal -->
		<div bind:this={titleElement} class="opacity-0 mb-12 sm:mb-16 text-center">
			<h2
				class="text-5xl sm:text-6xl md:text-7xl lg:text-8xl font-black transition-all duration-500 hover:tracking-wide pb-2"
				style="font-family: var(--font-serif); background: linear-gradient(135deg, rgb(221 214 254) 0%, rgb(186 230 253) 50%, rgb(221 214 254) 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text; line-height: 1.2;"
			>
				Pour bien manger
			</h2>
		</div>

		<!-- Intro -->
		<div bind:this={introElement} class="opacity-0 mb-10 sm:mb-12 text-center max-w-4xl mx-auto">
			<p class="text-xl sm:text-2xl text-white/90 font-light leading-relaxed">
				Comme vous pouvez l'imaginer, nourrir 60 personnes de façon simple, bonne et pas trop chère, c'est très galère.
			</p>
		</div>

		<!-- Solution -->
		<div bind:this={solutionElement} class="opacity-0 mb-10 sm:mb-12 text-center max-w-4xl mx-auto">
			<p class="text-lg sm:text-xl text-white/80 font-light leading-relaxed">
				Pour éviter de passer notre week-end dans la cuisine et passer un max de temps à profiter ensemble, on a opté pour une solution efficace :
			</p>
		</div>

		<!-- MAXI salades - gros titre mis en valeur -->
		<div bind:this={maxiSaladElement} class="opacity-0 mb-10 sm:mb-12 text-center" style="perspective: 1000px;">
			<div class="relative inline-block">
				<!-- Glow effect -->
				<div class="absolute -inset-4 bg-gradient-to-r from-green-500/30 via-lime-500/30 to-green-500/30 rounded-3xl blur-2xl opacity-70 animate-pulse-slow"></div>

				<h3
					class="relative text-6xl sm:text-7xl md:text-8xl lg:text-9xl font-black leading-tight"
					style="font-family: var(--font-serif); background: linear-gradient(135deg, rgb(134 239 172) 0%, rgb(187 247 208) 50%, rgb(134 239 172) 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text;"
				>
					MAXI salades<br />
					<span class="text-5xl sm:text-6xl md:text-7xl lg:text-8xl">de pâtes maison !</span>
				</h3>
			</div>
			<p class="text-lg sm:text-xl text-white/80 font-light mt-6">
				Ce sera donc le repas officiel proposé, offert par nos soins.
			</p>
		</div>

		<!-- Idée -->
		<div bind:this={ideaElement} class="opacity-0 mb-8 sm:mb-10 text-center max-w-4xl mx-auto">
			<p class="text-lg sm:text-xl text-white/80 font-light leading-relaxed">
				L'idée, c'est de faire simple, convivial, et de pouvoir manger dans la grande salle tous ensemble.
			</p>
		</div>

		<!-- Option -->
		<div bind:this={optionElement} class="opacity-0 mb-8 sm:mb-10 text-center max-w-4xl mx-auto">
			<p class="text-base sm:text-lg text-white/70 font-light leading-relaxed">
				Bien sûr, si vous préférez autre chose, vous êtes libres de ramener votre propre repas ou d'aller en chercher dans les environs.
			</p>
		</div>

		<!-- Encouragement -->
		<div bind:this={encouragementElement} class="opacity-0 mb-16 sm:mb-20 text-center max-w-4xl mx-auto">
			<p class="text-xl sm:text-2xl text-white/90 font-light leading-relaxed">
				Mais bon, ça serait quand même plus cool qu'on mange tous ensemble en plus c'est gratuit 😄
			</p>
		</div>

		<!-- À noter - Vendredi soir -->
		<div bind:this={noteElement} class="opacity-0 mb-8 sm:mb-10 p-8 sm:p-10 rounded-3xl border border-purple-500/30 bg-gradient-to-br from-purple-900/20 to-transparent backdrop-blur-sm">
			<h4 class="text-2xl sm:text-3xl font-bold text-purple-300 mb-6 text-center" style="font-family: var(--font-serif);">
				À noter
			</h4>

			<div bind:this={fridayDetailsElement} class="opacity-0 space-y-4">
				<p class="text-lg sm:text-xl text-white/90 font-light leading-relaxed">
					Pour notre premier repas ensemble, le vendredi soir, on vous propose un repas / apéro dînatoire.
				</p>
				<p class="text-lg sm:text-xl text-white/90 font-light leading-relaxed">
					L'idée, c'est que chacun apporte quelque chose à partager. Faites au plus simple, que ce soit fait maison ou acheté.
				</p>
			</div>
		</div>

		<!-- Liste d'idées -->
		<div bind:this={ideasListElement} class="opacity-0 mb-6 sm:mb-8">
			<h4 class="text-xl sm:text-2xl font-semibold text-white/90 mb-6 text-center">
				Des idées comme ça :
			</h4>
			<div class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 gap-4 sm:gap-5">
				{#each bringIdeas as idea}
					<div class="idea-item opacity-0 group relative p-5 sm:p-6 rounded-2xl border border-white/10 bg-gradient-to-br from-white/5 to-transparent backdrop-blur-sm hover:border-purple-500/30 transition-all duration-300">
						<!-- Glow hover -->
						<div class="absolute -inset-0.5 bg-gradient-to-br from-purple-500/0 to-blue-500/0 group-hover:from-purple-500/10 group-hover:to-blue-500/10 rounded-2xl blur-lg opacity-0 group-hover:opacity-100 transition-opacity duration-300"></div>

						<div class="relative flex flex-col items-center gap-3 text-center">
							<!-- Emoji -->
							<span class="text-4xl sm:text-5xl group-hover:scale-110 transition-transform duration-300">
								{idea.icon}
							</span>
							<!-- Label -->
							<span class="text-sm sm:text-base text-white/90 font-light">
								{idea.label}
							</span>
						</div>
					</div>
				{/each}
			</div>
		</div>

		<!-- Note chips -->
		<div bind:this={chipsNoteElement} class="opacity-0 mb-16 sm:mb-20 text-center">
			<p class="text-base sm:text-lg text-white/70 font-light italic">
				(on compte sur vous pour éviter l'armée de chips ✌️)
			</p>
		</div>

		<!-- Important -->
		<div bind:this={importantElement} class="opacity-0 p-8 sm:p-10 rounded-3xl border border-orange-500/40 bg-gradient-to-br from-orange-900/20 to-transparent backdrop-blur-sm">
			<div class="flex items-start gap-4 sm:gap-5">
				<div class="text-4xl sm:text-5xl flex-shrink-0">⚠️</div>
				<div>
					<h4 class="text-2xl sm:text-3xl font-bold text-orange-300 mb-4" style="font-family: var(--font-serif);">
						Important
					</h4>
					<p class="text-lg sm:text-xl text-white/90 font-light leading-relaxed">
						Si vous avez une allergie alimentaire ou si vous savez à l'avance que vous ne mangerez pas de salade de pâtes, merci de le dire au plus vite à <span class="font-semibold text-white">Maxence ou Younes</span>, pour qu'on puisse s'organiser au mieux côté logistique.
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
	/* Optimisations GPU */
	.food-orb-1,
	.food-orb-2 {
		transform: translate3d(0, 0, 0);
	}

	.idea-item {
		will-change: opacity, transform;
	}

	@keyframes pulse-slow {
		0%,
		100% {
			opacity: 0.7;
		}
		50% {
			opacity: 0.9;
		}
	}

	.animate-pulse-slow {
		animation: pulse-slow 3s ease-in-out infinite;
	}
</style>
