# 🔧 Ladder Logic Reducer

**Boolean Table Minimizer and Ladder Logic Generator for PLC applications**

[![Version](https://img.shields.io/badge/version-V4.1-blue.svg)](#version)
[![HTML5](https://img.shields.io/badge/HTML5-single--file-orange.svg)](#architecture)
[![Offline](https://img.shields.io/badge/offline-supported-success.svg)](#architecture)
[![License](https://img.shields.io/badge/license-GPL--3.0-blue.svg)](LICENSE)

Ladder Logic Reducer is a single-file HTML5 application designed to capture or import Boolean condition tables, minimize each output independently, optimize the resulting logic for practical PLC Ladder implementation, detect reusable logic shared across outputs, verify functional equivalence, and export the results in several useful formats.

The application is focused on **industrial rule tables** rather than only conventional strict truth tables. Version **V4.1** uses the **Flexible Industrial** interpretation.

## 🌐 Live Application

**https://firerayo.github.io/LadderLogicReducer/**

---

## 🚀 Main Features

- Dynamic Boolean table editor.
- Up to **40 input variables**.
- Up to **20 output variables**.
- Up to **2,000 table rows**.
- Inputs support `0`, `1`, `X`, or blank.
- `X` and blank inputs are interpreted as **Don't Care**.
- Outputs support `0` or `1`.
- Configurable single-letter input and output mnemonics.
- Input and output mnemonics must be different.
- Direct paste/import from TSV, Excel, and LibreOffice tables.
- Per-output Boolean minimization.
- Exact minimization for manageable functions using:
  - Quine–McCluskey
  - Essential prime implicants
  - Petrick coverage
- Verified bounded heuristic minimization when exact search would exceed configured complexity limits.
- Boolean absorption and redundancy elimination.
- Multi-level expression factorization oriented toward Ladder implementation.
- **Shared-logic optimization across multiple outputs using auxiliary relays.**
- **Complete common-factor detection across AND/OR networks without a fixed five-contact factor limit.**
- **Automatic auxiliary-relay naming that avoids collisions with existing input and output signal names.**
- Ladder cost evaluation based on contacts, branches, expression depth, terms, and auxiliary-relay cost.
- Independent functional-equivalence verification.
- Minimized Boolean expressions.
- Simplified ASCII Ladder representation.
- Visual SVG Ladder representation.
- LDmicro-style `.ld` export.
- Plain-text `.txt` report.
- Current-table `.tsv` export.
- English, Spanish, and Italian user interface.
- No server or external runtime required.

---

## 📊 Application Limits

| Parameter | V4.1 Limit |
|---|---:|
| Maximum inputs | **40** |
| Maximum outputs | **20** |
| Maximum rows | **2,000** |
| Initial inputs | 4 |
| Initial outputs | 2 |
| Initial rows | 8 |
| Exact Quine–McCluskey input range | **Up to 12 inputs**, subject to complexity limits |
| Exact-state limit | 4,096 states |
| Prime-implicant limit | 2,400 |
| Petrick product limit | 50,000 |
| Maximum shared auxiliary relays configured | 12 |
| Shared-factor contact count | **No fixed five-contact limit** |
| Maximum tracked shared intersections | 6,000 |
| Maximum shared-intersection pair evaluations | 200,000 |

> **Important:** the application supports up to **40 inputs**, but this does **not** mean that exact global minimization is guaranteed for every 40-input problem. Exact Quine–McCluskey/Petrick minimization is attempted only for functions within the configured exact-search limits. Larger or combinatorially expensive problems use the verified industrial heuristic.

The limits used by the shared-logic optimizer are safeguards against pathological combinatorial growth. They are not equivalent to a fixed maximum number of contacts in an auxiliary factor.

---

## 🧠 Flexible Industrial Interpretation

Version V4.1 intentionally uses the **Flexible Industrial** interpretation.

For each output:

- only rows where that output is `1` define its active Boolean region;
- rows where that output is `0` are **not** treated as global OFF prohibitions;
- repeated input conditions may contribute active outputs and are combined logically rather than treated as strict truth-table contradictions;
- input `X` or blank means that the corresponding input does not matter for that rule.

This behavior is useful for industrial condition tables where the table acts as an **activation rule list** rather than as a complete enumeration of every possible Boolean state.

### Example

| I1 | I2 | I3 | O1 | O2 |
|---:|---:|---:|---:|---:|
| 1 | 0 | 1 | 1 | 0 |
| X | 1 | 1 | 0 | 1 |
| X | X | 0 | 0 | 1 |

For `O1`, only the first row contributes an ON condition.

For `O2`, the second and third rows contribute ON conditions.

The `0` values in the output columns do not define global forbidden regions for the other rules.

---

## 🔢 Accepted Input Values

### Inputs

| Value | Meaning |
|---|---|
| `1` | TRUE / active |
| `0` | FALSE / inactive |
| `X` | Don't Care |
| blank | Don't Care |

Internally, blank input cells are normalized to `X`.

### Outputs

| Value | Meaning |
|---|---|
| `1` | This row activates the output |
| `0` | This row does not activate the output |

Outputs accept only `0` or `1`.

---

## 🏷️ Signal Names and Mnemonics

The application can generate signal labels using configurable input and output mnemonics.

Examples:

```text
I1 I2 I3 I4
O1 O2
```

or:

```text
E1 E2 E3 E4
S1 S2
```

or other single-letter combinations.

The input and output mnemonic letters must be different.

When a table with headers is imported, the application can preserve the imported signal names when they are valid and unique.

### Auxiliary relay names

Shared expressions are represented by auxiliary relays using the `M` prefix by default:

```text
M1
M2
M3
...
```

V4.1 reserves all existing input and output names before assigning auxiliary relays.

For example, if a table already contains signals named:

```text
M1 M2 M3
```

the optimizer will not reuse those names. It automatically selects the next available auxiliary relay name.

This prevents accidental name collisions between physical/logical signals and generated internal memories.

---

## 📥 Table Import

Tables can be entered manually or pasted from spreadsheet software.

Supported workflow includes:

- TSV text
- Excel copy/paste
- LibreOffice Calc copy/paste
- tables with headers
- tables using the currently configured dimensions

Example:

```text
I1	I2	I3	O1	O2
1	0	1	1	0
X	1	1	0	1
X	X	0	0	1
```

The application validates row length, signal names, input values, and output values before analysis.

---

## ⚙️ Minimization Strategy

V4.1 uses two minimization paths.

### 1. Exact minimization

For functions with up to **12 inputs**, when the configured combinatorial limits are not exceeded, the application uses:

1. ON-region expansion into Boolean minterms.
2. Quine–McCluskey prime implicant generation.
3. Essential prime implicant identification.
4. Petrick coverage for unresolved minterms.
5. Ladder-oriented cost comparison between valid covers.
6. Redundancy removal.
7. Functional-equivalence verification.

The exact method is bounded by internal safeguards to prevent uncontrolled combinatorial growth.

### 2. Verified industrial heuristic

If the exact method is not applicable or exceeds its internal complexity limits, V4.1 switches to a bounded heuristic based on:

1. safe Boolean cube expansion;
2. valid cube combinations;
3. absorption and duplicate elimination;
4. candidate generation;
5. greedy coverage selection;
6. redundant-cover elimination;
7. independent equivalence verification.

The heuristic is intended to remain scalable while preserving the declared Flexible Industrial ON region.

---

## 🧩 Boolean Cubes

Each input rule is internally represented as a Boolean cube.

For example:

```text
1 X 0 1
```

represents:

```text
I1 · ¬I3 · I4
```

because `I2 = X` does not constrain the rule.

Cube operations are used for:

- duplicate removal;
- absorption;
- compatibility analysis;
- expansion;
- coverage;
- minimization.

---

## 🪜 Ladder-Oriented Factorization

After Boolean minimization, the application does not simply stop at a flat sum-of-products expression.

It builds and evaluates a multi-level Boolean expression oriented toward Ladder implementation.

The factorization stage can reduce repeated contacts by extracting common logical factors.

Example:

```text
O1 = ¬I10 · I2 + ¬I10 · I4 + ¬I10 · I1 · ¬I5
```

may be represented as:

```text
O1 = ¬I10 · (I2 + I4 + I1 · ¬I5)
```

This structure maps naturally to Ladder as:

- series conditions for AND;
- parallel branches for OR;
- normally closed contacts for negated literals.

---

## 🔗 Shared Logic and Auxiliary Relay Optimization

V4.1 adds a significantly improved optimization stage after individual output minimization.

The application analyzes the factorized expressions of all outputs and searches for logic that can be evaluated once and reused through an auxiliary relay.

### Why this matters in PLC Ladder

Consider three independently minimized outputs that all contain:

```text
¬I5 · ¬I6 · ¬I7 · ¬I8 · I10 · I11
```

Without shared optimization, those six contacts may be duplicated in every output rung.

V4.1 can create:

```text
M1 = ¬I5 · ¬I6 · ¬I7 · ¬I8 · I10 · I11
```

and then use `M1` in the affected outputs.

Conceptually:

```text
M1 = COMMON_CONDITION

O1 = M1 · CONDITION_O1
O2 = M1 · CONDITION_O2
O3 = M1 · CONDITION_O3
```

This can reduce:

- duplicated contacts;
- Ladder program size;
- visual complexity;
- repeated condition evaluation;
- maintenance effort.

### Complete common factors

Previous shared-factor logic was constrained by candidate subset enumeration and a fixed maximum factor size.

V4.1 removes that fixed factor-size restriction.

Instead of enumerating every possible subset up to a hard contact count, the optimizer:

1. collects composite AND/OR nodes from each output expression;
2. represents their child expressions as sets;
3. computes shared intersections between expressions from different outputs;
4. recursively closes those intersections so factors common to three or more outputs can be discovered;
5. evaluates each candidate using the Ladder cost model;
6. creates an auxiliary relay only when the total Ladder cost is reduced.

This allows practical common factors containing six, ten, or more contacts to be detected when they exist in the expression.

### Cost-controlled memory creation

The application does **not** create an auxiliary relay merely because a repeated expression exists.

For each candidate it compares:

```text
Cost before auxiliary relay
vs.
Cost after replacement
+ Cost of auxiliary rung
+ Auxiliary-relay penalty
```

The memory is accepted only when the calculated result is beneficial.

This avoids generating unnecessary internal bits that make the PLC program longer rather than shorter.

---

## 📐 Ladder Cost Model

V4.1 evaluates candidate factorizations and shared auxiliary relays using a Ladder-oriented cost model.

The configured cost model considers:

- contacts;
- parallel branches;
- expression depth;
- number of terms;
- auxiliary relay usage.

This means the preferred result is selected not only by Boolean-expression length, but also by its practical Ladder structure.

The shared-logic optimizer evaluates the **complete program cost**, including the new auxiliary rung and the reduced output rungs.

---

## ✅ Equivalence Verification

The application includes an independent equivalence-verification stage.

The verifier compares:

1. the Boolean region represented by the original active rules; and
2. the final minimized/factorized expression used to generate the Ladder result.

The verifier supports:

- partial assignments;
- ternary evaluation;
- recursive pruning;
- auxiliary-relay definitions;
- counterexample detection.

Generated auxiliary relays are included in equivalence verification, so the final output expressions are checked using the actual auxiliary definitions.

This verification is particularly important when the scalable heuristic path or shared-logic optimization is used.

---

## 🖥️ Generated Results

After analysis, V4.1 displays:

### Minimized Boolean Expressions

The final Boolean expression for every generated auxiliary relay and output.

Example:

```text
M1 = ¬I5 · ¬I6 · ¬I7 · ¬I8 · I10 · I11
O1 = I1 · ¬I2 · M1
O2 = (I2 + I3) · M1
```

Auxiliary definitions are displayed before the outputs that use them.

### Simplified ASCII Ladder

A text representation using series and parallel structures.

Example:

```text
// ===== M1 =====
SERIES
  [/I5]
  [/I6]
  [/I7]
  [/I8]
  [I10]
  [I11]
  COIL ( M1 )
```

### Visual Ladder

The same logical structure is rendered graphically in the browser as SVG Ladder rungs.

Generated auxiliary-relay rungs are displayed together with the final output rungs.

Input contacts are kept ordered according to the original input-variable order wherever the expression structure allows it.

### Consistency and I/O Mapping

The result panel reports:

- validation warnings;
- minimization method used for each output;
- equivalence-verification state;
- generated auxiliary relays;
- input/output mapping.

---

## 📤 Export Formats

### `.tsv`

Exports the current logic table.

Default filename:

```text
tabla_logica.tsv
```

### `.txt`

Exports an analysis report containing:

- application version;
- Flexible Industrial interpretation;
- minimization method used for each output;
- equivalence-verification status;
- Boolean expressions;
- generated auxiliary-relay expressions;
- ASCII Ladder.

Default filename:

```text
ladder_logic.txt
```

### `.ld`

Exports an LDmicro-style textual Ladder representation based on the same factorized logical structure used by the visual result.

Default filename:

```text
ladder_logic.ld
```

The generated LD file includes:

- LDmicro header;
- I/O list;
- generated input aliases;
- generated output aliases;
- generated auxiliary-relay rungs;
- output Ladder rungs;
- Ladder program structure;
- comments preserving the original signal names for traceability.

Auxiliary-relay definitions are emitted before output rungs that reference them.

> The `.ld` generator is implemented as an LDmicro-style textual exporter. If a specific LDmicro release requires a different textual convention, the exporter may need to be adapted to that release.

---

## 🧱 Architecture

Ladder Logic Reducer is intentionally self-contained.

```text
index.html
│
├── HTML5 interface
├── CSS
├── JavaScript application state
├── data normalization
├── table validation
├── Boolean cube engine
├── exact minimizer
├── scalable heuristic minimizer
├── Ladder cost calculator
├── expression factorizer
├── cross-output shared-logic optimizer
├── collision-safe auxiliary-relay allocator
├── equivalence verifier
├── visual Ladder renderer
├── ASCII Ladder renderer
├── LDmicro-style exporter
└── TXT / TSV exporters
```

### Technology

- HTML5
- CSS3
- Vanilla JavaScript
- SVG for Ladder visualization

### Runtime

No frameworks are required.

No npm packages are required.

No backend is required.

No external database is required.

The application can run directly in a modern browser from a local HTML file.

---

## 💻 Usage

### Online

Open:

**https://firerayo.github.io/LadderLogicReducer/**

### Offline

1. Download the HTML file.
2. Open it with a modern web browser.
3. Configure the number of inputs, outputs, and initial rows.
4. Enter or paste the logic table.
5. Click **Minimize and generate Ladder**.
6. Review the minimized Boolean expressions.
7. Review any generated auxiliary relays.
8. Review the visual or ASCII Ladder representation.
9. Export the required `.txt`, `.ld`, or `.tsv` file.

---

## 📝 Typical Workflow

```text
Logic / condition table
        │
        ▼
Normalize values
        │
        ▼
Validate table
        │
        ▼
Build ON region independently for each output
        │
        ▼
Boolean minimization
        │
        ├── Exact Quine–McCluskey + Petrick
        │
        └── Verified scalable heuristic
        │
        ▼
Per-output Ladder-oriented factorization
        │
        ▼
Cross-output shared-factor detection
        │
        ▼
Evaluate auxiliary-relay cost savings
        │
        ├── No saving → keep original expressions
        │
        └── Saving found → create collision-safe Mx relay
        │
        ▼
Functional-equivalence verification
        │
        ▼
Generate Ladder
        │
        ├── Boolean expressions
        ├── Auxiliary relay rungs
        ├── ASCII Ladder
        ├── SVG visual Ladder
        ├── TXT report
        └── LDmicro-style LD file
```

---

## ⚠️ Important Technical Notes

### Flexible Industrial is not Strict ON/OFF interpretation

A `0` in an output cell does not globally forbid that input region.

Only rows with output `1` define the ON region used for that output.

This distinction is intentional and is central to V4.1 behavior.

### Maximum inputs and exact minimization are different limits

The application supports **40 input columns**, but exact minimization is limited to manageable functions of up to **12 inputs** and is also constrained by internal combinatorial safeguards.

### Don't Care is an input condition

`X` and blank are accepted for inputs.

Outputs do not use `X`.

### Shared factors are not limited to five contacts

V4.1 does not impose the previous fixed five-element ceiling on cross-output shared factors.

The optimizer instead uses bounded set-intersection analysis and overall Ladder-cost comparison.

### Auxiliary relays are generated only when beneficial

A common expression does not automatically become a memory bit.

The optimizer creates a relay only when the complete Ladder-cost model predicts a net reduction.

### Auxiliary names are protected

Generated `M` relay names are checked against existing input and output labels before use.

### Large problems

Boolean minimization and cross-output optimization can be computationally expensive. A table within the UI limits can still represent a very large Boolean search space.

For this reason, V4.1 uses bounded exact, heuristic, intersection, and verification algorithms rather than attempting unlimited exhaustive computation.

---

## 🌍 Interface Languages

The application includes:

- English
- Spanish
- Italian

Imported technical signal names are preserved and are not translated.

The V4.1 technical notes in all three languages also explain that complete common factors may be extracted across outputs when doing so reduces total Ladder cost.

---

## 🧾 Version

### V4.1

Key characteristics of this version:

- Flexible Industrial interpretation only.
- Maximum 40 inputs.
- Maximum 20 outputs.
- Maximum 2,000 rows.
- Exact Quine–McCluskey + Petrick minimization for manageable functions up to 12 inputs.
- Verified heuristic path for larger or combinatorially expensive functions.
- Ladder-oriented multi-level factorization.
- **Improved cross-output shared-factor optimization.**
- **Complete AND/OR common-factor intersection detection.**
- **No fixed five-contact limit for shared auxiliary factors.**
- **Auxiliary relays are created only when they reduce total Ladder cost.**
- **Collision-safe automatic `M1`, `M2`, ... relay naming.**
- Independent equivalence verification including auxiliary definitions.
- Visual SVG Ladder generation.
- ASCII Ladder generation.
- TXT, TSV, and LDmicro-style LD export.
- English / Spanish / Italian interface.

### V4.0

Previous major version characteristics:

- Flexible Industrial interpretation.
- Per-output minimization.
- Exact and verified heuristic minimization paths.
- Ladder-oriented factorization.
- Initial shared auxiliary-relay optimizer.
- Shared-factor subset generation was bounded by a fixed maximum candidate factor size.

---

## 👤 Author

**Raymundo Ortiz**

---

## 📄 License

This repository is distributed under the **GNU General Public License v3.0 (GPL-3.0)**.

See the repository `LICENSE` file for the complete license text.

---

## 🔗 Links

### Use the application

https://firerayo.github.io/LadderLogicReducer/

### Source repository

https://github.com/FireRayo/LadderLogicReducer
