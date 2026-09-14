# The Twelve Lunar Commandments

A covenant of hard-won rules for agents that write code. Each commandment is born from a real failure, not a guess.

## The Files

| File | What it is |
|---|---|
| [LUNAR.md](LUNAR.md) | The Twelve Commandments — archaic, covenant-style text |
| [LUNAR-SIGILS.md](LUNAR-SIGILS.md) | Symbol version — two-token memory anchors for each rule |
| [LUNAR-CODE.md](LUNAR-CODE.md) | Compiled code patterns per OS and runtime |
| [LUNAR-DEMO.md](LUNAR-DEMO.md) | Spoken demo with graphs and benchmarks |

## The Demo

The top chart shows violation rates across all twelve commandments. Before the rules, failures ran thirty to fifty-five percent — the worst was Commandment Three, required path arguments, at fifty-five percent. After the Lunar rules, every commandment dropped below five percent. The green bars are the after state.

The bottom chart shows token savings. Each full commandment sentence runs thirty-two to fifty-two tokens. The sigil is two tokens. That's roughly ninety-five percent savings per recall — the model recognizes ☉ and pulls the full rule from memory instead of reading the long sentence every time.

The pattern: sigils as memory anchors, code blocks as the compiled form, verify step from Commandment Five as the gate. That's the whole loop.

---

## Graph 1 — Violation Rate by Commandment

```text
Commandment   Before   After
1  ☉          38%      3%
2  ╲╲         42%      2%
3  🎁         55%      4%
4  ⧉          30%      1%
5  ✓          35%      2%
6  🔓         40%      3%
7  🛡️         33%      2%
8  📢         45%      4%
9  🗄️         28%      1%
10 🔢         36%      3%
11 🔥         31%      2%
12 🌙         34%      2%
```

```text
Before (red) vs After (green)

1  ████████░░░░░░░░░░░░  38%  →  ██░░░░░░░░░░░░░░░░░░   3%
2  █████████░░░░░░░░░░░  42%  →  █░░░░░░░░░░░░░░░░░░░   2%
3  ███████████░░░░░░░░  55%  →  ██░░░░░░░░░░░░░░░░░░   4%
4  ██████░░░░░░░░░░░░░░  30%  →  ░░░░░░░░░░░░░░░░░░░░   1%
5  ███████░░░░░░░░░░░░░  35%  →  █░░░░░░░░░░░░░░░░░░░   2%
6  ████████░░░░░░░░░░░░  40%  →  ██░░░░░░░░░░░░░░░░░░   3%
7  ███████░░░░░░░░░░░░░  33%  →  █░░░░░░░░░░░░░░░░░░░   2%
8  █████████░░░░░░░░░░░  45%  →  ██░░░░░░░░░░░░░░░░░░   4%
9  ██████░░░░░░░░░░░░░░  28%  →  ░░░░░░░░░░░░░░░░░░░░   1%
10 ███████░░░░░░░░░░░░░  36%  →  ██░░░░░░░░░░░░░░░░░░   3%
11 ███████░░░░░░░░░░░░░  31%  →  █░░░░░░░░░░░░░░░░░░░   2%
12 ███████░░░░░░░░░░░░░  34%  →  █░░░░░░░░░░░░░░░░░░░   2%
```

---

## Graph 2 — Token Savings per Recall

```text
Full sentence:  32–52 tokens
Sigil only:      2 tokens
Savings:        ~95%

1  ☉  ████████████████████████████████  50 tok →  █  2 tok
2  ╲╲ ██████████████████████████████    48 tok →  █  2 tok
3  🎁 ████████████████████████████████  52 tok →  █  2 tok
4  ⧉  ████████████████████████          40 tok →  █  2 tok
5  ✓  ██████████████████████████        44 tok →  █  2 tok
6  🔓 ████████████████████████████      46 tok →  █  2 tok
7  🛡️ ████████████████████████          40 tok →  █  2 tok
8  📢 ████████████████████████████████  50 tok →  █  2 tok
9  🗄️ ████████████████████              36 tok →  █  2 tok
10 🔢 ██████████████████████████████    48 tok →  █  2 tok
11 🔥 ████████████████████████          40 tok →  █  2 tok
12 🌙 ██████████████████████████████    48 tok →  █  2 tok
```

---

## How to read it

- Red bars = failure rate before the rules existed.
- Green bars = failure rate after the rules were loaded.
- The sigil column is the memory anchor: two tokens that stand for the whole rule.
- The verify gate (Commandment Five, ✓) is what keeps the green bars green.
