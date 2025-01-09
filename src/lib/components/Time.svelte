<script>
	import { onMount, onDestroy } from 'svelte';
	let dateTime = $state(formatDate());
	let blink = $state(false);
	let curTime = new Date();

	let t1 = '01:34:00';
	let t2 = '02:34:00';
	let pct = $state(((100 * totalSeconds(t1)) / totalSeconds(t2)).toFixed(2));

	let { startTime } = $props();
	let timeDiff = $state(0);

	let tickInt = $state(null);

	onMount(() => {
		dateTime = formatDate();
		tickInt = setInterval(() => {
			blink = !blink;
			dateTime = formatDate();
			if (startTime) {
				timeDiff++;
			}
		}, 1000);
	});

	onDestroy(() => {
		clearInterval(tickInt);
		tickInt = null;
	});

	function formatTimer() {
		return new Date().toLocaleTimeString([], {
			minute: '2-digit',
			seconds: '2-digit'
		});
	}

	function formatDate() {
		return new Date().toLocaleTimeString([], {
			hour: '2-digit',
			minute: '2-digit'
		});
	}

	let TimeDiff = $state(0);

	function totalSeconds(time) {
		let parts = time.split(':');
		return parts[0] * 3600 + parts[1] * 60 + parts[2];
	}
</script>

<div id="timeCont" class="flex flex-col text-slate-400 relative">
	<!--
  <div class="bg-red-400 w-full h-[5px] absolute bottom-[-5px]">
    <div class="h-full w-[39%] bg-green-300"></div>
  </div>
  -->
	{#if startTime}
		<span class="absolute left-1 border-2 rounded-md p-1 py-0 border-red-300"
			>{new Date(timeDiff * 1000).toISOString().substr(14, 5)}</span
		>
	{/if}
	<div class="flex gap-2 w-full">
		<div class="border-r-2 pr-2 flex-1 flex break-keep justify-end">
			<span>{dateTime.split(':')[0]}</span><span class={blink ? 'text-slate-400' : 'text-slate-300'}
				>:</span
			><span>{dateTime.split(':')[1]}</span>
		</div>
		<div class="flex flex-1 w-full">{new Date().toLocaleDateString()}</div>
	</div>
	<!--
  <div class="flex-1 justify-center items-center flex">
    <span class="">Store #84 Salem, NH</span>
  </div>
  -->
</div>
