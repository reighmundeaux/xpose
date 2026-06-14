# X-Pose

> You uploaded this data yourself. You just didn't know it.

X-Pose is a local OSINT intelligence tool that extracts and visualizes publicly available metadata and detectable information from uploaded photos.

## What it does

- Extracts EXIF metadata via a compiled C binary (GPS, device, timestamps, camera settings)
- Runs computer vision detection via Python 3 (objects, text, scene classification)
- Visualizes everything as a structured intelligence dossier — the "second photo"

## Stack

- **Frontend/Backend**: SvelteKit 2 + Svelte 5 + Tailwind 4 + Bun
- **Metadata extraction**: C + libexif
- **Detection layer**: Python 3 + YOLO + Tesseract + OpenCV
- **Optional**: Ollama (local LLM summary)

## Status

🔧 Active development — Block 0 (scaffold)

## Legal

X-Pose only reads data that is already embedded in files or publicly visible in images. No private data sources, no network scraping.

---

*Home Grown Software Solutions — "It's not a bug, it's a lab environment."*
