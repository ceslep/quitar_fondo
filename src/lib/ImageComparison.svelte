<script lang="ts">
  let { originalUrl, resultUrl }: { originalUrl: string; resultUrl: string } = $props();

  let sliderPosition = $state(50);
  let isDragging = $state(false);
  let container: HTMLDivElement | undefined = $state();

  function updatePosition(clientX: number) {
    if (!container) return;
    const rect = container.getBoundingClientRect();
    const x = clientX - rect.left;
    sliderPosition = Math.min(100, Math.max(0, (x / rect.width) * 100));
  }

  function handlePointerDown(e: PointerEvent) {
    isDragging = true;
    (e.target as HTMLElement).setPointerCapture(e.pointerId);
    updatePosition(e.clientX);
  }

  function handlePointerMove(e: PointerEvent) {
    if (!isDragging) return;
    updatePosition(e.clientX);
  }

  function handlePointerUp() {
    isDragging = false;
  }
</script>

<div
  bind:this={container}
  class="relative w-full max-h-[75dvh] rounded-2xl overflow-hidden select-none cursor-col-resize touch-none"
  onpointerdown={handlePointerDown}
  onpointermove={handlePointerMove}
  onpointerup={handlePointerUp}
  role="slider"
  aria-valuenow={Math.round(sliderPosition)}
  aria-valuemin={0}
  aria-valuemax={100}
  aria-label="Comparacion de imagen"
  tabindex={0}
>
  <!-- Result (background — checkerboard for transparency) -->
  <div class="checkerboard w-full">
    <img src={resultUrl} alt="Sin fondo" class="block w-full h-auto max-h-[75dvh] object-contain" draggable="false" />
  </div>

  <!-- Original (clipped overlay) -->
  <div
    class="absolute inset-0 overflow-hidden"
    style:width="{sliderPosition}%"
  >
    <img
      src={originalUrl}
      alt="Original"
      class="block w-full h-auto max-h-[75dvh] object-contain"
      style:min-width="0"
      style:width="{container ? container.offsetWidth + 'px' : '100%'}"
      draggable="false"
    />
  </div>

  <!-- Slider line -->
  <div
    class="absolute top-0 bottom-0 w-0.5 bg-white/80 -translate-x-1/2 pointer-events-none"
    style:left="{sliderPosition}%"
  >
    <!-- Handle -->
    <div class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-10 h-10 rounded-full bg-white/90 shadow-lg flex items-center justify-center pointer-events-none backdrop-blur-sm">
      <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="#050505" stroke-width="2.5" stroke-linecap="round">
        <polyline points="8 4 4 12 8 20" />
        <polyline points="16 4 20 12 16 20" />
      </svg>
    </div>
  </div>

  <!-- Labels -->
  <div class="absolute top-3 left-3 px-2.5 py-1 rounded-lg bg-black/60 backdrop-blur-sm text-xs font-display font-medium text-white/80 pointer-events-none">
    Original
  </div>
  <div class="absolute top-3 right-3 px-2.5 py-1 rounded-lg bg-black/60 backdrop-blur-sm text-xs font-display font-medium text-accent pointer-events-none">
    Sin fondo
  </div>
</div>
