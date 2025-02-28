<!-- FIXME sometimes it adds a trailing slash when landing -->
<script context="module">
	import { API_BASE } from '$lib/env';

	export async function load({ params }) {
		const example = await fetch(`${API_BASE}/docs/svelte/examples/${params.slug}`, {
			credentials: 'omit'
		});

		return {
			props: {
				example: await example.json(),
				slug: params.slug
			},
		};
	}
</script>

<script>
	import { getContext } from 'svelte';
	import { navigating } from '$app/stores';
	import Repl from '@sveltejs/repl';
	import ScreenToggle from '$lib/components/ScreenToggle.svelte';
	import {
		mapbox_setup, // see site/content/examples/15-context/00-context-api
		rollupUrl,
		svelteUrl
	} from '../../../config';
	import TableOfContents from './_TableOfContents.svelte';

	export let example;

	let width;
	let offset = 1;
	let repl;

	const { sections } = getContext('examples');

	const clone = (file) => ({
		name: file.name.replace(/.\w+$/, ''),
		type: file.type,
		source: file.content
	});

	$: mobile = width < 768; // note: same as per media query below
	$: replOrientation = mobile || width > 1080 ? 'columns' : 'rows';

	$: repl && repl.set({ components: example.files.map(clone) });
</script>

<svelte:head>
	<title>{example.name} {example.name ? '•' : ''} Svelte Examples</title>

	<meta name="twitter:title" content="Svelte examples" />
	<meta name="twitter:description" content="Cybernetically enhanced web apps" />
	<meta name="Description" content="Interactive example Svelte apps" />
</svelte:head>

<h1 class="visually-hidden">Examples</h1>
<div class="examples-container" bind:clientWidth={width}>
	<div class="viewport offset-{offset}">
		<TableOfContents {sections} active_section={example.slug} isLoading={$navigating} />
		<div class="repl-container" class:loading={$navigating}>
			<Repl
				bind:this={repl}
				{svelteUrl}
				{rollupUrl}
				orientation={replOrientation}
				fixed={mobile}
				relaxed
				injectedJS={mapbox_setup}
			/>
		</div>
	</div>
	{#if mobile}
		<ScreenToggle bind:offset labels={['index', 'input', 'output']} />
	{/if}
</div>

<style>
	.examples-container {
		position: relative;
		height: calc(100vh - var(--nav-h));
		overflow: hidden;
		padding: 0 0 42px 0;
		box-sizing: border-box;
	}

	.viewport {
		display: grid;
		width: 300%;
		height: 100%;
		grid-template-columns: 33.333% 66.666%;
		transition: transform 0.3s;
		grid-auto-rows: 100%;
	}

	.repl-container.loading {
		opacity: 0.6;
	}

	/* temp fix for #2499 and #2550 while waiting for a fix for https://github.com/sveltejs/svelte-repl/issues/8 */

	.repl-container :global(.tab-content),
	.repl-container :global(.tab-content.visible) {
		pointer-events: all;
		opacity: 1;
	}
	.repl-container :global(.tab-content) {
		visibility: hidden;
	}
	.repl-container :global(.tab-content.visible) {
		visibility: visible;
	}

	.offset-1 {
		transform: translate(-33.333%, 0);
	}
	.offset-2 {
		transform: translate(-66.666%, 0);
	}

	@media (min-width: 768px) {
		.examples-container {
			padding: 0;
		}

		.viewport {
			width: 100%;
			height: 100%;
			display: grid;
			grid-template-columns: var(--sidebar-mid-w) auto;
			grid-auto-rows: 100%;
			transition: none;
		}

		.offset-1,
		.offset-2 {
			transform: none;
		}
	}
</style>
