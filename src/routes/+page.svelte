<script>
	import { onMount } from 'svelte';
	import { fly, fade } from 'svelte/transition';

	import Time from '$lib/components/Time.svelte';

	import Header from '$lib/components/Header.svelte';
	import Grid from '$lib/components/Grid.svelte';
	import VoiceRecognition from '$lib/components/VoiceRecognition.svelte';
	import Logo from '$lib/components/Logo.svelte';

	let currentLanguage = $state(10);
	let sliderShown = $state(false);
	let order = $state([]);

	let infoHeaderShown = $state(false);
	let showLogo = $state(false);

	let states = $state([
		{ name: 'menu' },
		{ name: 'grid', showHeader: true },
		{ name: 'ticket' },
		{ name: 'order' },
		{ name: 'voiceRecognition', showHeader: true }
	]);

	let voiceStarted = $state(false);

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

	let showBody = $state(false);

	function startVoice() {
		voiceStarted = !voiceStarted;
	}

	function takeTicket() {
		curState = 1;
	}

	function changeState(state) {
		curState = state;
	}

	function setCurrentLanguage(idx) {
		console.log('setting.');
		currentLanguage = idx;
	}

	onMount(() => {
		setTimeout(() => {
			curState = 2;
		}, 100);

		setTimeout(() => {
			showBody = true;
			showLogo = true;
		}, 800);

		setTimeout(() => {
			sliderShown = true;
			infoHeaderShown = true;
		}, 1000);

		items = [
			{ name: 'beef', color: '#8b0000' },
			{ name: 'bologna', color: '#FF6347' },
			{ name: 'loaf', color: '' },
			{ name: 'cheese', color: '#b3a100' },
			{ name: 'chicken', color: '#ffb433' },
			{ name: 'ham', color: '#FFA07A' },
			{ name: 'italian', color: '#A52A2A' },
			{ name: 'turkey', color: '#edc478' },
			{ name: 'franks', color: '#F00F00' },
			{ name: 'platters', color: '#F00F00' }
		];

		/*
		items.sort((a, b) => {
			if (a.text < b.text) return -1;
			if (a.text > b.text) return 1;
		});
    	*/

		// items = [...items, ...Array(100)];

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
      */
		return alphabetical;
		// return ['test', 'hi']b;
	}
</script>

<div id="Container" class="overflow-hidden bg-slate-100">
	{#if states[curState]?.showHeader}
		<div in:fly={{ y: -100, delay: 100, duration: 600 }}>
			<Header
				{order}
				{setCurrentLanguage}
				{currentLanguage}
				{curState}
				{startVoice}
				{voiceStarted}
			/>
		</div>
	{/if}

	{#if showBody}
		<div in:fade id="Body" class="flex-1 h-full w-full flex relative overflow-hidden">
			{#if voiceStarted}
				<div
					in:fly={{ y: 10 }}
					out:fly={{ y: 10 }}
					class="absolute w-full h-full bg-slate-200 z-[99]"
				>
					<VoiceRecognition {currentLanguage} />
				</div>
			{/if}

			{#if states[curState].name == 'ticket'}
				<div class="w-full h-full flex flex-col">
					<div class="h-[30px] w-full relative">
						{#if infoHeaderShown}
							<div
								in:fly={{ y: -30 }}
								class="flex bg-slate-400/30 pb-2 p-1 text-slate-800/30 text-sm items-center w-full absolute top-0 h-full"
							>
								<div class="flex flex-1">store# 88</div>
								<div class="w-[152hx]"><Time /></div>
								<div class="flex-1 flex items-end justify-end">John Doe</div>
								<div class="w-full bg-red-400 h-[2px] absolute bottom-0 left-0">
									<div class="absolute left-0 bg-green-400 h-full w-[30px]"></div>
								</div>
							</div>
						{/if}
					</div>
					<div class="w-full h-full flex">
						<div class="flex-1 h-full flex justiy-center relative items-center">
							{#if showLogo}
								<Logo />
								<!--
								<div
									in:fly={{ x: 130, duration: 3800 }}
									class="bg-yellow-300 w-[300px] h-full absolute"
								>
								</div>
-->
							{/if}
						</div>
						<div class="p-2 relative w-[80px] h-full">
							{#if sliderShown}
								<div in:fly={{ x: 40, duration: 400, delay: 100 }} class="w-full h-full">
									<div class="h-full w-full rounded-md bg-slate-400/50 flex items-end">
										<div class="w-full h-[70px] p-2">
											<button onclick={takeTicket} class="bg-slate-500 w-full h-full rounded-md"
											></button>
										</div>
										<!--
										<div class="w-full h-[60px] h-max-[30px] absolute left-0 bottom-0">
										</div>
-->
									</div>
								</div>
							{/if}
						</div>
					</div>
				</div>
				<!--
				<div class="w-full h-full flex justify-center items-center relative">
					{#if infoHeaderShown}
						<div
							in:fly={{ y: -30 }}
							class="flex bg-slate-400/30 p-1 text-slate-800/30 text-sm items-center w-full absolute top-0"
						>
							<div class="flex flex-1">store# 88</div>
							<div class="w-[152hx]"><Time /></div>
							<div class="flex-1 flex items-end justify-end">John Doe</div>
							<div class="w-full bg-red-400 h-[2px] absolute bottom-0 left-0">
								<div class="absolute left-0 bg-green-400 h-full w-[30px]"></div>
							</div>
						</div>
					{/if}

					<Logo />
					{#if sliderShown}
						<div
							in:fly={{ x: 40, duration: 400, delay: 100 }}
							class="w-[80px] p-2 h-full absolute right-0 border-box"
						>
							<div class="h-full w-full rounded-md bg-slate-400/50 p-2">
								<div class="w-full h-[60px] h-max-[30px] absolute left-0 bottom-0 p-3">
									<button onclick={takeTicket} class="bg-slate-500 w-full h-full rounded-md"
									></button>
								</div>
							</div>
						</div>
					{/if}
				</div>
-->
			{/if}

			{#if states[curState].name == 'grid'}
				<div out:fade>
					<Grid {items} />
				</div>
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
	{/if}
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
