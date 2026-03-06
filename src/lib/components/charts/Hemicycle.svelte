<script lang="ts">
	import * as d3 from 'd3';
	import * as d3ParliamentChart from 'd3-parliament-chart';

	interface Props {
		data: any;
		source: string;
	}

	let { data, source = '' }: Props = $props();

	$inspect(data);

	let svg: SVGSVGElement;
	let width = $state(440);
	// let seats = 650;
	let seatRadius = $derived(Math.floor(width / 110));
	let rowHeight = $derived(seatRadius * 3);
	let sections = 1;
	let sectionGap = 0;
	let debug = false;
	// let showColors = true;

	$effect(() => {
		if (svg && d3.select('svg') && width) {
			const svgSelection = d3.select('svg');

			svgSelection.call(
				d3ParliamentChart
					.parliamentChart()
					.aggregatedData(data)
					.width(width)
					.sections(sections)
					.sectionGap(sectionGap)
					.seatRadius(seatRadius)
					.rowHeight(rowHeight)
					.debug(debug)
			);
		}
	});
</script>

<div class="w-full">
	<div>
		<div>
			<label for="seatRadius" class="form-label">Seat radius</label>
			<input id="seatRadius" type="number" bind:value={seatRadius} class="form-input" />
		</div>
		<div>
			<label for="rowHeight" class="form-label">Row height</label>
			<input id="rowHeight" type="number" bind:value={rowHeight} class="form-input" />
		</div>
	</div>
	<div class="relative" bind:clientWidth={width}>
		<svg viewBox="0 0 {width} {width / 2}" bind:this={svg} />
	</div>
	{#if source}
		<div>
			<p>Source: {source}</p>
		</div>
	{/if}
</div>
