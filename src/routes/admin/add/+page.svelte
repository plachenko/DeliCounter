<script>
	let items = $state(null);
	let inputTxt = $state(null);
</script>

<div class="w-full h-full flex-col bg-blue-400 flex">
	<div class="w-full flex-1 h-[80px] bg-red-400 p-2"></div>
	<div style="height: 80px" class="flex flex-col w-full bg-blue-400 p-2">
		<div class="flex gap-3 w-full flex-1 bg-slate-400">
			<div class="w-full bg-slate-300 rounded-md p-1">
				<div contenteditable="true"></div>
			</div>
			<button class="rounded-md">add</button>
		</div>
	</div>
</div>
<!-- <div class="flex-1 w-full bg-slate-400"></div> -->
<!--
	<div class="w-full h-[30px] bg-slate-500"></div>
    <div class="w-full h-full flex p-2">
    <div class="w-full h-full flex gap-2">
        <div class="flex-1 w-full h-full bg-white rounded-md flex items-center justify-center p-1">
    
            <div contenteditable="true" bind:this={inputTxt} class="flex-1 w-full h-full bg-white"></div>
        </div>
        <button onclick={() => {items.push({name: 'test'})}} class="h-full bg-slate-200 rounded-md font-bold px-3">new item</button>
    </div>
    <div class="w-full h-[20px] bg-slate-900">

    </div>
</div>
    -->

