<script lang="ts">
	import '../app.css';
	let { children } = $props();
</script>

<svelte:head>
	<link rel="icon" href="src\lib\components\favicon.png" />
</svelte:head>

{@render children()}
