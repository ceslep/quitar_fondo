<script lang="ts">
  let { onfileselect }: { onfileselect: (file: File) => void } = $props();

  let isDragging = $state(false);
  let fileInput: HTMLInputElement | undefined = $state();

  function handleDrop(e: DragEvent) {
    e.preventDefault();
    isDragging = false;
    const file = e.dataTransfer?.files[0];
    if (file && file.type.startsWith('image/')) {
      onfileselect(file);
    }
  }

  function handleDragOver(e: DragEvent) {
    e.preventDefault();
    isDragging = true;
  }

  function handleDragLeave() {
    isDragging = false;
  }

  function handleFileChange(e: Event) {
    const input = e.target as HTMLInputElement;
    const file = input.files?.[0];
    if (file) {
      onfileselect(file);
    }
  }

  function handlePaste(e: ClipboardEvent) {
    const file = e.clipboardData?.files[0];
    if (file && file.type.startsWith('image/')) {
      onfileselect(file);
    }
  }

  function handleClick() {
    fileInput?.click();
  }
</script>

<svelte:document onpaste={handlePaste} />

<button
  type="button"
  class="group relative w-full max-w-2xl mx-auto aspect-[4/3] rounded-2xl border-2 border-dashed transition-all duration-300 cursor-pointer outline-none focus-visible:ring-2 focus-visible:ring-accent/50 {isDragging
    ? 'border-accent bg-accent-dim scale-[1.02]'
    : 'border-white/10 bg-surface hover:border-white/25 hover:bg-surface-light'}"
  ondrop={handleDrop}
  ondragover={handleDragOver}
  ondragleave={handleDragLeave}
  onclick={handleClick}
>
  <div class="absolute inset-0 flex flex-col items-center justify-center gap-5 p-8">
    <!-- Upload icon -->
    <div class="w-16 h-16 rounded-2xl bg-surface-lighter flex items-center justify-center transition-transform duration-300 group-hover:scale-110 {isDragging ? 'scale-110' : ''}">
      <svg width="32" height="32" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round" class="text-accent">
        <path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4" />
        <polyline points="17 8 12 3 7 8" />
        <line x1="12" y1="3" x2="12" y2="15" />
      </svg>
    </div>

    <div class="text-center">
      <p class="font-display text-lg font-semibold text-white/90">
        {isDragging ? 'Suelta la imagen aqui' : 'Arrastra una imagen aqui'}
      </p>
      <p class="mt-2 text-sm text-white/40">
        o haz clic para seleccionar &middot; tambien puedes pegar (Ctrl+V)
      </p>
    </div>

    <div class="flex items-center gap-3 text-xs text-white/25">
      <span>PNG</span>
      <span class="w-1 h-1 rounded-full bg-white/20"></span>
      <span>JPG</span>
      <span class="w-1 h-1 rounded-full bg-white/20"></span>
      <span>WebP</span>
    </div>
  </div>

  <input
    bind:this={fileInput}
    type="file"
    accept="image/*"
    class="hidden"
    onchange={handleFileChange}
  />
</button>
