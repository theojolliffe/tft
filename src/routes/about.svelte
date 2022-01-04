<script context="module">
	import {page} from '$app/stores'

	import { browser, dev } from '$app/env';

	// we don't need any JS on this page, though we'll load
	// it in dev so that we get hot module replacement...
	export const hydrate = dev;

	// ...but if the client-side router is already loaded
	// (i.e. we came here from elsewhere in the app), use it
	export const router = browser;

	// since there's no dynamic data here, we can prerender
	// it so that it gets served as a static asset in prod
	export const prerender = true;

</script>

<svelte:head>
	<title>About</title>
</svelte:head>

<div class="content">
	<h1>About <strong>The Final Third</strong></h1>
	<p>
		This is an experimental project which uses machine learning models to to generate tweets based on football data.
	</p>
	<p>
		The langauge model I use is a Text-to-Text transformer called T5.
	</p>
	<p>
		The project is in an early stage of development and will contain errors. Please verify accuracy with the <a href="https://fbref.com/en/">original data source</a> before sharing content.
	</p>
</div>

<style>
	.content {
		width: 100%;
		max-width: var(--column-width);
		margin: var(--column-margin-top) auto 0 auto;
	}
</style>