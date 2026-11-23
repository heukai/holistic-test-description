# Output: Test Case, Qualification Strategy, Test Specification outlines

Write one Markdown file, `<TestCaseID>.test-case.md`, in the user's working directory (or where they ask). It has four parts. The same content also goes out as RDF in `<TestCaseID>.test-case.jsonld`, per `JSONLD.md`; use the same labels in both.

## 1. Test Case

The header, then the template table with every confirmed field from `TEMPLATE.md` in template order (Name, Narrative, FuI, OuI, DuI, PoI, SuT, FuT, TCR, TM, VA, QA), with the **SuT figure** embedded under the SuT row (the user's image by relative path, or the Mermaid block). Label PoIs `PoI1…` (with any `PoI1 → PoI2` relations), criteria `TCR1…`, subsystems `ss1…`, and tag each TCR, TM, VA and QA with its PoI.

## 2. Qualification Strategy

The free-text summary the template asks for (how the PoIs are met by the tests and how results combine), followed by:

- the final **mapping matrix**,
- the **criterion dependencies** and **decomposition dependencies** in the notation of `QUALIFICATION.md`,
- the **sequencing** line.

## 3. Test Specifications

One block per TS. Start with its outline from `QUALIFICATION.md`, then fill each Test Specification template field with a seed derived from the Test Case and the mapping, plus anything parked during the session. Mark every value **seed**.

| TS field | Seed from |
|---|---|
| Reference to Test Case | Test Case ID; PoIs and TCRs covered (with Q/C/P) |
| Title of Test | The TS subject |
| Test Rationale | Why this TS exists in the QS: what it qualifies, what it feeds |
| Specific Test System | The TS-SC: the subset of the SuT figure this TS uses, with concrete values, as its own Mermaid block titled `TS-SC: <TS ID>`, with the TS's OuI double-bordered |
| Target measures | The TMs of the covered TCRs |
| Input and output parameters | Inputs: controllable VAs and test signals. Outputs: the TMs |
| Test Design | Which VAs are varied and how; step sequence only if the user gave one |
| Initial system state | Parked user input, else open question |
| Evolution of system state and test signals | Parked user input, else open question |
| Other parameters | Parked user input, else `—` |
| Temporal resolution | Implied by the dynamics of the TMs; propose a value |
| Source of uncertainty | Uncontrollable VAs, with ranges or distributions |
| Suspension / Stopping criteria | QA thresholds and safety limits of the SuT |

## 4. Open questions and parked items

- **Open questions** for the Test Specification level, each tied to a TS and field.
- **Deferred TCRs** with their reasons.
- **Experiment-level notes** (lab, equipment, measurement precision, data storage), grouped by the TS they belong to, including candidate Experiment Specifications.
- **E-SC mapping table** (`CONFIGURATIONS.md`), one per experiment or platform: TC-GSC role → TS-SC object → E-SC element, with the realisation mode or the RI component's job, the RI-SC reference and notes. Always present after phase 0; otherwise present whenever equipment came up in the session.
