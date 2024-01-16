<script lang="ts">
	import Sonicscape from '../components/Sonicscape.svelte';
</script>

<h1 class="color-animation text-center text-5xl font-extrabold">Sonicscape</h1>
<Sonicscape />

<style>
	.color-animation {
		background: radial-gradient(blue, red);
		background-size: 200% auto;
		-webkit-background-clip: text;
		color: transparent;
		animation: move-gradient 2s linear infinite;
	}

	@keyframes move-gradient {
		to {
			background-position: 200% center;
		}
	}
</style>
