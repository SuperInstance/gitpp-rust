# gitpp-rust

> A 4th port of the git.pp fold in Rust. Doc-only discipline (written from `DESIGN.md` + `docs/CHECKPOINT-V2.md` + the witness-freshness formula in `docs/ENGINEERING.md` of [`patchwork-quilt/git.pp-ng`](https://github.com/patchwork-quilt/git.pp-ng) — the TypeScript and Python sources are never read). Catches assumptions the three existing ports (TS / Python / Elixir) share.

**Status:** scaffolded. The fold, the leaf, the closure root, the conformance runner.

## Why

The three existing ports are interpreted or BEAM-JIT. A compiled-systems port:

- Catches float64 canonicalization drift that the interpreted ports mask
- Catches any encoding assumption that "happens to" agree on the safe range
- Is the natural target for a no-runtime-dependency library that ships as a crate
- Stress-tests the conformance corpus (863 cases + 16,290 closure-root digests) on a fundamentally different runtime

## Layout (proposed)

```
src/
  fold.rs          # the fold, derive() -> {state, order, conflicts, orphans}
  leaf.rs          # format-v2 record leaf (SHA-256 over canonical JSON)
  closure.rs       # closure_root(at) -> SHA-256 of id:leaf join
  canon.rs         # float64 + leaf-number encoding (one canonical form)
  conformance.rs   # the 863-case harness, ported
tests/
  corpus/          # the conformance corpus (mirrored from the hub)
  fuzz.rs          # proptest / arbitrary fuzzer for property coverage
```

## Pass criterion

`cargo test --release` reproduces the pinned closure root for `poc-quilt-1@task-c`:

```
60effe23970d8a5cc837636c44685502361288c1f00577e9addfa10cbd4fc91d
```

and 863/863 on the conformance corpus, all in lockstep with the hub's referee.

## Doc-only discipline

The Rust implementation is written from the public spec only. The TypeScript and Python sources are never read. This is the same discipline `patchwork-quilt/gitpp-beam` (Elixir) followed — and the same test that discipine proves: if a fourth port reproduces the pinned digests from the spec alone, the spec is complete and the other ports are not "just the same code with the names swapped."

## Status

The repo is empty by design. The implementation is being built by a subagent (Mavis background task) and will land via PR with a green `cargo test --release` before the conformance gate is run. Watch the commit stream.
