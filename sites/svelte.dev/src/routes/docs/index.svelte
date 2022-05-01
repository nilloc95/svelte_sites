<script context="module">
	import { API_BASE } from '$lib/env';
	import QuickSearchBar from "svelte-quicksearch-bar";
	const onPick = e => {
		selectedOption = e.detail
		window.location.href = `/docs${selectedOption.link}`
	};
	let options = [
		{label: "Component Format", link: "#component-format"},
		{label: "Template Syntax", link: "#template-syntax"},
		{label: "Run Time", link: "#run-time"},
		{label: "Compile Time", link: "#compile-time"},
		{label: "Accessibility Warnings", link: "#accessibility-warnings"},
	];
	let selectedOption;

	export async function load({ fetch }) {
		const sections = await fetch(`${API_BASE}/docs/svelte/docs?content`).then((r) => r.json());
		return {
			props: { sections },
		};
	}
</script>

<script>
	import { Contents, Main, Section } from '@sveltejs/site-kit/docs';
	import { page } from '$app/stores';

	export let sections;

	let path;

	$: contents = sections.map((section) => ({
		path: `/docs#${section.slug}`,
		title: section.title,
		sections: section.sections.map((subsection) => ({
			path: `/docs#${subsection.slug}`,
			title: subsection.title,
			sections: subsection.sections.map((subsection) => ({
				path: `/docs#${subsection.slug}`,
				title: subsection.title
			}))
		}))
	}));
	// sections.forEach(element => {
	// 	console.log(element.title);
	// 	if (element.title == 'Component format'){
	// 		console.log(element.sections);

	// 	}
	// });
	function setFocusToTextBox(){
    document.getElementById("SearchField").focus();
}
</script>

<svelte:head>
	<title>Docs • Svelte</title>

	<meta name="twitter:title" content="Svelte docs" />
	<meta name="twitter:description" content="Complete documentation for Svelte" />
	<meta name="Description" content="Complete documentation for Svelte" />
</svelte:head>


<Main bind:path>
	<QuickSearchBar {options} on:pick={onPick} keys={['label', 'link']} />
	<h1>Documentation - Press CTRL + K to search</h1>

	{#each sections as section}
		<Section
			{section}
			edit="https://github.com/sveltejs/svelte/edit/master/site/content/docs/{section.file}"
			base="/docs"
		/>
	{/each}
</Main>

<Contents {contents} {path} />
