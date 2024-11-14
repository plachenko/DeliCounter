<script>
	import { onMount } from 'svelte';

	let SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
	let recognition;
	let speechResult = $state('');

	onMount(() => {
		if (SpeechRecognition) {
			recognition = new SpeechRecognition();
			recognition.lang = 'en-US';
			// recognition.continuous = true;
			recognition.interimResults = true;
			recognition.maxAlternatives = 1;
			
			recognition.onstart = () => {
				console.log('Voice recognition started');
			}

			recognition.onresult = (event) => {
				speechResult = event.results[0][0].transcript;
			}
		}
	});

	function startVoice() {
		recognition.start();
	}
</script>

<div class="flex justify-center items-center flex-col">
	<span>Please say your order</span>
	<span class="text-2xl font-bold italic">{speechResult}</span>
	<div class="absolute bottom-[50px] flex items-center justify-center bg-blue-400">
		<button
		onclick={startVoice}
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

		<div class="absolute rounded-full bg-blue-400 z-[5] size-[54px] animate-ping"></div>
	</div>
	<button class="bg-red-400 rounded-md">Cancel</button>
</div>
