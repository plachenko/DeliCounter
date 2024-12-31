<script>
	import { fly, fade } from 'svelte/transition';
	import { onMount } from 'svelte';
	import LanguageList from '$lib/LanguageList.json';
	import ItemSearchInp from './ItemSearchInp.svelte';

	let {
		currentLanguage,
		setCurrentLanguage,
		curState,
		changeState,
		startVoice,
		voiceStarted,
		order,
		showOrder,
		curOrderItem
	} = $props();

	let totalOrderLength = $state(2);

	let selectingLanguage = $state(false);

	let inputTxt = $state('');
	let inputTxtContainer = $state(null);

	let inputFocused = $state(false);
	// let languages = $state(LanguageList);
	//
	let languages = $state({
		English: [['United States', 'en-US']],
		'Español (Spanish)': [['Spain', 'es-ES']]
	});

	$effect(() => {
		if (order) {
			/*
			totalOrderLength = order
				.map((e) => {
					return e.qty;
				})
				.retuce((cur, acc) => {
					return cur + acc;
				});
      */
		}
		// console.log(totalOrderLength);
		if (!voiceStarted) {
			selectingLanguage = false;
		}
	});

	onMount(() => {
		selectingLanguage = false;
	});
</script>

<div id="" class="flex overflow-hidden w-full relative h-[50px] bg-slate-400 p-2 gap-1">
	{#if selectingLanguage && voiceStarted}
		<div
			in:fly={{ y: -10 }}
			out:fly={{ y: -10 }}
			class="bg-slate-400 rounded-md left-[10%] top-[55px] z-[399] w-[80%] absolute"
		>
			<div class="rounded-md max-h-[66vh] overflow-y-auto w-full bg-slate-400 absolute top-0">
				{#each Object.keys(LanguageList) as lang, idx}
					<button
						onclick={() => {
							setCurrentLanguage(idx);
							selectingLanguage = false;
						}}
						class={`${idx == currentLanguage ? 'font-bold' : ''} hover:bg-slate-300 w-full [&:last-child]:border-none border-b-2 border-dashed p-2 border-slate-500`}
						>{lang}</button
					>
				{/each}
				<button class="flex items-center justify-center p-2 w-full hover:bg-slate-300"
					>More ...</button
				>
			</div>
		</div>
	{/if}
	<button
		onclick={() => {
			changeState(2);
		}}
		aria-label="MenuButton"
		class="flex items-center justify-center bg-slate-200 w-[80px] h-full rounded-md"
	>
		<img alt="mbLogo" class="h-[75%] w-[70%]" src="mbstacked.png" />
	</button>

	<div class="w-full flex flex-1 justify-center relative">
		{#if curOrderItem?.name}
			<div
				transition:fly={{ y: 20, duration: 300 }}
				class="w-full h-full flex-1 flex justify-center absolute items-center bg-slate-300 rounded-md font-bold text-slate-500"
			>
				<span class="">{curOrderItem.name}</span>
			</div>
		{:else}
			<ItemSearchInp
				{startVoice}
				{inputTxt}
				{inputTxtContainer}
				{voiceStarted}
				{selectingLanguage}
			/>
		{/if}
	</div>

	<!-- <div class="absolute top-[30px] z-[999] w-full h-[400px] bg-slate-500"></div> -->
	<button
		disabled={order.length == 0}
		aria-label="ShopButton"
		onclick={() => {
			showOrder();
		}}
		class="bg-slate-300 text-slate-800 w-[50px] h-full rounded-md relative"
	>
		{#if totalOrderLength}
			<div
				class="bg-red-400 p-1 text-xs flex justify-center items-center text-white absolute top-[-2px] right-[-2px] rounded-full"
			>
				{totalOrderLength}
			</div>
		{/if}
		<div
			class={`${order.length ? 'hover:opacity-60' : 'opacity-20'} w-full h-full flex justify-center items-center`}
		>
			<svg
				xmlns="http://www.w3.org/2000/svg"
				fill="none"
				viewBox="0 0 24 24"
				stroke-width="1.5"
				stroke="currentColor"
				class="size-6"
			>
				<path
					stroke-linecap="round"
					stroke-linejoin="round"
					d="M15.75 10.5V6a3.75 3.75 0 1 0-7.5 0v4.5m11.356-1.993 1.263 12c.07.665-.45 1.243-1.119 1.243H4.25a1.125 1.125 0 0 1-1.12-1.243l1.264-12A1.125 1.125 0 0 1 5.513 7.5h12.974c.576 0 1.059.435 1.119 1.007ZM8.625 10.5a.375.375 0 1 1-.75 0 .375.375 0 0 1 .75 0Zm7.5 0a.375.375 0 1 1-.75 0 .375.375 0 0 1 .75 0Z"
				/>
			</svg>
		</div>
	</button>
</div>

<style>
	[contenteditable='true']:empty:before {
		content: attr(placeholder);
		color: #bbb;
		pointer-events: none;
		display: block; /* For Firefox */
	}
</style>
