<script>
	import '@sveltejs/site-kit/base.css';
	import { setContext } from 'svelte';
	import { page, navigating, session } from '$app/stores';
	import { Icon, Icons, Nav, NavItem, SkipLink } from '@sveltejs/site-kit';
	import PreloadingIndicator from '$lib/components/PreloadingIndicator.svelte';
	let word = '';

	
	setContext('app', {
		login: () => {
			const login_window = window.open(
				`${window.location.origin}/auth/login`,
				'login',
				'width=600,height=400'
			);

			window.addEventListener('message', function handler(event) {
				login_window.close();
				window.removeEventListener('message', handler);
				$session.user = event.data.user;
			});
		},

		logout: async () => {
			const r = await fetch(`/auth/logout`, {
				credentials: 'include'
			});

			if (r.ok) $session.user = null;
		}
	});
</script>

<Icons />

{#if $navigating && $navigating.to}
	<PreloadingIndicator />
{/if}

{#if $page.url.pathname !== '/repl/embed'}
	<SkipLink href="#main" />
	<Nav {page} logo="/stopwar.svg">
		<svelte:fragment slot="nav-center">
			<NavItem href="/tutorial">Tutorial</NavItem>
			<NavItem href="/docs">Docs</NavItem>
			<NavItem href="/examples">Examples</NavItem>
			<NavItem href="/repl">REPL</NavItem>
			<NavItem href="/blog">Blog</NavItem>
			<NavItem href="/faq">FAQ</NavItem>
			<!-- {#if $page.url.pathname == '/docs'}
			<NavItem href="#">Press CTRL + K to Search</NavItem>
			{/if} -->
			
		</svelte:fragment>

		<svelte:fragment slot="nav-right">
			<NavItem external="https://kit.svelte.dev">SvelteKit</NavItem>

			<NavItem external="/chat" title="Discord Chat">
				<span class="small">Discord</span>
				<span class="large"><Icon name="message-square" /></span>
			</NavItem>

			<NavItem external="https://github.com/sveltejs/svelte" title="GitHub Repo">
				<span class="small">GitHub</span>
				<span class="large"><Icon name="github" /></span>
			</NavItem>
		</svelte:fragment>
	</Nav>
{/if}

<main id="main">
	<slot />
</main>

<a target="_blank" rel="noopener noreferrer" href="https://www.stopputin.net/">
	<div class="ukr">
		<span class="small">
			<strong>We stand with Ukraine.</strong> Donate →
		</span>
		<span class="large">
			<strong>We stand with Ukraine.</strong> Petition your leaders. Show your support.
		</span>
	</div>
</a>

<style>
	.ukr {
		background-color: #0066cc;
		color: white;
		position: fixed;
		bottom: 0;
		width: 100vw;
		text-align: center;
		padding: 0.75em;
		z-index: 999;
	}

	@media (max-width: 830px) {
		:global(aside) {
			z-index: 9999 !important;
		}
	}

	main {
		position: relative;
		margin: 0 auto;
		/* padding: var(--nav-h) var(--side-nav) 0 var(--side-nav); */
		padding: var(--nav-h) 0 0 0;
		overflow: auto;
	}

	.small {
		display: inline;
	}

	.large {
		display: none;
	}

	@media (min-width: 800px) {
		.small {
			display: none;
		}

		.large {
			display: inline;
		}
	}

	/** Ukraine banner */
	:root {
		--ukr-footer-height: 48px;
	}

	main {
		padding-bottom: var(--ukr-footer-height);
	}

	.ukr {
		background-color: #0066cc;
		color: white;
		position: fixed;
		display: flex;
		align-items: center;
		justify-content: center;
		bottom: 0;
		width: 100vw;
		height: var(--ukr-footer-height);
		z-index: 999;
	}

	:global(.examples-container, .repl-outer, .tutorial-outer) {
		height: calc(100vh - var(--nav-h) - var(--ukr-footer-height)) !important;
	}

	:global(.toggle) {
		bottom: var(--ukr-footer-height) !important;
	}

	:global(.zen-mode) {
		height: calc(100vh - var(--ukr-footer-height)) !important;
	}

	@media (max-width: 830px) {
		:global(aside) {
			z-index: 9999 !important;
		}
	}
	.ukr strong {
		color: #ffcc00;
	}
</style>
