<script lang="ts">
	import { onMount } from 'svelte';
	import Needle from './Needle.svelte';

	let probability = $state(0);
	let lastUpdated = $state(new Date());

	const markets = $state([
		{
			id: '253591',
			yes: 'Trump',
			no: 'Kamala',
			flip: false,
			title: '2024 Election Winner',
			name: 'Who will be the next president of the USA? Trump or Kamala?',
			percentage: 0
		},
		{
			id: '253727',
			yes: 'Trump Popular Vote',
			no: 'Kamala Popular Vote Popular Vote',
			flip: true,
			title: '2024 Popular Vote Winner',
			name: 'Who will win the popular vote in the 2024 US presidential election?',
			percentage: 0
		},
		{
			id: '252298',
			yes: 'Republican Senate',
			no: 'Democratic Senate',
			flip: true,
			title: '2024 Senate Winner',
			name: 'Who will win the US Senate in 2024?',
			percentage: 0
		},
		{
			id: '255151',
			yes: 'Trump',
			no: 'Kamala',
			flip: true,
			title: '2024 Pennsylvania Winner',
			name: 'Who will win Pennsylvania in the 2024 US presidential election?',
			percentage: 0
		}
	]);

	async function getMarketPrices(): Promise<any> {
		for (let i = 0; i < markets.length; i++) {
			const MARKET_ID = markets[i].id;
			const endpoint = `https://gamma-api.polymarket.com/markets/${MARKET_ID}`;

			try {
				const response = await fetch(endpoint, {
					method: 'GET',
					headers: { 'Content-Type': 'application/json' }
				});

				if (!response.ok) {
					throw new Error(`Error fetching market data: ${response.statusText}`);
				}

				const data = await response.json();
				const lastPrice = markets[i].flip ? 1 - data.lastTradePrice : data.lastTradePrice;

				if (!data || !lastPrice) {
					throw new Error('Invalid response format');
				}

				markets[i].percentage = Math.round(lastPrice * 100);
			} catch (error) {
				console.error(error);
				throw error;
			}
		}
	}

	onMount(async () => {
		await getMarketPrices();
		setInterval(async () => {
			await getMarketPrices();
			lastUpdated = new Date();
		}, 60000);
	});
</script>

<div class="scroll-container">
	{#each markets as market}
		<div class="market-section">
			<Needle {lastUpdated} {market} />
		</div>
	{/each}
</div>

<style>
	.scroll-container {
		scroll-snap-type: y mandatory;
		overflow-y: scroll;
		height: 100vh;
	}

	.market-section {
		scroll-snap-align: start;
		height: 100vh;
	}
</style>
