<script>
	let {
		href = 'https://github.com/bmehder/',
		label = 'View source on GitHub',
		size = '2.5rem',
		color = 'var(--color-slate-300)',
	} = $props()
</script>

<a
	{href}
	aria-label={label}
	target="_blank"
	rel="noopener noreferrer"
	style={`--size: ${size}; --color: ${color}`}
>
	<svg
		viewBox="0 0 24 24"
		fill="currentColor"
		aria-hidden="true"
	>
		<path d="M12 2C6.477 2 2 6.484 2 12.017c0 4.425 2.865 8.18 6.839 9.504.5.092.682-.217.682-.483 0-.237-.008-.866-.013-1.699-2.782.605-3.369-1.343-3.369-1.343-.454-1.158-1.11-1.466-1.11-1.466-.908-.62.069-.608.069-.608 1.003.07 1.531 1.032 1.531 1.032.892 1.53 2.341 1.088 2.91.832.091-.647.349-1.088.635-1.338-2.22-.253-4.555-1.113-4.555-4.951 0-1.093.39-1.988 1.029-2.688-.103-.253-.446-1.272.098-2.65 0 0 .84-.269 2.75 1.026A9.564 9.564 0 0 1 12 6.844c.85.004 1.705.115 2.504.337 1.909-1.295 2.747-1.026 2.747-1.026.546 1.378.203 2.397.1 2.65.64.7 1.028 1.595 1.028 2.688 0 3.848-2.339 4.695-4.566 4.943.359.309.678.92.678 1.855 0 1.338-.012 2.419-.012 2.747 0 .269.18.58.688.481A10.019 10.019 0 0 0 22 12.017C22 6.484 17.523 2 12 2z"/>
	</svg>
</a>

<style>
	a {
		display: inline-flex;
		align-items: center;
		justify-content: center;
		inline-size: var(--size);
		block-size: var(--size);
		border-radius: 999px;
		color: var(--color);
		transition:
			background-color 150ms,
			color 150ms;
	}

	a:hover {
		background: var(--color-slate-700);
		color: var(--color-slate-100);
		text-decoration: none;
	}

	svg {
		inline-size: calc(var(--size) * 0.6);
		block-size: calc(var(--size) * 0.6);
	}
</style>