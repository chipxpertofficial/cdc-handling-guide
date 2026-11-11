# 01 · Why CDC is hard — metastability

## The setup/hold violation you can't avoid
A flip-flop needs its D input stable for a setup time before and a hold time
after the clock edge. When the source and destination clocks are unrelated, a
crossing signal *will* eventually change right at the destination's edge,
violating setup/hold. You cannot prevent it — the clocks have no fixed phase.

## Metastability
On a setup/hold violation the flop output can hang at an intermediate voltage,
neither 0 nor 1, for an unbounded time before resolving randomly. Downstream
logic sampling that half-level can read different values → a bug that depends on
temperature, voltage, and luck.

## MTBF — it's statistical, not solvable
```
MTBF = e^(t_r / τ) / (f_clk · f_data · T_0)
```
Giving the flop more time **t_r** to resolve makes MTBF astronomically large.
You don't eliminate metastability; you make its mean-time-between-failures
longer than the product's life.

## Why one flop isn't enough
A single destination flop gives the metastable value no time to settle before
the next stage uses it. The fix is to add a second flop so the first has a full
clock period to resolve — the [two-flop synchroniser](02-synchronizers.md).
