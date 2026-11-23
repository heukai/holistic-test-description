# Alternate export: JSON-LD with HTD-O v0.2

Write `<TestCaseID>.test-case.jsonld` next to the Markdown file, carrying the same confirmed content as RDF. Read [`examples/LightDimmer.test-case.jsonld`](examples/LightDimmer.test-case.jsonld) before writing one: it is the pattern for every node below.

Vocabularies:
- **HTD-O v0.2** `htd:` `https://w3id.org/erigrid/htd-o#`: the test-description (planning) ontology, core and uncertainty modules. Declare it on the test case: `conformsTo` `https://w3id.org/erigrid/htd-o/core/0.2.0`.
- **SCM-O v0.2** `scm:` `https://w3id.org/erigrid/scm-o#` with the domain taxonomy `scmd:` `https://w3id.org/erigrid/scm-o/domains#`, for the SuT sketch.

## HTD-O v0.2 conventions

- **Text first.** Every template field takes plain text. Add structure (a link to a node) where the session settled it; nothing else is required except the bold links in the mapping table.
- **References point back** to what is defined earlier: a criterion names its PoI (`forPoI`), a metric its criteria (`forCriterion`), a quality attribute its metric (`forMetric`), a Test Specification its Test Case (`testCase`) and the criteria it serves. The Test Case lists only its own parts: PoIs, functions, OuI, DuI, SuT, Qualification Strategy.
- **Status** comes from the session: `status` on a node, one of `htd:Settled`, `htd:Draft`, `htd:Seed`, `htd:Deferred` (with `reason`), `htd:Parked`. Test Specification nodes are `htd:Seed`. For a single field whose status differs from its node, write the value as a field-value node: `{"type": "htd:FieldValue", "value": "<text>", "refersTo": "<id>", "valueStatus": "htd:Draft"}`.
- **Versions** are `major.minor` on the Test Case, Qualification Strategy and Test Specifications: `0.x` concept, `1.x` test design, `2.x` post test / documentation. A description written after the experiment carries `describedRetrospectively: true`.

## File layout

- `@context`: copy the whole object from [`htd-o.context.jsonld`](htd-o.context.jsonld) inline, and add one prefix `tc` = `urn:erigrid-htd:<TestCaseID>:` for the test case's own nodes. Inline, because the file must parse offline.
- `graph`: a flat list of nodes, each with `id` (`tc:…`) and `type`. The labels from the Markdown (`PoI1`, `TCR1a`, `ss2`, `TS3`) are the local names and go in `identifier` (`dcterms:identifier`; `scmIdentifier` for SuT components), so both exports cite each other.
- Text fields carry the confirmed wording verbatim. Leave out fields that are `—` or open questions, unless the session deferred them: then write them with `htd:Deferred` and a `reason`.

## Mapping

| Template field / QS element | JSON-LD key → HTD-O term (direction) | Node type |
|---|---|---|
| Test Case header | `identifier`, `name`, `author`, `project`, `version`, `date` | `htd:TestCase` |
| Narrative | `narrative`; a use case reference as text in `useCase` | |
| OuI | `objectsUnderInvestigation` → SuT components (never a function) | |
| DuI | `domainsUnderInvestigation` → `scm:Domain` | |
| SuT | `systemUnderTest` → the `scm:Region` over the sketch (rules below) | `scm:Region` |
| FuI, FuT | `functionsUnderInvestigation`, `functionsUnderTest` (every FuI also listed as FuT); per function `specification` (reference or text), `realisedBy` → component, `subFunctionOf` | `htd:Function` |
| PoI | `purposes` (on the TC); per PoI **`poiKind`** `htd:Characterization` / `htd:Verification` / `htd:Validation`, wording in `name`, `concernsObject`, `concernsFunction`, `dependsOnPoI` → PoI (`PoI1 → PoI2` from goal sharpening, written on PoI2) | `htd:PurposeOfInvestigation` |
| Test criterion | **`forPoI`** → exactly one PoI; `judgesPropertyOf` → component; `refinesCriterion` → parent TCR | `htd:TestCriterion` |
| Target metric | **`forCriterion`** → TCRs; `unit` (text), `measurementType` `quantitative` / `qualitative` | `htd:TargetMetric` |
| Variability attribute | `forPoI` → PoIs; `controllability` `htd:Controllable` / `htd:Uncontrollable`, `variabilityRange` (text), `actsOn` → component | `htd:VariabilityAttribute` |
| Quality attribute | **`forMetric`** → exactly one TM. Verification and validation: `threshold` + `thresholdProvenance` `htd:StatedInSource` / `htd:Assumed`. Characterization: `resultQuality` only, no pass/fail threshold | `htd:QualityAttribute` |
| Qualification Strategy | `qualificationStrategy` (on the TC) → node with `description` (the free text) and `version` | `htd:QualificationStrategy` |
| Test Specification | `testCase` → the TC; `identifier` `<TestCaseID>.TSn`; `name` = subject; `status` `htd:Seed`; `version` | `htd:TestSpecification` |
| TS OuI (decomposition dependency `TC.SuT.ssN → TSi.OuI`) | `testObject` → component | |
| TS specific test system | `testSystem` → the TS-SC container (see *Configuration levels*); where the session only cut the SuT figure without concrete values, a second `scm:Region` in the TC-GSC container instead | TS-SC container or `scm:Region` |
| Matrix cell Q / C | `qualifiesCriteria` / `contributesToCriteria` → TCR; for C cells, `combinationRule` (text) on the TCR | |
| Matrix cell P, sequencing | `prerequisiteFor` → TS, always with `precedes` → the same TS and a `testObject`; `precedes` → TS for every `<` in the sequencing line; parallel TSs have no `precedes` between them | |
| Deferred TCR | `status` `htd:Deferred` + `reason` on the TCR | |
| TS template seeds | `testingApproach`, `rationale`, `targetMeasures` → TMs, `inputParameters`, `outputParameters`, `testDesign`, `variesAttribute` → VAs, `initialState`, `stateEvolution`, `otherParameters`, `temporalResolution`, `uncertaintySources` → uncontrollable VAs or text, `stoppingCriteria`, `riMapping` (text) | |

Experiment Specifications and E-SCs are written only when the session settled them, always after phase 0, otherwise when the user described an experiment (*Configuration levels*). Remaining experiment-level notes stay in the Markdown.

## SuT sketch rules

The SuT is an SCM-O configuration with two views:

- **Artefact (document view):** one `htd:GenericSystemConfigurationArtifact` with `scType` `htd:TC-GSC` and two `representations`: the figure (`htd:SCRepresentation`, `representationFormat` `htd:Diagram`; Mermaid text in `value` with `format` `text/vnd.mermaid`, or a user image by `source` path) and the SCM-O graph (also typed `scm:SystemConfigurationContainer`, `representationFormat` `htd:RDF_OWL`, `containerSCType` `htd:TC-GSC`, `representsArtifact` → the artefact).
- **Region (what the SuT is):** an `scm:Region` with `members` → every component of the sketch. `systemUnderTest` points here.

Inside the container:
- One `scm:Component` per `ssN` (`scmIdentifier` `ssN`). Actors and environment from the figure's rounded nodes are components too, with `sutRole` `htd:Actor` or `htd:Environment`.
- Per figure edge, one `scm:ConnectionPoint` (`name` = the edge label) that `joins` two `scm:Terminal`s, one on each component, both with the edge's `domain`.
- Edge domains: an `scmd:` domain where one fits (`scmd:LV`, `scmd:Thermal`, `scmd:Communication`, `scmd:Control`, …); otherwise a local `scm:Domain` (`tc:Haptic`) with `parentDomain` set to the closest `scmd:` domain. Every edge domain is listed in the DuI.
- The container `contains` the region, every component, terminal, connection point and local domain.

## Configuration levels

Each level is its own artefact and container, typed by `scType` / `containerSCType` (`CONFIGURATIONS.md`). The SuT sketch above is the TC-GSC.

- **TS-SC** (one per Test Specification that has one): a `htd:SpecificSystemConfigurationArtifact`, `scType` `htd:TS-SC`, `conformsToGSC` → the TC-GSC artefact, with a figure (titled `TS-SC: <TS ID>`) and a container (`containerSCType` `htd:TS-SC`, `containerConformsTo` → the TC-GSC container). Its components carry the **same `scmIdentifier` as the TC-GSC role** they instantiate (`ss1`); that is the only object-level link HTD-O and SCM-O offer today. Built like the sketch: terminals, connection points, `sutRole`.
- **E-SC** (one per Experiment Specification): a `htd:SpecificSystemConfigurationArtifact`, `scType` `htd:E-SC`, with a container (`containerSCType` `htd:E-SC`; `allocatedFrom` → the RI-SC container when the lab has one). Its components use lab names. Per realising element one `scm:Realization` (in the E-SC container) with `realiser` → E-SC component(s), `realised` → TS-SC component(s), `realizationMode` `scm:Physical` / `scm:Emulated` / `scm:Simulated`. RI components are additionally typed `scm:InterfacingComponent`, realise nothing, and state their job in `description`.
- **Experiment Specification**: `htd:ExperimentSpecification` with `identifier` `<TS ID>.<name>`, `realizesTestSpecification` → TS, `researchInfrastructure` (text), `realisation` (`htd:Simulation`, `htd:CoSimulation`, `htd:Hardware`, `htd:HIL`, `htd:PHIL`, `htd:Field`, `htd:Mixed`), `setup` → the E-SC artefact, `experimentDesign`, `setupUncertainties`, `equipmentPrecision`, `measurementUncertainty`, `uncertaintyManagement`, `plannedDataStorage` (text).
- **Test-bed characterization** (`RI.x → TS0.OuI`): `testObject` of TS0 → the RI component in the E-SC.

The E-SC mapping table of the Markdown is the same information: one table row per `scm:Realization` or interfacing component.

## Check

Before reporting the export done:

1. The file parses as JSON-LD (e.g. `rdflib.Graph().parse(path, format="json-ld")`).
2. Every `id` that is linked to is defined in the graph, and every label in the Markdown export has its node.
3. If the HTD-O v0.2 validator is available (`python -m htdo.validate FILE`, with `--scm core,domains,realisation` when the file has an E-SC), run it. It applies the skill's focus checks as rules H01–H19 and the SCM-O shapes to the sketch. Fix every violation; report the warnings that remain and why.

Say in the report which checks ran.
