<script lang="ts">
	import { onDestroy, onMount } from 'svelte';
	import '../app.css';
	import * as Tone from 'tone';
	import type { Interval } from 'tone/build/esm/core/type/Units';
	import { sequence } from '@sveltejs/kit/hooks';

	//insantiate variables for sequencer
	let kit = {
		kick: {
			sequence: [false, false, false, false, false, false, false, false],
			note: 'C1',
			synth: undefined,
		},
		snare: {
			sequence: [false, false, false, false, false, false, false, false],
			note: 'C4',
			synth: undefined,
		},
	};
	let beat = 0;

	/** Responsible for looping based on tone's musical time and triggering sequence*/
	function startSequencer() {
		Tone.Transport.start();
	}

	/** Toggle value in sequencer*/
	function setSequence(i: number, loop: boolean[]) {
		loop[i] = !loop[i];
		kit = kit;
	}

	let interval: Interval;

	onMount(() => {
		kit.kick.synth = new Tone.MembraneSynth().toDestination();
		kit.snare.synth = new Tone.MembraneSynth().toDestination();

		interval = setInterval(() => {
			Tone.context.resume();
		}, 0);

		Tone.Transport.scheduleRepeat((time) => {
			// if (kit.kick.sequence[beat]) {
			// 	kit.kick.synth.triggerAttackRelease('C1', '8n', time);
			// }
			// if (kit.snare.sequence[beat]) {
			// 	kit.snare.synth.triggerAttackRelease('C4', '8n', time);
			// }

			for (const sound in kit) {
				if (kit[sound].sequence[beat]) {
					kit[sound].synth.triggerAttackRelease(kit[sound].note, '8n', time);
				}
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
		{#each kit.snare.sequence as beat, i}
			<button
				class="size-12 rounded-md border-2 {kit.snare.sequence[i]
					? 'bg-yellow-500'
					: ''}"
				on:click={() => setSequence(i, kit.snare.sequence)}
			/>
		{/each}
	</div>
	<div class="my-5 flex gap-5">
		{#each kit.kick.sequence as beat, i}
			<button
				class="size-12 rounded-md border-2 {kit.kick.sequence[i]
					? 'bg-yellow-500'
					: ''}"
				on:click={() => setSequence(i, kit.kick.sequence)}
			/>
		{/each}
	</div>
	<button on:click={startSequencer}> Start Sequence </button>
	<button on:click={() => Tone.Transport.pause()}> Pause Sequence </button>
</div>
