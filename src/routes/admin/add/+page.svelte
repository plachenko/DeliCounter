<script>
	import Product from '$lib/components/Product.svelte';
	import VoiceRecognition from '$lib/components/VoiceRecognition.svelte';
	import { onMount } from 'svelte';
	import { preventDefault } from 'svelte/legacy';

	let items = $state([]);
	let inputTxt = $state(null);
	let categoryOpts = $state(['Root']);
	let selectedCat = $state(0);

	const colorArr = ['bg-red-400', 'bg-green-400', 'bg-blue-400'];

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
		let curCat = window.location.search.split('=')[1];

		getItemList();
		getCategoryOpts();

		if (curCat) {
			addingItem = true;
			let curIdx = categoryOpts.findIndex((e) => e.id == curCat);
			selelctedCat = curIdx;
		}

		// console.log(curCat, categoryOpts);
	});

	function getItemList() {
		items = JSON.parse(window.localStorage.getItem('allItemList'));
	}

	function getCategoryOpts() {
		let optItems = items
			.filter((e) => {
				if (e.type == 0) return e;
			})
			.map((e) => {
				return { id: e.id, name: e.name };
			});

		categoryOpts = ['Root', ...optItems];
		addObj.category.opts = categoryOpts;
	}

	function setItemList() {
		let _items = JSON.stringify(items);
		window.localStorage.setItem('allItemList', _items);
	}

	function editItem(idx) {}
	function removeItem(idx) {
		items.splice(idx, 1);
		setItemList();
	}
	function addItem() {}

	$effect(() => {
		if (fieldIdx) {
			fields[fieldIdx].focus();
		}
	});

	function randomHash() {
		return Math.random().toString(36).slice(2);
	}

	function addObject(obj) {
		items = [...items, curObj];
		setItemList();
		curObj = {};
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
			<div class="absolute top-0 w-full left-0 overflow-y-auto">
				{#each items as item, idx}
					<div
						class={`bg-slate-300 p-2 relative [&:not(:first-child)]:border-t-2 border-slate-400/30 flex w-full`}
					>
						<button
							onclick={() => {
								curObj = item;
								addingItem = true;
							}}
							class="absolute w-[80%] h-full left-0 top-0"
						></button>
						<div class={`w-[4px] h-full absolute left-0 top-0`}></div>
						<span class="flex w-full items-center">
							{item?.name || item}
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
					{#if !addObj[prop].typeUnder || addObj[prop].typeUnder.includes(curObj.type)}
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
											bind:value={selectedCat}
										>
											{#each addObj[prop].opts as type, idx}
												<option value={type.id || idx}>{type.name || type}</option>
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
											bind:value={curObj[prop]}
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
					curObj = {};
					addingItem = false;
				}}
				class="bg-red-400 flex rounded-md p-3 font-bold w-full"
			>
				<span class="flex-1 text-slate-100">Cancel</span>
			</button>
			{#if curObj.name !== ''}
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
					getCategoryOpts();

					addingItem = true;
					console.log(addObj);
					Object.keys(addObj).forEach((e) => {
						curObj[e] = addObj[e].value;
					});
					console.log('test', curObj);
				}}
				class="bg-green-400 flex rounded-md p-3 font-bold w-full"
				><span class="border-r-2 border-green-600 px-3 drop-shadow">+</span>
				<span class="flex-1 text-slate-100">Add Item</span>
			</button>
		{/if}
	</div>
</div>
