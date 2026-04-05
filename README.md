# Unit Test Derivation Training

This repository supports an empirical study on developer experience and skills
development in systematic unit test derivation. It serves as the working material
for a structured protocol training conducted as part of an ongoing research program
in human-centered software engineering.

## Background

This repository is grounded in the following published protocol:

> M. R. K. Pratama, D. Utama, R. Fahmi, "Test-First Protocol for Deriving Unit Tests from Use Case Specifications," _JNTETI_, vol. 15, no. 1, pp. 72–80, Feb. 2026.  
> DOI: [10.22146/jnteti.v15i1.24073](https://doi.org/10.22146/jnteti.v15i1.24073)  
> Link: [https://journal.ugm.ac.id/v3/JNTETI/article/view/24073](https://journal.ugm.ac.id/v3/JNTETI/article/view/24073)

The protocol provides a systematic, human-centric approach to deriving coverage-aware
unit test scenarios from use case specifications, using:

- Object-oriented analysis and design (use case specifications)
- Behavioral modeling via sequence diagrams
- Control flow graph (CFG) construction
- Basis path testing (McCabe's cyclomatic complexity)
- Arrange-Act-Assert test case structure

## Repository Structure

The files in this repository follow the five-stage training sequence of the protocol:

| File                        | Stage        | Description                                    |
| --------------------------- | ------------ | ---------------------------------------------- |
| `0-case-study.md`           | Prerequisite | Case study narrative as training context       |
| `1-use-case-description.md` | Stage 1      | Use case specification                         |
| `2-sequence-diagram.puml`   | Stage 2      | Behavioral modeling (PlantUML)                 |
| `3-algorithm.txt`           | Stage 3      | Extracted internal logic from sequence diagram |
| `4-cfg.svg`                 | Stage 4      | Control flow graph + cyclomatic complexity     |
| `5-test-scenario.md`        | Stage 5      | Derived unit test scenarios (AAA pattern)      |

## Research Context

This repository is part of a multi-study empirical research program examining how
developers and graduate students acquire skills in systematic unit testing. The study
measures developer experience dimensions — including affect, motivation, cognitive
development, and self-efficacy — across multiple observation points during protocol adoption.

The training materials here are used as the primary learning artifact in the study.

## Related Study

The empirical study is conducted at the graduate level within an Information Systems
context. Participant data is collected under a confidential protocol and is not
included in this repository.

For more about the foundational protocol, refer to the JNTETI paper linked above.

## AI Agent Validation

The protocol has been formalized in `SKILL.md` and validated through AI agent application. Key findings:

**Quality Comparison:**

- ✅ All protocol stages correctly executed
- ✅ Artifact quality meets expert standards
- ✅ **Traceability matrix generated automatically** (not produced manually in training due to overhead)

**Significance:**
AI validation demonstrates that the protocol's formalization enables complete, rigorous documentation that even expert practitioners conceptually understand but pragmatically omit during manual execution.

**Implications:**
Tool support could bridge the gap between:

- Conceptual understanding (expert has this)
- Documentation completeness (expert skips this)

See: [AI validation chat](https://claude.ai/share/8f930541-04b7-4d2b-85cb-8ae74284a797) for detailed comparison.
