# Ackermann Function

The **Ackermann function** is a classic example in computability theory — a total computable function that is not primitive recursive. It grows faster than any primitive recursive function.

## Why It Matters

Ackermann demonstrates that not all computable functions are primitive recursive. It's used as a benchmark for recursion depth, stack overflow handling, and compiler optimization of deep recursion. In practice, it stress-tests language runtimes and JIT compilers.

## How It Works

Defined recursively: A(0, n) = n+1; A(m+1, 0) = A(m, 1); A(m+1, n+1) = A(m, A(m+1, n)). Even small inputs produce enormous outputs — A(4,2) has more digits than atoms in the universe.

## Usage

```toml
[dependencies]
ackermann_function = "0.1.0"
```

```rust
use ackermann_function;

// See examples/ directory for detailed usage
```

## API

API documentation is generated from source doc-comments.

## Architecture

This crate is part of the **[SuperInstance](https://github.com/SuperInstance)** ecosystem — a conservation-law-based framework for fleet coordination, ternary computation, and distributed agent systems.

### Related Crates

- [`superinstance-core`](https://github.com/SuperInstance/superinstance-core) — Core conservation law (γ + η = C)
- [`superinstance-harness`](https://github.com/SuperInstance/superinstance-harness) — Build harness and self-improving loop
- [`fleet-coordinator`](https://github.com/SuperInstance/fleet-coordinator) — Fleet-level coordination

## References

- [SuperInstance Architecture](https://github.com/SuperInstance/SuperInstance/blob/main/ARCHITECTURE.md)
- [Conservation Law Paper](https://github.com/SuperInstance/SuperInstance/blob/main/docs/conservation-law.md)

## License

MIT
