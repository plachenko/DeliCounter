<script>
	import { onMount } from 'svelte';
	let dateTime = $state(formatDate());
	let blink = $state(false);
	let curTime = new Date();

	let t1 = '01:34:00';
	let t2 = '02:34:00';
	let pct = $state(((100 * totalSeconds(t1)) / totalSeconds(t2)).toFixed(2));

	onMount(() => {
		dateTime = formatDate();
		setInterval(() => {
			blink = !blink;
			dateTime = formatDate();
		}, 1000);

		// gsap.from("#timeCont", { opacity: 0, duration: 0.4, y: -30, delay: 0.4 });

		// Check if date is between 6am and 9pm
	});

	function formatDate() {
		return new Date().toLocaleTimeString([], {
			hour: '2-digit',
			minute: '2-digit'
		});
	}

	function totalSeconds(time) {
		let parts = time.split(':');
		return parts[0] * 3600 + parts[1] * 60 + parts[2];
	}
</script>

<div id="timeCont" class="flex py-2 flex-col text-slate-400 align-center relative w-full">
	<!--
  <div class="bg-red-400 w-full h-[5px] absolute bottom-[-5px]">
    <div class="h-full w-[39%] bg-green-300"></div>
  </div>
  -->
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
