<script>
	import { fly, fade } from 'svelte/transition';
	import { onMount } from 'svelte';
	import LanguageList from '$lib/LanguageList.json';

	let { currentLanguage, setCurrentLanguage, curState, changeState, startVoice, voiceStarted } =
		$props();

	let selectingLanguage = $state(false);

	let inputTxt = $state('');
	let inputTxtContainer = $state(null);

	let order = $state([]);
	let inputFocused = $state(false);
	// let languages = $state(LanguageList);

	$effect(() => {
		if (!voiceStarted) {
			selectingLanguage = false;
		}
	});

	onMount(() => {
		selectingLanguage = false;
	});
</script>

<div id="Header" class="flex w-full relative h-[50px] bg-slate-400 p-2 gap-1">
	{#if selectingLanguage && voiceStarted}
		<div
			in:fly={{ y: -10 }}
			out:fly={{ y: -10 }}
			class="bg-slate-400 rounded-md left-[10%] top-[55px] z-[399] w-[80%] absolute"
		>
			<div class="rounded-md h-[66vh] overflow-y-auto w-full bg-slate-400 absolute top-0">
				{#each Object.keys(LanguageList) as lang, idx}
					<button
						onclick={() => {
							setCurrentLanguage(idx);
							selectingLanguage = false;
						}}
						class={`${idx == currentLanguage ? 'font-bold' : ''} hover:bg-slate-300 w-full border-b-2 border-dashed p-2 border-slate-500`}
						>{lang}</button
					>
				{/each}
			</div>
		</div>
	{/if}
	<button
		aria-label="MenuButton"
		class="flex items-center justify-center bg-slate-200 w-[80px] h-full rounded-md"
	>
		<img alt="mbLogo" class="h-[75%] w-[70%]" src="mbstacked.png" />
	</button>
	<div
		class={`p-1 border-box flex-1 overflow-hidden bg-slate-200 relative max-w-full rounded-md flex items-center pr-9`}
		`
	>
		{#if inputFocused}
			<div
				in:fade
				out:fade
				class="border-blue-400 border-[2px] w-full h-full rounded-md absolute left-0"
			></div>
		{/if}

		{#if !voiceStarted}
			<div
				id="inputTxtField"
				out:fly={{ y: 10 }}
				in:fly={{ y: 10 }}
				onfocus={() => {
					inputFocused = true;
				}}
				onblur={() => {
					inputFocused = false;
				}}
				placeholder="Enter a product or tag..."
				contenteditable="true"
				bind:this={inputTxtContainer}
				bind:textContent={inputTxt}
				class="pl-2 overflow-x-auto whitespace-nowrap w-full flex items-center outline-none linebreak-none"
			></div>
		{:else}
			<div in:fly={{ y: -10 }} out:fly={{ y: -10 }} class="left-0 pr-9 absolute p-1 w-full h-full">
				<button
					onclick={() => {
						selectingLanguage = !selectingLanguage;
					}}
					class={`relative select-none cursor-pointer w-full h-full bg-slate-400/50 rounded-md text-center`}
				>
					{#if selectingLanguage}
						<div
							in:fade
							class="absolute w-full h-full border-2 border-slate-500/60 rounded-md"
						></div>
					{/if}
					<div class="flex h-full">
						<div
							class="w-[50px] gap-1 h-full text-slate-500 flex border-r border-slate-500 justify-center items-center"
						>
							<div class="flex flex-col flex-1 w-full h-full">
								{#if selectingLanguage}
									<span class="absolute" in:fly={{ y: 10 }} out:fly={{ y: 10 }}>&#9650;</span>
								{:else}
									<span class="absolute" in:fly={{ y: -10 }} out:fly={{ y: -10 }}>&#9660;</span>
								{/if}
							</div>
							<span> &#x1F310; </span>
						</div>
						<!--
							{#if selectingLanguage}
								<span class="absolute" in:fly={{ y: 10 }} out:fly={{ y: 10 }}>&#9650;</span>
							{:else}
								<span class="absolute" in:fly={{ y: -10 }} out:fly={{ y: -10 }}>&#9660;</span>
							{/if}
							<span class="ml-[40px]"> &#x1F310; </span>
						</div>
-->

						<span class="flex-1 flex relative items-center justify-center text-slate-500">
							<!--
							<span class="absolute left-[5px] text-slate-500 bg-white/30 rounded-md p-1 text-xs"
								>&#x1F310;</span
							>-->
							<strong>{Object.keys(LanguageList)[currentLanguage]}</strong>
						</span>
					</div>
				</button>
			</div>
		{/if}

		{#if inputTxt}
			<button
				in:fade={{ duration: 300 }}
				out:fade={{ duration: 300 }}
				onclick={() => {
					inputTxt = '';
					inputTxtContainer.focus();
					setTimeout(() => {
						inputFocused = true;
					}, 1);
				}}
				class="select-none bg-slate-500/30 text-slate-500/40 hover:text-slate-500/80 font-bold size-7 flex items-center justify-center absolute right-[5px] rounded-md"
			>
				X
			</button>
		{:else}
			<button
				aria-label="start voice recognition"
				in:fade={{ duration: 300 }}
				out:fade={{ duration: 300 }}
				onclick={() => {
					/*
					if (curState == 4) {
						changeState(1);
						return;
					}
					changeState(4);
          */
					startVoice();
				}}
				class={`${voiceStarted ? 'border-2 border-slate-500/60 text-slate-500/80' : ''} border-box select-none bg-slate-500/30 p-1 text-slate-500/40 hover:text-slate-500/80 font-bold size-7 flex items-center justify-center absolute right-[5px] rounded-md`}
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
						d="M12 18.75a6 6 0 0 0 6-6v-1.5m-6 7.5a6 6 0 0 1-6-6v-1.5m6 7.5v3.75m-3.75 0h7.5M12 15.75a3 3 0 0 1-3-3V4.5a3 3 0 1 1 6 0v8.25a3 3 0 0 1-3 3Z"
					/>
				</svg>
			</button>
		{/if}
	</div>
	<!-- <div class="absolute top-[30px] z-[999] w-full h-[400px] bg-slate-500"></div> -->
	<button
		disabled={order.length == 0}
		aria-label="ShopButton"
		class="bg-slate-300 w-[50px] h-full rounded-md relative"
	>
		{#if order.length}
			<div class="bg-red-400 size-3 absolute top-[2px] right-[2px] rounded-full"></div>
		{/if}
		<div
			class={`opacity-20 ${order.length ? 'hover:opacity-60' : ''} w-full h-full flex justify-center items-center`}
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
