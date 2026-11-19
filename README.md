# fauzan0096.github.io

Root of the domain. `/` opens the Bayyin app (`/quran-recitation-checker/`), and
`/.well-known/assetlinks.json` lets the Play build open without a URL bar.

## `vendor/ort/` — shared by the app

The ONNX runtime that runs the Quran speech model on the phone:

- `ort-wasm-simd-threaded.jsep.wasm` — 27 MB, the runtime itself
- `ort-wasm-simd-threaded.jsep.mjs` — 46 KB, its loader

Version `onnxruntime-web@1.31.0-dev.20260914-8d85527a0` — the exact build
`@huggingface/transformers` 4.3.2 uses. MIT (Microsoft Corporation).

It lives here because the app repository refuses a file this size. `asr.js` and
`asr-worker.js` (in the app repo) set `env.backends.onnx.wasm.wasmPaths` to
`/vendor/ort/`, so the app asks nobody but this domain; the service worker caches
the `.wasm` after the first check, and the app falls back to its CDN copy if this
folder is ever missing.

## `vendor/bayyin-base/` — the high-accuracy model

Filled by the workflow **Bundle the Base model** (Actions → Run workflow). It
downloads the Tarteel base Quran model (Apache-2.0) once and commits it here:
config + tokenizer files plus `onnx/encoder_model_quantized.onnx` (23 MB) and
`onnx/decoder_model_merged_quantized.onnx` (80 MB).

After that run the app's "Bayyin Base" model loads from this domain too, and
Hugging Face is only a fallback. To refresh it later, run the workflow again with
`force` checked.

