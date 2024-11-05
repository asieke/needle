<script lang="ts">
	import { onMount } from 'svelte';

	type Market = {
		id: string;
		yes: string;
		title: string;
		flip: boolean;
		no: string;
		name: string;
		percentage: number;
	};

	let { lastUpdated, market }: { lastUpdated: Date; market: Market } = $props();

	//if probability = 100 then deg = 90
	//if probability = 0 then deg = -90
	let deg = $derived((market.percentage / 100) * 180 - 90);

	let color = $derived(market.percentage > 50 ? '#c93136ff' : '#1675b7ff');

	let size = $state(0);
	let bubbleSize = $state(24);
	let needleWidth = $derived(20);
	let carveoutSize = $derived(size * 0.4);

	onMount(() => {
		console.log(window.innerWidth);
		size = Math.min(800, window.innerWidth * 0.8);
	});
</script>

{#if market.percentage > 0}
	<div class="h-[100vh] overflow-hidden">
		<div class="prose mx-auto">
			<h1 class="w-full pt-10 text-center font-serif">{market.title}</h1>
			<p class="px-12 text-center text-2xl">{market.name}</p>
		</div>
		<div
			class="guage relative mx-auto mt-20"
			style={`width: ${size}px; height: ${size}px; border-radius: ${size / 2}px ${size / 2}px 0 0;`}
		>
			<div class="absolute z-50 text-center" style={`top: ${size / 2 + 32}px; width: ${size}px;`}>
				{#if market.percentage > 50}
					<h1 class="font-serif text-3xl font-bold" style="color: {color}">
						{market.yes}: {market.percentage}%
					</h1>
				{:else}
					<h1 class="font-serif text-3xl" style="color: {color}">
						{market.no}
						{100 - market.percentage}%
					</h1>
				{/if}
				<h3 class="pt-2 text-slate-400">
					Last Updated: {lastUpdated.toLocaleString()}
				</h3>
			</div>
			<div
				class="absolute z-20 rounded-full bg-white"
				style={`width: ${carveoutSize}px; height: ${carveoutSize}px; top: ${size / 2 - carveoutSize / 2}px; left: ${size / 2 - carveoutSize / 2}px`}
			></div>
			<div
				class="absolute text-center text-xs md:text-lg"
				style="width: {size * 0.2}px; top: {size / 2.5}px; left: {size / 24}px"
			>
				Very Likely
			</div>
			<div
				class="absolute text-center text-xs md:text-lg"
				style="width: {size * 0.2}px; top: {size / 4}px; left: {size / 10}px"
			>
				Likely
			</div>
			<div
				class="absolute text-center text-xs md:text-lg"
				style="width: {size * 0.2}px; top: {size / 7}px; left: {size / 4.5}px"
			>
				Leaning
			</div>
			<div
				class="absolute text-center text-xs md:text-lg"
				style="width: {size * 0.2}px; top: {size / 15}px; left: {size / 2 - size * 0.1}px"
			>
				Tossup
			</div>
			<div
				class="absolute text-center text-xs md:text-lg"
				style="width: {size * 0.2}px; top: {size / 7}px; right: {size / 4.5}px"
			>
				Leaning
			</div>
			<div
				class="absolute text-center text-xs md:text-lg"
				style="width: {size * 0.2}px; top: {size / 4}px; right: {size / 10}px"
			>
				Likely
			</div>
			<div
				class="absolute text-center text-xs md:text-lg"
				style="width: {size * 0.2}px; top: {size / 2.5}px; right: {size / 25}px"
			>
				Very Likely
			</div>

			<!-- Needle-->
			<div
				class="needle absolute z-40"
				style={`transform: rotate(${deg}deg); top: -${needleWidth / 2}px; left:${(size - needleWidth) / 2}px; height: ${
					size + needleWidth
				}px; width: ${needleWidth}px; background: linear-gradient(to bottom, ${color} 50%, transparent 50%);`}
			></div>

			<div class="absolute z-30 h-[2px] w-full bg-black" style="top: {size / 2}px;"></div>
			<div
				class="absolute z-50 rounded-full border-[1px] border-white"
				style={`background-color: ${color}; width: ${bubbleSize}px; height: ${bubbleSize}px; left: ${size / 2 - bubbleSize / 2}px; top: ${size / 2 - bubbleSize / 2}px`}
			></div>
		</div>
	</div>
{/if}

<style lang="postcss">
	.guage {
		border-radius: 250px 250px 0 0;

		background: conic-gradient(
			#fbfbfbff 0deg 15deg,
			#fcf2f2ff 15deg 40deg,
			#f8e4e5ff 40deg 65deg,
			#f7d8d7ff 65deg 90deg,
			white 90deg 270deg,
			#cfe2f3ff 270deg 295deg,
			#dfebf6ff 295deg 320deg,
			#eff5f9ff 320deg 345deg,
			#fbfbfbff 345deg 360deg
		);
	}

	.needle {
		clip-path: polygon(0% 50%, 45% 0%, 55% 0%, 100% 50%);
		transition: transform 1s cubic-bezier(0.4, 0, 0.2, 1);
	}
</style>
