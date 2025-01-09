<script>
	import { onMount } from 'svelte';
	import { fly } from 'svelte/transition';
	import Time from '$lib/components/Time.svelte';
	import LineItems from '$lib/components/LineItems.svelte';
	import Logo from '$lib/components/Logo.svelte';

	let orderCat = ['Pizza', 'Panini', 'Sub', 'Panini', 'Grill', 'Fried Food', 'Soups'];
	let toppings = ['Cheese', 'Pepperoni', 'Mushrooms', 'Green Peppers', 'Onions', 'Black Olives', 'Sausage', 'Pineapple', 'Ham', 'Brocolli', 'Garlic', 'Chicken', 'Bacon'];

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

	let curArr = $state([]);
	let curCat = $state('');
	let order = $state([]);
	let showIngredients = $state(0);

	let curToppings = $state([]);
	let allSelect = $state(false);
	let lineItemEl = $state(null);

	let startOrder = $state(false);
	let startTime = $state(null);

	onMount(() => {
		curArr = orderCat;
	});

	function handleClick(cat) {
		if(curCat == "Pizza"){
			addPizza(cat);
			return;
		}
		
		//showIngredients = true;
		
		if (cat && products[cat].length) {
			curArr = products[cat];
			curCat = cat;
		}
	}

	function addPizza(type){
		let pizzaObj = {
			name: 'Pizza',
			toppings: []
		}
		if(type == 'Custom'){
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

		<Time {startTime} />
		<div class="grid grid-cols-3 gap-1 grid-rows-3 h-full p-1">
			{#each curArr as cat, idx}
				<button
					onclick={() => handleClick(cat)}
					class="rounded-md bg-slate-400 flex justify-center items-center">{cat}</button
				>
			{/each}
		</div>
		<div class="w-full p-1">
			<button
				class="w-full bg-red-400 p-2 rounded-md"
				onclick={() => {
					startOrder = false;
				}}>Cancel Order</button
			>
		</div>
	{:else}
		<div
			transition:fly={{ y: -20 }}
			class="p-2 pt-0 w-full bg-slate-100 gap-2 h-full flex absolute justify-center items-center flex-col"
		>
			<div class="w-full border-b-2">
				<Time {startTime} />
			</div>
			<Logo />
			<button
				onclick={() => {
					startOrder = true;
				}}
				class="bg-slate-300 rounded-md p-3"
			>
				start order
			</button>
		</div>
	{/if}
</div>
