<script>
	import { onMount } from 'svelte';
	import { fly, fade } from 'svelte/transition';
	import NutritionLabel from './NutritionLabel.svelte';

	let showCancelButton = $state(false);
	let showAddButton = $state(false);

	let showNutritionLabel = $state(false);
	let showImage = $state(false);

	let { curOrderItem, addItemToOrder, cancelOrder, order } = $props();

	let itemQnty = $state(0);

	let orderParams = $state(['nutrition', 'quantity', 'toppings']);

	$effect(() => {
		curOrderItem.qty = itemQnty;
	});

	function viewNutrition() {
		showNutritionLabel = !showNutritionLabel;
	}

	onMount(() => {
		setTimeout(() => {
			showCancelButton = true;
			showAddButton = true;
			showImage = true;
		}, 300);
	});

	function startCancelOrder() {
		showCancelButton = false;
		showAddButton = false;
		showImage = false;
		curOrderItem = null;
		setTimeout(() => {
			cancelOrder();
		}, 300);
	}
</script>

<div class="absolute flex flex-col w-full h-full">
	<div class="flex w-full items-center p-1 border-b-2">
		<!--
		<div class="w-full flex-1 text-center text-xl">{curOrderItem.name}</div>
		<div class="p-1 text-slate-200 bg-slate-400 rounded-md">{curOrderItem.price}</div>
-->
	</div>
	<div class="px-2 py-1 text-sm text-slate-500">{curOrderItem.description}</div>

	<div class="flex w-full portrait:flex-col landscape:flex-row flex-1 h-full">
		<div class="flex-1 w-full h-full p-2">
			{#if showImage}
				<div transition:fly={{ y: 10 }} class="overflow-hidden w-full h-[69%] bg-slate-200">
					{#if showNutritionLabel}
						<div transition:fade={{ duration: 300 }} class="overflow-y-auto h-full">
							<NutritionLabel />
						</div>
					{/if}
				</div>
			{/if}
		</div>

		<div class="flex-1 h-full w-full p-2 pb-[200px]">
			<button
				onclick={() => {
					viewNutrition();
				}}
				class={`border-2 ${showNutritionLabel ? 'border-slate-400' : 'border-slate-300'}  bg-slate-200 rounded-md p-2 w-full relative flex items-center flex-1`}
				><span class="w-full flex-1 flex items-center">
					<div class="flex w-full relative justify-center items-center">
						<span class="flex-1 w-full text-left z-[10] items-center"
							><span class="bg-slate-200 text-xs whitespace-nowrap left-0 pr-2">View Nutrition</span
							></span
						>
						<div
							class="w-full border-t-2 border-dashed h-[1px] z-[6] absolute border-slate-600/20"
						></div>
						<span class="text-xs pl-1 text-slate-400 font-bold z-[10]">
							<div class="pl-2 bg-slate-200">
								<span class="bg-slate-200 border-2 border-slate-300 py-1 rounded-md px-2">
									{curOrderItem.calories} Cal
								</span>
							</div>
						</span>
					</div>
				</span>
			</button>
			<!--
			<div class="p-2 flex border-2 rounded-md mt-1 border-slate-400">
				<span class="flex-1 text-sm w-full">Quantity: {itemQnty}</span>
				<input class="flex-1 w-full" type="range" bind:value={itemQnty} min="1" max="10" />
			</div>
    -->
		</div>
	</div>

	<div class="w-full gap-1 absolute bg-white border-t bottom-0 flex py-2 px-2">
		<div class="flex-1 flex justify-start">
			{#if showCancelButton}
				<button
					transition:fly={{ x: -10 }}
					onclick={() => {
						// cancelOrder();
						startCancelOrder();
					}}
					class="text-xs w-full border-2 text-white border-red-500 bg-red-400 rounded-md p-2"
					>Back</button
				>
			{/if}
		</div>

		<div class="flex-1 w-full flex justify-end">
			{#if showAddButton}
				<button
					transition:fade
					onclick={() => {
						//addItemToOrder();
						console.log(curOrderItem);
						/*
            if(curOrderItem){

            }
            */
						// order.push(curOrderItem);
						itemQnty++;
						// order
					}}
					class="text-xs flex-1 border-2 border-green-500 bg-green-400 flex items-center justify-center px-4 rounded-md"
					>Add to order
					{#if curOrderItem.qty}
						<span class="text-xs text-green-700 pl-1">({curOrderItem.qty})</span>{/if}</button
				>
			{/if}
		</div>

		{#if curOrderItem.qty}
			<button
				transition:fly={{ x: 10 }}
				disabled={curOrderItem.qty == 0}
				class={`${curOrderItem.qty == 0 ? 'opacity-50 bg-slate-300' : 'bg-green-400 border-green-500'} flex-1  border-2 text-xs rounded-md`}
				>Proceed to checkout
				{#if curOrderItem.qty}
					<span class="text-xs px-2">
						(${(parseFloat(curOrderItem.price.split('$')[1]) * itemQnty).toFixed(2)})
					</span>
				{/if}
			</button>
		{/if}
	</div>
</div>
