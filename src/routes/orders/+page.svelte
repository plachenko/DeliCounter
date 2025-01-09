<script>
	import { onMount } from 'svelte';
	let orders = $state([]);

	onMount(() => {
		for (let i = 0; i <= 12; i++) {
			orders.push({ name: 'order', items: [] });
		}
	});
</script>

<div class="flex flex-col bg-blue-300 w-full h-full">
	<div class="flex items-center p-1">
		<h3 class="text-2xl text-center flex-1">Orders</h3>
		<span>{orders.length + 1}</span>
	</div>
	<div class="overflow-y-auto bg-red-300 flex-1">
		{#each orders as order, idx}
			<button class="w-full p-2 flex border-b-2 bg-slate-300">
				<span class="w-full text-left">
					{order.name}
				</span>
			</button>
		{/each}
	</div>
</div>
