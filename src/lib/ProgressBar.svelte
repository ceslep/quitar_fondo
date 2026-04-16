<script lang="ts">
  let { progress, label = '' }: { progress: number; label?: string } = $props();

  let displayProgress = $derived(Math.min(100, Math.max(0, Math.round(progress))));
</script>

<div class="w-full max-w-md mx-auto flex flex-col items-center gap-4">
  <div class="relative w-full h-2 rounded-full bg-surface-lighter overflow-hidden">
    <div
      class="absolute inset-y-0 left-0 rounded-full bg-accent transition-all duration-500 ease-out"
      style:width="{displayProgress}%"
    >
      <div class="absolute inset-0 bg-gradient-to-r from-transparent to-white/20 animate-pulse"></div>
    </div>
  </div>

  <div class="flex items-center gap-3 text-sm">
    <span class="font-display font-semibold text-accent tabular-nums">
      {displayProgress}%
    </span>
    {#if label}
      <span class="text-white/40">{label}</span>
    {/if}
  </div>
</div>
