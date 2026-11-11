# 03 · Reviewing and verifying CDC

Functional simulation alone will NOT catch CDC bugs — RTL sim treats flops as
ideal and never models metastability. You need structural CDC analysis.

## What a CDC lint tool checks
- Every crossing has a recognised synchroniser (flags raw combinational
  crossings and single-flop crossings).
- Multi-bit buses use gray coding / handshake, not parallel 2-FF.
- **Reconvergence:** two separately synchronised signals that recombine can
  glitch because they resolve on different cycles — flagged.
- Each synchroniser has a matching **set_false_path** / `set_max_delay -datapath`
  constraint so STA doesn't try (and fail) to time the async crossing.

## Metastability injection
Good flows re-run gate simulation with random metastability delay injected on
synchroniser outputs, to prove downstream logic tolerates a one-cycle-late
resolve. This catches reconvergence bugs sim otherwise hides.

## Common mistakes
- Bus through parallel 2-FF synchronisers (bits skew) → use a FIFO/handshake.
- Synchronising a signal, then fanning it out to logic that *also* uses the
  unsynchronised copy.
- Forgetting the false-path constraint → STA noise or, worse, the tool buffering
  the async path as if it were real timing.
- Gating/combining a signal *before* the synchroniser, re-introducing glitches.

CDC review belongs in every design review, next to the
[STA sign-off](https://github.com/chipxpertofficial/sta-timing-closure-notes).
