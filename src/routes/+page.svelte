<script>
	import { onMount } from 'svelte';
	import { fly } from 'svelte/transition';
	import Time from '$lib/components/Time.svelte';
	import LineItems from '$lib/components/LineItems.svelte';
	import Logo from '$lib/components/Logo.svelte';

	let allOrders = $state([]);
	let orderType = $state(null);
	let futureDateEl = $state(null);
	let futureDate = $state(null);
	let orderTypes = $state(['counter', 'phone']);
	let orderCat = ['Pizza', 'Panini', 'Sub', 'Panini', 'Grill', 'Fried Food', 'Soups'];
	let toppings = [
		'Cheese',
		'Pepperoni',
		'Mushrooms',
		'Green Peppers',
		'Onions',
		'Black Olives',
		'Sausage',
		'Pineapple',
		'Ham',
		'Brocolli',
		'Garlic',
		'Chicken',
		'Bacon'
	];

	const lineItems = [
		'Mushrooms',
		'Olives',
		'Green Peppers',
		'White Onions',
		'Red Onions',
		'pickles',
		'Bannana Peppers',
		'lettuce',
		'tomato'
	];

	let subs = ['italian', 'turkey', 'roast beef', 'ham and cheese'];
	let paninis = ['Reuben', 'turkey', 'BLT melt', 'Grilled Cheese and tomato'];
	let sauces = ['Ketchup', 'mayo', 'mustard', 'oil', 'vinegar'];

	let seasoning = ['salt', 'pepper', 'oregano'];

	let products = {
		Pizza: ['Custom', 'Cheese', 'pepperoni', 'buffalo Chicken']
	};
	let itemList = $state(lineItems);

	let startPrint = $state(false);

	let curArr = $state([]);
	let curCat = $state('');
	let order = $state([]);
	let showIngredients = $state(0);

	let curToppings = $state([]);
	let allSelect = $state(false);
	let lineItemEl = $state(null);

	let startOrder = $state(false);
	let startTime = $state(null);
	let addingFuture = $state(false);

	function addFutureDate() {
		addingFuture = true;
		futureDateEl.focus();
		console.log('adding future');
	}

	function setOrder() {
		return {
			name: 'order',
			takenBy: 'Dennys',
			type: orderTypes[orderType],
			dateStart: startTime,
			endTime: new Date(),
			futureDate: futureDate,
			items: order
		};
	}

	onMount(() => {
		allOrders = JSON.parse(window.localStorage.getItem('allOrders')) || [];
		curArr = orderCat;
	});

	function handleClick(cat) {
		if (curCat == 'Pizza') {
			addPizza(cat);
			return;
		}

		order.push(cat);

		if (cat && products[cat]?.length) {
			curArr = products[cat];
			curCat = cat;
		}
	}

	function addPizza(type) {
		let pizzaObj = {
			name: 'Pizza',
			toppings: []
		};
		if (type == 'Custom') {
			itemList = toppings;
			showIngredients = true;
		}
		console.log('adding pizza', type);
	}

	$effect(() => {
		if (startOrder) {
			startTime = new Date();
		} else {
			startTime = null;
		}
	});
</script>

<div class="flex flex-col h-full relative w-full">
	{#if startPrint}
		<div
			class="w-full h-full absolute left-0 top-0 z-[9999] bg-white flex justify-center items-center"
		>
			<div
				class="flex justify-center items-center w-full border-dashed border-slate-800 border-t-4 border-b-4 py-2 pb-10"
			>
				<span>test</span>
			</div>
		</div>
		<button onclick={(startPrint = false)} class="absolute bg-red-400 p-2 rounded-md">Close</button>
	{/if}
	{#if addingFuture}
		<div class="absolute z-[9999] top-1 flex justify-center w-full">
			<input
				bind:this={futureDateEl}
				bind:value={futureDate}
				oncancel={() => {
					console.log('canceled');
					addingFuture = false;
				}}
				oninput={(e) => {
					addingFuture = false;
				}}
				type="datetime-local"
			/>
		</div>
	{/if}
	{#if startOrder}
		{#if showIngredients}
			<div
				transition:fly={{ y: -90 }}
				class="z-[99] bg-slate-300 flex flex-col absolute w-full h-full top-0 left-0 flex justify-center items-center"
			>
				<div class="flex w-full p-1">
					<div class="text-xl flex-1 text-center">Toppings</div>
					<button
						onclick={() => {
							allSelect = !allSelect;
							lineItemEl.selectAll();
						}}
						class="flex-1 w-full bg-slate-100 rounded-md"
					>
						{#if !allSelect}
							All
						{:else}
							None
						{/if}
					</button>
				</div>

				<div class="h-full overflow-y-auto relative w-full">
					<LineItems bind:this={lineItemEl} {allSelect} {itemList} />
				</div>
				<div class="w-full flex gap-1 p-1 pt-0">
					<button
						class="w-full bg-red-400 rounded-md p-1"
						onclick={() => {
							allSelect = false;
							showIngredients = false;
						}}>Cancel</button
					>
					<button
						class="w-full bg-green-400 rounded-md p-1"
						onclick={() => {
							allSelect = false;
							showIngredients = false;
						}}>Add</button
					>
				</div>
			</div>
		{/if}

		<div class="relative w-full p-1">
			<Time {startTime} {futureDate} {addFutureDate} />
			<button
				onclick={() => (orderType = orderType ? 0 : 1)}
				class="top-1 absolute right-1 rounded-md border-2 bg-slate-300/30 px-1"
				>{orderTypes[orderType]}</button
			>
		</div>
		<div class="grid grid-cols-3 gap-1 grid-rows-3 h-full p-1">
			{#each curArr as cat, idx}
				<button
					onclick={() => handleClick(cat)}
					class="rounded-md bg-slate-400 flex justify-center items-center bg-gradient-to-b from-slate-200 to-slate-300/40 drop-shadow font-bold border-2 border-slate-300"
					><span class="drop-shadow-[0_2px_2px_#999] text-2xl text-slate-700">{cat}</span></button
				>
			{/each}
		</div>
		<div class="w-full p-1 flex w-full gap-1">
			<button
				class="w-full bg-red-400 p-2 rounded-md flex-1"
				onclick={() => {
					startOrder = false;
					addingFuture = false;
					curArr = orderCat;
					order = [];
					curCat = '';
					futureDate = null;
				}}>Cancel Order</button
			>
			{#if order?.length}
				<button
					onclick={() => {
						allOrders.push(setOrder());
						window.localStorage.setItem('allOrders', JSON.stringify(allOrders));

						startOrder = false;
						addingFuture = false;
						curArr = orderCat;
						order = [];
						curCat = '';
						futureDate = null;
					}}
					class="bg-green-400 rounded-md w-full flex-1 h-full relative flex items-center justify-center"
					>Add Order <span
						class="border-2 border-green-700 text-sm rounded-full px-1 absolute left-2 font-bold"
						>{order.length}</span
					><span class="absolute right-2 bg-green-300/60 rounded-md px-1">$126.69</span></button
				>
				<button
					onclick={() => {
						startPrint = true;
						window.print();
					}}
					class="w-[50px] bg-green-300 rounded-md">🖨️</button
				>
			{/if}
		</div>
	{:else}
		<div
			transition:fly={{ y: -20 }}
			class="z-[999] p-2 pt-0 w-full bg-slate-100 gap-2 h-full flex absolute justify-center items-center flex-col"
		>
			<div class="w-full border-b-2 relative">
				<Time {startTime} />
			</div>
			<Logo />
			<div class="flex gap-1 w-full">
				{#each orderTypes as type, idx}
					<button
						onclick={() => {
							startOrder = true;
							orderType = idx;
						}}
						class="bg-slate-300 rounded-md p-3 flex-1"
					>
						{orderTypes[idx]}
					</button>
				{/each}

				<!-- <button
					onclick={() => {
						startOrder = true;
						orderType = 'Phone';
					}}
					class="bg-slate-300 rounded-md p-3 flex-1"
				>
					Phone order
				</button> -->
				{#if allOrders.length}
					<a href="/orders" class="bg-slate-700 text-white rounded-md p-3">
						all orders ({allOrders.length})</a
					>
				{/if}
			</div>
		</div>
	{/if}
</div>

<style>
	@media print {
		@page {
			size: 58mm 105mm;
			margin: 10mm;
		}
	}
</style>
