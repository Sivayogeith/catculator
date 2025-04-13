<script lang="ts">
	import 'bootstrap/dist/css/bootstrap.css';
	import scriptSrc from 'bootstrap/dist/js/bootstrap.bundle.js?url';
	interface Props {
		children?: import('svelte').Snippet;
	}

	let { children }: Props = $props();
</script>

<style>
	:root {
		--bs-primary: #910b6b;
		--bs-primary-rgb: 145, 11, 107;
	}
</style>

<svelte:head>
	<script src={scriptSrc}></script>
</svelte:head>

{@render children?.()}