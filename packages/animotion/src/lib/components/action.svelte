<script lang="ts">
	import { Action } from '$lib/index.js'

	type ActionProps = {
		order?: number
		do?: () => void
		undo?: () => void
		actions?: Array<() => void>
	}

	let { order, actions, ...props }: ActionProps = $props()
	let el: HTMLDivElement = $state()!

	const noop = () => {}
	const action = () => props?.do?.() ?? noop()
	const undo = () => props?.undo?.() ?? noop()

	$effect(() => {
		el?.addEventListener('current', action)
		el?.addEventListener('out', undo)

		return () => {
			el?.removeEventListener('current', action)
			el?.removeEventListener('out', undo)
		}
	})
</script>

{#if actions}
	{#each actions as action, i}
		<Action do={action} undo={i === 0 ? undo : actions[i - 1]} />
	{/each}
{:else}
	<div bind:this={el} class="fragment hidden" data-fragment-index={order}></div>
{/if}
