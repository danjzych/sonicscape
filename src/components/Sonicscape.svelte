<script lang="ts">
	import { onDestroy, onMount } from 'svelte';
	import '../app.css';
	import * as Tone from 'tone';
	import type { Interval } from 'tone/build/esm/core/type/Units';

	let synth; //how to type as a tone js class?
	let beat = 0;
	function playSequencer() {
		Tone.Transport.scheduleRepeat((time) => {
			synth.triggerAttackRelease('C1', '4n', time);
			beat = beat === 8 ? 0 : beat + 1;
		}, '8n');

		Tone.Transport.start();
	}

	let interval: Interval;

	onMount(() => {
		synth = new Tone.MembraneSynth().toDestination();
		interval = setInterval(() => {
			Tone.context.resume();
		}, 0);
	});

	onDestroy(() => {
		clearInterval(interval);
	});
</script>

<div class="flex flex-col items-center justify-center">
	<button on:click={playSequencer}> Start Sequence </button>
	<button on:click={() => Tone.Transport.stop()}> Stop Sequence </button>
</div>
