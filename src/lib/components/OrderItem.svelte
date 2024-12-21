<script>
    import { onMount } from 'svelte';
	import { fly } from 'svelte/transition';

    let showCancelButton = $state(false);
    let showAddButton = $state(false);

    let showImage = $state(false);

    let { curOrderItem, addItemToOrder, cancelOrder } = $props();

    let itemQnty = $state(1);

    let orderParams = $state(['nutrition', 'quantity', ]);

    onMount(() => {
        setTimeout(() => {
            showCancelButton = true;
            showAddButton = true;
            showImage = true;
        }, 300);
        
    });

    function startCancelOrder() {
        showCancelButton = false;
        showAddButton = false;
        showImage = false;
        setTimeout(() => {
            cancelOrder();
        }, 300);
    }
</script>

<div class="absolute flex flex-col w-full h-full">
    <div class="flex w-full items-center p-1 border-b-2">
        <div class="w-full flex-1 text-center text-xl">{curOrderItem.name}</div>
        <div class="px-2">{curOrderItem.price}</div>
    </div>
    <div class="px-2 py-1 text-sm text-slate-500">{curOrderItem.description}</div>

    <div class="flex w-full portrait:flex-col landscape:flex-row flex-1 h-full">
        <div class="flex-1 w-full h-full p-2">
            {#if showImage}
                <div transition:fly={{ y: -10 }} class="w-full h-[69%] bg-red-400"></div>
            {/if}
        </div>

        <div class="flex-1 h-full w-full p-2">
            <button
                class="border-2 border-slate-400 bg-slate-200 rounded-md p-2 w-full relative flex items-center flex-1"
                ><span class="w-full flex-1 flex items-center"
                    >
                    <div class="flex w-full relative justify-center items-center">
                        <span class="flex-1 w-full text-left z-[10]"><span class="bg-slate-200 px-2 text-sm">View Nutrition</span></span>
                        <div class="w-full border-t-2 border-dashed h-[1px] z-[6] absolute border-slate-600/20"></div> 
                        <span class="text-xs pl-1 text-slate-400 font-bold z-[10]">
                            <div class="pl-2 bg-slate-200">
                                <span class="bg-slate-200 border-2 border-slate-300 py-1 rounded-md px-2">
                                    {curOrderItem.calories * itemQnty} Cal
                                </span>
                            </div>
                        </span>
                    </div>
                    </span>
                </button>
            <div class="p-2 flex border-2 rounded-md mt-1 border-slate-400">
                <span class="flex-1 text-sm w-full">Quantity: {itemQnty}</span><input
                    type="range"
                    bind:value={itemQnty}
                    min="1"
                    max="10"
                />
            </div>
        </div>
    </div>

    <div class="w-full absolute bottom-0 flex py-2">
        <div class="flex-1 w-full flex justify-start">
            {#if showCancelButton}
            <button
                transition:fly={{ x: -10 }}
                onclick={() => {
                    // cancelOrder();
                    startCancelOrder();
                }}
                class="border-2 text-white border-red-500 bg-red-400 rounded-md p-2 px-4 ml-2"
                >Cancel</button
            >
            {/if}
        </div>
        <div class="flex-1 w-full flex justify-end">
            {#if showAddButton}
            <button
            transition:fly={{ x: 10 }}
                onclick={() => {
                    addItemToOrder();
                }}
                class="border-2 border-green-500 bg-green-400 flex items-center px-4 rounded-md mr-2"
                >Add to order <span class="text-xs text-green-700 pl-1">(${(parseFloat(curOrderItem.price.split('$')[1]) * itemQnty).toFixed(2)})</span
                ></button
            >
            {/if}
        </div>
    </div>
</div>