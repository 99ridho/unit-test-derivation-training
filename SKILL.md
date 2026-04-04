---
name: unit-test-derivation
description: >
  Automates the 5-stage Test-First Protocol for deriving coverage-aware unit test
  scenarios from use case specifications, as defined in Pratama et al. (JNTETI, 2026).
  Use this skill whenever a user provides a case study narrative, use case description,
  or any system feature description and wants to derive unit tests systematically.
  Also trigger when a user mentions: "derive unit tests", "basis path testing",
  "CFG", "cyclomatic complexity", "test scenario from use case", "protocol derivasi",
  "skenario pengujian dari use case", or asks to run any stage of the protocol
  (use case spec, sequence diagram, pseudocode, control flow, AAA test cases).
  This skill enforces strict one-to-one traceability from requirements to test cases
  and works for any domain, condition structure, and programming language context.
---

# Unit Test Derivation Protocol

## Overview

This skill implements the 5-stage Test-First Protocol from:

> M. R. K. Pratama, D. Utama, R. Fahmi, "Test-First Protocol for Deriving Unit Tests from Use Case Specifications," _JNTETI_, vol. 15, no. 1, pp. 72–80, Feb. 2026.  
> DOI: [10.22146/jnteti.v15i1.24073](https://doi.org/10.22146/jnteti.v15i1.24073)  
> Link: [https://journal.ugm.ac.id/v3/JNTETI/article/view/24073](https://journal.ugm.ac.id/v3/JNTETI/article/view/24073)

The protocol transforms a plain-language case study narrative (Stage 0) into
coverage-aware, fully traceable unit test scenarios (Stage 5) through five
sequential artifact-producing stages.

**Repository reference**: https://github.com/99ridho/unit-test-derivation-training

---

## Input

The user provides a **case study narrative** — a plain-language description of
a system feature in any language (Indonesian or English). It must describe:

- The actor(s) involved
- The conditional checks or validations the system performs
- The success outcome (happy path)
- The failure or alternate outcome for each check

If any of these elements are missing, ask the user to supply them before
proceeding. Do not infer or fabricate conditions not stated in the narrative.

---

## Execution Rules

1. **Execute stages sequentially.** Do not skip, merge, or reorder stages.
2. **Label each stage explicitly** before outputting its artifact.
3. **Do not proceed to the next stage** if the current stage output contains a
   broken traceability link. Flag it and ask the user to confirm or correct.
4. **Language**: Write narrative artifact content in **Indonesian** by default
   unless the user specifies otherwise. Class names, method names, and field
   names always use **English** (camelCase / PascalCase).
5. After Stage 5, always output a **Traceability Summary Table**.
6. **Adapt to structure, not assumptions.** Each case narrative may have a
   different condition topology. Run the **Structure Classification** at Stage 0
   and carry that classification forward into Stage 2 and Stage 3 decisions.

---

## Stage 0 — Case Study Narrative (Input Validation + Structure Classification)

**This is the user's input, not a generated artifact.**

### Step 0A — Completeness Check

Verify the narrative contains:

- [ ] At least one actor
- [ ] At least one conditional check with a defined outcome
- [ ] A defined happy-path outcome
- [ ] At least one alternate-flow outcome per check

If any item is missing, ask the user to supply it before proceeding.

### Step 0B — Structure Classification

Analyze the narrative's condition topology and classify it as one of:

| Type                              | Description                                                                                                   | Example signal phrases                                                           |
| --------------------------------- | ------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| **A — Flat Sequential Guards**    | Conditions are checked one-by-one in order; each failure exits immediately without evaluating the rest        | "secara berurutan", "pertama... kedua... ketiga", "if not X, stop"               |
| **B — Branching / Decision Tree** | Conditions lead to different sub-flows rather than early exits; multiple paths reach different success states | "jika A maka alur 1, jika B maka alur 2", "depending on", "based on the type of" |
| **C — Compound / Composite**      | Some conditions are checked together (AND/OR logic) before a single decision is made                          | "jika A dan B", "either X or Y must hold", "combination of"                      |
| **D — Mixed**                     | The narrative has a flat sequential section plus one or more branching or compound sub-sections               | Narrative contains both sequential guards and a decision fork                    |

**State the classification explicitly** before continuing to Stage 1:

```
[STRUCTURE] This narrative is classified as Type [A/B/C/D]: [brief justification]
```

The classification drives the PlantUML construct choice in Stage 2 and the
pseudocode pattern in Stage 3. See each stage for type-specific rules.

---

## Stage 1 — Use Case Specification

**Output file**: `1-use-case-description.md`

**Goal**: Formalize the narrative into a structured use case description with
a normal flow and explicit alternate flows.

### Output Format

```markdown
# Use Case Description

## Normal flow

1. [Actor] melakukan [action]
2. Sistem memproses [input]
3. Sistem melakukan [pengecekan / keputusan sesuai struktur narasi]
4. Sistem [success action]
5. [Actor] mendapatkan [confirmation]

## Alternate flow

1. Jika [condition A fails / branch condition]: [system response + exact message]
2. Jika [condition B fails / branch condition]: [system response + exact message]
   ...
```

### Rules

- Each alternate flow item maps to **exactly one** distinct condition or branch
  from the narrative. Do not merge two conditions into one item.
- For **Type A (flat sequential)**: alternate flows are ordered by check sequence.
- For **Type B (branching)**: alternate flows represent each branch outcome,
  including non-error branches that lead to different success states.
- For **Type C (compound)**: one alternate flow item may describe a compound
  condition (e.g., "Jika A dan B tidak terpenuhi"), but the compound must be
  stated explicitly, not split arbitrarily.
- For **Type D (mixed)**: apply Type A rules to the sequential section and
  Type B rules to the branching section; label each group clearly.
- Steps are written from the **system's perspective**: "Sistem memeriksa...",
  "Sistem memutuskan...", "Sistem menambahkan..."
- The exact wording of messages in alternate flows **must be preserved verbatim**
  in Stage 5 Assert blocks. Lock them here.

---

## Stage 2 — Sequence Diagram

**Output file**: `2-sequence-diagram.puml`

**Goal**: Model behavioral interaction between actors and system components
as a PlantUML sequence diagram.

### Output Format

Valid `.puml` PlantUML syntax using `@startuml` / `@enduml`.

### Participant Identification (Generic)

Identify and declare participants from the narrative, regardless of domain:

- One **actor** — the initiating human or external system
- One **UI/boundary participant** — the page, controller, or API endpoint
  receiving the actor's request (if present; omit if the narrative describes
  a pure service-to-service interaction)
- One **primary service class** — the class owning the method under test
- One or more **domain entity participants** — objects the service queries
  or mutates

Name all participants using English PascalCase. Alias them to short identifiers.

### PlantUML Construct Selection by Structure Type

Use the structure classification from Stage 0 to select the correct construct:

| Structure Type                    | PlantUML Construct                                             | When to use                                                                                    |
| --------------------------------- | -------------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| **A — Flat Sequential Guards**    | `break` block                                                  | Each condition is an early exit; control never returns to the main flow after a guard triggers |
| **B — Branching / Decision Tree** | `alt / else / end` block                                       | Conditions lead to distinct sub-flows; multiple branches may succeed differently               |
| **C — Compound / Composite**      | `alt / else / end` with compound condition label               | The condition label describes AND/OR logic explicitly                                          |
| **D — Mixed**                     | `break` for sequential guards + `alt/else` for branch sections | Apply each construct to its respective sub-section                                             |

**Do not default to `break` for all cases.** Use `break` only when the semantic
meaning is "abort/exit early." Use `alt/else` when the semantic meaning is
"choose between distinct flows."

### General Rules

- Each alternate flow from Stage 1 maps to **exactly one** PlantUML block
  (`break` or `alt` branch), in the same order.
- Use `activate` / `deactivate` for service and entity lifelines.
- Self-calls on a service participant (internal computations) are shown as
  `service -> service : methodName(args)`.
- The happy path ends with a success message or return value at the end of
  the main flow.
- Use `skinparam` or `!theme` for styling only; avoid inline color overrides.
- The diagram must be self-contained and renderable without external includes.

### Reference Patterns

**Type A — Flat Sequential Guards (using `break`)**:

```plantuml
service -> service : checkConditionA(input)
break condition_A_fails
  service -->> boundary : message = "[error A]"
end

service -> entity : queryForB()
entity -->> service : resultB
break condition_B_fails
  service -->> boundary : message = "[error B]"
end

' ... happy path continues
service -->> boundary : message = "[success]"
```

**Type B — Branching (using `alt/else`)**:

```plantuml
service -> service : evaluateCondition(input)
alt branch_A_condition
  service -> entity : doPathA()
  entity -->> service : resultA
  service -->> boundary : message = "[outcome A]"
else branch_B_condition
  service -> entity : doPathB()
  entity -->> service : resultB
  service -->> boundary : message = "[outcome B]"
end
```

**Type C — Compound (using `alt/else` with compound label)**:

```plantuml
alt conditionA AND conditionB
  service -->> boundary : message = "[compound failure message]"
else
  service -> entity : proceed()
  service -->> boundary : message = "[success]"
end
```

---

## Stage 3 — Algorithm / Pseudocode

**Output file**: `3-algorithm.txt`

**Goal**: Extract the internal logic of the primary service method as structured
pseudocode, directly derived from the sequence diagram.

### Output Format

```
class [ServiceClassName] {
  function [methodName]([params]) {

    // [check or query]
    [variable] = [expression]
    [conditional block — see pattern by type below]

    // ... repeat per condition

    [success action]
    return "[success message from Stage 1]"
  }
}
```

### Pseudocode Pattern by Structure Type

**Type A — Flat Sequential Guards** (guard clause / early return pattern):

```
isConditionA = checkConditionA(input)
if !isConditionA {
  return "[error message A]"
}

isConditionB = checkConditionB(input)
if !isConditionB {
  return "[error message B]"
}

// all guards passed
doSuccessAction()
return "[success message]"
```

**Type B — Branching / Decision Tree** (if/else if pattern):

```
branchKey = evaluateCondition(input)

if branchKey == A {
  doPathA()
  return "[outcome A message]"
} else if branchKey == B {
  doPathB()
  return "[outcome B message]"
} else {
  return "[default/fallback message]"
}
```

**Type C — Compound / Composite**:

```
condA = checkA(input)
condB = checkB(input)

if condA AND condB {
  return "[compound failure message]"
}

doSuccessAction()
return "[success message]"
```

**Type D — Mixed**: combine the patterns above in the order they appear in
the narrative. Label each section with a comment.

### General Rules

- Method name must be **consistent** with Stage 2.
- Use language-neutral pseudocode — no Python, Java, Swift, or other
  language-specific syntax.
- Return messages must **exactly match** the wording locked in Stage 1.
- Variable names must be **semantically meaningful** and derived from the
  domain (e.g., `isWithinPeriod`, `prerequisiteFound`, `limitExceeded`,
  `paymentAuthorized`, `stockSufficient`).
- Guard clause order (Type A) must match Stage 1 alternate flow order exactly.
- For Type B/C/D, the structure of the pseudocode must mirror the Stage 2
  PlantUML block structure exactly.

---

## Stage 4 — Control Flow Graph + Cyclomatic Complexity

**Output**: Inline textual CFG description + CCN calculation + basis path
enumeration.

**Goal**: Construct the CFG from Stage 3 pseudocode, calculate McCabe's
cyclomatic complexity, and enumerate all independent basis paths.

### CFG Node and Edge Rules by Structure Type

**Type A — Flat Sequential Guards**:

- Each `if !condition { return }` = one **decision node** with two edges:
  - TRUE edge → early terminal node (exit)
  - FALSE edge → next decision node or success node
- The happy path traverses all FALSE edges to the success terminal.

**Type B — Branching**:

- The `if / else if / else` structure = one **multi-branch decision node**
  (or a chain of binary decisions, one per branch condition).
- Each branch leads to its own terminal node.
- There is no single "happy path" — all branch terminals are valid outcomes.

**Type C — Compound**:

- Treat AND/OR as a single decision node with two outcomes (compound true / compound false).
- If the compound condition uses short-circuit evaluation, model each sub-condition
  as a separate decision node.

**Type D — Mixed**:

- Apply Type A rules to the sequential guard section.
- Apply Type B/C rules to the branching section.
- Count decision nodes across the entire method.

### CCN Calculation

```
CCN = number_of_binary_decision_nodes + 1
```

Equivalently: `CCN = E − N + 2P`
where E = edges, N = nodes, P = connected components (always 1 for a single method).

For **Type B** with a single multi-branch decision node having K branches:
model as (K−1) binary decision nodes, giving `CCN = (K−1) + 1 = K`.

### Basis Path Enumeration

List **exactly CCN paths**. Label them Path 1, Path 2, ..., Path N.

For each path, state:

- Which decision node(s) are evaluated
- Which edge is taken at each decision (TRUE/FALSE, or branch label)
- The terminal return message

**Generic example format** (adapt labels to the actual domain):

```
Path 1: Decision 1 = TRUE → exit ("[message from alternate flow 1]")
Path 2: Decision 1 = FALSE, Decision 2 = TRUE → exit ("[message from alternate flow 2]")
...
Path N: Decision 1..N-1 all FALSE → success ("[success message]")
```

For **Type B** (branching):

```
Path 1: Condition = Branch A → "[outcome A message]"
Path 2: Condition = Branch B → "[outcome B message]"
...
Path K: Condition = Branch K (default) → "[fallback message]"
```

### Validation

- Total basis paths listed = CCN value. If not, recount decision nodes.
- Each basis path must be **structurally distinct** — they must differ in at
  least one edge traversal.
- No path may be a subset of another path.

---

## Stage 5 — Unit Test Scenarios

**Output file**: `5-test-scenario.md`

**Goal**: Derive one AAA-structured unit test scenario per basis path from Stage 4.

### Output Format

````markdown
# Test Suite : [ServiceClassName].[methodName]

## Scenario N: [Short descriptive name]

### Arrange

\```
[System/service configuration needed for this scenario]
[Input object(s) with all relevant field values]
[Dependency or collaborator state needed to trigger or bypass this condition]
\```

### Act

\```
[Single method call on the service under test]
\```

### Assert

\```
[expected output] = "[exact message or return value from Stage 1]"
\```
````

### Rules

- **Total scenarios = CCN.** One scenario per basis path. No more, no less.
- **Act** = single method call. Zero setup logic inside Act.
- **Assert** must **exactly match** the message or return value locked in Stage 1.
  Do not paraphrase, abbreviate, or rephrase.
- Use a **consistent object model** across all scenarios: same class names,
  same field names, same value types throughout the test suite.
- Scenario naming: use the condition or branch being exercised as the name.
  The happy path (or each valid branch terminal for Type B) is named descriptively,
  not generically as "Test N".

### Arrange Construction by Structure Type

**Type A — Flat Sequential Guards**

For Scenario N (testing guard clause N):

- Guard clauses 1 through N−1 must evaluate to **FALSE** → supply valid data
  that satisfies each earlier check.
- Guard clause N must evaluate to **TRUE** → supply data that triggers this failure.
- Guard clauses after N are irrelevant → may be omitted from Arrange.

For the happy path scenario:

- All guard clauses must evaluate to **FALSE** → supply valid data for every check.

**Type B — Branching**

For each branch scenario:

- Supply input data that directs the flow into the specific branch under test.
- Supply any collaborator state required for that branch's sub-flow.
- Other branches are not relevant to this scenario's Arrange.

**Type C — Compound**

For the compound-failure scenario:

- Supply data such that **all sub-conditions in the compound** evaluate to
  trigger the compound outcome (TRUE for AND, or any TRUE for OR — match the
  compound logic from Stage 3).

For the success scenario:

- Supply data such that the compound condition evaluates to its passing state.

**Type D — Mixed**:
Apply Type A guidance to guard-clause scenarios and Type B/C guidance to
branch scenarios within the same test suite.

### Arrange Content Guidelines (Domain-Agnostic)

The Arrange block should contain any subset of the following that is relevant
to the scenario under test:

- **Service/system configuration**: any stateful properties of the service
  itself that affect the outcome (time ranges, thresholds, feature flags,
  quotas, external state, rate limits)
- **Primary input object**: the main input to the method under test, with
  all fields needed to exercise the condition
- **Collaborator/dependency state**: return values or state of entities,
  repositories, or external services the method queries
- **Subject/actor state**: the state of the subject entity (user, account,
  resource, order) that the method inspects

Do not include fields or state that have no effect on the condition under test
in this scenario.

---

## Traceability Summary Table

After completing Stage 5, output this table. Every cell must be filled.
A missing cell indicates a broken traceability link.

```markdown
## Traceability Summary

| Structure Type | Condition (Narrative)      | Alternate Flow (Stage 1) | Diagram Block (Stage 2)    | Pseudocode Block (Stage 3) | CFG Path (Stage 4) | Test Scenario (Stage 5) |
| -------------- | -------------------------- | ------------------------ | -------------------------- | -------------------------- | ------------------ | ----------------------- |
| [A/B/C/D]      | [condition from narrative] | Alternate flow N         | break/alt block N          | guard/branch N             | Path N             | Scenario N: [name]      |
| ...            | ...                        | ...                      | ...                        | ...                        | ...                | ...                     |
| —              | (all pass / target branch) | Normal flow              | (no break / target branch) | success return             | Path CCN           | Scenario CCN: [name]    |
```

---

## Error Conditions and Flags

Flag and halt if any of the following occur. Show the specific mismatch and
ask the user whether to correct the source stage or adjust the current stage.

| Condition                                         | Flag                                                                                    |
| ------------------------------------------------- | --------------------------------------------------------------------------------------- |
| Narrative lacks actor, condition, or outcome      | `[INPUT ERROR] Stage 0: narrative incomplete — missing [element]`                       |
| Structure type not classifiable                   | `[STRUCTURE ERROR] Stage 0: narrative structure ambiguous — clarify condition topology` |
| Alternate flow count ≠ decision node count        | `[TRACEABILITY ERROR] Stage 1→4: condition count mismatch`                              |
| Diagram block count ≠ alternate flow count        | `[TRACEABILITY ERROR] Stage 2→1: diagram block count mismatch`                          |
| Pseudocode block count ≠ diagram block count      | `[TRACEABILITY ERROR] Stage 3→2: pseudocode block count mismatch`                       |
| Pseudocode block order differs from Stage 1 order | `[TRACEABILITY ERROR] Stage 3: block order must match Stage 1`                          |
| Wrong PlantUML construct used for structure type  | `[STRUCTURE ERROR] Stage 2: use [break/alt] for Type [A/B/C/D] narratives`              |
| CCN ≠ number of basis paths enumerated            | `[TRACEABILITY ERROR] Stage 4: basis path count must equal CCN`                         |
| Scenario count ≠ CCN                              | `[TRACEABILITY ERROR] Stage 5: scenario count must equal CCN`                           |
| Assert value does not match Stage 1 wording       | `[TRACEABILITY ERROR] Stage 5→1: Assert mismatch in Scenario N`                         |
| Act block contains setup logic                    | `[STRUCTURE ERROR] Stage 5: Act must contain only the single method call`               |

---

## Quick Reference: Stage → Artifact Mapping

| Stage | Artifact                                          | Key Constraint                                                 |
| ----- | ------------------------------------------------- | -------------------------------------------------------------- |
| 0     | Narrative (user input) + Structure Classification | Completeness check + Type A/B/C/D label                        |
| 1     | `1-use-case-description.md`                       | One alternate flow per condition; exact messages locked        |
| 2     | `2-sequence-diagram.puml`                         | Construct matches structure type; one block per alternate flow |
| 3     | `3-algorithm.txt`                                 | Pattern matches structure type; order matches Stage 1          |
| 4     | CFG + CCN + basis paths                           | CCN = decision nodes + 1; paths = CCN; all paths distinct      |
| 5     | `5-test-scenario.md`                              | Scenarios = CCN; Assert = exact Stage 1 message                |

---

## Protocol Citation

When referencing this protocol in research outputs, use:

```
M. R. K. Pratama, D. Utama, and R. Fahmi, "Test-First Protocol for Deriving
Unit Tests from Use Case Specifications," Jurnal Nasional Teknik Elektro dan
Teknologi Informasi, vol. 15, no. 1, pp. 72–80, Feb. 2026.
DOI: 10.22146/jnteti.v15i1.24073
```
