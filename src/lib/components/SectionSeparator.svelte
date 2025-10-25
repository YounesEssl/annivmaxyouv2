<script lang="ts">
	import { onMount } from 'svelte';
	import { gsap } from 'gsap';
	import { ScrollTrigger } from 'gsap/ScrollTrigger';

	interface Props {
		variant?: 'waves' | 'dots' | 'gradient' | 'sparkles' | 'particles' | 'orbit';
		colors?: string[];
	}

	let { variant = 'waves', colors = ['#a78bfa', '#60a5fa', '#34d399'] }: Props = $props();

	let separatorElement: HTMLElement;
	let isMobile = $state(false);

	onMount(() => {
		gsap.registerPlugin(ScrollTrigger);

		const mediaQuery = window.matchMedia('(max-width: 767px)');
		isMobile = mediaQuery.matches;

		const handleResize = () => {
			isMobile = mediaQuery.matches;
		};
		mediaQuery.addEventListener('change', handleResize);

		// Animation à l'apparition
		gsap.fromTo(
			separatorElement,
			{ opacity: 0, scaleX: 0 },
			{
				opacity: 1,
				scaleX: 1,
				duration: 1.2,
				ease: 'power2.out',
				scrollTrigger: {
					trigger: separatorElement,
					start: 'top 80%',
					toggleActions: 'play none none reverse'
				}
			}
		);

		// Animations spécifiques selon le variant
		if (variant === 'waves') {
			const wave1 = separatorElement.querySelector('.wave-1');
			const wave2 = separatorElement.querySelector('.wave-2');
			if (wave1 && wave2) {
				gsap.to(wave1, {
					x: -20,
					duration: 3,
					repeat: -1,
					yoyo: true,
					ease: 'sine.inOut'
				});
				gsap.to(wave2, {
					x: 20,
					duration: 4,
					repeat: -1,
					yoyo: true,
					ease: 'sine.inOut'
				});
			}
		} else if (variant === 'dots') {
			const dots = gsap.utils.toArray<HTMLElement>('.separator-dot');
			dots.forEach((dot, i) => {
				gsap.to(dot, {
					y: -15,
					duration: 1.5,
					repeat: -1,
					yoyo: true,
					ease: 'sine.inOut',
					delay: i * 0.2
				});
			});
		} else if (variant === 'gradient') {
			const line = separatorElement.querySelector('.gradient-line');
			if (line) {
				gsap.to(line, {
					backgroundPosition: '200% center',
					duration: 3,
					repeat: -1,
					ease: 'none'
				});
			}
		} else if (variant === 'sparkles') {
			const sparkles = gsap.utils.toArray<HTMLElement>('.sparkle');
			sparkles.forEach((sparkle, i) => {
				gsap.fromTo(
					sparkle,
					{ scale: 0, opacity: 0 },
					{
						scale: 1,
						opacity: 1,
						duration: 1,
						repeat: -1,
						yoyo: true,
						ease: 'power2.inOut',
						delay: i * 0.3
					}
				);
			});
		} else if (variant === 'particles') {
			const particles = gsap.utils.toArray<HTMLElement>('.float-particle');
			particles.forEach((particle, i) => {
				gsap.to(particle, {
					y: 'random(-30, 30)',
					x: 'random(-20, 20)',
					duration: 'random(2, 4)',
					repeat: -1,
					yoyo: true,
					ease: 'sine.inOut',
					delay: i * 0.2
				});
			});
		} else if (variant === 'orbit') {
			const orbs = gsap.utils.toArray<HTMLElement>('.orbit-dot');
			orbs.forEach((orb, i) => {
				gsap.to(orb, {
					rotation: 360,
					duration: 8,
					repeat: -1,
					ease: 'none',
					transformOrigin: 'center center'
				});
				gsap.to(orb, {
					scale: 1.3,
					duration: 2,
					repeat: -1,
					yoyo: true,
					ease: 'sine.inOut',
					delay: i * 0.5
				});
			});
		}

		return () => {
			mediaQuery.removeEventListener('change', handleResize);
			ScrollTrigger.getAll().forEach((trigger) => trigger.kill());
		};
	});
</script>

<div
	bind:this={separatorElement}
	class="separator-container relative w-full h-24 flex items-center justify-center opacity-0 overflow-x-hidden"
	style="transform-origin: center;"
>
	{#if variant === 'waves'}
		<div class="relative w-full max-w-4xl h-full flex items-center justify-center">
			<svg class="wave-1 absolute w-full h-16" viewBox="0 0 1200 100" preserveAspectRatio="none">
				<path
					d="M0,50 Q300,20 600,50 T1200,50"
					fill="none"
					stroke={colors[0]}
					stroke-width="2"
					opacity="0.6"
				/>
			</svg>
			<svg class="wave-2 absolute w-full h-16" viewBox="0 0 1200 100" preserveAspectRatio="none">
				<path
					d="M0,50 Q300,80 600,50 T1200,50"
					fill="none"
					stroke={colors[1]}
					stroke-width="2"
					opacity="0.4"
				/>
			</svg>
		</div>
	{:else if variant === 'dots'}
		<div class="flex items-center gap-4">
			{#each Array(7) as _, i}
				<div
					class="separator-dot w-3 h-3 rounded-full"
					style="background: {colors[i % colors.length]}; opacity: {1 - i * 0.1};"
				></div>
			{/each}
		</div>
	{:else if variant === 'gradient'}
		<div class="w-full max-w-4xl">
			<div
				class="gradient-line h-1 rounded-full"
				style="background: linear-gradient(90deg, transparent 0%, {colors[0]} 25%, {colors[1]} 50%, {colors[2]} 75%, transparent 100%); background-size: 200% 100%;"
			></div>
		</div>
	{:else if variant === 'sparkles'}
		<div class="flex items-center gap-12">
			<div class="flex-1 h-px bg-white/10"></div>
			{#each Array(5) as _, i}
				<div class="sparkle relative">
					<div
						class="w-2 h-2 rounded-full"
						style="background: {colors[i % colors.length]}; box-shadow: 0 0 10px {colors[i % colors.length]};"
					></div>
					<div
						class="absolute inset-0 w-2 h-2 rounded-full blur-sm"
						style="background: {colors[i % colors.length]};"
					></div>
				</div>
			{/each}
			<div class="flex-1 h-px bg-white/10"></div>
		</div>
	{:else if variant === 'particles'}
		<div class="relative w-full max-w-3xl h-full flex items-center justify-center">
			<div class="h-px w-full bg-gradient-to-r from-transparent via-white/20 to-transparent"></div>
			{#each Array(12) as _, i}
				<div
					class="float-particle absolute w-1.5 h-1.5 rounded-full"
					style="background: {colors[i % colors.length]}; left: {(i / 12) * 100}%; opacity: {Math.random() * 0.8 + 0.2};"
				></div>
			{/each}
		</div>
	{:else if variant === 'orbit'}
		<div class="relative w-64 h-24 flex items-center justify-center">
			<div class="absolute w-full h-px bg-white/10"></div>
			{#each Array(3) as _, i}
				<div
					class="orbit-dot absolute w-4 h-4 rounded-full"
					style="background: {colors[i % colors.length]}; left: {30 + i * 30}%; box-shadow: 0 0 15px {colors[i % colors.length]};"
				></div>
			{/each}
		</div>
	{/if}
</div>

<style>
	.separator-container {
		will-change: transform, opacity;
	}

	.separator-dot,
	.sparkle,
	.float-particle,
	.orbit-dot {
		will-change: transform, opacity;
	}

	.gradient-line {
		will-change: background-position;
	}

	.wave-1,
	.wave-2 {
		will-change: transform;
	}
</style>
