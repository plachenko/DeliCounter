<script>
	import '../app.css';
	import terminal from 'virtual:terminal';
	import { onMount } from 'svelte';

	onMount(() => {
		globalThis.console = terminal;
	});
</script>

<slot />
