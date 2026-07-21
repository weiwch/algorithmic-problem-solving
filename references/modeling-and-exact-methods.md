# Modeling and Exact/Hybrid Feasibility

Use this reference while converting a statement/checker into a mathematical model and deciding whether exact, constructive, hybrid, or heuristic work is justified.

## 1. Normalize the contract

Write the problem without story terms:

```text
Given instance I, choose x in F(I).
Hard constraints: C_j(I, x) = true for every j.
Raw objective: minimize/maximize f(I, x).
Observed score: S(I, x) = transform(f, baseline, bound, aggregation).
```

For interactive work add hidden state `h`, transcript `z_1..z_t`, response model `P(z_t | h, query_t, history)`, and query/action budget. For reactive work add system state, observation, transition, horizon, and per-round reward.

Check these common modeling traps:

- a fine-grained constraint was replaced with an unjustifiably stronger one;
- equality, duplicates, orientation, or order are semantically meaningful;
- the score uses a clamp, ratio, rounding, logarithm, lexicographic priority, baseline, or whole-run failure rule;
- input contains several instances sharing one time budget;
- the public statement and executable checker disagree;
- a stochastic generation claim is a guarantee only in distribution, not per instance;
- interactive responses may be adversarial rather than sampled independently.

## 2. Extract scale before technique

Create an operation and memory envelope from the actual bounds.

```text
number of states or objects
branching / transitions / queries per state
number of test cases or rounds
bytes per state, edge, table entry, cache, and copy
I/O and reconstruction cost
```

Use worst-case bounds for correctness and representative pilots for constants. Account for bitset word count, hash/map constants, allocator traffic, recursion, cache locality, and repeated clearing. A mathematically smaller Big-O method can lose when constants or memory bandwidth dominate, but empirical speed does not rescue a method whose worst case violates the contract.

For bounded exponential methods estimate the actual exponent after reductions:

```text
raw variables -> forced decisions -> components -> symmetry classes ->
kernel size -> meet-in-the-middle split -> remaining states
```

## 3. Exact-method checklist

Test families in roughly this order, stopping when a clean fit is established:

1. **Direct structure:** sorting, counting, prefix/suffix summaries, two pointers, monotonicity, greedy exchange, sweep, binary search on answer.
2. **Graph formulation:** connectivity, shortest path, DAG order, SCC, MST, matching, max-flow/min-cut, min-cost flow, 2-SAT, difference constraints.
3. **Dynamic programming:** minimal sufficient state, ordering, transition locality, rolling memory, bitset acceleration, state dominance.
4. **Algebra/geometry/string:** exploit the exact mathematical structure rather than discretizing blindly.
5. **Decomposition:** independent components, separators, treewidth, centroid, block structure, coordinate compression, offline ordering.
6. **Bounded exponential:** subset DP, meet-in-the-middle, branch-and-bound, iterative deepening, memoized search, inclusion-exclusion.
7. **General solver:** SAT/SMT, CP-SAT, ILP/MIP, or convex optimization when the environment permits it and model size is supportable.

Do not infer hardness only from a large raw search space. Constraints may imply total unimodularity, matroid/flow structure, interval order, convexity, a small parameter, or a decomposable interaction graph.

### When exact methods do not fit

If no complete exact or exact-hybrid route fits the verified time and memory envelope with adequate margin, do not force an incomplete method. When the output contract explicitly permits non-optimal solutions or uses a score-based objective, switch to a heuristic or scored hybrid. Preserve hard feasibility, and, when early termination could otherwise lose all value, include a guaranteed-valid fallback. Keep the true raw objective authoritative for comparison and final output. The first candidate should still implement the strongest plausible complete scored route; do not stop at the fallback. Once validated, that artifact becomes the champion.

Do not load [heuristic-search.md](heuristic-search.md) merely because non-optimal output is allowed. Read it only when a verified champion produces its routing signal. If the plateau criteria are met, first complete [the mandatory fresh-context plateau escape](../SKILL.md#mandatory-fresh-context-plateau-escape) before selecting another method family.

## 4. State design and dominance

A DP/search state should retain exactly what affects future feasibility and value. Derive it by asking which histories are equivalent for every possible continuation. Then look for:

- canonicalization under interchangeable labels, rotations, reflections, or component order;
- dominance: discard state `a` if state `b` has no worse resources/value and at least the same continuation set;
- monotone resources enabling Pareto frontiers;
- sparse reachable states enabling maps instead of dense arrays;
- a small boundary between processed and unprocessed structure;
- reversible transitions and compact reconstruction parents.

Validate a proposed compression by constructing two histories with the same compressed state and trying to find a continuation that treats them differently. Such a continuation is a counterexample to the state definition.

## 5. Proof obligations

Choose the proof shape that matches the algorithm:

- **Greedy:** feasibility plus an exchange/cut/stays-ahead argument. Naming a greedy score is not a proof.
- **DP:** state meaning, base cases, exhaustive and non-overlapping transition cases, induction order, and reconstruction.
- **Graph reduction:** map feasible solutions both ways and show objective preservation; check capacities, integrality, and direction.
- **Binary search:** monotone predicate, correct boundary convention, and constructive witness when required.
- **Invariant-based construction:** initialization, preservation by every operation, progress, termination, and output conversion.
- **Randomized exact:** distinguish one-sided/two-sided error from Las Vegas correctness; bound error probability and runtime tails.

Try to falsify each proof assumption with tiny exhaustive enumeration before trusting prose.

## 6. Constructive feasibility

When many outputs are accepted but not scored, model the construction as a sequence of invariant-preserving decisions. Prefer representations that make duplicates, overlap, disconnectedness, or capacity violations impossible.

Useful patterns include:

- build a spanning/tree/path backbone, then fill optional structure;
- satisfy the most constrained object first;
- use Hall/cut/degree/parity conditions to detect impossibility early;
- reserve slack explicitly rather than consuming all capacity greedily;
- canonicalize symmetric choices to simplify proof and testing;
- validate the final serialization separately from the internal structure.

If the task is always promised feasible, do not let the promise replace a proof that the chosen construction reaches a solution.

## 7. Hybrid designs

"Hybrid" describes composition, not a relaxation of the output contract. For an exact-output task every pruning rule, decomposition, and subsolver must keep the overall algorithm complete. Exact-hybrid examples include admissible bounds inside complete branch-and-bound, exact component decomposition with exact coupling, and relaxation bounds used only for certified pruning.

When approximation is explicitly allowed, scored/approximate hybrids are often stronger and easier to validate than a monolithic heuristic. Common partitions:

```text
heuristic global order + exact local scheduling
heuristic destroy set + exact DP/matching/flow repair
exact solution on small components + heuristic coupling
candidate generation + exact selection/assignment
online estimator + exact shortest path/MST/action optimizer
```

For each exact subproblem quantify its size, solve cost, call frequency, cache reuse, and timeout behavior. Bound variable work so one hard local solve cannot consume the release budget. Verify that the local objective aligns with the global true score and that splicing preserves all cross-boundary constraints.

## 8. Feasibility decision record

Before implementation, the decision can be as short as:

```text
Candidate A: O(...), ... MB; rejected/accepted because ...
Candidate B: O(...), ... MB; rejected/accepted because ...
Chosen route: exact / exact hybrid / constructive / scored heuristic or hybrid
Critical assumptions: ...
Oracle/bound used for validation: ...
```

The goal is not ceremony. It prevents accidental heuristic use, impossible complexity claims, and untracked assumptions.
