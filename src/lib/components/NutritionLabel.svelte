<script>
	import NutritionLabel from './NutritionLabel.json';
	import { onMount } from 'svelte';

	let servingSize = '1 Cup (228g)';
	/*
	export let calories = 200;
	export let totalFat = 8;
	export let saturatedFat = 1;
	export let transFat = 0;
	export let cholesterol = 0;
	export let sodium = 160;
	export let totalCarbs = 31;
	export let dietaryFiber = 4;
	export let sugars = 5;
	export let protein = 3;
	export let vitamins = ['Vitamin A 10%', 'Vitamin C 20%', 'Calcium 15%', 'Iron 10%'];
  */

	let nutrition = $state(NutritionLabel.NutritionFacts);

	onMount(() => {
		nutrition = NutritionLabel.NutritionFacts;
		console.log(nutrition);
	});
</script>

<!--
<div class="bg-slate-300">
	<div>Ingredients</div>
</div>
-->
<div class="label w-[40px]">
	<div class="label-heading">Nutrition Facts</div>
	<span class="text-sm">1 Servings per container</span>

	<div class="w-full h-[6px] bg-black my-1"></div>
	<div class="flex text-sm border-b-2 border-black/20 mb-1 pb-1">
		<span class="flex-1">Calories</span>
		<span>530</span>
	</div>
	<span class="text-xs w-full flex justify-end font-bold pb-1">% Daily Value</span>
	<!--
	{#each nutritionArr as nut}
		<div class="flex text-xs mb-1 pb-1 border-b-2 border-slate-300">
			<div class="flex-1">{nut}</div>
		</div>
	{/each}
-->

	{#each Object.keys(nutrition) as nut, idx}
		{#if idx > 2}
			<div class="flex text-xs mb-1 pb-1 [&:not(:last-child)]:border-b-2 border-slate-300/40">
				<div class="flex-1">{nut.match(/[A-Z][a-z]+/g).join(' ')}</div>
				<div class="pr-2">{nutrition[nut]['Amount']}</div>
				<div class="font-bold">{nutrition[nut]['DailyValue']}</div>
			</div>
		{/if}
	{/each}

	<!--
	<div class="label-section">
		<div>
			<span>Calories</span>
			<span>{nutrition.calories}</span>
		</div>
	</div>

	<div class="label-section">
<span>Total Fat</span>
		<div class="label-nutrient">
			<span>{nutrition.totalFat}g</span>
		</div>
		<div class="label-nutrient">
			<span>Saturated Fat</span>
			<span>{saturatedFat}g</span>
		</div>
		<div class="label-nutrient">
			<span>Trans Fat</span>
			<span>{transFat}g</span>
		</div>
	</div>

	<div class="label-section">
		<div class="label-nutrient">
			<span>Cholesterol</span>
			<span>{cholesterol}mg</span>
		</div>
	</div>

	<div class="label-section">
		<div class="label-nutrient">
			<span>Sodium</span>
			<span>{sodium}mg</span>
		</div>
	</div>

	<div class="label-section">
		<div class="label-nutrient">
			<span>Total Carbohydrate</span>
			<span>{totalCarbs}g</span>
		</div>
		<div class="label-nutrient">
			<span>Dietary Fiber</span>
			<span>{dietaryFiber}g</span>
		</div>
		<div class="label-nutrient">
			<span>Sugars</span>
			<span>{sugars}g</span>
		</div>
	</div>

	<div class="label-section">
		<div class="label-nutrient">
			<span>Protein</span>
			<span>{protein}g</span>
		</div>
	</div>

	<div class="label-section">
		{#each vitamins as vitamin}
			<div class="label-nutrient">{vitamin}</div>
		{/each}
	</div>
-->
</div>

<style>
	.label {
		width: 100%;
		font-family: Arial, sans-serif;
		border: 1px solid #000;
		padding: 10px;
		box-shadow: 2px 2px 5px rgba(0, 0, 0, 0.2);
	}

	.label-heading,
	.label-subheading {
		font-weight: bold;
	}

	.label-heading {
		font-size: 18px;
		margin-bottom: 5px;
	}

	.label-subheading {
		font-size: 12px;
		margin-bottom: 2px;
	}

	.label-section {
		margin-bottom: 10px;
	}

	.label-section div {
		display: flex;
		justify-content: space-between;
	}

	.label-section div span {
		font-weight: bold;
	}

	/*
	.label-nutrient {
		font-size: 12px;
	}
  */
</style>
