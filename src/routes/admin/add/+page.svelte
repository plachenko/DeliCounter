<script>
	import { onMount } from 'svelte';
	let items = $state([]);
	let inputTxt = $state(null);

	let addingItem = $state(false);

	onMount(() => {});

	let objTypes = [
		'Category',
		'Item',
		'Line Item'
	]

	function editItem(idx) {}
	function removeItem(idx) {}
	function addItem() {}

	function randomHash() {
		return Math.random().toString(36).slice(2);
	}

	let addObj = $state({
		id: randomHash(),
		name: {
			type: 'text',
			value: ''
		},
		type: {
			type: 'select',
			value: 0
		},
		category: null
	});
</script>

<div class="w-full h-full flex-col flex">
	<div class="w-full flex-1 h-[80px] bg-red-400 p-2">
		{#if !addingItem}
			{#each items as item, idx}
				<div>
					<span>
						{item}
					</span>
					<button
						onclick={() => {
							removeItem(idx);
						}}>remove</button
					>
					<button
						onclick={() => {
							editItem(idx);
						}}>edit</button
					>
				</div>
			{/each}
		{:else}
		
		<div class="bg-green-400 w-full h-[10px]">
			{#each Object.keys(addObj) as prop}
				<div class="flex">
					{prop}
				</div>
				{#if addObj[prop]?.type == 'text'}
				<input required type="text" bind:value={addObj[prop].value} />
				{:else if addObj[prop]?.type == 'select'}
				<select bind:value={addObj[prop].value}>
					{#if prop == 'type'}
					{#each objTypes as type, idx}
						<option value={idx}>{type}</option>
					{/each}
					{/if}
				</select>
				{:else}
				<span>{addObj[prop]}</span>
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
				class="bg-red-400 flex rounded-md p-1 font-bold w-full"
			>
				<span class="flex-1 text-slate-100">Cancel</span>
			</button>
			<button onclick={() => {console.log(addObj)}} class="bg-green-400 flex rounded-md p-1 font-bold w-full"
				><span class="border-r-2 border-green-600 px-3 drop-shadow">+</span>
				<span class="flex-1 text-slate-100">Add</span>
			</button>
		{:else}
			<button
				onclick={() => {
					addingItem = true;
				}}
				class="bg-green-400 flex rounded-md p-1 font-bold w-full"
				><span class="border-r-2 border-green-600 px-3 drop-shadow">+</span>
				<span class="flex-1 text-slate-100">Add Item</span>
			</button>
		{/if}
	</div>
</div>
