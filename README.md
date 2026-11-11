# Clock Domain Crossing (CDC) Guide

Signals that cross between unrelated clocks are the source of bugs that pass
simulation and fail in silicon intermittently. This guide covers why, and the
standard synchroniser patterns that make crossings safe.

## Notes
| Note | Topic |
|------|-------|
| [01 · Why CDC is hard](notes/01-metastability.md) | metastability, MTBF, why one flop isn't enough |
| [02 · Synchroniser patterns](notes/02-synchronizers.md) | 2-FF, pulse, handshake, async FIFO |
| [03 · Review & verification](notes/03-verification.md) | CDC lint, what tools catch, common mistakes |

## The one rule
**Never let a signal from clock A be sampled combinationally by clock B.** Pass
single bits through a two-flop synchroniser, multi-bit buses through a
gray-coded async FIFO or a req/ack handshake — never a raw parallel bus through
parallel 2-FF synchronisers.

## Companion material
- The FIFOs behind async crossings → [vlsi-verilog-examples](https://github.com/chipxpertofficial/vlsi-verilog-examples)
- CDC interview questions → [vlsi-interview-prep](https://github.com/chipxpertofficial/vlsi-interview-prep)
- Mentored training → [ChipXpert](https://chipxpert.in/)

## License
MIT — see [LICENSE](LICENSE). Maintained by the ChipXpert team.
