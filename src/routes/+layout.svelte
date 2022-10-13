<script>
	// @ts-nocheck

	import Nav from '../components/nav.svelte';
	import Footer from '../components/footer.svelte';
	import SvgGraphics from '../components/svg-graphics.svelte';
	import ContactForm from '../components/contact-form.svelte';
	import { onDestroy } from 'svelte';
	import { onMount } from 'svelte';
	export let data;

	let ready = true;
</script>

<Nav {...data.navigation} />

<main>
	<ContactForm />
	<slot />
</main>

<Footer {...data.footer} bind:showFooter="{ready}" />

<SvgGraphics />

<style>
	@import '../styles/base.css';
	@import '../styles/grid.css';
</style>
