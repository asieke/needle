<script lang="ts">
	import { onMount } from 'svelte';
	import Needle from './Needle.svelte';

	let probability = $state(0);
	let lastUpdated = $state(new Date());

	async function getMarketPrices(): Promise<any> {
		const MARKET_ID = '1730835757006';
		const endpoint = `https://gamma-api.polymarket.com/markets/253591`;

		try {
			const response = await fetch(endpoint, {
				method: 'GET',
				headers: { 'Content-Type': 'application/json' }
			});

			if (!response.ok) {
				throw new Error(`Error fetching market data: ${response.statusText}`);
			}

			const data = await response.json();
			const lastPrice = data.lastTradePrice;

			if (!data || !lastPrice) {
				throw new Error('Invalid response format');
			}

			probability = Math.floor(lastPrice * 100);
		} catch (error) {
			console.error(error);
			throw error;
		}
	}

	async function manualUpdate() {
		getMarketPrices();
		lastUpdated = new Date();
		console.log('updating');
	}

	onMount(async () => {
		await getMarketPrices();
		setInterval(async () => {
			await getMarketPrices();
			lastUpdated = new Date();
		}, 20000);
	});
</script>

<div class="prose">
	<h1 class="w-full pt-10 text-center font-serif">Election Needle!</h1>
	<p class="px-12 text-center text-xl">
		Will it be next president of the US? Trump or Comrade Kamala?
	</p>
</div>
<Needle {probability} {lastUpdated} />

<div class="flex w-full justify-center">
	<button class="center bg-slate-200 p-3" onclick={manualUpdate}>Fetch Market Data</button>
</div>
