<script>
	import { onMount } from 'svelte';
	import { fly, fade } from 'svelte/transition';
	import Cities from '$lib/Cities.json';

	import Time from '$lib/components/Time.svelte';

	import StoreLocations from '$lib/stores.json';

	import Header from '$lib/components/Header.svelte';
	import Grid from '$lib/components/Grid.svelte';
	import VoiceRecognition from '$lib/components/VoiceRecognition.svelte';
	import Logo from '$lib/components/Logo.svelte';

	import DeliItems from '$lib/DeliItems.json';

	let currentLanguage = $state(10);
	let sliderShown = $state(false);
	let order = $state([]);

	let stores = $state([]);
	let curStore = $state(0);

	let showStoreInfo = $state(false);

	let ticketShown = $state(true);

	let currentCity = $state('Tewksbury');
	let currentUSState = $state(20);

	let infoBubble = $state(false);
	let ticking = $state(false);
	let timeWidthTicker = $state(null);

	let showFullHeader = $state(false);

	let timeCol = $state('#0F0');

	let showTimer = $state(false);

	let infoHeaderShown = $state(false);
	let showLogo = $state(false);
	let showGrid = $state(false);

	let curLocation = $state(null);

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

	let distThreshold = $state(0.02);

	const curDate = new Date();

	const curTime = `${curDate.getHours()}:${curDate.getMinutes()}`;
	let scaleWidth = $state(0);

	function startVoice() {
		voiceStarted = !voiceStarted;
	}

	function takeTicket() {
		setTimeout(() => {
			showLogo = false;
			infoHeaderShown = false;
			sliderShown = false;
			setTimeout(() => {
				curState = 1;
			}, 200);
		}, 400);
		ticketShown = false;
		infoBubble = false;
	}

	function changeState(state) {
		setTimeout(() => {
			curState = state;
		}, 100);

		if (state !== 2) {
			showGrid = false;
		}

		if (state == 2) {
			showTicketEls();
			voiceStarted = false;
			clearInterval(timeWidthTicker);
			timeWidthTicker = null;
		}
	}

	function setCurrentLanguage(idx) {
		// console.log('setting.');
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
			timeCol = '#Ff0';
			return 1; // After closing time
		} else {
			// Calculate the position of current time within open hours
			return (currentTime - openingTime) / totalOpenMinutes;
		}
	}

	function showTicketEls() {
		setTimeout(() => {
			// infoBubble = true;
			ticking = true;
			ticketShown = true;
			setTimeout(() => {
				ticking = false;
			}, 100);
			setTimeout(() => {
				infoBubble = false;
			}, 4500);
			sliderShown = true;
			infoHeaderShown = true;
			showLogo = true;
		}, 700);

		setTimeout(() => {
			showTimer = true;
		}, 700);
	}

	function between(val, min = 1, max = 1) {
		let v = val * 1000000;

		return v >= v - min && v <= v + max;
	}

	function distance(p1, p2) {
		let x = p2[0] - p1[0];
		let y = p2[1] - p1[1];

		let d = Math.sqrt(Math.pow(x, 2) + Math.pow(y, 2));

		return d;
	}
	function getNearestMB() {
		stores = [];
		navigator.geolocation.getCurrentPosition((e) => {
			let curLoc = [e.coords.latitude, e.coords.longitude];
			StoreLocations.forEach((eSt) => {
				let storeLoc = eSt.field_geolocation.split(',');

				const dist = distance(curLoc, storeLoc);
				eSt.distance = dist;

				if (dist < distThreshold) {
					stores = [...stores, eSt];
					stores = stores.sort((a, b) => {
						return a.distance - b.distance;
					});
				}
			});
		});
	}

	onMount(() => {
		showTicketEls();
		setTimeout(() => {
			curState = 2;
			// document.getElementById('TicketNumber').textContent = 'new';
		}, 100);

		let areas = [];
		StoreLocations.forEach((e) => {
			if (areas.includes(e.field_address_administrative_area)) return;

			areas.push(e.field_address_administrative_area);
		});
		console.log(areas);

		for (let e in DeliItems) {
			// console.log(DeliItems[e].description);
		}

		// getNearestMB();

		setTimeout(() => {
			showBody = true;
		}, 800);
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
	});

	const USStates = [
		'AL',
		'AK',
		'AZ',
		'AR',
		'CA',
		'CO',
		'CT',
		'DE',
		'FL',
		'GA',
		'HI',
		'ID',
		'IL',
		'IN',
		'IA',
		'KS',
		'KY',
		'LA',
		'ME',
		'MD',
		'MA',
		'MI',
		'MN',
		'MS',
		'MO',
		'MT',
		'NE',
		'NV',
		'NH',
		'NJ',
		'NM',
		'NY',
		'NC',
		'ND',
		'OH',
		'OK',
		'OR',
		'PA',
		'RI',
		'SC',
		'SD',
		'TN',
		'TX',
		'UT',
		'VT',
		'VA',
		'WA',
		'WV',
		'WI',
		'WY'
	];

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
		<div out:fade in:fly={{ y: -100, delay: 100, duration: 600 }}>
			<Header
				{order}
				{setCurrentLanguage}
				{changeState}
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
				{#if showStoreInfo}
					<div
						transition:fly={{ y: -100 }}
						class="overflow-y-scroll bg-slate-300 w-full h-full absolute top-[27px] left-0 z-[960]"
					>
						{#if !stores.length}
							<div class="flex flex-col justify-center items-center h-full w-full">
								<div class="flex flex-col gap-7">
									<div class="font-bold text-slate-400">No Current Store has been set</div>
									<button
										class="bg-slate-400 p-2 rounded-md"
										onclick={() => {
											getNearestMB();
										}}>Use device location</button
									>
								</div>
								<div class="w-full flex items-center justify-center h-[30px]">
									<div class="w-full border-b"></div>
									<span class="absolute bg-slate-300 px-2">or</span>
								</div>
								<div class="flex flex-col">
									<span>enter a city / state</span>
									<div class="flex gap-2">
										<div class="flex w-full h-full relative">
											<input
												type="text"
												placeholder="please enter a city"
												class="rounded-md p-1"
												bind:value={currentCity}
											/>
											{#if currentCity !== ''}
												<button
													class="absolute right-[6px] top-[8px] text-slate-300"
													onclick={() => {
														currentCity = '';
													}}>x</button
												>
											{/if}
										</div>
										<select class="rounded-md" bind:value={currentUSState}>
											{#each USStates as state, stateIdx}
												<option value={stateIdx}>{state}</option>
											{/each}
										</select>
										<button
											disabled={currentCity == ''}
											class={`bg-slate-400 p-2 rounded-md ${currentCity == '' ? 'opacity-30' : ''}`}
											>search</button
										>
									</div>
								</div>
							</div>
						{:else}
							<div>
								stores within distance<input
									bind:value={distThreshold}
									min=".01"
									max="2"
									step=".01"
									oninput={() => {
										getNearestMB();
									}}
									type="range"
								/>{distThreshold}

								{stores.length} /
								{StoreLocations.length}
							</div>
						{/if}
						{#each stores as store, idx}
							<!--
							{#each Object.keys(store) as prop}
								<div class="flex text-xs p-1">
									<span class="block flex-1">{prop}</span>
									<span class="block">{store[prop]}</span>
								</div>
							{/each}
              -->
							<div class="border-b flex justify-center relative">
								{#if curStore !== idx}
									<button
										aria-label="set current store"
										onclick={() => {
											curStore = idx;
										}}
										class="w-full h-full absolute"
									></button>
								{/if}
								{store.title}
							</div>
							{#if idx == curStore}
								<div class=" bg-slate-400">
									<h3 class="header text-xl text-center">Manager</h3>
									<div class="flex border-b border-slate-800">
										<span class="flex-1">
											{store.storeInfo.Manager.name}
										</span>
										<span>
											{stores[curStore].storeInfo.Manager.years} years
										</span>
									</div>
									<h3 class="h3 text-xl text-center">Assistant Manager</h3>
									{#each store.storeInfo.Assistant as assistant}
										<div class="border-b border-slate-800 flex">
											<span class="flex-1">
												{assistant.name}
											</span>
											<span>
												{assistant.years} years
											</span>
										</div>
									{/each}
								</div>
							{/if}
						{/each}
					</div>
				{/if}
				<div class="w-full h-full flex flex-col">
					<div class="h-[30px] w-full relative">
						{#if infoHeaderShown}
							<div
								out:fly={{ y: -30 }}
								in:fly={{ y: -30 }}
								class="z-[21] flex bg-slate-400/30 px-3 p-2 text-slate-800/30 text-sm items-center w-full absolute top-0 h-full"
							>
								{#if stores.length}
									<div class="flex flex-1">MB {stores[curStore].title.split(' ').pop()}</div>
								{:else}
									<span>No store found</span>
								{/if}

								<div class="justify-end items-end flex flex-1">
									<div class={`${!stores.length ? 'flex-1 w-full flex justify-end' : 'w-[156px]'}`}>
										<Time />
									</div>
								</div>
								{#if stores.length}
									<div class="flex-1 flex items-end justify-end">
										<span>
											{stores[curStore].storeInfo.Manager.name}
										</span>
									</div>
								{/if}
								<div class="w-full bg-red-400 h-[2px] absolute bottom-0 left-0">
									{#if showTimer}
										<div
											in:fly={{ x: -100, delay: 100, duration: 1000 }}
											style={`width: ${scaleWidth}px`}
											class="absolute left-0 h-full bg-green-300"
										></div>
									{/if}
								</div>
								<button
									aria-label="show store information"
									onclick={() => {
										showStoreInfo = !showStoreInfo;
									}}
									class="absolute left-0 w-full h-full"
								></button>
							</div>
						{/if}
					</div>
					<div class="w-full h-full flex">
						<div class="relative flex-1 h-full flex justify-center relative items-center">
							{#if infoBubble}
								<div
									in:fly={{ y: -10, duration: 1000 }}
									out:fly={{ y: -20 }}
									class="absolute top-[10px] w-full flex h-[30px] justify-center items-center"
								>
									<div class="absolute bg-slate-800/20 overflow-hidden rounded-md w-[230px] h-full">
										{#if ticking}
											<div
												out:fly={{ x: -260, duration: 8000 }}
												class="z-[8] w-full h-full bg-slate-800/80 absolute"
											></div>
										{/if}
										<div class="absolute z-[19] w-full h-full p-1">
											<div
												class="bg-red-300 w-full h-full z-[19] rounded-md text-red-800 border-red-400 flex justify-center items-center text-sm p-1"
											>
												Store will be closing in 15 minutes
											</div>
										</div>
									</div>
								</div>
							{/if}

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
							{#if !infoBubble && sliderShown}
								<div
									out:fly={{ y: 10 }}
									in:fly={{ y: 10 }}
									class="bg-red-400/30 border-2 border-red-400 p-2 rounded-md absolute bottom-[15px]"
								>
									<span class="text-red-400"> Slide down to start order </span>
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
									<div
										class="h-full w-full relative rounded-md border-box border-[2px] bg-slate-400/50 flex items-end"
									>
										<!-- <div class="bg-red-400 flex-1 w-full h-[10px] absolute top-[0px]"></div> -->
										<div class="w-full h-[100px] absolute top-0 p-1">
											{#if ticketShown}
												<div
													out:fly={{ y: 80 }}
													class="p-2 bg-slate-400 border-[2px] border-slate-800/20 rounded-md w-full h-full"
												>
													<img alt="Ticket Stub" src="ticket.svg" />
												</div>
											{/if}
										</div>
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
					<Grid {items} {showGrid} />
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
