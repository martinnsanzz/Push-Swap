<h3 align="center">
  <em>Because Swap_push doesn’t feel as natural</em>
</h3>

## ⚠️ Disclaimer

- **Full Portfolio:** This repository focuses on this specific project. You can find my entire
42 curriculum 👉 [here](https://github.com/martinnsanzz/42-Curriculum).
- **Subject Rules:** I strictly follow the rules regarding 42 subjects; I cannot share the PDFs,
but I explain the concepts in this README.
- **Archive State:** The code is preserved exactly as it was during evaluation (graded state).
I do not update it, so you can see my progress and mistakes from that time.
- **Academic Integrity:** I encourage you to try the project yourself first. Use this repo only
as a reference, not for copy-pasting. Be patient, you will succeed.
- **Group Project:** This project was done in a team composed by [Daniel](https://github.com/Danitell0)

## 📂 Description
The goal of this project is to sort a stack of integers using two stacks (`a` and `b`) with a
limited set of instructions, while minimizing the total number of operations used. It serves as
a deep dive into **algorithmic complexity** and **data structures**

Unlike standard sorting problems where you can swap any values, here we are constrained by the
stack limited moves. We can only manipulate the top of the stacks, push elements between them,
or rotate the stacks. The challenge isn't just to sort, but to sort with the **minimum number of
operations** possible.

**📊 The Challenge:**

We have to produce a program that takes a set of flags and a list of random numbers as arguments
and outputs the series of instructions (`sa`, `pb`, `ra`, `rra`...) required to sort them. The
efficiency is graded based on the number of moves:

* **100 numbers:** Target is less than 1500 moves.
* **500 numbers:** Target is less than 8000 moves.

**Available Instructions:**
- **sa** / **sb** / **ss**	: swap stack a, b or both.
- **pa** / **pb**		: push the first int into the other stack.
- **ra** / **rb** / **rr**	: rotate stack a, b or both (the first one become the last, other move by one upwards).
- **rra** / **rrb** / **rrr** : rotate stack a, b or both (the last one become the first, other move by one downards)

**➕ Bonus:**
For the bonus of this project a checker program was created. This program takes as argument a 
list of integer. It then waits and reads from standard input the operations needed followed by a
new line character to sort the list provided. Once all instructions are read the program will
display `OK` on succeed or `KO` on failure to solve the list with the operations provided.

You can use this program to check the output of `push_swap`.

**📦 Library Integration:**
We are allowed to use our `libft` (especially linked lists or standard helper functions).
However, the core logic relies entirely on the custom sorting algorithm we design.

**🏆 Goal:**
Sort the integers in ascending order in Stack A using the lowest possible instruction count.

## 🔷 Usage

### Clone repository
To use this project you first need to clone the repository in your directory.

```bash
git clone git@github.com:martinnsanzz/Push-Swap.git
```

### Run make
To compile this projet you need to run `make` in the terminal to create`push_swap` program or
`make bonus` to create `checker` program.

```bash
make
make bonus
```

### Using push_swap
In order to run the program you need to pass an optional set of flags and a list of numbers to be sorted.

**🏁 Allowed flags:**

Alghoritm selection:
* `--simple` : selection sort, O(n²)
* `--medium` : chunk-based, O(n√n)
* `--complex` : radix sort, O(n log n)

> **Note:** Using this flags will force to select an algorithm no matter the disorder of the list.
> If no flag is provided an algorithm will be selected based on the disorder of the list:
> * disorder `< 0.2` → **Simple** (selection sort, O(n²))
> * disorder `0.2–0.5` → **Medium** (chunk-based, O(n√n))
> * disorder `> 0.5` → **Complex** (radix sort, O(n log n))

Bench mode:
* `--bench`: Activates bench mark

If this flag is written statistic of the algorithm will be shown at the end.

- Example:

```txt
[bench] disorder: 56.32%
[bench] strategy: Simple / O(n^2)
[bench] total_ops: 1356
[bench] sa:  0 sb: 0 ss: 0 pa: 100 pb: 100
[bench] ra: 605 rb: 0 rr: 0 rra: 551 rrb: 0 rrr: 0
```

>Note: A shell script is provided to run the program with a random number list generator for easier use.

```bash
./run.sh --bench --medium 100 # Generate 100 random number using medium strat
./run.sh --bench --complex 500 # Generate 500 random number using complex strat
./run.sh --bench --simple 50 # Generate 50 random number using simple strat
./run.sh --bench 100  # Generate 100 random number and strat will be selected based on disorder
shuf -i 0-9999 -n 500 > args.txt && ./push_swap --bench $(cat args.txt) 1> actions.txt # Without using the shell script
```

For the `checker` usage is recommend to export the operations to a text file `1> actions.txt`
for easier use.

```bash
shuf -i 0-9999 -n 500 > args.txt && ./push_swap --bench $(cat args.txt) 1> actions.txt
./checker (cat args.txt) # Read list from args.txt file
```

## 📒 Tasks Division

| Module | Martin | Daniel |
|---|---|---|
| Input validation | ✅ | |
| Linked list creation | ✅ | |
| Stack operations (sa, sb, pa, pb, ra, rb, rr, rra, rrb, rrr) | | ✅ |
| Disorder metric | | ✅ |
| Simple strategy (selection sort) | | ✅ |
| Adaptive strategy selection | | ✅ |
| Medium strategy (chunk-based) | ✅ | |
| Complex strategy (radix sort) | ✅ | |
| Bench mode | ✅ | |
| Bonus | ✅ | ✅ |

## 🤖 Algorithm and Data Structures

**Data structure — Linked list**

Both stacks are implemented as singly linked lists (`t_list`) rather than fixed-size arrays with
an index. Each node holds a heap-allocated `int` as its content. This choice means stack
operations (push, rotate, reverse rotate) are pointer manipulations rather than index
arithmetic, which maps naturally to the conceptual model of a stack and avoids any hardcoded
size limit.

### Input normalization

*Implemented by Martin.*

Before any sorting strategy runs, the integer array is normalized to contiguous 
ranks in `[0, n-1]`. For each element, its rank equals the count of elements strictly
smaller than it. This lets all three strategies operate on small non-negative integers
regardless of the original input range, which is especially important for the radix sort.

### Adaptive strategy selection

*Implemented by Daniel.*

In adaptive mode (default), the disorder of the input is computed as the inversion ratio: the number of pairs `(i, j)` where `i` appears before `j` but holds a greater value, divided by the total number of pairs. This produces a float in `[0.0, 1.0]`.

- disorder `< 0.2` → **Simple** (selection sort, O(n²))
- disorder `0.2–0.5` → **Medium** (chunk-based, O(n√n))
- disorder `> 0.5` → **Complex** (radix sort, O(n log n))

The idea is that nearly-sorted inputs should not pay the overhead of a full radix sort, while heavily disordered inputs should not be handled by a quadratic algorithm.

### Simple strategy — Selection sort (O(n²))

*Implemented by Daniel.*

At each iteration, the minimum value in `stack_a` is located by index. The cheaper rotation
direction (forward `ra` or reverse `rra`) brings it to the top, then it is pushed to `stack_b`.
Once `stack_a` is empty, all elements are pushed back to `stack_a` in order. This is correct but
scales poorly; it is only used on nearly-sorted inputs.

### Medium strategy — Chunk-based sort (O(n√n))

*Implemented by Martin.*

The stack size is divided into `⌈√n⌉` chunks of equal size (ceiling division for non perfect
square roots). Each chunk covers a contiguous range of normalized ranks. The algorithm iterates
chunk by chunk: for every element in `stack_a` belonging to the current chunk, the cheapest
rotation brings it to the top and it is pushed to `stack_b`. Once all chunks are processed,
`stack_b` is drained back into `stack_a` by repeatedly locating the current maximum, rotating it
to the top, pushing it to `stack_a`, and reverse-rotating to restore order. The result is
`stack_a` sorted in ascending order.

### Complex strategy — Radix sort (O(n log n))

*Implemented by Martin.*

The algorithm performs one pass per bit position, up to `⌊log₂(n-1)⌋ + 1` passes. On each pass,
every element in `stack_a` is inspected at bit position `i`: elements with that bit unset are
pushed to `stack_b`, elements with it set are rotated to the bottom via `ra`. After the pass,
all of `stack_b` is pushed back to `stack_a`. Correctness depends entirely on values being
normalized to `[0, n-1]` before entry.

### Bench mode

*Implemented by Martin.*
When `--bench` is passed, a summary is written to stderr: the disorder score as a percentage,
the strategy that was selected and its time complexity class, the total operation count, and a
per-operation breakdown (sa, sb, ss, pa, pb, ra, rb, rr, rra, rrb, rrr).

## 📖 Resources

- [Medium - Explanation of Push_Swap](https://medium.com/@jamierobertdawson/push-swap-the-least-amount-of-moves-with-two-stacks-d1e76a71789a)
- [Wikipedia - Big O notation](https://en.wikipedia.org/wiki/Big_O_notation)
- [YT - 10 Sorting Alghorithms Easily Explain](https://www.youtube.com/watch?v=rbbTd-gkajw)
- [YT - Alghorithms explained for begginers](https://www.youtube.com/watch?v=JJkWemM03Lg)
- [YT - CS50 Alghorithms](https://www.youtube.com/watch?v=iCx3zwK8Ms8)
- [Comparison Sorting Alghorithms](https://www.cs.usfca.edu/~galles/visualization/ComparisonSort.html)
- [Sort Visualizer](https://sortvisualizer.com/)
- [Push Swap Visualizer](https://saadloukili.github.io/Push-Swap-Visualizer/index.html)
- [N log N alghorithms](https://www.educative.io/answers/nlogn-sorting-algorithms)
- [GeeksforGeeks - Radix Sort](https://www.geeksforgeeks.org/dsa/radix-sort/)
- [YT - Bitwise Operators](https://www.youtube.com/watch?v=igIjGxF2J-w)

**Au usage:**

Claude by Anthropic was used in this project for the following:
- **As a teacher:** Explaining the time complexity (Big O) of the three sorting algorithms and how to reason about algorithmic efficiency.
- **Visualizing stack operations:** Helping trace and understand stack state during operation sequences, making it easier to debug and reason about algorithm correctness.
- **Rewriting comments:** Improving clarity and precision of function-level documentation.
- **Testing:** Discussing edge cases and verifying expected behavior of the sorting strategies.

AI was NOT used to write or generate any code. All function bodies were written by Martin and Daniel.
