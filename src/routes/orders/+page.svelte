<script>
	import { onMount } from 'svelte';
	let orders = $state([]);
	let showOrder = $state(false);

	let curOrder = $state(null);

	let options = $state(['back', 'cancel order', 'finish order']);

	onMount(() => {
		orders = JSON.parse(window.localStorage.getItem('allOrders')) || [];
	});

	function setAllOrders() {
		window.localStorage.setItem('allOrders', JSON.stringify(orders));
	}
</script>

{#if showOrder}
	<div class="absolute top-0 left-0 w-full h-full bg-slate-100">
		<div class="flex flex-col w-full h-full">
			<span class="text-2xl w-full text-center">
				{orders[curOrder].name}
			</span>
			<div class="h-full w-full flex-1">
				{#each orders[curOrder].items as item}
					<div class="bg-slate-300">{item}</div>
				{/each}
			</div>
			<div class="flex gap-1 p-1 w-full">
				{#each options as opt, idx}
					<button
						onclick={() => {
							switch (idx) {
								case 0:
									break;
								case 1:
									break;
								case 2:
									orders[curOrder].finished = true;
									break;
							}
							showOrder = false;
							curOrder = null;
						}}
						class="bg-slate-400 rounded-md p-2 w-full flex-1">{opt}</button
					>
				{/each}
			</div>
		</div>
	</div>
{/if}

<div class="flex flex-col w-full h-full">
	<div class="flex items-center p-1 border-b-2 border-dashed">
		<h3 class="text-2xl text-center flex-1">Orders</h3>
		{#if orders.length}
			<span>{orders.length}</span>
		{/if}
	</div>
	<div class="overflow-y-auto flex-1">
		{#if orders.length}
			{#each orders as order, idx}
				<button
					onclick={() => {
						curOrder = idx;
						showOrder = true;
					}}
					class="w-full p-2 flex border-b-2 bg-slate-300"
				>
					<span class="w-full text-left flex-1">
						{order.name}
					</span>
					<span class="w-full flex-1 text-right"
						>{new Date(order.dateStart).toLocaleTimeString([], {
							hour: '2-digit',
							minute: '2-digit'
						})}</span
					>
				</button>
			{/each}
		{:else}
			<div class="w-full h-full flex justify-center items-center">
				<span class="opacity-30">No Orders</span>
			</div>
		{/if}
	</div>
	<div class="p-1 w-full flex gap-1">
		<a href="/" class="bg-red-300 p-2 rounded-md w-full text-center">Back</a>
		{#if orders.length}
			<button
				onclick={() => {
					orders = [];
					setAllOrders();
				}}
				class="bg-red-300 p-2 rounded-md w-full text-center">clear orders</button
			>
		{/if}
	</div>
</div>
