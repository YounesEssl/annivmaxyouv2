<script lang="ts">
	import { onMount } from 'svelte';
	import { gsap } from 'gsap';
	import { ScrollTrigger } from 'gsap/ScrollTrigger';

	let heroSection: HTMLElement;
	let typewriterText: HTMLElement;
	let cursor: HTMLElement;
	let scrollButton: HTMLElement;
	let isMobile = $state(false);
	let showParticles = $state(false);

	const baseText = "Vous êtes invités à célébrer un moment ";
	const words = ["unique...", "inoubliable...", "immanquable..."];

	onMount(() => {
		gsap.registerPlugin(ScrollTrigger);

		// Detect mobile
		isMobile = window.innerWidth < 768;
		showParticles = window.innerWidth >= 768;

		// Mobile check on resize
		const handleResize = () => {
			const wasMobile = isMobile;
			isMobile = window.innerWidth < 768;
			showParticles = window.innerWidth >= 768;

			// Si on passe de mobile à desktop ou vice versa, on pourrait recharger les animations
			if (wasMobile !== isMobile) {
				// Optionnel: recharger les animations
			}
		};
		window.addEventListener('resize', handleResize);

		// Animation des orbes (desktop uniquement)
		if (!isMobile) {
			gsap.to('.orb-1', {
				x: 50,
				y: 30,
				scale: 1.1,
				duration: 8,
				repeat: -1,
				yoyo: true,
				ease: 'sine.inOut'
			});

			gsap.to('.orb-2', {
				x: -30,
				y: -50,
				scale: 1.15,
				duration: 10,
				repeat: -1,
				yoyo: true,
				ease: 'sine.inOut'
			});

			// Animation des particules
			gsap.utils.toArray<HTMLElement>('.particle').forEach((particle, i) => {
				gsap.to(particle, {
					y: `random(-100, 100)`,
					x: `random(-100, 100)`,
					duration: `random(3, 6)`,
					repeat: -1,
					yoyo: true,
					ease: 'sine.inOut',
					delay: i * 0.1
				});
			});
		}

		// Effet parallaxe sur les orbes de la HeroSection au scroll
		gsap.to('.orb-1', {
			scrollTrigger: {
				trigger: heroSection,
				start: 'top top',
				end: 'bottom top',
				scrub: 1
			},
			y: 150,
			ease: 'none'
		});

		gsap.to('.orb-2', {
			scrollTrigger: {
				trigger: heroSection,
				start: 'top top',
				end: 'bottom top',
				scrub: 1.5
			},
			y: -150,
			ease: 'none'
		});

		// Fade out du contenu de la HeroSection au scroll pour transition fluide
		gsap.to('.hero-content', {
			scrollTrigger: {
				trigger: heroSection,
				start: 'top top',
				end: 'bottom top',
				scrub: 1
			},
			opacity: 0,
			y: -50,
			ease: 'none'
		});

		// Animation d'entrée de la date
		gsap.from('.hero-date', {
			y: -50,
			opacity: 0,
			duration: 1,
			ease: 'power3.out'
		});

		// Animation typewriter - vitesses plus rapides et naturelles
		const typeSpeed = isMobile ? 40 : 50;
		const eraseSpeed = isMobile ? 25 : 35;
		const timeline = gsap.timeline();

		// Fonction pour trouver le préfixe commun
		function getCommonPrefix(str1: string, str2: string): string {
			let i = 0;
			while (i < str1.length && i < str2.length && str1[i] === str2[i]) {
				i++;
			}
			return str1.substring(0, i);
		}

		// Animation typewriter intelligente - une seule fois
		function typewriterAnimation() {
			let currentWordIndex = 0;
			let buttonShown = false;

			function typeWord(isLastWord: boolean) {
				const word = words[currentWordIndex];
				const nextWord = words[(currentWordIndex + 1) % words.length];
				const commonPrefix = getCommonPrefix(word, nextWord);
				const charsToErase = isLastWord ? 0 : word.length - commonPrefix.length;

				// Écrire le mot avec variation naturelle
				for (let i = 0; i <= word.length; i++) {
					const variation = Math.random() * 0.3 + 0.85; // Variation entre 0.85 et 1.15
					timeline.to(
						typewriterText,
						{
							duration: (typeSpeed * variation) / 1000,
							onStart: () => {
								typewriterText.textContent = baseText + word.substring(0, i);
							}
						},
						`+=${(typeSpeed * variation) / 1000}`
					);
				}

				// Faire apparaître le bouton après le premier mot
				if (currentWordIndex === 0 && !buttonShown) {
					timeline.to(
						scrollButton,
						{
							opacity: 1,
							y: 0,
							duration: 0.5,
							ease: 'power2.out',
							onComplete: () => {
								// Lancer les animations continues du bouton
								// Animation bounce de la flèche (desktop uniquement)
								if (!isMobile) {
									gsap.to('.scroll-arrow', {
										y: 10,
										duration: 1,
										repeat: -1,
										yoyo: true,
										ease: 'power1.inOut'
									});
								}

								// Animation subtile du texte (pulse léger)
								gsap.to('.scroll-text', {
									opacity: 0.7,
									duration: 2,
									repeat: -1,
									yoyo: true,
									ease: 'sine.inOut'
								});
							}
						},
						'-=0.2'
					);
					buttonShown = true;
				}

				// Pause plus courte
				timeline.to({}, { duration: isLastWord ? 0 : 0.8 });

				// Effacer seulement la partie non commune (sauf pour le dernier mot)
				if (!isLastWord) {
					for (let i = 0; i < charsToErase; i++) {
						const variation = Math.random() * 0.3 + 0.85;
						timeline.to(
							typewriterText,
							{
								duration: (eraseSpeed * variation) / 1000,
								onStart: () => {
									const currentLength = word.length - i;
									typewriterText.textContent = baseText + word.substring(0, currentLength - 1);
								}
							},
							`+=${(eraseSpeed * variation) / 1000}`
						);
					}
				}

				currentWordIndex = (currentWordIndex + 1) % words.length;
			}

			// Animer chaque mot une seule fois
			for (let i = 0; i < words.length; i++) {
				typeWord(i === words.length - 1);
			}
		}

		typewriterAnimation();

		// Animation du curseur
		gsap.to(cursor, {
			opacity: 0,
			duration: 0.5,
			repeat: -1,
			yoyo: true,
			ease: 'power1.inOut'
		});

		return () => {
			window.removeEventListener('resize', handleResize);
			ScrollTrigger.getAll().forEach(trigger => trigger.kill());
		};
	});

	function handleScrollClick() {
		const introSection = document.querySelector('#intro-section');
		if (introSection) {
			introSection.scrollIntoView({ behavior: 'smooth' });
		}
	}
</script>

<section
	bind:this={heroSection}
	class="relative w-full"
	style="height: 100dvh; background: linear-gradient(180deg,
		rgb(2 6 23) 0%,
		rgb(74 4 78) 15%,
		rgb(24 51 71) 35%,
		rgb(15 23 42) 70%
	); overflow-x: clip; overflow-y: visible;"
>
	<!-- Orbes lumineux -->
	<div
		class="orb-1 absolute -right-20 -top-20 h-96 w-96 rounded-full bg-fuchsia-500/22 blur-xl md:blur-3xl pointer-events-none"
		style="z-index: 5;"
	></div>
	<div
		class="orb-2 absolute -bottom-20 -left-20 h-96 w-96 rounded-full bg-cyan-500/22 blur-xl md:blur-3xl pointer-events-none"
		style="z-index: 5;"
	></div>

	<!-- Particules flottantes (desktop uniquement) -->
	{#if showParticles}
		{#each Array(20) as _, i}
			<div
				class="particle absolute h-2 w-2 rounded-full bg-white/10"
				style={`left: ${Math.random() * 100}%; top: ${Math.random() * 100}%;`}
			></div>
		{/each}
	{/if}

	<!-- Contenu -->
	<div class="hero-content relative z-10 flex h-full flex-col items-center justify-between py-12 px-4">
		<!-- Date -->
		<div class="hero-date text-center">
			<p class="text-xs md:text-sm uppercase tracking-[0.4em] text-purple-300/70 font-light" style="letter-spacing: 0.4em;">
				27 - 29 Mars 2025
			</p>
		</div>

		<!-- Texte typewriter -->
		<div class="flex-1 flex items-center justify-center px-4">
			<div class="text-center max-w-5xl">
				<h1 class="text-2xl sm:text-3xl md:text-4xl lg:text-5xl font-black text-white leading-[1.2] tracking-tight" style="font-family: var(--font-serif);">
					<span bind:this={typewriterText}>{baseText}</span>
					<span bind:this={cursor} class="inline-block w-[3px] bg-white ml-2" style="height: 0.85em; vertical-align: baseline; transform: translateY(0.05em);"></span>
				</h1>
			</div>
		</div>

		<!-- Bouton scroll -->
		<div bind:this={scrollButton} class="opacity-0 translate-y-4">
			<button
				onclick={handleScrollClick}
				class="group flex flex-col items-center gap-4 text-white/60 hover:text-white transition-all duration-500"
			>
				<span class="scroll-text text-xs md:text-sm tracking-[0.2em] uppercase font-light">Découvrir la suite</span>
				<svg
					class="scroll-arrow w-5 h-5 md:w-6 md:h-6 opacity-70 group-hover:opacity-100 transition-opacity"
					fill="none"
					stroke="currentColor"
					viewBox="0 0 24 24"
				>
					<path
						stroke-linecap="round"
						stroke-linejoin="round"
						stroke-width="1.5"
						d="M19 14l-7 7m0 0l-7-7m7 7V3"
					></path>
				</svg>
			</button>
		</div>
	</div>

</section>

<style>
	/* Optimisations GPU */
	.orb-1,
	.orb-2,
	.particle {
		will-change: transform;
		transform: translateZ(0);
	}

	.scroll-arrow {
		will-change: transform;
	}
</style>
