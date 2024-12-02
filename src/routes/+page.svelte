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

	let timeCol = $state('#0F0');

	let showTimer = $state(false);

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

	const curDate = new Date();

	const curTime = `${curDate.getHours()}:${curDate.getMinutes()}`;

	function startVoice() {
		voiceStarted = !voiceStarted;
	}

	function takeTicket() {
		showLogo = false;
		infoHeaderShown = false;
		sliderShown = false;
		setTimeout(() => {
			curState = 1;
		}, 200);
	}

	function changeState(state) {
		curState = state;
	}

	function setCurrentLanguage(idx) {
		console.log('setting.');
		currentLanguage = idx;
	}

	function timeScale(currentTimeStr) {
		// Define opening and closing times in 24-hour format (e.g., "HH:MM")
		const openingTimeStr = '09:00'; // Opening time at 9:00 AM
		const closingTimeStr = '21:00'; // Closing time at 5:00 PM

		// Convert time strings into minutes from midnight
		function timeToMinutes(timeStr) {
			const [hours, minutes] = timeStr.split(':').map(Number);
			return hours * 60 + minutes;
		}

		// Compute minutes from midnight for each time
		const openingTime = timeToMinutes(openingTimeStr);
		const closingTime = timeToMinutes(closingTimeStr);
		const currentTime = timeToMinutes(currentTimeStr);

		// Calculate the duration in minutes the store is open
		const totalOpenMinutes = closingTime - openingTime;

		// Make sure current time is within the bounds of open and close times
		if (currentTime < openingTime) {
			timeCol = '#F00';
			return 0; // Before opening time
		} else if (currentTime > closingTime) {
			timeCol = '#F00';
			return 1; // After closing time
		} else {
			// Calculate the position of current time within open hours
			return (currentTime - openingTime) / totalOpenMinutes;
		}
	}

	onMount(() => {
		setTimeout(() => {
			curState = 2;
		}, 100);

		setTimeout(() => {
			showBody = true;
		}, 800);

		setTimeout(() => {
			sliderShown = true;
			showLogo = true;
			infoHeaderShown = true;
		}, 1000);

		setTimeout(() => {
			showTimer = true;
		}, 1100);

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
		return alphabetical;
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
								out:fly={{ y: -30 }}
								in:fly={{ y: -30 }}
								class="flex bg-slate-400/30 pb-2 p-1 text-slate-800/30 text-sm items-center w-full absolute top-0 h-full"
							>
								<div class="flex flex-1">store# 88</div>
								<div class="w-[152hx]"><Time /></div>
								<div class="flex-1 flex items-end justify-end">John Doe</div>
								<div class="w-full bg-red-400 h-[2px] absolute bottom-0 left-0">
									{#if showTimer}
										<div
											in:fly={{ x: -100, delay: 300, duration: 1000 }}
											style={`background-color: ${timeCol}; width: ${~~(timeScale('21:59') * window.innerWidth)}px`}
											class="absolute left-0 h-full"
										></div>
									{/if}
								</div>
							</div>
						{/if}
					</div>
					<div class="w-full h-full flex">
						<div class="relative flex-1 h-full flex justify-center relative items-center">
							{#if showLogo}
								<div
									in:fly={{ y: 30 }}
									out:fly={{ y: -10 }}
									class="absolute flex justify-center items-center w-[100px] h-[100px]"
								>
									<div class="absolute top-[30px]">
										<Logo />
									</div>
								</div>
							{/if}
						</div>
						<div class="p-2 relative w-[80px] h-full">
							{#if sliderShown}
								<div
									out:fly={{ x: 40 }}
									in:fly={{ x: 40, duration: 400, delay: 100 }}
									class="w-full h-full"
								>
									<div class="h-full w-full rounded-md bg-slate-400/50 flex items-end">
										<div class="w-full h-[70px] p-2">
											<button
												aria-label="take a ticket"
												onclick={takeTicket}
												class="bg-slate-500 w-full h-full rounded-md"
											></button>
										</div>
									</div>
								</div>
							{/if}
						</div>
					</div>
				</div>
			{/if}

			{#if states[curState].name == 'grid'}
				<div out:fade>
					<Grid {items} />
				</div>
			{/if}
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
