<script>
	import { fade, fly } from 'svelte/transition';
	let { inputTxt, inputTxtContainer, voiceStarted, selectingLanguage, startVoice } = $props();
	let inputFocused = $state(false);
</script>

<div
	transition:fly={{ y: -29 }}
	class={`p-1 border-box flex-1 overflow-hidden bg-slate-200 relative max-w-full rounded-md flex items-center pr-9`}
>
	{#if inputFocused}
		<div
			in:fade
			out:fade
			class="border-slate-600/80 border-[2px] w-full h-full rounded-md absolute left-0"
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
					<div in:fade class="absolute w-full h-full border-2 border-slate-500/60 rounded-md"></div>
				{/if}
				<div class="flex h-full">
					<div
						class="w-[50px] gap-1 h-full relative text-slate-500 flex border-r border-slate-500 justify-center items-center"
					>
						<div class="bg-slate-200/30 rounded-l-md flex flex-col relative flex-1 w-full h-full">
							{#if selectingLanguage}
								<span class="absolute left-[4px]" in:fly={{ y: 10 }} out:fly={{ y: 10 }}
									>&#9650;</span
								>
							{:else}
								<span class="absolute left-[4px]" in:fly={{ y: -10 }} out:fly={{ y: -10 }}
									>&#9660;</span
								>
							{/if}
						</div>
						<span class="opacity-30 absolute right-[8px]"> &#x1F310; </span>
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
