# 02 · Synchroniser patterns

## Two-flop synchroniser — single bits only
```systemverilog
always_ff @(posedge dclk or negedge drst_n)
  if (!drst_n) {sync1, sync2} <= '0;
  else         {sync1, sync2} <= {async_in, sync1};
// use sync2 downstream
```
The first flop may go metastable; it gets a full `dclk` period to settle before
the second flop samples it. Works for **one bit**. Never run a multi-bit bus
through parallel copies of this — the bits resolve on different cycles and you
latch a value that never existed.

## Pulse / toggle synchroniser
To pass an event (not a level) from fast to slow domain: toggle a flop in the
source on the event, 2-FF synchronise it, and XOR the last two destination
samples to recover a one-cycle pulse. Handles pulses shorter than the
destination period.

## Handshake (req/ack)
For passing a multi-bit value safely with no FIFO: source raises `req` and holds
the data stable; destination 2-FF-synchronises `req`, captures the (now stable)
data, raises `ack`; source 2-FF-synchronises `ack` and drops `req`. Slow but
rock-solid — the data is guaranteed stable while sampled.

## Asynchronous FIFO — the workhorse for buses/throughput
Dual-clock FIFO with **gray-coded** read/write pointers synchronised across
domains. Gray coding means only one pointer bit changes at a time, so a
metastable sample is off by at most one — safe for the full/empty comparison.
This is how you move streaming data between clocks. See the FIFO RTL in
[vlsi-verilog-examples](https://github.com/chipxpertofficial/vlsi-verilog-examples).
