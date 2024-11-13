<script>
	import { onMount } from 'svelte';
	import { fly } from 'svelte/transition';

	import Header from '$lib/components/Header.svelte';
	import Grid from '$lib/components/Grid.svelte';

	let states = $state([
		{ name: 'menu' },
		{ name: 'grid', showHeader: true },
		{ name: 'ticket' },
		{ name: 'order' }
	]);

	let curState = $state(2);
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
		// document.getElementById('Body')?.scrollTo(document.getElementsByClassName('')[0]);

		setTimeout(() => {
			curState = 1;
		}, 1);
		/*
		document.addEventListener('resize', () => {
			console.log('resizing.');
			itemHeight = ~~document.getElementById('Body').offsetHeight / itemRows - 10;
		});
    */
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

		/*
		items.sort((a, b) => {
			if (a.text < b.text) return -1;
			if (a.text > b.text) return 1;
		});
    */

		// items = [...items, ...Array(800)];

		// $inspect('hi', session);
		// itemHeight = ~~document.getElementById('Body').offsetHeight / itemRows - 10;
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

<div id="Container" class="overflow-hidden bg-slate-100">
	{#if states[curState]?.showHeader}
		<div in:fly={{ y: -100, delay: 100, duration: 600 }}>
			<Header />
		</div>
	{/if}
	<div id="Body" class="flex-1 h-full w-full bg-slate-100 flex relative">
		<!-- <Items /> -->

		{#if states[curState].name == 'grid'}
			<Grid {items} />
		{/if}

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
