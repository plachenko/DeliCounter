<script>
	import { onMount } from 'svelte';

	let current = $state(null);

	let links = [
		{ name: 'Dashboard', link: '/admin' },
		{ name: 'Items', link: '/admin/add' }
	];

	onMount(() => {
		current = links[0];
	});
</script>

<div class="w-full h-full flex flex-col bg-slate-100">
	<div class="flex gap-2 bg-slate-300 border-b-2 w-full">
		{#each links as link}
			<a
				aria-label={link.name}
				onclick={() => {
					current = link;
				}}
				href={link.link}
				class={`text-black p-2 hover:bg-slate-600 ${current?.name == link.name ? 'border-slate-600 border-b-2 font-bold' : ''}`}
				>{link.name}</a
			>
		{/each}
		<a class="absolute right-2 top-1 bg-red-400 p-1 rounded-md" href="/">back</a>
	</div>

	<slot />
</div>
