---
name: grill-test-case
description: Grill the user to fill in an ERIGrid Holistic Test Description (HTD) Test Case and its Qualification Strategy, round by round, until every PoI and Test Criterion maps to outlined Test Specifications.
disable-model-invocation: true
---

Interview the user relentlessly to fill in the **Test Case** of an ERIGrid Holistic Test Description (HTD) and its **Qualification Strategy**: the plan for which Test Specifications qualify which PoIs and Test Criteria. The HTD has three levels: Test Case → Test Specification → Experiment Specification. This session works at the Test Case level and ends with **outlines** of the Test Specifications it calls for. Detail below the outline (step sequences, initial states, lab equipment) is parked as **seeds** for the next levels.

A Test Case is meant to be broad: several PoIs, several Test Criteria per PoI. The **mapping** from PoIs and Test Criteria to Test Specifications is the main deliverable, so breadth is resolved by mapping, never by trimming the Test Case.

Three references, read all before the first round:

- [`TEMPLATE.md`](TEMPLATE.md): the Test Case fields, their dependency order, and the focus checks.
- [`QUALIFICATION.md`](QUALIFICATION.md): the Qualification Strategy steps, dependency notation, and mapping checks.
- [`CONFIGURATIONS.md`](CONFIGURATIONS.md): the system configuration levels (TC-GSC, TS-SC, E-SC, RI-SC), the rule that separates SuT roles from RI components, and the bottom-up lift from an experiment setup to the SuT.

## Entry: top-down, bottom-up or from an experiment

Ask which situation the user is in, unless their material makes it obvious:

- **Top-down**: the user has an object and a goal. Fill the Test Case first (phase 1), then derive the Test Specifications (phase 2).
- **Bottom-up**: the user already has experiments or setups in mind. Collect those first as candidate Test Specifications, then build the Test Case that frames them (phase 1), then map (phase 2). The mapping checks will expose PoIs no experiment serves and experiments that serve no PoI.
- **From an experiment**: the material is an experiment already run or set up (a dataset, lab configuration files, a test report). Everything in it is described as the lab set it up, so start with **phase 0**: the bottom-up lift in `CONFIGURATIONS.md` (E-SC → classification → TS-SC → TC-GSC). The resulting TC-GSC is the SuT for phase 1. Mark the description as retrospective when the experiment has already run.

In every situation, apply the classification rule of `CONFIGURATIONS.md` whenever an element of a lab, a platform or a piece of equipment comes up: it becomes a role in the SuT, or an RI component parked for the Experiment Specification.

If the user supplies a draft (a filled `.docx`, notes, a paper, a system diagram), pre-fill every field you can. Run the focus checks on draft values from round 1: a draft field that passes every check is listed once under *Accepted unless you object*; a draft field that fails one becomes a question.

## Rounds

Map the work as a **design tree**: each field hangs off the fields it depends on. The **frontier** is every open field and every failed check whose prerequisites are settled. Ask the whole frontier in one round, number each question, and give your recommended answer, phrased in HTD vocabulary and drawn from what the user has already said. Then wait.

```
❓ **Q1** - **<HTD field or QS step>: <question title>**: <question body; offer concrete options where they help>

➡️ <your recommended answer>

---

❓ **Q2** - ...
```

After every round, print the current state: the Test Case table (settled values, _(draft)_ values, `—` for empty), the SuT figure whenever it changed, and, once phase 2 has started, the **mapping matrix** from `QUALIFICATION.md`. Then recompute the frontier.

Re-open a settled field whenever a later answer contradicts it. Phase 2 regularly sends you back: a TCR that two Test Specifications must combine to qualify, or a SuT component that needs characterizing first, adds a TCR or PoI to the Test Case. That loop is the skill working.

Finding facts is your job: standards, typical metrics, grid codes, the user's own files. Look them up (dispatch a sub-agent when it takes digging) instead of asking. Decisions about goals, scope and thresholds are the user's: put each to them.

## Phase 0: configuration lift (from an experiment)

Follow the four steps of the bottom-up lift in `CONFIGURATIONS.md`, one round per step, drawing the E-SC, TS-SC and TC-GSC figures in turn. Done when every E-SC element realises a TS-SC object or is an RI component with a stated job, every TS-SC object conforms to a TC-GSC role, and the user has confirmed the three figures.

## Phase 1: Test Case

Fill the fields in `TEMPLATE.md` order, except the Qualification Strategy. That order settles the OuI and the SuT, with its figure, before the PoIs: until then the objectives stay in plain words in the Narrative. Done when every field is settled and every Test Case focus check passes.

## Phase 2: Qualification Strategy

Work through the steps in `QUALIFICATION.md`. Done when every mapping check passes: every TCR qualified by named Test Specifications or explicitly deferred, every Test Specification outlined, dependencies and sequencing stated.

## Done

Show the final Test Case, mapping matrix and Test Specification outlines, and ask the user to confirm they reflect a shared understanding. Only after they confirm, write the Markdown output described in [`HANDOFF.md`](HANDOFF.md) and, beside it, the JSON-LD export described in [`JSONLD.md`](JSONLD.md).
