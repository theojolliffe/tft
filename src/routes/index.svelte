<script context="module">
	export const prerender = true;
</script>

<script>
	import Counter from '$lib/Counter.svelte';

	import { page } from '$app/stores';

	import { teams } from './team/fb-utils'


</script>

<svelte:head>
	<title>Home</title>
</svelte:head>

<section>
	<h1>
		Statistical match reports<br />from the Premier League
	</h1>
</section>
<div class='grid-container'>
	{#each teams as { id, name }, i}
		<div class="team-div" class:active={$page.path === '/'+id}><a sveltekit:prefetch href={'/team/'+id}>{name}</a></div>
	{/each}
</div>

<style>
	section {
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		flex: 1;
	}

	.grid-container {
		display: grid;
		grid-template-columns: auto auto;
		grid-gap: 10px;
		padding: 10px;
	}

	.team-div {
		background-color: rgba(255, 255, 255, 0.8);
		text-align: center;
		padding: 20px 0;
		font-size: 24px;
		width: 100%;
	}

	.team-div > a {
		color: black;
	}

	h1 {
		width: 100%;
	}

	.welcome {
		position: relative;
		width: 100%;
		height: 0;
		padding: 0 0 calc(100% * 495 / 2048) 0;
	}

	.welcome img {
		position: absolute;
		width: 100%;
		height: 100%;
		top: 0;
		display: block;
	}
</style>
