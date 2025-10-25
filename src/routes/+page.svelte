<script lang="ts">
	import HeroSection from '$lib/components/HeroSection.svelte';
	import IntroSection from '$lib/components/IntroSection.svelte';
	import VenueSection from '$lib/components/VenueSection.svelte';
	import AccommodationSection from '$lib/components/AccommodationSection.svelte';
	import FoodSection from '$lib/components/FoodSection.svelte';
	import DrinkSection from '$lib/components/DrinkSection.svelte';
	import LuggageSection from '$lib/components/LuggageSection.svelte';
	import TransportSection from '$lib/components/TransportSection.svelte';
	import PriceSection from '$lib/components/PriceSection.svelte';
	import CTASection from '$lib/components/CTASection.svelte';
	import DownloadSection from '$lib/components/DownloadSection.svelte';
	import SectionSeparator from '$lib/components/SectionSeparator.svelte';
</script>

<HeroSection />

<SectionSeparator variant="waves" colors={['#fb7185', '#14b8a6', '#67e8f9']} />

<IntroSection />

<SectionSeparator variant="sparkles" colors={['#10b981', '#fb7185', '#34d399']} />

<VenueSection />

<SectionSeparator variant="gradient" colors={['#06b6d4', '#fb923c', '#fbbf24']} />

<AccommodationSection />

<SectionSeparator variant="dots" colors={['#84cc16', '#fbbf24', '#34d399']} />

<FoodSection />

<SectionSeparator variant="particles" colors={['#a78bfa', '#60a5fa', '#fbbf24']} />

<DrinkSection />

<SectionSeparator variant="orbit" colors={['#d946ef', '#facc15', '#fb7185']} />

<LuggageSection />

<SectionSeparator variant="waves" colors={['#a3e635', '#fbbf24', '#fb923c']} />

<TransportSection />

<SectionSeparator variant="sparkles" colors={['#a78bfa', '#ec4899', '#fbbf24']} />

<PriceSection />

<SectionSeparator variant="gradient" colors={['#10b981', '#ec4899', '#34d399']} />

<CTASection />

<SectionSeparator variant="dots" colors={['#0ea5e9', '#fbbf24', '#60a5fa']} />

<DownloadSection />
