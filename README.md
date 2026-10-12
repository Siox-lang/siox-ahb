# siox-ahb

The AMBA AHB family interfaces for [siox](https://github.com/Siox-lang/sioxc). Each
module has one width-generic struct with every signal and one view per
endpoint role.

| Module | Interface | Struct | Views |
|---|---|---|---|
| `ahb::lite` | AHB-Lite | `AhbLite<A, D>` | `Manager` / `Subordinate` |

The clock and reset are not part of the structs; they stay ordinary ports of
the endpoint entities. Each module's header says what is left out.

## Use

Until siox has a package manager, put the `ahb/` directory at your project
root (copy, symlink or submodule) and import from it:

```siox
use ahb::lite::{AhbLite, Subordinate};

entity Peripheral { clk: Bit in, bus: AhbLite<32, 32> Subordinate }
```

A parent connects a `Manager` and a `Subordinate` through one `let wire: AhbLite<32, 32>;`.

## Components

| Entity | What it does |
|---|---|
| `ahb::lite::Interconnect<A, D, N, REGION>` | One Manager to `N` Subordinates: the spec's decoder and multiplexer. Subordinate `i` owns the `2^REGION` bytes at `i << REGION`. Subordinates that aren't addressed see `htrans = IDLE`, so point-to-point Subordinates need no `hsel`. Unmapped addresses get the two-cycle `ERROR` response. |

## Test

Each `*_test.siox` drives an endpoint or component from a testbench:

```bash
sioxc --std <sioxc>/std --test lite_test.siox -o lite_test && ./lite_test
```

Needs a `sioxc` with Siox-lang/sioxc#69: arrays of view ports.
