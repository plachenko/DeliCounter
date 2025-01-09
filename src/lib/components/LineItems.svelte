<script>
	import { onMount } from 'svelte';
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

	const condiments = ['ketchup', 'mayonaise', 'mustard', 'BBQ sauce', 'hots', 'oil', 'vinegar'];

	const amtArr = ['lite', 'extra'];

	let { allSelect } = $props();

	let itemsObj = $state([]);

	export function selectAll() {
		itemsObj.forEach((e) => {
			e.checked = allSelect;
		});
	}

	onMount(() => {
		itemsObj = lineItems.map((e) => {
			return { name: e, checked: ~~(Math.random() * 3 - 1), amt: 0, grilled: false };
		});

		selectAll();
	});
</script>

<div class="absolute grid grid-cols-3 h-full w-full left-0 gap-1 p-1">
	{#each itemsObj as item, idx}
		<div
			class={`rounded-md ${item.checked ? 'bg-slate-600/20 text-slate-800 font-bold rounded-b-none' : 'bg-slate-200 '} mb-[16px] relative flex w-full gap-1 p-1 px-3 `}
		>
			{#if item.checked}
				{#if !item.name.startsWith('Fresh')}
					<button
						onclick={() => {
							item.grilled = !item.grilled;
							if (item.grilled) {
								let idx = itemsObj.push({ name: 'Fresh ' + item.name });
								item.freshIdx = idx;
							} else {
								itemsObj.splice(item.freshIdx - 1, 1);
							}
						}}
						class={`px-1 h-full z-[90] bg-red-600/30 ${!item.grilled ? 'opacity-20' : ''} flex items-center border-red-600 border-r-2 rounded-l-md absolute left-0 top-0`}
					>
						🔥
					</button>
				{/if}
			{/if}
			{#if item.checked}
				<div
					class="flex w-full z-[99] absolute text-xs bg-slate-200 rounded-b-md font-normal bottom-[-16px] left-0"
				>
					{#each amtArr as amt, idx}
						<button
							onclick={() => {
								if (item.amt == idx + 1) {
									item.amt = 0;
									return;
								}
								item.amt = idx + 1;
							}}
							class={`text-center flex-1 ${idx + 1 == item.amt ? `${item.amt == 1 ? 'rounded-bl-md' : 'rounded-br-md'} bg-slate-400 box-border font-bold border-b-0 border border-slate-500` : 'text-slate-400 [&:not(:last-child)]:border-r border-slate-500'}`}
							>{amt}</button
						>{/each}
				</div>
				<div
					class={`border top-0 left-0 border-[2px] ${!item.grilled ? 'border-red-300' : 'border-red-600'} rounded-b-none w-full h-full rounded-md absolute`}
				></div>
			{/if}
			<div class={`pl-5 relative w-full flex gap-1 justify-center items-center`}>
				<input class="pointer-events-none" type="checkbox" checked={item.checked} />
				<span class="text-xs flex-1 text-center"
					>{#if item.grilled}Grilled{/if}
					{item.name}</span
				>
				<button
					onclick={() => {
						item.checked = !item.checked;
					}}
					class="absolute w-full h-full left-0 top-0"
				></button>
			</div>
		</div>
	{/each}
</div>
