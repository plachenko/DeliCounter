<script>
	import { onMount } from 'svelte';
	import { fly, fade } from 'svelte/transition';

	let { items } = $props();
	let itemHeight = $state(null);
	let itemRows = $state(3);
	let curItem = $state(-1);
	let itemTick = $state(null);
	let selectedItem = $state(null);
	let itemDivs = $state([]);
	let gridContainer = $state(null);
	let show = $state(false);

	function setItem(idx) {
		if (itemTick) {
			clearInterval(itemTick);
			itemTick = null;
		}

		selectedItem = idx;

		setTimeout(() => {
			curItem = -1;
		}, 1);

		setTimeout(() => {
			ticker();
		}, 550);
	}

	function outT(node, options) {
		if (options.idx == selectedItem) {
			// selectedItem = null;
			return fly(node, { y: -50, delay: 150 });
		}

		return fade(node, { duration: 100 });
	}

	function scrollEnd() {
		let offsetIdx = ~~(gridContainer.scrollTop / itemHeight);
		let offsetDiv =
			itemDivs[(offsetIdx - Math.floor(offsetIdx / 10)) * 3].querySelector('.offsetDiv');

		offsetDiv.scrollIntoView({ behavior: 'smooth' });
	}

	function ticker() {
		selectedItem = null;
		itemTick = setInterval(() => {
			if (curItem < items.length - 1) {
				curItem++;
				return;
			} else {
				clearInterval(itemTick);
				itemTick = null;
			}
		}, 60);
	}

	onMount(() => {
		itemHeight = ~~document.getElementById('Body').offsetHeight / itemRows - 10;
		ticker();
		setTimeout(() => {
			show = true;
		}, 40);
	});
</script>

<div class="overflow-hidden bg-slate-800">
	{#if show}
		<div in:fly={{ y: -100 }} class="absolute w-full h-full flex-1">
			<div
				bind:this={gridContainer}
				onscrollend={scrollEnd}
				class="w-full h-full grid overflow-y-auto grid-cols-3 p-1 border-box"
			>
				{#each items as item, idx}
					<div bind:this={itemDivs[idx]} class="p-1 relative">
						<div class="w-full h-full flex items-center justify-center absolute">
							{#if selectedItem == idx}
								<div
									style={`background-color: ${item.color};`}
									class="animate-ping left-[20px] top-[0px] z-[0] absolute w-[60%] h-[60%] bg-red-400 rounded-md"
								></div>
							{/if}
						</div>
						<div class="overflow-hidden rounded-md" style={`height: ${~~itemHeight}px`}>
							<div class={`offsetDiv absolute top-[-3px] size-2 cursor-pointer`}></div>
							{#if curItem >= idx}
								<button
									in:fly={{ y: 70 }}
									out:outT={{ idx: idx }}
									style={`height: ${~~itemHeight}px;`}
									onclick={() => {
										setItem(idx);
									}}
									class="relative select-none rounded-md bg-slate-300 w-full flex items-center justify-center"
								>
									<div
										style={`background-color: ${item.color};`}
										class={`z-[1] absolute ${selectedItem !== idx ? 'opacity-30' : ''} rounded-md w-full h-full`}
									></div>
									<div class="z-[2] w-[80%] flex justify-center items-center">
										<div class="border-r pr-2 mr-2 border-slate-800/30">
											<img
												class="opacity-30 landscape:size-7 portrait:size-20 flex-1"
												alt={`${item.name} icon`}
												src={`icons/${item?.name}.svg`}
											/>
										</div>
										<div class="flex-1 text-left text-sm">
											{item?.name || 'item ' + idx}
										</div>
									</div>
								</button>
							{/if}
						</div>
					</div>
				{/each}
			</div>
		</div>
	{/if}
</div>
