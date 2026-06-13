# Voice — speaker embedding model

On-device + server speaker-embedding model for Parsomnia voice identity.

- **Realized model:** SpeechBrain `spkrec-ecapa-voxceleb` (ECAPA-TDNN), 192-d.
- **Compatibility class:** `pyannote-embedding-v1:coreml-v1` (the opaque
  interoperability id the schema/clients are wired to; the *realized* model is
  ECAPA — see `model_metadata.json`).
- **Contract:** input `audio` = raw 16 kHz mono Float32 PCM, **40000 samples
  (2.5 s)**; output `embedding` = 192-d (raw; callers L2-normalize). The log-mel
  front-end is embedded in the graph, so callers pass raw PCM. Arbitrary-length
  audio is split into 2.5 s windows and the per-window L2-normalized vectors are
  mean-pooled, identically on device and server.

## Files (version `local-coreml-v1`)

| File | What | Where |
| --- | --- | --- |
| `speaker-embedding.mlpackage.zip` | Core ML (mlprogram, fp16) — device backend `coreml-fp16-anepreferred-v1` | this dir (raw CDN) |
| `speaker-embedding.canary.json` | Fixed PCM + reference vector for the on-device canary self-check (cosine ≥ 0.999) | this dir (raw CDN) |
| `speaker-embedding.onnx` | ONNX (fp32) — server backend `server-onnx-fp32-v1` | Release `voice-local-coreml-v1` (84 MB) |
| `MANIFEST.json` | Versions, URLs, sha256, parity numbers | this dir |
| `model_metadata.json` / `parity_report.json` | Export + parity provenance | this dir |

The on-device app downloads the `.mlpackage.zip`, unzips, compiles with
`MLModel.compileModel(at:)`, and verifies the canary before enabling local
matching. Server (Core ML, fp16) ↔ ONNX (fp32) parity is **cosine 0.999989**
(tolerance 0.995).

## Regenerating / updating

Artifacts are produced by `ios-native/tools/voice-model-export/` in the main
`parsomnia` repo (`export_model.py` then `make_canary.py`). Bump `assetVersion`
in `MANIFEST.json` and the client constant on any model/front-end change so
clients cache-bust.
