<script>
	import { fly } from 'svelte/transition';
	import { onMount } from 'svelte';

	let SpeechRecognition;
	let recognition;
	let speechResult = $state('testing');
	let voiceStarted = $state(false);
	let loadProducts = $state(false);
	let showing = $state(false);

	onMount(() => {
		setTimeout(() => {
			showing = true;
		}, 100);
		SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;

		if (SpeechRecognition) {
			recognition = new SpeechRecognition();

			recognition.lang = 'en-EN';
			// recognition.continuous = true;
			recognition.interimResults = true;
			recognition.maxAlternatives = 1;

			recognition.onstart = () => {
				voiceStarted = true;
			};

			recognition.onend = () => {
				voiceStarted = false;
			};

			recognition.onresult = (event) => {
				speechResult = event.results[0][0].transcript;
				voiceStarted = false;
			};
		}
	});

	function startVoice() {
		speechResult = '';
		if (voiceStarted) {
			recognition.stop();
			voiceStarted = false;
			return;
		}

		recognition.start();
	}

	function stopVoice() {}
</script>

<div class="flex justify-center items-center flex-col h-full w-full">
	<div
		class="normal-case flex justify-center items-center flex-1 w-full h-full text-2xl italic text-slate-500/60 font-bold"
	>
		{#if speechResult !== ''}
			<span class="font-serif">&ldquo;</span><span>{speechResult}</span><span class="font-serif"
				>&rdquo;</span
			>
		{/if}
	</div>
	<div class="relative h-[120px] flex border-t-2 w-full shrink justify-center items-center">
		{#if showing}
			<button
				in:fly={{ y: -10, delay: 300 }}
				out:fly={{ y: 10 }}
				onclick={startVoice}
				aria-label="start recognition"
				class="absolute drop-shadow rounded-full border-2 bg-red-400 bottom-[10px] size-[79px] z-[10] flex justify-center items-center"
			>
				{#if voiceStarted}
					<span
						in:fly={{ y: -10 }}
						out:fly={{ y: -10 }}
						class="absolute bottom-[2px] font-bold text-xs text-white">X</span
					>
				{/if}
				<svg
					xmlns="http://www.w3.org/2000/svg"
					fill="none"
					viewBox="0 0 24 24"
					stroke-width="1.5"
					stroke="white"
					class="size-10"
				>
					<path
						stroke-linecap="round"
						stroke-linejoin="round"
						d="M12 18.75a6 6 0 0 0 6-6v-1.5m-6 7.5a6 6 0 0 1-6-6v-1.5m6 7.5v3.75m-3.75 0h7.5M12 15.75a3 3 0 0 1-3-3V4.5a3 3 0 1 1 6 0v8.25a3 3 0 0 1-3 3Z"
					/>
				</svg>
			</button>

			{#if voiceStarted}
				<div
					class="absolute rounded-full bg-blue-400 z-[5] size-[54px] animate-ping bottom-[24px]"
				></div>
			{/if}
		{/if}

		{#if showing}
			<div
				in:fly={{ y: 10, delay: 300 }}
				class="absolute top-0 flex justify-center w-full text-sm text-slate-400"
			>
				{#if voiceStarted}
					<div class="absolute" in:fly={{ y: 10 }} out:fly={{ y: 10 }}>Listening...</div>
				{:else}
					<div class="absolute" in:fly={{ y: 10 }} out:fly={{ y: 10 }}>
						Please press the microphone to start your order
					</div>
				{/if}
			</div>
		{/if}
	</div>
	<!--

	<span class="text-2xl font-bold italic text-slate-400/80">&ldquo;{speechResult}&rdquo;</span>
	<div class="absolute bottom-[50px] flex items-center justify-center bg-blue-400">
		{#if loadProducts}
			<div
				class="animate-spin-slow absolute z-[99] border-[4px] border-dashed border-slate-800/50 size-[89px] rounded-full"
			></div>
		{/if}
		<button
			onclick={startVoice}
			aria-label="start recognition"
			class="absolute rounded-full bg-red-400 size-[79px] z-[10] flex justify-center items-center"
		>
			<svg
				xmlns="http://www.w3.org/2000/svg"
				fill="none"
				viewBox="0 0 24 24"
				stroke-width="1.5"
				stroke="white"
				class="size-10"
			>
				<path
					stroke-linecap="round"
					stroke-linejoin="round"
					d="M12 18.75a6 6 0 0 0 6-6v-1.5m-6 7.5a6 6 0 0 1-6-6v-1.5m6 7.5v3.75m-3.75 0h7.5M12 15.75a3 3 0 0 1-3-3V4.5a3 3 0 1 1 6 0v8.25a3 3 0 0 1-3 3Z"
				/>
			</svg>
		</button>

-->

	<!--
		<div class="w-full border-b-2 border-dashed border-slate-400 py-3 text-center">
			Please press the microphone and say your order
		</div>
	</div>
-->
	<!-- <button class="bg-red-400 rounded-md">Cancel</button> -->
</div>
