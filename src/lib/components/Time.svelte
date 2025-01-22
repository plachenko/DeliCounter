<script>
	import { onMount, onDestroy } from 'svelte';
	let dateTime = $state(formatDate());
	let blink = $state(false);
	let curTime = new Date();
	let showFuture = $state(false);
	let futureDateEl = $state(null);

	let t1 = '01:34:00';
	let t2 = '02:34:00';
	let pct = $state(((100 * totalSeconds(t1)) / totalSeconds(t2)).toFixed(2));

	let { startTime, addFutureDate, futureDate } = $props();
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
		showFuture = false;
		clearInterval(tickInt);
		tickInt = null;
	});

	function formatTimer() {
		return new Date().toLocaleTimeString([], {
			minute: '2-digit',
			seconds: '2-digit'
		});
	}

	function formatFuture() {
		return futureDate.toLocaleTimeString([], {
			hour: '2-digit',
			minute: '2-digit'
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
	{#if startTime}
		<span class="absolute left-1 border-2 rounded-md p-1 py-0 border-red-300"
			>{new Date(timeDiff * 1000).toISOString().substr(14, 5)}</span
		>
	{/if}
	<div class="flex gap-2 w-full">
		{#if addFutureDate}
			<div class="w-full absolute h-full items-center flex justify-center">
				<div class="w-[200px] z-[999] absolute top-0 h-full">
					<button
						bind:this={futureDateEl}
						onclick={() => addFutureDate()}
						class={`${!showFuture ? 'bg-slate-400/20' : 'bg-green-400/20 font-bold border-red-400'} rounded-md border-2 absolute top-0 w-full h-full`}
					>
					</button>
					{#if futureDate}
						<button
							onclick={() => {
								showFuture = !showFuture;
							}}
							class="bg-slate-400/30 absolute right-[-26px] rounded-l-none rounded-md px-1"
							>🕓</button
						>{/if}
				</div>
			</div>
		{/if}
		<div class="relative border-r-2 pr-2 flex-1 flex break-keep justify-end">
			{#if showFuture}
				{new Date(futureDate).toLocaleTimeString([], {
					hour: '2-digit',
					minute: '2-digit'
				})}
			{:else}
				<span>{dateTime.split(':')[0]}</span><span
					class={blink ? 'text-slate-400' : 'text-slate-300'}>:</span
				><span>{dateTime.split(':')[1]}</span>
			{/if}
		</div>
		<div class="flex flex-1 w-full">
			{#if showFuture}
				{new Date(futureDate).toLocaleDateString()}
			{:else}
				{new Date().toLocaleDateString()}
			{/if}
		</div>
	</div>
</div>
