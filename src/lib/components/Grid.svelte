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
	let touchPing = $state(null);

	function setItem(idx, e) {
		if (itemTick) {
			clearInterval(itemTick);
			itemTick = null;
		}

		selectedItem = idx;

		setTimeout(() => {
			curItem = -1;
			touchPing.style.left = e.clientX - 8 + 'px';
			touchPing.style.top = e.clientY - 55 + 'px';
		}, 1);

		setTimeout(() => {
			selectedItem = null;
		}, 300);

		setTimeout(() => {
			ticker();
		}, 650);
	}

	function outT(node, options) {
		if (options.idx == selectedItem) {
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
		document.getElementById('gridContainer').addEventListener('resize', () => {
			itemHeight = ~~document.getElementById('gridContainer').offsetHeight / itemRows - 10;
		});

		ticker();

		setTimeout(() => {
			show = true;
			itemHeight = ~~document.getElementById('gridContainer').offsetHeight / itemRows - 10;
		}, 40);
	});
</script>

<div id="gridContainer" class="h-full overflow-hidden bg-slate-800">
	{#if show}
		<!-- <div class="h-[20px] bg-red-400 w-full"></div> -->
		<div in:fly={{ y: -100 }} class="absolute w-full h-full flex-1">
			<div class="w-full h-full absolute top-0 left-0 z-[4] pointer-events-none">
				{#if selectedItem !== null}
					<div
						bind:this={touchPing}
						in:fade={{ duration: 100 }}
						out:fly={{ y: -10, delay: 70 }}
						style={`background-color: ${items[selectedItem]?.color || '#CCC'};`}
						class="size-4 bg-red-400 rounded-full absolute animate-ping"
					></div>
				{/if}
			</div>
			<div
				bind:this={gridContainer}
				onscrollend={scrollEnd}
				class="w-full h-full grid overflow-y-auto grid-cols-3 p-1 border-box"
			>
				{#each items as item, idx}
					<div bind:this={itemDivs[idx]} class="p-1 relative">
						<div class="overflow-hidden rounded-md relative" style={`height: ${~~itemHeight}px`}>
							<div class={`offsetDiv absolute top-[-3px] size-2 cursor-pointer`}></div>
							{#if curItem >= idx}
								<button
									in:fly={{ y: 70 }}
									out:outT={{ idx: idx }}
									style={`height: ${~~itemHeight}px;`}
									onclick={(e) => {
										setItem(idx, e);
									}}
									class="relative select-none rounded-md bg-slate-300 w-full flex items-center justify-center"
								>
									<div
										style={`background-color: ${item?.color || '#CCC'};`}
										class={`z-[1] absolute ${selectedItem !== idx ? 'opacity-30' : 'opacity-30'} rounded-md w-full h-full`}
									></div>
									<div
										class="z-[2] w-[80%] flex portrait:flex-col landscape:flex-row justify-center items-center"
									>
										<div
											class="landscape:border-r portrait:border-b landscape:pr-2 landscape:mr-2 portrait:pb-2 portrait:size-[80%] portrait:mb-2 border-slate-800/30 flex items-center justify-center"
										>
											{#if item?.name}
												<img
													class="opacity-30 landscape:size-7 portrait:size-20 flex-1"
													alt={`${item?.name} icon`}
													src={`icons/${item?.name}.svg`}
												/>
											{/if}
										</div>
										<div class="flex-1 text-left text-sm">
											{item?.name || 'item ' + idx}
										</div>

										<!-- <span class="absolute top-1 text-xs text-slate-800/30">0 Products</span> -->
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
