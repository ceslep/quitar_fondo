<script lang="ts">
  import { removeBackground } from '@imgly/background-removal';
  import DropZone from './lib/DropZone.svelte';
  import ProgressBar from './lib/ProgressBar.svelte';
  import ImageComparison from './lib/ImageComparison.svelte';

  let originalFile: File | null = $state(null);
  let resultBlob: Blob | null = $state(null);
  let processing = $state(false);
  let progressPercent = $state(0);
  let progressLabel = $state('');
  let error: string | null = $state(null);

  let originalUrl = $derived(originalFile ? URL.createObjectURL(originalFile) : '');
  let resultUrl = $derived(resultBlob ? URL.createObjectURL(resultBlob) : '');
  let hasResult = $derived(!!resultBlob);

  // Cleanup object URLs
  $effect(() => {
    const url = originalUrl;
    return () => { if (url) URL.revokeObjectURL(url); };
  });

  $effect(() => {
    const url = resultUrl;
    return () => { if (url) URL.revokeObjectURL(url); };
  });

  async function handleFileSelect(file: File) {
    originalFile = file;
    resultBlob = null;
    error = null;
    processing = true;
    progressPercent = 0;
    progressLabel = 'Cargando modelo...';

    try {
      const blob = await removeBackground(file, {
        model: 'isnet',
        progress: (key: string, current: number, total: number) => {
          if (total > 0) {
            progressPercent = Math.round((current / total) * 100);
          }
          if (key.includes('download')) {
            progressLabel = 'Descargando modelo...';
          } else if (key.includes('compute') || key.includes('inference')) {
            progressLabel = 'Procesando imagen...';
          }
        },
      });
      resultBlob = blob;
    } catch (err) {
      error = err instanceof Error ? err.message : 'Error al procesar la imagen';
      console.error('Background removal failed:', err);
    } finally {
      processing = false;
    }
  }

  function handleDownload() {
    if (!resultBlob || !originalFile) return;
    const a = document.createElement('a');
    a.href = URL.createObjectURL(resultBlob);
    const baseName = originalFile.name.replace(/\.[^.]+$/, '');
    a.download = `${baseName}-sin-fondo.png`;
    a.click();
    URL.revokeObjectURL(a.href);
  }

  function handleReset() {
    originalFile = null;
    resultBlob = null;
    error = null;
    processing = false;
    progressPercent = 0;
    progressLabel = '';
  }
</script>

<div class="min-h-dvh flex flex-col">
  <!-- Header -->
  <header class="flex items-center justify-between px-6 py-5 md:px-10">
    <h1 class="font-display text-xl font-bold tracking-tight text-white">
      qf<span class="text-accent">remove</span>
    </h1>
    {#if originalFile}
      <button
        type="button"
        onclick={handleReset}
        class="text-sm text-white/40 hover:text-white/70 transition-colors font-body cursor-pointer"
      >
        Nueva imagen
      </button>
    {/if}
  </header>

  <!-- Main content -->
  <main class="flex-1 flex flex-col items-center justify-center px-6 pb-10 gap-8">
    {#if !originalFile}
      <!-- Empty state: drop zone -->
      <div class="w-full max-w-2xl flex flex-col items-center gap-8 animate-fade-in">
        <div class="text-center">
          <h2 class="font-display text-4xl md:text-5xl font-extrabold text-white tracking-tight leading-tight">
            Quita el fondo<br />
            <span class="text-accent">sin perder calidad</span>
          </h2>
          <p class="mt-4 text-white/40 text-base max-w-md mx-auto font-body">
            Procesamiento 100% local en tu navegador. Tu imagen nunca sale de tu dispositivo.
          </p>
        </div>
        <DropZone onfileselect={handleFileSelect} />
      </div>
    {:else if processing}
      <!-- Processing state -->
      <div class="w-full max-w-2xl flex flex-col items-center gap-8 animate-fade-in">
        <div class="relative w-full rounded-2xl overflow-hidden bg-surface">
          <img
            src={originalUrl}
            alt="Procesando"
            class="block w-full h-auto opacity-40"
          />
          <div class="absolute inset-0 flex items-center justify-center bg-black/40 backdrop-blur-[2px]">
            <div class="w-full px-10">
              <ProgressBar progress={progressPercent} label={progressLabel} />
            </div>
          </div>
        </div>
      </div>
    {:else if error}
      <!-- Error state -->
      <div class="w-full max-w-2xl flex flex-col items-center gap-6 animate-fade-in">
        <div class="w-full p-6 rounded-2xl bg-red-500/10 border border-red-500/20 text-center">
          <p class="text-red-400 font-display font-semibold">Error</p>
          <p class="mt-2 text-sm text-red-300/70">{error}</p>
        </div>
        <button
          type="button"
          onclick={handleReset}
          class="px-6 py-2.5 rounded-xl bg-surface-light hover:bg-surface-lighter text-white/80 text-sm font-medium transition-colors cursor-pointer"
        >
          Intentar de nuevo
        </button>
      </div>
    {:else if hasResult}
      <!-- Result state -->
      <div class="w-full max-w-4xl flex flex-col items-center gap-6 animate-fade-in">
        <ImageComparison {originalUrl} {resultUrl} />

        <div class="flex items-center gap-4">
          <button
            type="button"
            onclick={handleDownload}
            class="group flex items-center gap-2.5 px-6 py-3 rounded-xl bg-accent text-[#050505] font-display font-semibold text-sm hover:brightness-110 transition-all cursor-pointer"
          >
            <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="transition-transform group-hover:translate-y-0.5">
              <path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4" />
              <polyline points="7 10 12 15 17 10" />
              <line x1="12" y1="15" x2="12" y2="3" />
            </svg>
            Descargar PNG
          </button>

          <button
            type="button"
            onclick={handleReset}
            class="px-6 py-3 rounded-xl bg-surface-light hover:bg-surface-lighter text-white/70 text-sm font-medium transition-colors cursor-pointer"
          >
            Nueva imagen
          </button>
        </div>
      </div>
    {/if}
  </main>

  <!-- Footer -->
  <footer class="text-center pb-5 text-xs text-white/15 font-body">
    Procesamiento local &middot; Tu imagen no se sube a ningun servidor
  </footer>
</div>

<style>
  @keyframes fade-in {
    from { opacity: 0; transform: translateY(8px); }
    to { opacity: 1; transform: translateY(0); }
  }

  :global(.animate-fade-in) {
    animation: fade-in 0.4s ease-out;
  }
</style>
