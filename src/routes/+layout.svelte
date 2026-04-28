<script>
	import './layout.css';
	import favicon from '$lib/assets/moon.png';

	let { children } = $props();
</script>

<svelte:head><link rel="icon" href={favicon} /> <title>mother's day | BES-AR Kominfo!</title></svelte:head>
{@render children()}
