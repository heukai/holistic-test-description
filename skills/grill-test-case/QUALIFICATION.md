# Qualification Strategy: steps, notation, mapping checks

Source: `Guidelines/Qualification Strategy_Extended_Guideline.pdf` in https://github.com/ERIGrid2/holistic-test-description. The Qualification Strategy (QS) breaks the Test Case into a set of Test Specifications (TS) that together qualify the OuI against the PoIs and TCRs.

## Notation

Use the guideline's notation in the matrix and in the output.

- **Hierarchy**: `TC → (TS1 … TSn)`, `TSi → (ES1 … ESn)`.
- **Criterion dependency**: which TS qualifies which TCR. `TC.TCR1 ← TS1` (TCR1 is qualified in TS1); `TC.TCR2 ← (TS2 && TS3)` (TCR2 needs both results combined).
- **Decomposition dependency**, two kinds:
  - a SuT subsystem becomes the OuI of its own TS: `TC.SuT.ss2 → TS1.OuI`, typically because it has unknown dynamics that must be characterized first;
  - **test-bed characterization**: an RI component becomes the OuI of its own TS, numbered `TS0` (`TS0a`, `TS0b`…): `RI.dc_supply → TS0.OuI`, because one of its limits (a setup uncertainty) may distort a target metric. The RI component never enters the SuT for this.
- **Sequencing**: `TS1 < TS2` (TS1 before TS2); `TS2 | (TS3 < TS4)` (TS2 in parallel with the TS3–TS4 chain).

## Steps

Phase 2 follows the guideline's top-down steps. The questions under each step are the frontier for that step.

1. **Goal sharpening.** Does any PoI depend on another: does qualifying one need the result of another first (a characterization feeding a verification, say)? Record such relations as `PoI1 → PoI2`. PoIs with no relation stay independent peers; never impose a ranking.
2. **Breakdown.** Name SuT subsystems and sub-FuTs. Associate each PoI with the OuI/FuI it qualifies. Ask which subsystems must be qualified on their own first, and which RI components need test-bed characterization (decomposition dependency).
3. **Group into Test Specifications.** Do the PoIs and TCRs need different testing approaches (different SuT subset, different signals, human subjects vs instruments, simulation vs hardware)? Each distinct approach is a TS. For each TS, settle its **subject** (one line: what it qualifies), its OuI (the TC OuI or a subsystem), and the PoIs and TCRs it covers.
4. **Combine.** For every TCR: which TS qualifies it, alone or combined? How are results combined into the PoI outcome (comparison, statistical pooling, one TS calibrating another)? Which factors vary between TSs that share a reference system, and are they test factors or nuisances?
5. **Sequence.** Which TSs must precede others (a characterization feeding a validation, a maturing OuI)? Which can run in parallel?

## Mapping matrix

Print after every phase 2 round. Rows are TCRs grouped by PoI; columns are TSs. Mark each cell **Q** (qualifies), **C** (contributes; combined with others), **P** (prerequisite: characterizes something this TCR's TS needs), or blank.

```
| PoI | TCR | TS1 <subject> | TS2 <subject> | TS3 <subject> | Dependency |
|---|---|---|---|---|---|
| PoI1 Verify … | TCR1 … | Q | | | TC.TCR1 ← TS1 |
| PoI2 Characterize … | TCR2 … | P | C | C | TC.TCR2 ← (TS2 && TS3) |
```

Below the matrix, list decomposition dependencies and the sequencing line.

## Test Specification outline

One block per TS, at outline depth only:

- **ID and subject**: `<TC>.TS1`, one line.
- **OuI** (TC OuI or `TC.SuT.ssN`) and the **FuT** exercised.
- **PoIs and TCRs** covered, with Q/C/P.
- **Specific test system**: a **TS-SC** that conforms to the TC-GSC, i.e. the subset of SuT roles this TS uses, instantiated with concrete values (drawn as a cut of the SuT figure, titled `TS-SC: <TS ID>`); and the **testing approach** (e.g. hardware lab test, simulation, CHIL, user study).
- **Key variability**: which VAs are varied here.
- **Feeds / fed by**: other TSs.

## Mapping checks

Phase 2 is done when all pass.

- **Every TCR covered.** Each TCR has at least one Q, or a set of C cells with a stated combination, or is explicitly *deferred* with a reason.
- **Every TS earns its place.** Each TS covers at least one TCR.
- **Every PoI closed.** Each PoI is fully qualified once its TSs are done, or its open TCRs are deferred.
- **Combination stated.** Every TCR with C cells says how the results combine.
- **Prerequisites explicit.** Every P cell has a decomposition dependency and a sequencing entry.
- **Test bed justified.** Every test-bed characterization TS names the setup uncertainty it resolves and the target metric that uncertainty would distort.
- **Subject distinct.** No two TSs share the same OuI, TS-SC and approach. If they do, merge them, or move the difference down to Experiment Specifications (e.g. two bulb types as two experiments under one TS).
