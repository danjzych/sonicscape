<script lang="ts">
	import { onDestroy, onMount } from 'svelte';
	import '../app.css';
	import * as Tone from 'tone';
	import type { Interval } from 'tone/build/esm/core/type/Units';

	//insantiate variables for sequencer
	let loop = [false, false, false, false, false, false, false, false];
	let beat = 0;

	//instantiate variable for instruments
	let kick; //how to type as a tone js class?

	/** Responsible for looping based on tone's musical time and triggering sequence*/
	function playSequencer() {
		Tone.Transport.scheduleRepeat((time) => {
			if (loop[beat]) {
				kick.triggerAttackRelease('C1', '8n', time);
			}
			beat = beat === 7 ? 0 : beat + 1;
		}, '8n');

		Tone.Transport.start();
	}

	/** Toggle value in sequencer*/
	function setSequence(i) {
		loop[i] = !loop[i];
	}

	let interval: Interval;

	onMount(() => {
		kick = new Tone.MembraneSynth().toDestination();
		interval = setInterval(() => {
			Tone.context.resume();
		}, 0);
	});

	onDestroy(() => {
		clearInterval(interval);
	});
</script>

<div class="flex flex-col items-center justify-center">
	<div class="my-5 flex gap-5">
		{#each loop as beat, i}
			<button class="border-2 p-3" on:click={() => setSequence(i)}>
				{#if beat}
					X
				{/if}
				{i + 1}
			</button>
		{/each}
	</div>
	<button on:click={playSequencer}> Start Sequence </button>
	<button on:click={() => Tone.Transport.stop()}> Stop Sequence </button>
</div>
