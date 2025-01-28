<script>
	import { onMount } from 'svelte';
	import Time from '$lib/components/Time.svelte';

	let orders = $state([]);
	let showOrder = $state(false);
	let cursor = $state(null);
	let timeslotEl = $state(null);
	let timeslotLock = $state(true);
	let curOrder = $state(null);
	let options = $state(['back', 'cancel order']);
	let curTime = $state(new Date().toLocaleTimeString([]));

	$effect(() => {
		if (showOrder == false) {
			window.requestAnimationFrame(tick);
		}

		if (!orders[curOrder]?.finished) {
			options[2] = 'finish order';
		} else {
			options[2] = 'reopen order';
		}
	});

	onMount(() => {
		orders = JSON.parse(window.localStorage.getItem('allOrders')) || [];
		timeslotEl.addEventListener('scroll', () => {
			console.log('scrolling');
			timeslotLock = false;
		});
		window.requestAnimationFrame(tick);
	});

	function formatTime(seconds) {
		const hours = ~~seconds / 60;
	}
	function tick(el) {
		// console.log((new Date().getMinutes() / 60) * (new Date().getSeconds() / 60));
		orders.forEach((e) => {
			// e.timeSince = new Date().getTime() - new Date(e.dateStart).getTime() / 60;
			// console.log(e.timeSince);
			e.timeSince = msToTime(new Date().getTime() - new Date(e.dateStart).getTime());
		});

		function msToTime(ms) {
			let seconds = ~~(ms / 1000);
			let minutes = ~~(seconds / 60);
			let hours = ~~(minutes / 60);

			return `${hours > 0 ? `${hours} hours` : ''} ${minutes % 60} mins`;
		}

		curTime = new Date().toLocaleTimeString([], {
			hour: '2-digit',
			minute: '2-digit',
			second: '2-digit'
		});
		if (showOrder || !cursor?.offsetLeft) return;
		let l = cursor.offsetLeft;
		cursor.style.left = l + 1 + 'px';
		focusCursor();
		setTimeout(() => {
			window.requestAnimationFrame(tick);
		}, 1000);
	}

	function focusCursor() {
		if (!timeslotLock || !cursor) return;
		cursor.scrollIntoView();
		// timeslotEl.scrollLeft = cursor?.offsetLeft;
		// timeslotEl.scrollLeft = 100;
		// timeslotEl.scrollLeft = cursor?.offsetLeft;
	}

	function linearPos(second, divWidth) {
		if (second < 0 || second > 59) {
			throw new Error('Second not valid');
		}

		const increment = divWidth / (60 * 60);
		const xPos = second * increment;

		return xPos;
	}

	function setAllOrders() {
		window.localStorage.setItem('allOrders', JSON.stringify(orders));
	}
</script>

{#if showOrder}
	<div class="absolute top-0 left-0 w-full h-full bg-slate-100">
		<div class="flex flex-col w-full h-full">
			<span class="text-2xl w-full text-center">
				{orders[curOrder].name}
			</span>
			<div class="h-full w-full flex-1">
				{#each orders[curOrder].items as item}
					<div class="bg-slate-300 flex p-1 border-b-2 border-dashed">
						<span class="w-full flex-1">
							{item.name}
						</span>
						<span>
							${item.price.toFixed(2)}
						</span>
					</div>
				{/each}
			</div>
			<div class="flex gap-1 p-1 w-full">
				{#each options as opt, idx}
					<button
						onclick={() => {
							switch (idx) {
								case 0:
									break;
								case 1:
									break;
								case 2:
									if (orders[curOrder].finished == null) {
										orders[curOrder].finished = new Date();
									} else {
										orders[curOrder].finished = null;
										orders[curOrder].reopened.push(new Date());
									}
									setAllOrders();
									break;
							}
							showOrder = false;
							curOrder = null;
						}}
						class="bg-slate-400 rounded-md p-2 w-full flex-1">{opt}</button
					>
				{/each}
			</div>
		</div>
	</div>
{:else}
	<div class="flex flex-col w-full h-full">
		<div class="flex items-center p-1 border-b-2 border-dashed">
			<h3 class="text-2xl">Orders</h3>
			<div class="w-full flex-1 flex justify-center">
				<Time />
			</div>
		</div>
		<div class="flex-1 flex-col flex relative">
			<div class="h-full flex flex-col flex-1 overflow-hidden relative">
				<div bind:this={timeslotEl} class="bg-red-400 h-[200px] w-full relative overflow-x-auto">
					{#each Array(24) as col, idx}
						<div class="bg-blue-300/30 w-[200px] h-full rounded-md border-l-2 border-slate-900">
							{idx}
						</div>
					{/each}
					<div class="absolute w-full h-full">
						<button
							onclick={() => {
								timeslotLock = !timeslotLock;
								focusCursor();
							}}
							class={`${timeslotLock ? 'bg-slate-500/70' : 'bg-slate-200/70'} p-2 absolute right-1 bottom-1 rounded-md z-[99]`}
						>
							{#if timeslotLock}
								🔒
							{:else}
								🔓
							{/if}
						</button>
					</div>

					<!-- <div class="bg-green-300 w-full h-[10px] absolute left-[0px] bottom-[0px]"></div> -->

					<div class="absolute h-full overflow-x-auto relative">
						<div bind:this={cursor} class="absolute h-full z-[39] left-[2380px] w-[100vw]">
							<div class="w-[10px] h-full border-r-2 border-blue-400"></div>
							<span class="bg-blue-500/30 rounded-md absolute left-[15px] p-1 text-xs bottom-[5px]"
								>{curTime}</span
							>
						</div>
						<div
							class="sticky flex items-center justify-center top-0 w-[4000px] h-[20px] bg-yellow-300"
						>
							{#each Array(24) as time, idx}
								<div class="flex justify-center flex-1 text-center relative">
									<span>{(idx % 12) + 1}:00&nbsp;</span>
									{#if idx >= 12}
										<span>PM</span>
									{:else}
										<span>AM</span>
									{/if}
									<div
										class="bg-black/40 absolute border-right-2 top-[25px] h-[100vh] w-[1px] z-[9999] border-slate-800"
									></div>
								</div>
							{/each}
						</div>
					</div>
				</div>
				{#if orders.length}
					<div class="h-full w-full relative overflow-y-auto">
						<div class="overflow-y-auto absolute top-0 w-full h-full">
							{#each orders as order, idx}
								<button
									onclick={() => {
										curOrder = idx;
										showOrder = true;
									}}
									class="w-full p-2 flex border-b-2 bg-slate-300"
								>
									<span class="w-full flex">
										<span>
											{#if order.type == 'phone'}
												📱
											{:else}
												🧑
											{/if}
										</span>
										<span class={`${order.finished ? 'line-through' : ''} w-full text-left flex-1`}>
											{order.name}
										</span>

										<div class="w-[80px] flex justify-center items-center font-bold text-xs">
											{#if order.finished}
												<span class="pl-3">
													{new Date(order.finished).toLocaleTimeString([], {
														hour: '2-digit',
														minute: '2-digit'
													})}
												</span>
											{/if}
										</div>
										<div class="flex items-center">
											<span
												class={`${order.finished ? 'line-through text-slate-600/40' : ''} text-right w-full flex-1`}
												>{new Date(order.dateStart).toLocaleTimeString([], {
													hour: '2-digit',
													minute: '2-digit'
												})}</span
											>
											{#key order?.timeSince}
												<span class="opacity-30 text-xs">&nbsp;{order?.timeSince} ago</span>
											{/key}
										</div>
									</span></button
								>
							{/each}
						</div>
					</div>
				{:else}
					<div class="w-full h-full flex justify-center items-center">
						<span class="opacity-30">No Orders</span>
					</div>
				{/if}
			</div>
		</div>
		<div class="p-1 w-full flex gap-1 h-[40px]">
			<a href="/" class="bg-red-300 rounded-md w-full justify-center items-center flex">Back</a>
			{#if orders.length}
				<button
					onclick={() => {
						orders = [];
						setAllOrders();
					}}
					class="bg-red-300 p-2 relative rounded-md w-full flex justify-center items-center"
					>clear orders
					{#if orders.length}
						<span
							class="bg-slate-300 absolute right-2 opacity-80 rounded-full px-1 border-slate-500 border-2"
							>{orders.length}</span
						>
					{/if}
				</button>
			{/if}
		</div>
	</div>
{/if}
