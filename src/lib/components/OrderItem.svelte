<script>
	import { onMount } from 'svelte';
	import { fly, fade } from 'svelte/transition';
	import NutritionLabel from './NutritionLabel.svelte';

	let showCancelButton = $state(false);
	let showAddButton = $state(false);

	let showBtns = $state(false);

	let showNutritionLabel = $state(false);
	let showImage = $state(false);
	let showCheckout = $state(false);

	let { curOrderItem, showOrder, addItemToOrder, cancelOrder, order } = $props();

	let itemQnty = $state(0);

	let orderParams = $state(['nutrition', 'quantity', 'toppings']);

	$effect(() => {
		/*
		if (itemQnty) {
			let curItemIdx = order.indexOf((e) => {
				return e.name == curOrderItem.name;
			});

			console.log(curItemIdx);
			if (curItemIdx > 0) return;

			order.push(curOrderItem);
		}
		curOrderItem.qty = itemQnty;
    */
		curOrderItem.qty = itemQnty;
		showCheckout = itemQnty > 0;
	});

	function viewNutrition() {
		showNutritionLabel = !showNutritionLabel;
	}

	onMount(() => {
		setTimeout(() => {
			showCancelButton = true;
			showAddButton = true;
			showImage = true;
			showBtns = true;
		}, 300);
	});

	function startCancelOrder() {
		showCancelButton = false;
		showAddButton = false;
		showCheckout = false;
		showImage = false;
		curOrderItem = null;
		showBtns = false;
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
				<div transition:fly={{ x: -20 }} class="overflow-hidden w-full h-[69%] bg-slate-200">
					{#if showNutritionLabel}
						<div transition:fade={{ duration: 300 }} class="overflow-y-auto h-full">
							<NutritionLabel />
						</div>
					{/if}
				</div>
			{/if}
		</div>

		{#if showBtns}
			<div
				transition:fly={{ x: 20 }}
				class="flex gap-1 flex-col flex-1 h-full w-full p-2 pb-[200px]"
			>
				<button
					class="bg-slate-200 border-slate-500/20 border-2 py-3 px-2 flex w-full rounded-md text-xs"
					>Details</button
				>
				<button
					onclick={() => {
						viewNutrition();
					}}
					class={`border-2 ${showNutritionLabel ? 'border-slate-400' : 'border-slate-300'}  bg-slate-200 rounded-md p-2 w-full relative flex items-center flex-1`}
					><span class="w-full flex-1 flex items-center">
						<div class="flex w-full relative justify-center items-center">
							<span class="flex-1 w-full text-left z-[10] items-center"
								><span class="bg-slate-200 text-xs whitespace-nowrap left-0 pr-2"
									>View Nutrition</span
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
				<button
					class="bg-slate-200 border-slate-500/20 border-2 py-3 px-2 flex w-full rounded-md text-xs"
					>What's inside</button
				>
			</div>
		{/if}
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

		{#if showCheckout && curOrderItem.qty}
			<button
				transition:fly={{ y: 60 }}
				disabled={curOrderItem.qty == 0}
				onclick={() => showOrder()}
				class={`${curOrderItem.qty == 0 ? 'opacity-50 bg-slate-300' : 'bg-green-400 border-green-500'} flex-1 border-2 text-xs rounded-md`}
				>Checkout
				{#if curOrderItem.qty}
					<span class="text-xs px-2">
						(${(parseFloat(curOrderItem.price.split('$')[1]) * itemQnty).toFixed(2)})
					</span>
				{/if}
			</button>
		{/if}

		<div class="flex-1 w-full flex justify-end">
			{#if showAddButton}
				{#if itemQnty}
					<button
						transition:fly={{ x: 10 }}
						onclick={() => {
							itemQnty--;
						}}
						class="text-2xl text-slate-100/70 h-full rounded-l-md font-bold justify-center items-center flex px-3 bg-red-400/50 border-red-400 border-y-2 border-l-2 rounded-l-md"
					>
						-
					</button>
				{/if}
				<button
					transition:fly={{ x: 70 }}
					onclick={() => {
						if (itemQnty) return;
						itemQnty++;
					}}
					class={`text-xs relative flex-1 ${itemQnty == 0 ? 'border-l-2 rounded-l-md' : ''} border-y-2 border-green-500 bg-green-400 flex items-center justify-center`}
				>
					<div class="flex-1 flex flex-col justaify-center items-center">
						{#if itemQnty}
							<span class="flex-1"> Quantity </span>
							<span
								class="flex-1 bg-white/30 border-t-2 border-slate-100/30 mt-1 p-1 flex justify-center items-center text-xs w-full"
								>{itemQnty}</span
							>
						{:else}
							<span class="flex-1"> Add to order </span>
						{/if}
					</div>
				</button>
				<button
					transition:fade
					onclick={() => {
						// order = [...order, curOrderItem];
						// console.log(curOrderItem);
						itemQnty++;
						order.push(curOrderItem);
					}}
					class="text-2xl border-y-2 border-r-2 border-green-500 text-slate-800/50 h-full bg-green-500/30 justify-center rounded-r-md items-center flex px-2"
				>
					+
				</button>
			{/if}
		</div>
	</div>
</div>
