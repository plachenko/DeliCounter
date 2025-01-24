<script>
	import Product from '$lib/components/Product.svelte';
	import { onMount } from 'svelte';
	import { preventDefault } from 'svelte/legacy';

	let items = $state(['test', 'uh']);
	let inputTxt = $state(null);

	let fieldIdx = $state(0);
	let fields = $state([]);

	let addingItem = $state(false);

	let curObj = $state({});
	let addObj = $state({
		id: {
			type: 'crypto',
			hidden: true,
			value: randomHash()
		},
		name: {
			type: 'text',
			value: '',
			placeholder: 'Enter field name'
		},
		category: {
			type: 'select',
			value: 0,
			opts: ['Root']
		},
		type: {
			type: 'select',
			value: 0,
			opts: ['Category', 'Item', 'Line Item']
		},
		price: {
			step: 0.01,
			min: 0,
			type: 'number',
			value: null,
			typeUnder: [1, 2],
			placeholder: 'Enter dollar amount'
		}
	});

	onMount(() => {
		// console.log(curObj);
	});

	function editItem(idx) {}
	function removeItem(idx) {
		items.splice(idx, 1);
	}
	function addItem() {}

	$effect(() => {
		if (fieldIdx) {
			fields[fieldIdx].focus();
		}

		if (addingItem) {
			Object.keys(addObj).forEach((e) => {
				curObj[e] = null;
			});
		}
	});

	function randomHash() {
		return Math.random().toString(36).slice(2);
	}

	function addObject(obj) {
		items.push(obj);
		addingItem = false;
	}
</script>

<div class="w-full h-full flex-col flex">
	<div class="w-full flex-1 h-[80px] overflow-y-auto relative flex flex-col p-2">
		{#if !addingItem}
			{#if !items.length}
				<div
					class="w-full h-full text-2xl text-slate-600 font-bold flex justify-center items-center"
				>
					No items
				</div>
			{/if}
			<div class="absolute top-0 w-full left-0 p-2 overflow-y-auto">
				{#each items as item, idx}
					<div
						class="bg-slate-300 p-3 [&:is(:first-child)]:rounded-t-md [&:is(:last-child)]:rounded-b-md [&:not(:first-child)]:border-t-2 border-slate-400/30 flex w-full"
					>
						<span class="flex w-full">
							{item?.name?.value || item}
						</span>
						<div class="flex justify-end w-[200px]">
							<button
								class="bg-slate-400 rounded-md text-slate-100 p-1"
								onclick={() => {
									removeItem(idx);
								}}>remove</button
							>
						</div>
					</div>
				{/each}
			</div>
		{:else}
			<div class="">
				{#each Object.keys(addObj) as prop, idx}
					{#if !addObj[prop].typeUnder || addObj[prop].typeUnder.includes(addObj['type'].value)}
						{#if !addObj[prop]?.hidden}
							<div class="flex w-full border-b-2 border-slate-300/20 py-2 gap-1">
								<div class="flex pr-2 w-[20%]">
									<span class="flex justify-end w-full font-bold border-r-2 pr-1">
										{prop}
									</span>
								</div>
								<div id="fieldCont" class="w-[80%] flex-1 text-sm flex items-center">
									{#if addObj[prop]?.type == 'text'}
										<div class="flex gap-1 w-full">
											<div
												bind:this={fields[idx]}
												role="textbox"
												tabindex={idx}
												onkeydown={(e) => {
													e.preventDefault();
													if (e.which == 13) {
														fieldIdx++;
														return false;
													}
												}}
												contenteditable
												bind:textContent={curObj[prop]}
												class="w-full flex-1 p-1 bg-white no-"
											></div>
											{#if curObj[prop]}
												<button
													class="p-1 px-2 rounded-md bg-slate-200"
													onclick={() => {
														curObj[prop] = '';
														fields[fiseldIdx].focus();
													}}>x</button
												>
											{/if}
										</div>
										<!-- <input required class="w-full p-1" type="text" bind:value={addObj[prop].value} /> -->
									{:else if addObj[prop]?.type == 'select'}
										<select
											bind:this={fields[idx]}
											class="w-full p-1 bg-white"
											bind:value={curObj[prop]}
										>
											{#each addObj[prop].opts as type, idx}
												<option value={idx}>{type}</option>
											{/each}
										</select>
									{:else if addObj[prop]?.type == 'number'}
										<input
											class="p-1 w-full"
											onchange={(e) => {
												addObj[prop].value = addObj[prop].value.toFixed(2);
											}}
											step={addObj[prop].step || 1}
											min={addObj[prop].min || 0}
											max={addObj[prop].max || null}
											bind:value={addObj[prop].value}
											placeholder={addObj[prop].placeholder}
											type="number"
										/>
									{:else}
										<span>{addObj[prop]}</span>
									{/if}
								</div>
							</div>
						{/if}
					{/if}
				{/each}
			</div>
		{/if}
	</div>
	<div class="flex p-1 gap-1 w-full p-2">
		{#if addingItem}
			<button
				onclick={() => {
					addingItem = false;
				}}
				class="bg-red-400 flex rounded-md p-3 font-bold w-full"
			>
				<span class="flex-1 text-slate-100">Cancel</span>
			</button>
			{#if curObj.name.value !== ''}
				<button
					onclick={() => {
						addObject(addObj);
					}}
					class="bg-green-400 flex rounded-md p-3 font-bold w-full"
					><span class="border-r-2 border-green-600 px-3 drop-shadow">+</span>
					<span class="flex-1 text-slate-100">Add</span>
				</button>
			{/if}
		{:else}
			<button
				onclick={() => {
					addingItem = true;
				}}
				class="bg-green-400 flex rounded-md p-3 font-bold w-full"
				><span class="border-r-2 border-green-600 px-3 drop-shadow">+</span>
				<span class="flex-1 text-slate-100">Add Item</span>
			</button>
		{/if}
	</div>
</div>
