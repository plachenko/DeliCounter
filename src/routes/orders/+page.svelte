<script>
	import { onMount } from 'svelte';
	let orders = $state([]);
	let showOrder = $state(false);

	let curOrder = $state(null);

	let options = $state(['back', 'cancel order']);

	$effect(() => {
		if (!orders[curOrder]?.finished) {
			options[2] = 'finish order';
		} else {
			options[2] = 'reopen order';
		}
	});

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
									if (orders[curOrder].finished == null) {
										orders[curOrder].finished = new Date();
									} else {
										console.log('resetting.');
										orders[curOrder].finished = null;
										orders[curOrder].reopened.push(new Date());
									}
									setAllOrders();
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
{:else}
	<div class="flex flex-col w-full h-full">
		<div class="flex items-center p-1 border-b-2 border-dashed">
			<h3 class="text-2xl text-center flex-1">Orders</h3>
			{#if orders.length}
				<span>{orders.length}</span>
			{/if}
		</div>
		<div class="flex-1 flex-col flex relative">
			<div class="h-full flex flex-col flex-1 overflow-hidden">
				<div class="bg-red-400 h-[200px] w-full relative overflow-x-auto">
					<div class="absolute top-0 w-[900px] h-[20px] bg-yellow-300"></div>
				</div>
				{#if orders.length}
					<div class="bg-blue-400 h-full w-full relative overflow-y-auto">
						<div class="overflow-y-auto absolute top-0 w-full h-full">
							{#each orders as order, idx}
								<button
									onclick={() => {
										curOrder = idx;
										showOrder = true;
									}}
									class="w-full p-2 flex border-b-2 bg-slate-300"
								>
									<span>
										<span>
											{#if order.type == 'phone'}
												📱
											{:else}
												🧑
											{/if}
										</span>
										<span class={`${order.finished ? 'line-through' : ''} w-full text-left flex-1`}>
											{order.name}
										</span>
										<span
											class={`${order.finished ? 'line-through text-slate-600/40' : ''} w-full flex-1 text-right`}
											>{new Date(order.dateStart).toLocaleTimeString([], {
												hour: '2-digit',
												minute: '2-digit'
											})}</span
										>
										{#if order.finished}
											<span class="pl-3">
												{new Date(order.finished).toLocaleTimeString([], {
													hour: '2-digit',
													minute: '2-digit'
												})}
											</span>
										{/if}
									</span></button
								>
							{/each}
						</div>
					</div>
				{:else}
					<div class="w-full h-full flex justify-center items-center">
						<span class="opacity-30">No Orders</span>
					</div>
				{/if}
			</div>
		</div>
		<div class="p-1 w-full flex gap-1 h-[40px]">
			<a href="/" class="bg-red-300 rounded-md w-full justify-center items-center flex">Back</a>
			{#if orders.length}
				<button
					onclick={() => {
						orders = [];
						setAllOrders();
					}}
					class="bg-red-300 p-2 rounded-md w-full flex justify-center items-center"
					>clear orders</button
				>
			{/if}
		</div>
	</div>
{/if}
