---
layout: page
title: open questions
permalink: /questions/
nav: true
nav_order: 1
last_updated: 2026-09-30
---

*Last updated: {{ page.last_updated | date: "%B %-d, %Y" }}*

This page is a collection of questions that I find important, curious, or simply interesting.

Some of them may eventually become research projects; others are problems that I would simply like to understand, explore, or solve someday.


___

<details markdown="1">
<summary><strong> Observing Real Memory Errors on a GPU Without ECC</strong></summary>

Many consumer GPUs do not use **ECC (Error-Correcting Code) memory**.

This means that, if a bit stored in memory changes unexpectedly, the hardware may not necessarily detect or correct that error before the data is used by a program.

What interests me is not artificially injecting errors, but trying to design an experiment in which **naturally occurring memory errors can actually be observed**.

### Question

> **Can I design a reproducible experiment that makes real bit errors observable on a GPU without ECC memory?**

The challenge is that these errors may be rare.

If I run a program once and obtain the expected result, that tells me very little.  
A useful experiment would therefore need to run for a long time, perform a very large number of memory operations, and continuously verify whether the data stored in GPU memory remains correct.

### Possible Experiment

One possible approach would be:

1. Allocate a large portion of the available GPU memory.
2. Fill that memory with values whose correct contents are known.
3. Repeatedly read, process, and verify those values.
4. Run the experiment for many iterations or for several hours.
5. Record every mismatch between the expected and observed values.
6. Repeat the complete experiment multiple times.

For example, the GPU memory could be filled with deterministic bit patterns such as:

```text
00000000000000000000000000000000
11111111111111111111111111111111
10101010101010101010101010101010
01010101010101010101010101010101
```

If a value changes unexpectedly, the experiment could record:

- the original value,
- the corrupted value,
- which bit changed,
- the memory location,
- the time at which the error occurred,
- and the total number of memory operations performed before the failure.

The important part would be distinguishing an actual memory corruption event from errors produced by the program itself, nondeterminism, driver problems, or other causes.

### Reproducibility

If an error is observed once, an interesting question is whether the experiment can make similar errors appear again.

For example:

- Does the same memory region fail repeatedly?
- Do errors become more common after the GPU has been under load for a long time?
- Does the error rate change with the amount of memory being used?
- Are some bit patterns more likely to reveal failures than others?
- Does repeatedly reading and writing memory make errors easier to observe?
- How long does the experiment need to run before a failure is observed?

Instead of asking only whether errors exist, I would like to estimate something closer to an **empirical error rate**.

For example:

$$
R =
\frac{
\text{number of detected corruptions}
}{
\text{number of memory operations}
}.
$$

Or perhaps:

$$
T =
\text{average computation time before observing an error}.
$$

This could provide an experimental way of understanding how likely silent memory corruption actually is on a particular GPU.

### Can Software Detect or Correct These Errors?

A second question is whether some of the protection normally provided by ECC could be implemented at the software level.

For example, a program could store additional information such as:

- checksums,
- parity information,
- duplicated values,
- redundant computations,
- or multiple copies of important data.

This raises another question:

> **How much reliability can software provide when the underlying GPU memory does not provide ECC?**

A software mechanism might be able to detect that something changed, but correcting the error could require additional redundancy.

For example, keeping two copies of the same value may reveal that the copies disagree, but it does not necessarily tell us which one is correct.

Keeping three copies could make majority voting possible, but would increase memory usage significantly.

### The Cost of Reliability

This leads to another question that interests me:

> **What is the computational and memory cost of implementing error detection or correction in software?**

There is likely a trade-off between reliability and performance.

A software protection mechanism could increase:

- memory consumption,
- memory bandwidth,
- execution time,
- energy consumption,
- and implementation complexity.

It would therefore be interesting to measure something like:

$$
\text{Reliability gain}
\quad \text{vs.} \quad
\text{performance overhead}.
$$

For example, an experiment could compare:

```text
Normal GPU computation

vs.

GPU computation + checksum verification

vs.

GPU computation + redundant copies

vs.

GPU computation + software-level error correction
```

and measure the additional execution time and memory required by each approach.

### Broader Question

The broader question I would like to explore is:

> **How observable are naturally occurring bit errors on a GPU without ECC, how reproducible are they, and how much would it cost to detect or correct them entirely in software?**


</details>

---

<details markdown="1">
<summary><strong> Measuring the Energy Consumption of Programs</strong></summary>

Programs are commonly evaluated using metrics such as execution time, memory consumption, or computational complexity.

However, another important resource is **energy**.

### Question

Can we build a program or tool that measures or estimates the energy consumed by another program while it is running?

Some things I would like to explore are:

- How accurately can software energy consumption be measured?
- Can energy consumption be associated with individual functions or parts of a program?
- What information can modern CPUs expose about their energy usage?
- How strongly are execution time, CPU usage, memory access, and energy consumption related?
- Can two algorithms with similar execution times have significantly different energy consumption?

The long-term idea would be to create a tool capable of **monitoring and comparing the energy efficiency of programs and algorithms**.

</details>

---

<details markdown="1">
<summary><strong> Learning Without Starting Over</strong></summary>

Neural networks are generally trained using a fixed dataset.

When new information becomes available, training often involves using old data again or retraining the model on a mixture of old and new examples.

### Question

Can a small neural network continuously learn new information **without reusing its previous training data and without changing its architecture**?

More specifically:

- Can the network acquire new knowledge while preserving previously learned information?
- How much new information can a fixed neural network incorporate?
- At what point does previously learned information begin to disappear?
- Can this capacity be measured experimentally?
- What properties determine how much information a fixed architecture can continue to absorb?

This question is related to **continual learning** and **catastrophic forgetting**, but I am particularly interested in what can be achieved under the constraints of a **fixed architecture and no access to previous training examples**.

</details>

---

<details markdown="1">
<summary><strong> The 24 Game</strong></summary>

The **24 Game** is an arithmetic puzzle in which four numbers are given, and the goal is to obtain **24** by using each number exactly once and combining them with addition, subtraction, multiplication, and division.

For example, given:

`3, 3, 8, 8`

one solution is:

$$
8 \div \left(3 - \frac{8}{3}\right) = 24
$$

The game can be seen as a particular case of the more general **Arithmetic Expression Construction** problem: given a collection of numbers and a target value, determine whether an arithmetic expression using those numbers can produce the target.

### Questions

<details markdown="1">
<summary>🔹 <strong>Can we detect unsolvable combinations without exhaustive search?</strong></summary>

Is there a mathematical criterion, invariant, or set of necessary conditions that allows us to determine that a combination is **unsolvable without enumerating every possible arithmetic expression**?

I am particularly interested in whether properties of the input numbers and the target can rule out a solution before performing a complete search.

</details>

<details markdown="1">
<summary>🔹 <strong>What happens when the target is not 24?</strong></summary>

Instead of fixing the target at 24, suppose the target is an arbitrary integer \(t\).

For each target:

- What proportion of possible combinations can produce \(t\)?
- What proportion cannot produce \(t\)?
- How does this proportion change as \(t\) changes?

</details>

<details markdown="1">
<summary>🔹 <strong>What happens for an arbitrary number of inputs?</strong></summary>

Suppose we choose \(n\) numbers from a finite domain

$$
\{1,\ldots,m\}.
$$

For a target \(t\), we can define the proportion of unsolvable combinations as

$$
U_{n,m}(t)
=
\frac{
\text{number of combinations that cannot produce }t
}{
\text{total number of combinations}
}.
$$

I would like to understand the behavior of this quantity as a function of:

- the target \(t\),
- the number of input values \(n\),
- and the input range \(m\).

Can this proportion be characterized or estimated mathematically without exhaustively evaluating every possible combination?

</details>

<details markdown="1">
<summary>🔹 <strong>Can we predict the percentage of unsolvable combinations?</strong></summary>

For example, if four numbers are selected from the values \(1,\ldots,13\), allowing repetitions and ignoring order, the total number of combinations is

$$
\binom{13+4-1}{4}
=
\binom{16}{4}
=
1820.
$$

For the classical target \(24\), 458 of these combinations are unsolvable, approximately **25.16%**.

This motivates the broader question:

> **For an arbitrary target \(t\), can we understand or predict the proportion of unsolvable combinations without testing every combination and every possible expression?**

</details>

#### Related problem

A general version of this problem has been studied under the name **Arithmetic Expression Construction**.

**Reference**

Alcock, L., Asif, S., Bosboom, J., et al. (2020).  
*Arithmetic Expression Construction*.  
31st International Symposium on Algorithms and Computation (ISAAC 2020).

[DOI: 10.4230/LIPIcs.ISAAC.2020.12](https://doi.org/10.4230/LIPIcs.ISAAC.2020.12)

</details>

---

<details markdown="1">
<summary><strong> Squadro and Winning Strategies</strong></summary>

[Squadro](https://boardgamegeek.com/boardgame/245222/squadro) is a two-player abstract strategy game.

I am interested in studying it as a computational game: given a particular state of the board, can we determine whether one of the players has a forced winning strategy?

### Questions

<details markdown="1">
<summary>🔹 <strong>Can we determine a winning strategy from an arbitrary position?</strong></summary>

Given a particular Squadro position, can we determine whether the current player has a forced win?

If so, can we also determine the sequence of moves required to guarantee that win?

</details>

<details markdown="1">
<summary>🔹 <strong>How far from the end can we compute the game exactly?</strong></summary>

Positions close to the end of the game may be small enough to solve by exploring all possible future moves.

But as we move backwards toward the initial position, the number of possible states grows.

I would like to know:

> **How far from the end of the game can we still compute an exact winning strategy?**

One possible approach would be to start from terminal positions and work backwards, classifying states as **winning** or **losing**.

The interesting part is identifying the point at which exact computation becomes impractical.

</details>

</details>

---
