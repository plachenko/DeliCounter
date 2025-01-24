<script>
	import { onMount } from 'svelte';
	let items = $state(['test', 'uh']);
	let inputTxt = $state(null);

	let addingItem = $state(false);

	onMount(() => {});


	function editItem(idx) {}
	function removeItem(idx) {
		items.splice(idx, 1);
	}
	function addItem() {}

	function randomHash() {
		return Math.random().toString(36).slice(2);
	}

	function addObject(obj) {
		items.push(obj);
		addingItem = false;
	}

	let addObj = $state({
		id: {
			type: 'crypto',
			hidden: true,
			value: randomHash()
		},
		name: {
			type: 'text',
			value: ''
		},
		category: {
			type: 'select',
			value: 0,
			opts:[
				'Root'
			]
		},
		type: {
			type: 'select',
			value: 0,
			opts:[
				'Category', 
				'Item', 
				'Line Item'
			]
		},
		price: {
			type: 'number',
			value: 0,
			typeUnder: 1
		}
	});
</script>

<div class="w-full h-full flex-col flex">
	<div class="w-full flex-1 h-[80px] overflow-y-auto relative flex flex-col p-2">
		{#if !addingItem}
			{#if items.length}{:else}
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
				{#each Object.keys(addObj) as prop}
					{#if !addObj[prop]?.hidden}
						<div class="flex w-full border-b-2 border-slate-300/20 py-2 gap-1">
							<div class="flex pr-2 w-[20%]">
								<span class="flex justify-end w-full font-bold border-r-2 pr-1">
									{prop}
								</span>
							</div>
							<div class="w-[80%] flex-1 text-sm flex items-center">
								{#if addObj[prop]?.type == 'text'}
									<div
										contenteditable
										bind:textContent={addObj[prop].value}
										class="w-full p-1 bg-white"
									></div>
									<!-- <input required class="w-full p-1" type="text" bind:value={addObj[prop].value} /> -->
								{:else if addObj[prop]?.type == 'select'}
									<select class="w-full p-1 bg-white" bind:value={addObj[prop].value}>
										{#each addObj[prop].opts as type, idx}
											<option value={idx}>{type}</option>
										{/each}
									</select>
								{:else if addObj[prop]?.type == 'number'}
									<input
										class="p-1 w-full"
										step="0.01"
										bind:value={addObj[prop].value}
										type="number"
									/>
								{:else}
									<span>{addObj[prop]}</span>
								{/if}
							</div>
						</div>
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
			{#if addObj.name.value !== ''}
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
