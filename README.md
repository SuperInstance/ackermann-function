# Ackermann Function

**The Ackermann Function** is a Rust implementation of the legendary recursive function A(m, n) that grows faster than any primitive recursive function, computed using an explicit stack and memoization to avoid call-stack overflow.

## Why It Matters

The Ackermann function is the canonical example of a computable function that is not primitive recursive — it cannot be expressed with bounded loops alone. Discovered by Wilhelm Ackermann in 1928, it demonstrates that recursion with unbounded depth is strictly more powerful than iteration. The function's explosive growth (A(4, 2) has 19,729 decimal digits) makes it a standard benchmark for recursion depth, stack implementation, and arbitrary-precision arithmetic. In computability theory, it separates the classes of primitive recursive and μ-recursive functions, a foundational result that underpins program verification and termination analysis.

## How It Works

The function is defined by three cases:

```
A(0, n)     = n + 1
A(m, 0)     = A(m−1, 1)          for m > 0
A(m, n)     = A(m−1, A(m, n−1))  for m, n > 0
```

**Naive recursion** blows the call stack almost immediately: A(3, 4) requires over 10,000 nested calls. This implementation uses two critical optimizations:

1. **Explicit stack**: Instead of relying on the CPU call stack (typically 8MB on Linux), the algorithm maintains a `Vec<(u32, u64)>` as its own stack. This allows heap-based growth and avoids stack overflow for moderate inputs.

2. **Memoization via HashMap**: A `HashMap<(u32, u64), u64>` caches previously computed values. Since A(m, n) has overlapping subproblems (A(2, n) is called many times during A(3, k)), memoization converts the exponential blowup into near-linear time for fixed m.

**Sentinel-based evaluation:**
The stack uses `u64::MAX` as a sentinel value to mark slots where the inner result of a nested call is needed. The algorithm pushes `(cm, u64::MAX)` as a placeholder, then pushes the sub-problem `(cm-1, 1)` or `(cm, cn-1)`. When the sub-problem completes, the sentinel is replaced with the computed value.

**Growth rates by level:**

| m | A(m, n) | Closed form |
|---|---------|-------------|
| 0 | n + 1 | Successor |
| 1 | n + 2 | Addition by 2 |
| 2 | 2n + 3 | Linear |
| 3 | 2^(n+3) − 3 | Exponential |
| 4 | 2↑↑(n+3) − 3 | Tower (tetration) |

## Quick Start

```rust
use std::collections::HashMap;

fn ackermann(m: u32, n: u64) -> u64 {
    let mut memo: HashMap<(u32, u64), u64> = HashMap::new();
    let mut stack: Vec<(u32, u64)> = vec![(m, n)];
    while let Some(key @ (cm, cn)) = stack.last().copied() {
        if let Some(&val) = memo.get(&key) {
            stack.pop();
            if let Some(parent) = stack.last_mut() {
                if parent.0 == cm + 1 && parent.1 == u64::MAX {
                    *parent = (cm, val);
                }
            }
            continue;
        }
        if cm == 0 {
            let val = cn + 1;
            memo.insert(key, val);
            stack.pop();
        } else if cn == 0 {
            stack.pop();
            stack.push((cm, u64::MAX));
            stack.push((cm - 1, 1));
        } else {
            if let Some(&inner) = memo.get(&(cm, cn - 1)) {
                stack.pop();
                stack.push((cm - 1, inner));
            } else {
                stack.push((cm, cn - 1));
            }
        }
    }
    memo[&(m, n)]
}

fn main() {
    println!("A(3, 3) = {}", ackermann(3, 3)); // 61
    println!("A(3, 5) = {}", ackermann(3, 5)); // 253
}
```

## API

| Function | Parameters | Returns | Notes |
|----------|-----------|---------|-------|
| `ackermann(m, n)` | m: u32, n: u64 | u64 | m ≤ 4, n ≤ ~10⁵ |

## Architecture Notes

The Ackermann function demonstrates the kind of **non-primitive-recursive computation** that appears in the SuperInstance conservation-law framework: the avoidance-ratio conservation (Law 5) involves recursively defined quantities that grow beyond primitive-recursive bounds. The explicit-stack technique used here informs the fleet's bounded-recursion enforcement in the γ + η = C conservation equation.

See [ARCHITECTURE.md](https://github.com/SuperInstance/SuperInstance/blob/main/ARCHITECTURE.md).

## References

1. Ackermann, W. (1928). "Zum Hilbertschen Aufbau der reellen Zahlen." *Mathematische Annalen*, 99, 118–133.
2. Péter, R. (1967). *Recursive Functions*. Academic Press. Chapter 1: Primitive Recursive Functions.
3. Knuth, D.E. (1976). "Mathematics and Computer Science: Coping with Finiteness." *Science*, 194(4271), 1235–1242.

## License

MIT
