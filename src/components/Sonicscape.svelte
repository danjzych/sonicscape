<script lang="ts">
	import { onDestroy, onMount } from 'svelte';
	import '../app.css';
	import * as Tone from 'tone';
	import type { Interval } from 'tone/build/esm/core/type/Units';

	//insantiate variables for sequencer
	let loop = [false, false, false, false, false, false, false, false];
	let beat = 0;

	//instantiate variable for instruments
	let kick: Tone.MembraneSynth;

	/** Responsible for looping based on tone's musical time and triggering sequence*/
	function startSequencer() {
		Tone.Transport.start();
	}

	/** Toggle value in sequencer*/
	function setSequence(i: number) {
		loop[i] = !loop[i];
	}

	let interval: Interval;

	onMount(() => {
		kick = new Tone.MembraneSynth().toDestination();
		interval = setInterval(() => {
			Tone.context.resume();
		}, 0);

		Tone.Transport.scheduleRepeat((time) => {
			if (loop[beat]) {
				kick.triggerAttackRelease('C1', '8n', time);
			}
			beat = beat === 7 ? 0 : beat + 1;
		}, '8n');
	});

	onDestroy(() => {
		clearInterval(interval);
	});
</script>

<div class="flex flex-col items-center justify-center">
	<div class="my-5 flex gap-5">
		{#each loop as beat, i}
			<button
				class="size-12 rounded-md border-2 {loop[i] ? 'bg-yellow-500' : ''}"
				on:click={() => setSequence(i)}
			/>
		{/each}
	</div>
	<button on:click={startSequencer}> Start Sequence </button>
	<button on:click={() => Tone.Transport.pause()}> Pause Sequence </button>
</div>
