# Contest and Solver Engineering

Use this reference while implementing and hardening the final program. Correct
algorithm design can still score zero when the target compiler, protocol,
serialization, numeric range, or deadline is wrong.

## 1. Reproduce the target environment

Read the config and build command. Record:

```text
language standard and compiler version
optimization/debug flags and architecture flags
available headers, libraries, and runtime
time and memory limits per case/process
input/output protocol and working directory
```

Compile early with the actual standard. Avoid assuming optional libraries,
Boost components, compiler extensions, AVX/AVX2, or native CPU flags exist.
Intrinsics require matching target flags and judge hardware. Prefer portable
scalar code unless the environment explicitly guarantees the feature and the
gain is measured.

Use warnings in development, for example:

```text
-Wall -Wextra -Wshadow -Wconversion
```

Treat warnings involving return types, narrowing, signedness, uninitialized
state, comparator requirements, and ambiguous names as correctness issues.

## 2. Numeric safety

Derive value bounds for every multiplication, sum, distance, count, and score.

- Use `int64_t`/`long long` when 32-bit can overflow; use `__int128` for
  intermediate products when supported by the target.
- Check sentinel arithmetic. `INF + weight`, negating the minimum signed value,
  and evaluating a sentinel line can overflow before comparison.
- Normalize negative modular residues and prove modular inverse existence.
- Keep objective direction consistent when negating scores or costs.
- Avoid exact equality on floating point unless values are constructed to be
  exact. Use scale-aware predicates and one boundary convention.
- Keep scoring computations compatible with the official rounding order.

Sanitizers (`address`, `undefined`) are valuable on development tests, but use a
release build for timing and verify sanitizer availability first.

## 3. Memory layout

Estimate bytes, not just Big-O:

```text
vector capacity, node padding, allocator metadata, hash load factor,
recursion stack, duplicated versions, rollback logs, per-test clearing
```

Avoid enormous arrays as local stack objects. Allocate large pools statically or
on the heap with checked capacity. Persistent structures need a proven maximum
node count and overflow guard. Prefer flat contiguous arrays when traversal is
hot; reserve vectors and reuse buffers inside search loops.

Do not clear an `O(max_size)` table per tiny test when timestamps or touched
indices suffice. Release or reuse per-case data according to total memory.

## 4. Runtime and deadlines

Use a monotonic clock such as `steady_clock`. Set an internal limit below the
official time and include parsing, construction, final validation, and output.

```text
deadline = start + internal_budget
periodic check interval * worst move time bounds overshoot
```

Measure timer-call cost before checking on every tiny transition. Conversely,
do not run a large uninterruptible loop/subsolve without a deadline check or
work cap. Host load and measurement granularity make near-limit results flaky.

For multiple cases under one process limit, allocate time adaptively but retain
a guaranteed path to output for every remaining case. Keep the best legal state
available if search stops immediately.

Profile optimized binaries with representative data. Common hidden costs:

- copying full state on every move;
- full rescoring instead of local delta;
- repeated allocation/free and string construction;
- maps/hashes where sorted vectors or arrays suffice;
- RNG/distributions in the hottest loop;
- excessive logging or flushing;
- pathological repair or recursion tails.

## 5. Randomness

Use a fast, reproducible generator appropriate for the task. During debugging,
accept or log an explicit seed. Distinguish the program seed from instance and
judge-noise seeds.

- Avoid modulo bias when it can affect the method materially.
- Check empty/singleton ranges before sampling.
- Keep deterministic tie-breaking when comparing algorithms.
- Do not reseed repeatedly from low-resolution time.
- A time-derived release seed may improve diversity, but preserve a way to
  reproduce failures and confirm the judge permits nondeterminism.

## 6. Input/output and serialization

Parse the exact token grammar and sizes. For large input, use suitable fast I/O,
but do not mix incompatible buffered I/O APIs carelessly.

For batch output:

- print exactly the required number/order of tokens or lines;
- avoid extra debug text unless comments are explicitly permitted;
- validate indices, uniqueness, counts, and bounds after conversion from the
  internal state;
- ensure the output buffer/string cannot grow without bound;
- retain enough time for serialization and flush.

For interactive output:

- read required initial data before issuing a query;
- flush after every query/action that expects a response;
- parse special termination/error responses before updating state;
- never print diagnostics to stdout; use stderr only if permitted;
- do not wait for an initial token the protocol never sends;
- avoid naming helpers so they collide with standard-library functions.

Test the real process through pipes against a simulator to expose buffering and
EOF mistakes.

## 7. Data structures and invariants

Advanced structures are high-risk code. For each operation state:

```text
precondition, mutation, lazy/tag composition, maintained aggregate,
index convention, complexity, inverse/rollback behavior
```

Property-test them independently. Important cases:

- empty/single element and full range;
- repeated equal keys and duplicate insert/delete;
- overlapping lazy updates in different orders;
- split then merge identity;
- link/cut only across valid components;
- custom comparator is a strict weak ordering;
- coordinate compression maps every queried value;
- pools and queues cannot exceed capacity.

Prefer a simpler verified structure if the advanced option does not decide the
complexity bound.

## 8. Development and release modes

Development build can include:

- assertions and full invariant checks;
- apply/undo and delta/full-score comparisons;
- deterministic seeds and verbose stderr traces;
- sanitizers and slow reference paths;
- operation counters and stage timers.

Release build should:

- retain cheap guards needed for legality;
- disable or sample expensive diagnostics;
- bound all variable work;
- use target-compatible flags only;
- contain no filesystem/network/private-data dependency unless authorized;
- produce a valid fallback under early deadline.

Run both modes on the same regression corpus, then rerun release in a clean
process/container. Reused containers may retain writable temporary state, so a
clean run is part of validation.

## 9. Other target languages

The examples above are C++-first, but the same contract applies elsewhere.
Verify the judge's exact runtime and version, integer and recursion semantics,
available packages, startup/JIT cost, memory representation, buffered I/O, and
process deadline. In Java, watch boxing and heap/stack limits; in Python, bound
object-heavy state and interpreter-scale loops; in Rust, check ownership-driven
copies, integer conversions, and stable toolchain support. Benchmark the target
language rather than translating C++ operation folklore.

## 10. Final audit

Before submission, answer concretely:

- Which compiler command succeeded?
- Which official or local judge command succeeded?
- What are worst and p95 runtime and memory on representative/stress cases?
- What margin remains below the limit?
- Which numeric products have explicit bounds?
- Does every early-exit path produce legal output or terminate protocol safely?
- Is the saved final artifact the best verified one?
- Are unavailable headers, CPU features, accidental debug output, and private
  data access absent?
