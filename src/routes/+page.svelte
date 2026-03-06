<script lang="ts">
	import Hemicycle from '$lib/components/charts/Hemicycle.svelte';
	import dec2024 from '$lib/data/2024-12-mrp_tables_totals.csv?raw';
	import apr2025 from '$lib/data/2025-04-mrp_tables_totals.csv?raw';
	import jul2025 from '$lib/data/2025-07-mrp_tables.csv?raw';
	import sep2025 from '$lib/data/2025-09-mrp_tables.csv?raw';
	import jan2026 from '$lib/data/2026-01-mrp_tables.csv?raw';
	import { csvParse } from 'd3-dsv';

	interface ParliamentarySeats {
		label: string;
		seats: number;
		color: string;
	}

	const dates = [
		{
			label: 'December 2024',
			value: '2024-12'
		},
		{
			label: 'April 2025',
			value: '2025-04'
		},
		{
			label: 'July 2025',
			value: '2025-07'
		},
		{
			label: 'September 2025',
			value: '2025-09'
		},
		{
			label: 'January 2026',
			value: '2026-01'
		}
	];
	let selectedDate: '2024-12' | '2025-04' | '2025-07' | '2025-09' | '2026-01' = $state('2024-12');
	let dateLabel = $derived(dates.filter((d) => d.value === selectedDate)[0].label);

	const aggregateSeats = (data: any) => {
		let totalSeats: ParliamentarySeats[] = [];

		const parties = [
			{ fullName: 'Other', shortName: 'Ind', color: '#FF66A1' },
			{ fullName: 'Green Party', shortName: 'Green', color: '#5FB25F' },
			{ fullName: 'Scottish National Party', shortName: 'SNP', color: '#FFD02C' },
			{ fullName: 'Plaid Cymru', shortName: 'PC', color: '#13E594' },
			{ fullName: 'Liberal Democrat', shortName: 'LD', color: '#FF9A02' },
			{ fullName: 'Labour', shortName: 'Lab', color: '#e91d0e' },
			{ fullName: 'Conservative', shortName: 'Con', color: '#0575c9' },
			{ fullName: 'Reform UK', shortName: 'RUK', color: '#0AD1E0' }
		];

		for (const party of parties) {
			let seatsCount: number = 0;
			if (Object.hasOwn(data[0], 'Winner')) {
				seatsCount = data.filter(
					(d: any) => d.Winner === party.shortName || d.Winner === party.fullName
				).length;
			} else {
				seatsCount = parseInt(data.filter((d: any) => d.Party == party.fullName)[0].Seats);
			}

			totalSeats = [
				...totalSeats,
				{
					label: party.fullName,
					seats: seatsCount,
					color: party.color
				}
			];
		}

		return totalSeats;
	};

	const data = {
		'2024-12': aggregateSeats(csvParse(dec2024)),
		'2025-04': aggregateSeats(csvParse(apr2025)),
		'2025-07': aggregateSeats(csvParse(jul2025)),
		'2025-09': aggregateSeats(csvParse(sep2025)),
		'2026-01': aggregateSeats(csvParse(jan2026))
	};
</script>

<main>
	<h1 class="text-2xl font-bold">Data Visualisations</h1>
	<div>
		<div>
			<label for="dateSelector">Select date</label>
			<select id="dateSelector" bind:value={selectedDate}>
				{#each dates as date (date)}
					<option value={date.value}>{date.label}</option>
				{/each}
			</select>
		</div>
		<Hemicycle data={data[selectedDate]} source="More in Common - {dateLabel} MRP" />
		<p>
			This is a parliamentary chart showing More in Common's MRP projection of parliamentary seats
			split by party from {dateLabel}.
		</p>
	</div>
</main>
