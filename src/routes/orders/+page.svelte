<script>
	import { onMount } from 'svelte';
	let orders = $state([]);

	onMount(() => {
		orders = window.localStorage.getItem('orders') || [];
		/*
		for (let i = 0; i <= 12; i++) {
			orders.push({ name: 'order', items: [] });
		}
    */
	});
</script>

<div class="flex flex-col w-full h-full">
	<div class="flex items-center p-1 border-b-2 border-dashed">
		<h3 class="text-2xl text-center flex-1">Orders</h3>
		{#if orders.length}
			<span>{orders.length + 1}</span>
		{/if}
	</div>
	<div class="overflow-y-auto flex-1">
		{#if orders.length}
			{#each orders as order, idx}
				<button class="w-full p-2 flex border-b-2 bg-slate-300">
					<span class="w-full text-left">
						{order.name}
					</span>
				</button>
			{/each}
		{:else}
			<div class="w-full h-full flex justify-center items-center">
				<span class="opacity-30">No Orders</span>
			</div>
		{/if}
	</div>
	<div class="p-1 w-full flex">
		<a href="/" class="bg-red-300 p-2 rounded-md w-full text-center">start order</a>
	</div>
</div>
