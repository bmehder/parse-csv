<script>
	import './layout.css'
	import favicon from '$lib/assets/favicon.svg'
	import Header from '$lib/components/Header.svelte'
	import Footer from '$lib/components/Footer.svelte'

	let { children } = $props()
</script>

<svelte:head><link rel="icon" href={favicon} /></svelte:head>

<div class="grid block-svh grid-rows-[auto_1fr_auto]">
	<Header />
	<main>
		{@render children()}
	</main>
	<Footer />
</div>
