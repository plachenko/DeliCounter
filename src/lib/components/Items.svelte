<script>
	import Items from '$lib/DeliItems.json';
	import { onMount } from 'svelte';

	onMount(() => {
		console.log(Items);
	});
</script>

<div class="flex gap-1 flex-col absolute h-full w-full overflow-y-auto">
	<div class="flex gap-2">
		{#each Object.keys(Items[0]) as prop}
			<div>{prop}</div>
		{/each}
	</div>

	{#each Items as item}
		<div class="bg-slate-300 flex gap-2">
			{#each Object.keys(item) as prop, idx}
				{#if ['description', 'shelf price'].includes(prop)}
					<div class={`p-2 ${idx == 1 ? 'bg-red-300' : ''}`}>
						{isNaN(item[prop]) ? item[prop] : parseInt(item[prop]) / 100}
					</div>
				{/if}
			{/each}
		</div>
	{/each}
</div>
