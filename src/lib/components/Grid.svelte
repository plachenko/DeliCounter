<script>
	import { onMount } from 'svelte';
	import { fly, fade } from 'svelte/transition';

	let { items, showGrid, setNewItems, setOrderItem } = $props();
	let itemHeight = $state(null);
	let itemRows = $state(3);
	let curItem = $state(-1);
	let itemTick = $state(null);
	let selectedItem = $state(null);
	let itemDivs = $state([]);
	let gridContainer = $state(null);
	let show = $state(false);
	let touchPing = $state(null);
	let tickDone = $state(false);
	let taken = $state(false);
	let colNumber = $state(3);

	let currentCategory = $state(null);

	let curOver = $state(null);

	$effect(() => {
		if (!showGrid) {
			show = false;
			currentCategory = null;
		}
	});

	function setItem(idx, e) {
		if (itemTick) {
			clearInterval(itemTick);
			itemTick = null;
		}

		selectedItem = idx;

		setTimeout(() => {
			curItem = -1;
			touchPing.style.left = e.clientX - 8 + 'px';
			touchPing.style.top = e.clientY - 55 - (currentCategory ? 30 : 0) + 'px';
		}, 1);

		setTimeout(() => {
			taken = true;
			selectedItem = null;
		}, 300);

		setTimeout(() => {
			currentCategory = items[idx];
			// console.log(Object.keys(items[idx].objects));

			// let arr = items[idx].objects;
			// let firstArr = arr[Object.keys(arr)[0]];

			/*
			console.log(currentCategory.objects);

      if(currentCategory?.objects){
        items = currentCategory.objects;
      }
      */

			/*
			if (Object.keys(firstArr)[0] == 'Half') {
				console.log('setting half');
			}
      */

			/*
			if (firstArr[0] == 'Half') {
				console.log('subs');
			}
      */

			if (items[idx]?.objects) {
				setNewItems(items[idx]?.objects);
			} else {
				setOrderItem(items[idx]);
			}

			setItemHeight();
		}, 500);

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
		tickDone = false;
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

	function setItemHeight() {
		itemRows = Math.ceil(items.length / 3);
		itemHeight =
			~~document.getElementById('gridContainer').offsetHeight / itemRows -
			10 -
			(currentCategory ? (itemRows == 1 ? 30 : 10) : 0);
		itemHeight = items.length <= 6 ? itemHeight - 6 : itemHeight;
	}

	onMount(() => {
		window.addEventListener('resize', (e) => {
			setItemHeight();
		});

		ticker();

		setTimeout(() => {
			show = true;
			setItemHeight();
		}, 40);
	});
</script>

<div id="gridContainer" class="h-full overflow-hidden w-full">
	{#if currentCategory}
		<div
			in:fly={{ y: -100 }}
			style={`background-color: ${currentCategory?.color || '#CCC'}`}
			class="border-b-2 border-slate-600 select-none px-3 opacity-40 flex gap-3 flex-row items-center bg-slate-800 flex-1 w-[100vw] h-[30px]"
		>
			<div class="px-2 absolute w-full flex h-[30px] flex py-2 items-center left-0 top-0">
				<div class="size-6 flex items-center">
					<!--
					<img
						class="flex-1"
						alt={`${currentCategory?.name} icon`}
						src={`icons/${currentCategory?.name}.svg`}
					/>
-->
				</div>
				<div class="text-white border-l-2 pl-2 ml-2 border-slate-800 h-full flex items-center">
					{currentCategory.name}
				</div>
			</div>
		</div>
	{/if}
	<!-- <img class="bg-slate-200 absolute size-[190px] z-[3] " src="ticket.svg" /> -->
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
				class={`pt-2 w-full grid ${curItem <= 8 ? 'overflow-hidden' : 'overflow-y-auto'} ${items.length < 3 ? 'grid-cols-2' : 'grid-cols-3'} gap-2 px-1`}
			>
				{#each items as item, idx}
					<div
						style={`height: ${~~itemHeight}px;`}
						class="border-box relative flex justify-center items-center p-2"
					>
						{#if curItem >= idx}
							<button
								in:fly={{ y: 70 }}
								out:outT={{ idx: idx }}
								onpointerover={() => {
									curOver = idx;
								}}
								onclick={(e) => {
									setTimeout(() => {
										gridContainer.scrollTo(0, 0);
									}, 300);
									setItem(idx, e);
								}}
								class="flex-1 absolute h-full select-none rounded-md bg-slate-300 w-full flex items-center justify-center"
							>
								<!--
								{#if curItem?.price}
									<span>{curItem.price}</span>
								{/if}
-->
								<div
									style={`background-color: ${item?.color || '#CCC'};`}
									class={`z-[1] absolute ${selectedItem !== idx ? 'opacity-30' : 'opacity-30'} rounded-md w-full h-full`}
								></div>
								<div
									class="h-full z-[2] w-[80%] flex portrait:flex-col landscape:flex-row justify-center items-center"
								>
									<div
										class=" landscape:border-r portrait:border-b landscape:pr-2 landscape:mr-2 portrait:pb-2 portrait:size-[80%] portrait:mb-2 border-slate-800/30 flex items-center justify-center"
									>
										{#if item?.name}
											<!--
											<img
												class="opacity-30 landscape:size-7 portrait:size-20 flex-1"
												alt={`${item?.name} icon`}
												src={`icons/${item?.name}.svg`}
											/>
-->
										{/if}
									</div>

									<div
										class="capitalize relativeflex-1 text-xs landscape:text-left landscap:text-sm portrait:text-lg"
									>
										{item?.name || 'item ' + idx}
										{#if item.price}
											<span class="absolute font-bold bottom-1 right-1 bg-slate-400 rounded-md p-1"
												>{item.price}</span
											>
										{/if}
									</div>
								</div>
							</button>
							<!-- <div class="bg-red-400 rounded-md w-full h-full"></div> -->
						{/if}
					</div>
					<!--
					<div
						bind:this={itemDivs[idx]}
						style={`height: ${~~itemHeight}px;`}
						class="p-1 relative border-b-2 border-dashed border-red-400"
					>
						<div class="rounded-md relative" style={`height: ${~~itemHeight}px`}>
							<div class={`offsetDiv absolute top-[-7px] size-2`}></div>

						</div>
					</div>
        -->
				{/each}
			</div>
		</div>
	{/if}
</div>
