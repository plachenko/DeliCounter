<script>
	import { onMount } from 'svelte';
	let options = $state([{ text: 'next' }, { text: 'previous' }]);
	let items = $state([]);
	let session = $state({
		id: Math.random() * 10,
		ticketNumber: getTicketNumber(),
		timeStarted: new Date(),
		timeEnded: null,
		user: null,
		order: getOrder()
	});

	let gridContainer = $state(null);

	onMount(() => {
		items = Array(39);
	});

	function getOrder() {
		return null;
	}

	function getTicketNumber() {
		return 'A' + ~~(Math.random() * 100);
	}
</script>

<div id="Container" class="overflow-hidden">
	<div id="Header" class="flex w-full h-[50px] bg-slate-400 p-2 gap-1">
		<button
			aria-label="MenuButton"
			class="flex items-center justify-center bg-slate-300 w-[80px] h-full rounded-md"
		>
			<img class="h-[75%] w-[70%]" src="mbstacked.png" />
		</button>
		<div class="flex-1 bg-slate-800 rounded-md"></div>
		<button aria-label="ShopButton" class="bg-slate-300 w-[50px] h-full rounded-md"></button>
	</div>
	<div id="Body" class="flex-1 w-full bg-red-700 relative">
		<div class="absolute w-full h-full">
			<div
				bind:this={gridContainer}
				onscrollend={(e) => {
					console.log('sroll ended at...', gridContainer.scrollTop);
					gridContainer.scrollTo(0, 1);
				}}
				onscroll={(e) => {
					console.log(~~gridContainer.scrollTop);
				}}
				class="w-full h-full grid overflow-y-auto grid-cols-3 gap-2 p-2 border-box"
			>
				{#each items as item, idx}
					<button
						style={`height: ${document.getElementById('Body').offsetHeight / 3 - 10}px`}
						onclick={() => {}}
						class="rounded-md bg-slate-300 w-full flex items-center justify-center"
						>{item?.name || 'item'}</button
					>
				{/each}
			</div>
		</div>
		<!--
    -->
	</div>
	<!--
	<div id="Footer" class="w-full h-[50px] bg-slate-300 flex flex-row">
		{#each options as option}
			<button
				class="bg-slate-400 flex-1 flex items-center justify-center border-r-2 btn last:border-r-0"
				onclick={() => {
					if (option.action) option.action();
					return;
				}}
			>
				{option.text}
			</button>
		{/each}
	</div>
  -->
</div>

<style>
	#Container {
		height: 100%;
		width: 100%;
		position: absolute;
		left: 0px;
		top: 0px;
		display: flex;
		flex-direction: column;
	}
</style>
