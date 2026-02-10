<script lang="ts">
	import { onMount } from 'svelte';
	import { gsap } from 'gsap';
	import { ScrollTrigger } from 'gsap/ScrollTrigger';

	let djSection: HTMLElement;
	let titleElement: HTMLElement;
	let imageElement: HTMLElement;
	let bioElement: HTMLElement;
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
			orb1Tween = gsap.to('.dj-orb-1', {
				scale: 1.2,
				opacity: 0.3,
				duration: 5,
				repeat: -1,
				yoyo: true,
				ease: 'sine.inOut'
			});

			orb2Tween = gsap.to('.dj-orb-2', {
				scale: 1.15,
				opacity: 0.25,
				duration: 6,
				delay: 1,
				repeat: -1,
				yoyo: true,
				ease: 'sine.inOut'
			});
		}

		// Titre
		gsap.fromTo(
			titleElement,
			{ opacity: 0, y: 50, scale: 0.9 },
			{
				opacity: 1,
				y: 0,
				scale: 1,
				duration: 0.8,
				ease: 'back.out(1.5)',
				scrollTrigger: {
					trigger: titleElement,
					start: 'top 70%',
					toggleActions: 'play none none reverse'
				}
			}
		);

		// Image
		gsap.fromTo(
			imageElement,
			{ opacity: 0, x: -50, rotateY: 15 },
			{
				opacity: 1,
				x: 0,
				rotateY: 0,
				duration: 0.9,
				ease: 'power3.out',
				scrollTrigger: {
					trigger: imageElement,
					start: 'top 65%',
					toggleActions: 'play none none reverse'
				}
			}
		);

		// Bio
		const bioLines = bioElement.querySelectorAll('.bio-line');
		gsap.fromTo(
			bioLines,
			{ opacity: 0, x: 40 },
			{
				opacity: 1,
				x: 0,
				duration: 0.6,
				stagger: 0.15,
				ease: 'power2.out',
				scrollTrigger: {
					trigger: bioElement,
					start: 'top 65%',
					toggleActions: 'play none none reverse'
				}
			}
		);

		// Parallaxe orbes
		gsap.to('.dj-orb-1', {
			scrollTrigger: {
				trigger: djSection,
				start: 'top 80%',
				end: 'bottom top',
				scrub: 1
			},
			y: -100,
			ease: 'none'
		});

		gsap.to('.dj-orb-2', {
			scrollTrigger: {
				trigger: djSection,
				start: 'top 80%',
				end: 'bottom top',
				scrub: 1.2
			},
			y: 100,
			ease: 'none'
		});

		// Pause animations orbes hors viewport
		if (!isMobile && orb1Tween && orb2Tween) {
			ScrollTrigger.create({
				trigger: djSection,
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
	id="dj-section"
	bind:this={djSection}
	class="relative min-h-screen pt-20 sm:pt-24 pb-20 sm:pb-24 px-6 sm:px-10"
	style="background: linear-gradient(to bottom,
		rgb(15 23 42) 0%,
		rgb(5 15 25) 50%,
		rgb(15 23 42) 100%
	);"
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

	<!-- Orbes lumineux verts -->
	<div
		class="dj-orb-1 absolute left-0 top-20 w-96 h-96 lg:w-[500px] lg:h-[500px] rounded-full bg-green-500/20 blur-3xl pointer-events-none"
		style="will-change: transform, opacity; z-index: 1;"
	></div>
	<div
		class="dj-orb-2 absolute right-0 bottom-40 w-96 h-96 lg:w-[500px] lg:h-[500px] rounded-full bg-emerald-500/15 blur-3xl pointer-events-none"
		style="will-change: transform, opacity; z-index: 1;"
	></div>

	<!-- Éléments décoratifs -->
	<div class="absolute top-1/4 right-1/4 w-24 h-24 rounded-full bg-gradient-to-br from-green-400/10 to-lime-500/10 blur-2xl pointer-events-none" style="z-index: 1;"></div>
	<div class="absolute bottom-1/3 left-1/5 w-20 h-20 rounded-lg bg-gradient-to-tr from-emerald-400/12 to-green-500/12 blur-xl pointer-events-none rotate-12" style="z-index: 1;"></div>

	<!-- Contenu -->
	<div class="relative z-10 max-w-7xl mx-auto">
		<!-- Titre principal -->
		<div bind:this={titleElement} class="opacity-0 mb-12 sm:mb-16 text-center">
			<p class="text-xl sm:text-2xl text-white/50 font-light mb-4 tracking-widest uppercase">
				Samedi soir
			</p>
			<p class="text-lg sm:text-xl md:text-2xl text-white/80 font-light leading-relaxed max-w-3xl mx-auto mb-8">
				La soirée d'anniv aura lieu de samedi soir à partir de <span class="font-semibold" style="color: rgb(163 230 53);">21h30</span> jusqu'au bout de la nuit. Pour l'occasion nous sommes fiers de vous annoncer le <span class="font-bold" style="color: rgb(74 222 128);">guest</span> de la soirée.
			</p>
			<h2
				class="text-6xl sm:text-7xl md:text-8xl lg:text-9xl font-black pb-2"
				style="font-family: var(--font-serif); background: linear-gradient(135deg, rgb(74 222 128) 0%, rgb(34 197 94) 50%, rgb(163 230 53) 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text; line-height: 1.2; text-shadow: 0 0 60px rgba(74, 222, 128, 0.3);"
			>
				DJ Raph
			</h2>
		</div>

		<!-- Image + Bio -->
		<div class="grid grid-cols-1 lg:grid-cols-2 gap-10 lg:gap-16 items-center mb-16 sm:mb-20">
			<!-- Image -->
			<div bind:this={imageElement} class="opacity-0">
				<div class="relative">
					<!-- Glow -->
					<div class="absolute -inset-3 bg-gradient-to-r from-green-500/30 via-emerald-500/25 to-lime-500/30 rounded-3xl blur-2xl opacity-70 animate-pulse-slow"></div>

					<div class="relative">
						<img
							src="/djraph.jpeg"
							alt="DJ Raph en action"
							class="w-full aspect-square object-cover rounded-3xl border-2 border-green-500/30 shadow-2xl"
							loading="lazy"
						/>
					</div>
				</div>
			</div>

			<!-- Bio -->
			<div bind:this={bioElement}>
				<div class="space-y-6">
					<div class="bio-line opacity-0">
						<p class="text-xl sm:text-2xl md:text-3xl text-white/90 font-light leading-relaxed">
							Vous le connaissiez pour ses <span class="font-semibold" style="color: rgb(74 222 128);">centres brossés au deuxième poteau</span> et sa technique hors norme. Après avoir passé sa carrière à infliger ses <span class="font-semibold" style="color: rgb(163 230 53);">playlists douteuses</span> à ses coéquipiers dans les vestiaires, l'ex-footballeur pro a enfin décidé qu'il était temps de <span class="font-semibold" style="color: rgb(134 239 172);">traumatiser un public plus large</span>.
						</p>
					</div>

					<div class="bio-line opacity-0">
						<p class="text-lg sm:text-xl md:text-2xl text-white/80 font-light leading-relaxed">
							Considéré comme une <span class="font-semibold" style="color: rgb(74 222 128);">légende du mix</span> par sa propre mère et comme une véritable <span class="font-semibold" style="color: rgb(163 230 53);">nuisance sonore</span> par ses anciens coachs, <span class="font-bold" style="color: rgb(74 222 128);">DJ Raph</span> débarque pour son
							<span
								class="font-bold"
								style="background: linear-gradient(135deg, rgb(74 222 128) 0%, rgb(250 204 21) 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text;"
							>tout premier set officiel</span>. Au programme : <span class="font-semibold" style="color: rgb(250 204 21);">Rap FR</span>, <span class="font-semibold" style="color: rgb(163 230 53);">House</span>, <span class="font-semibold" style="color: rgb(74 222 128);">Tech</span> et <span class="font-semibold" style="color: rgb(134 239 172);">Pop</span>.
						</p>
					</div>

					<div class="bio-line opacity-0">
						<p class="text-lg sm:text-xl md:text-2xl text-white/80 font-light leading-relaxed">
							Est-ce qu'il va vraiment assurer ? C'est ce qu'il a promis sur son CV. Est-ce qu'on le croit ? <span class="font-semibold" style="color: rgb(163 230 53);">Absolument pas</span>, mais c'est justement ça qui est <span class="font-semibold" style="color: rgb(74 222 128);">beau</span>.
						</p>
					</div>

				</div>
			</div>
		</div>

	</div>

	<!-- Dégradé de transition -->
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
	.dj-orb-1,
	.dj-orb-2 {
		transform: translate3d(0, 0, 0);
	}

	.bio-line {
		will-change: opacity, transform;
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
