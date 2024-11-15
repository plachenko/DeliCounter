<script>
	import { fly, fade } from 'svelte/transition';

	let { curState, changeState, startVoice } = $props();

	let inputTxt = $state('');
	let inputTxtContainer = $state(null);

	let order = $state([]);
	let inputFocused = $state(false);
</script>

<div id="Header" class="flex w-full h-[50px] bg-slate-400 p-2 gap-1">
	<button
		aria-label="MenuButton"
		class="flex items-center justify-center bg-slate-200 w-[80px] h-full rounded-md"
	>
		<img alt="mbLogo" class="h-[75%] w-[70%]" src="mbstacked.png" />
	</button>
	<div
		class={`border-box flex-1 overflow-hidden bg-slate-200 relative max-w-full rounded-md flex items-center pl-2 pr-9`}
		`
	>
		{#if inputFocused}
			<div
				in:fade
				out:fade
				class="border-slate-800 border-2 w-full h-full rounded-md absolute left-0"
			></div>
		{/if}

		<div
			id="inputTxtField"
			out:fly={{ x: 100 }}
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
			class="overflow-x-auto whitespace-nowrap w-full flex items-center outline-none linebreak-none"
		></div>
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
				class={`${curState == 4 ? 'border-2 border-slate-500/60 text-slate-500/80' : ''} border-box select-none bg-slate-500/30 p-1 text-slate-500/40 hover:text-slate-500/80 font-bold size-7 flex items-center justify-center absolute right-[5px] rounded-md`}
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
