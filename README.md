# Parsomnia Assets

Public static assets for Parsomnia clients.

## Wikidata

- `wikidata/sample-wikidata.jsonl` - small deterministic sample seed used for offline matching development.

## Voice

- `voice/` - speaker-embedding model (Core ML + ONNX), canary, and manifest for
  on-device voice identity. See [`voice/README.md`](voice/README.md). Large
  binaries (e.g. the 84 MB ONNX) are attached to GitHub Releases; small files and
  the manifest live in-tree and are served over the raw CDN.
