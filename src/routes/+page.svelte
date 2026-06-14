<script lang="ts">
  // State variables — Svelte 5 uses $state() for reactive variables
  // These are like "memory" for the component; when they change, the UI updates
  let selectedFile = $state<File | null>(null);
  let previewUrl = $state<string | null>(null);
  let isDragging = $state(false);

  // Called when the user picks a file via the input or drag-and-drop
  function handleFile(file: File) {
    // Only accept image files — MIME type starts with "image/"
    if (!file.type.startsWith('image/')) return;

    selectedFile = file;

    // FileReader reads the file as a data URL (base64 string)
    // This lets us display the image in the browser without uploading it yet
    const reader = new FileReader();
    reader.onload = (e) => {
      previewUrl = e.target?.result as string;
    };
    reader.readAsDataURL(file);
  }

  // Fires when user clicks the file input and selects a file
  function handleInputChange(e: Event) {
    const input = e.target as HTMLInputElement;
    if (input.files?.[0]) handleFile(input.files[0]);
  }

  // Drag-and-drop handlers
  // preventDefault() stops the browser from opening the file itself
  function handleDragOver(e: DragEvent) {
    e.preventDefault();
    isDragging = true;
  }

  function handleDragLeave() {
    isDragging = false;
  }

  function handleDrop(e: DragEvent) {
    e.preventDefault();
    isDragging = false;
    const file = e.dataTransfer?.files[0];
    if (file) handleFile(file);
  }

  // Clears the current selection so user can upload a different photo
  function reset() {
    selectedFile = null;
    previewUrl = null;
  }
</script>

<main class="min-h-screen bg-black text-amber-400 font-mono flex flex-col items-center justify-center p-6">

  <!-- Header -->
  <div class="mb-8 text-center">
    <h1 class="text-4xl font-bold tracking-widest text-amber-400 mb-1">X-POSE</h1>
    <p class="text-amber-600 text-sm tracking-wider">YOU UPLOADED THIS DATA YOURSELF. YOU JUST DIDN'T KNOW IT.</p>
  </div>

  <!-- Drop zone — conditionally shows preview if a file is selected -->
  {#if !previewUrl}
    <!-- svelte-ignore a11y_no_static_element_interactions -->
    <div
      class="w-full max-w-lg border-2 border-dashed transition-colors duration-200 rounded-sm p-12 flex flex-col items-center justify-center cursor-pointer
             {isDragging ? 'border-amber-400 bg-amber-400/10' : 'border-amber-700 hover:border-amber-500'}"
      ondragover={handleDragOver}
      ondragleave={handleDragLeave}
      ondrop={handleDrop}
    >
      <!-- Hidden file input — the label above triggers it on click -->
      <input
        type="file"
        accept="image/*"
        capture="environment"
        class="hidden"
        id="fileInput"
        onchange={handleInputChange}
      />

      <!-- Clicking this label triggers the hidden file input -->
      <label for="fileInput" class="flex flex-col items-center cursor-pointer">
        <!-- Camera icon (inline SVG, no dependency needed) -->
        <svg class="w-12 h-12 text-amber-600 mb-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5"
            d="M3 9a2 2 0 012-2h.93a2 2 0 001.664-.89l.812-1.22A2 2 0 0110.07 4h3.86a2 2 0 011.664.89l.812 1.22A2 2 0 0018.07 7H19a2 2 0 012 2v9a2 2 0 01-2 2H5a2 2 0 01-2-2V9z" />
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5"
            d="M15 13a3 3 0 11-6 0 3 3 0 016 0z" />
        </svg>
        <span class="text-amber-400 text-sm tracking-wider">DROP PHOTO HERE</span>
        <span class="text-amber-700 text-xs mt-1">or tap to select from camera / library</span>
      </label>
    </div>

  {:else}
    <!-- Preview state — shows after file is selected -->
    <div class="w-full max-w-lg flex flex-col items-center gap-4">

      <!-- Image preview -->
      <div class="w-full border border-amber-700 rounded-sm overflow-hidden">
        <img src={previewUrl} alt="Selected" class="w-full object-contain max-h-72" />
      </div>

      <!-- File info -->
      <div class="w-full bg-amber-950/30 border border-amber-800 rounded-sm p-3 text-xs text-amber-500 space-y-1">
        <div class="flex justify-between">
          <span>FILE</span>
          <span class="text-amber-300">{selectedFile?.name}</span>
        </div>
        <div class="flex justify-between">
          <span>SIZE</span>
          <span class="text-amber-300">{selectedFile ? (selectedFile.size / 1024).toFixed(1) + ' KB' : ''}</span>
        </div>
        <div class="flex justify-between">
          <span>TYPE</span>
          <span class="text-amber-300">{selectedFile?.type}</span>
        </div>
      </div>

      <!-- Action buttons -->
      <div class="flex gap-3 w-full">
        <button
          onclick={reset}
          class="flex-1 border border-amber-700 text-amber-600 text-xs tracking-wider py-2 hover:border-amber-500 hover:text-amber-400 transition-colors"
        >
          CLEAR
        </button>
        <button
          class="flex-1 bg-amber-400 text-black text-xs tracking-wider font-bold py-2 hover:bg-amber-300 transition-colors"
        >
          ANALYZE →
        </button>
      </div>
    </div>
  {/if}

  <!-- Footer -->
  <p class="mt-12 text-amber-900 text-xs tracking-widest">HGSS // X-POSE // LOCAL ONLY</p>

</main>
