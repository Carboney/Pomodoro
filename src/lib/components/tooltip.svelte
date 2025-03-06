<script lang="ts">
	let { actionName, key = '', children } = $props();
</script>

<div class="tooltip tooltip-bottom">
	<div class="tooltip-content">
		<span class="align-baseline">{actionName}</span>
		{#if key != ''}
			<span class="align-top text-white/50"><b>.</b></span>
			<kbd class="kbd kbd-md">{key}</kbd>
		{/if}
	</div>
	{@render children()}
</div>
