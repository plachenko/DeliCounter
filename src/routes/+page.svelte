<script>
	import { onMount } from 'svelte';
	// import Items from '$lib/components/Items.svelte';
	import Grid from '$lib/components/Grid.svelte';

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

	onMount(() => {
		items = [
			{ name: 'beef', color: '#8b0000' },
			{ name: 'bologna', color: '#FF6347' },
			{ name: 'loaf', color: '' },
			{ name: 'cheese', color: '#b3a100' },
			{ name: 'chicken', color: '#ffb433' },
			{ name: 'ham', color: '#FFA07A' },
			{ name: 'italian', color: '#A52A2A' },
			{ name: 'turkey', color: '#edc478' },
			{ name: 'franks', color: '#F00F00' }
		];

		items = items.sort((a, b) => {
			if (a.text < b.text) return -1;
			if (a.text > b.text) return 1;
		});

		// items = [...items, ...Array(800)];

		// $inspect('hi', session);
		itemHeight = ~~document.getElementById('Body').offsetHeight / itemRows - 10;
	});

	function getOrder() {
		return null;
	}

	function getTicketNumber() {
		return 'A' + ~~(Math.random() * 100);
	}

	function sorted(arr) {
		arr.sort((a, b) => {
			if (a.text < b.text) return -1;
			if (a.text > b.text) return 1;
		});
		/*
    let alphabetical = arr.sort((a, b) => {
      if(a < b) return a;
      return b;
    });
    return alphabetical;
      */
		// return ['test', 'hi']b;
	}
</script>

<div id="Container" class="overflow-hidden">
	<div id="Header" class="flex w-full h-[50px] bg-slate-400 p-2 gap-1">
		<button
			aria-label="MenuButton"
			class="flex items-center justify-center bg-slate-300 w-[80px] h-full rounded-md"
		>
			<img alt="mbLogo" class="h-[75%] w-[70%]" src="mbstacked.png" />
		</button>
		<div class="flex-1 bg-slate-200 rounded-md"></div>
		<button aria-label="ShopButton" class="bg-slate-300 w-[50px] h-full rounded-md"></button>
	</div>
	<div id="Body" class="flex-1 w-full bg-slate-100 flex relative">
		<!-- <Items /> -->
		<Grid {items} />
		<!--
			<div class="h-full bg-slate-400 w-[30px] p-1 absolute right-[0px] top-[0px]">
				<div
					style={`
          height: ${itemHeight}px;
          `}
					class="bg-slate-700 w-full rounded-md h-full"
				></div>
>
			</div>
	</div>
-->
	</div>
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
</div>
  -->

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
