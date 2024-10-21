<div id="Container">
	<div class="w-full h-[50px] bg-slate-400"></div>
	<div class="flex-1 w-full bg-slate-700"></div>
	<div class="w-full h-[50px] bg-slate-300"></div>
</div>

<style>
	.sections {
		display: flex;
	}

	#Container {
		height: 100%;
		width: 100%;
		position: absolute;
		left: 0px;
		top: 0px;
		display: flex;
		flex-direction: column;
	}

	#Header {
		height: 40px;
		width: 100%;
		background-color: #fff;
		border-bottom: 1px solid;
	}

	#Body {
		flex: 1;
		height: 100%;
		background-color: #f0f;
	}

	#Footer {
		background-color: #00f;
		border-top: 1px solid;
	}
</style>
