# Repro: rsbuild 2.1.1 persistent cache writes no pack files

Demonstrates a regression introduced in rsbuild 2.1.1 (rspack 2.1.x) where the
persistent build cache creates the expected directory structure but never writes
pack files, so every build runs at full cold-build time.

| Branch | rsbuild | Cache |
|---|---|---|
| `main` | 2.1.1 | **broken** — no pack files written |
| `working` | 2.0.15 | working — warm builds ~3x faster |

## Reproduce

```bash
pnpm install
pnpm run build:rsbuild   # run 1 — cold build, note the time
pnpm run build:rsbuild   # run 2 — same time as run 1 (expected: significantly faster)
pnpm run build:rsbuild   # run 3 — same time again + error:
                         # "Rspack persistent cache save failed: Rspack FS Error:IO error: No such file or directory (os error 2)"
```

After run 1, inspect the cache directory — subdirectories are created but no pack files are written:

```
node_modules/.cache/rsbuild/
└── rspack_v_2_1_1/
    └── occasion_minimize/
        (empty)
```

## Working version

Switch to the `working` branch to confirm rsbuild 2.0.15 restores cache behavior:

```bash
git checkout working
pnpm install
pnpm run build:rsbuild   # run 1 — cold build
pnpm run build:rsbuild   # run 2 — significantly faster (warm cache)
```
