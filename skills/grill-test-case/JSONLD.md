# Alternate export: JSON-LD with HTD-O

Write `<TestCaseID>.test-case.jsonld` next to the Markdown file, carrying the same confirmed content as RDF. Read [`examples/LightDimmer.test-case.jsonld`](examples/LightDimmer.test-case.jsonld) before writing one: it is the pattern for every node below.

Vocabularies:
- **HTD-O** `htd:` `https://w3id.org/erigrid/htd-o#`: v0.1 minimal core, published with Heussen et al., OSMSES 2026, https://doi.org/10.5281/zenodo.18868542.
- **SCM-O** `scm:` `https://w3id.org/erigrid/scm-o#` with the domain taxonomy `scmd:` `https://w3id.org/erigrid/scm-o/domains#`, for the SuT graph.

HTD-O v0.1 covers only the test case skeleton. Every other field uses `htd:` terms planned for HTD-O v0.2, or proposed here, so the file will need a term migration when v0.2 is published. The **Status** column below records which is which.

## File layout

- `@context`: copy the whole object from [`htd-o.context.jsonld`](htd-o.context.jsonld) inline, and add one prefix `tc` = `urn:erigrid-htd:<TestCaseID>:` for the test case's own nodes. Inline, because the file must parse offline and the context has no stable URL yet.
- `graph`: a flat list of nodes, each with `id` (`tc:…`) and `type`, linked by `id`. Reuse the labels from the Markdown (`PoI1`, `TCR1a`, `ss2`, `TS3`) as local names, so both exports cite each other.
- Text fields carry the confirmed wording verbatim. Test Specification nodes carry `"status": "seed"`.

## Mapping

| Template field / QS element | JSON-LD key → term | Node type | Status |
|---|---|---|---|
| Test Case | — | `htd:HolisticTestCase` | v0.1 |
| Name, author, version, date | `name`, `author`, `version`, `date` → `rdfs:label`, `dcterms:creator`, `owl:versionInfo`, `dcterms:date` | | standard |
| Project | `project` → `htd:project` | | proposed |
| Narrative | `narrative` → `htd:narrative` | | proposed |
| OuI | `objectsUnderInvestigation` → `htd:hasObjectUnderInvestigation`, pointing at the `scm:Component` nodes of the SuT graph | | v0.1 |
| SuT | `systemUnderTest` → `htd:hasSystemUnderTest`, pointing at one artifact node | `htd:GenericSystemConfigurationArtifact`, `scType` `htd:TC-GSC` | v0.1 |
| SuT figure | `representations` → `htd:hasRepresentation`; Mermaid text in `value`, `format` `text/vnd.mermaid`, `representationFormat` `htd:Diagram`; a user image by `source` path instead of `value` | `htd:SCRepresentation` | v0.1 |
| SuT subsystems and connections | second representation, `representationFormat` `htd:RDF_OWL`, also typed `scm:SystemConfigurationContainer` (rules below) | `scm:Component`, `scm:Terminal`, `scm:ConnectionPoint` | SCM-O v0.2 |
| DuI | `domainsUnderInvestigation` → `htd:hasDomainUnderInvestigation`, pointing at `scm:Domain` nodes | | v0.2 concept, name proposed |
| FuI, FuT | `functionsUnderInvestigation`, `functionsUnderTest` → `htd:hasFunctionUnderInvestigation`, `htd:hasFunctionUnderTest`; every FuI is also listed as FuT. Per function: `specification`, `realisedBy` → subsystem | `htd:Function` | v0.2 class; properties proposed |
| PoI | `purposes` → `htd:hasPurposeOfInvestigation`; wording in `name` | `htd:PurposeOfInvestigation` | v0.1 |
| PoI verb | `poiType` → `htd:hasPoIType`: `htd:Characterization`, `htd:Verification`, `htd:Validation` | | v0.2 concept, names proposed |
| PoI → OuI / FuI | `concernsObject`, `concernsFunction` | | v0.2 |
| Test Criteria | `criteria` → `htd:hasTestCriteria` (on the PoI); sub-criteria: `refinesCriteria` | `htd:TestCriteria` | v0.1; `refinesCriteria` v0.2 |
| Target Metric | `targetMetrics` → `htd:hasTargetMetric` (on the TCR); `unit`, `measurementType` (`quantitative`/`qualitative`) | `htd:TargetMetric` | v0.1; attributes proposed |
| Variability Attribute | `variabilityAttributes` (on the TCR); `controllability` `htd:Controllable`/`htd:Uncontrollable`, `variabilityRange` (text), `actsOn` → subsystem | `htd:VariabilityAttribute` | v0.2 class; rest proposed |
| Quality Attribute | `qualityAttributes` (on the TCR); `forMetric` → TM, `threshold` (text) | `htd:QualityAttribute` | v0.2 class; rest proposed |
| Qualification Strategy | `qualificationStrategy` → node with `description` | `htd:QualificationStrategy` | v0.2 class; property proposed |
| Test Specification | `testCase` → `dcterms:isPartOf` the test case | `htd:TestSpecification` | v0.1 class |
| TS subject, OuI, approach | `name`; `testObject` → `htd:hasTestObject` (TC OuI or a subsystem: the decomposition dependency `TC.SuT.ssN → TSi.OuI`); `testingApproach` | | proposed |
| Matrix cell Q / C | `qualifiesCriteria`, `contributesToCriteria` → TCR; the combination for C cells as `combinationRule` on the TCR | | proposed |
| Matrix cell P, sequencing | `prerequisiteFor` → TS; `precedes` → TS for every `<` in the sequencing line; parallel TSs have no `precedes` between them | | proposed |
| TS key variability | `variesAttribute` → VA | | proposed |
| TS template seeds | `rationale`, `inputParameters`, `outputParameters`, `testDesign`, `initialState`, `stateEvolution`, `otherParameters`, `temporalResolution`, `uncertaintySources` (→ uncontrollable VAs), `stoppingCriteria` | | proposed |

Leave out keys whose value is `—` or an open question; the JSON-LD carries confirmed values and seeds only.

## SuT graph rules

The SuT figure and the SuT graph describe the same configuration: one `scm:Component` per `ssN` (with `identifier` `ssN`), and per figure edge one `scm:ConnectionPoint` that `joins` two `scm:Terminal`s, one on each component, both with the edge's domain. The container `contains` every component, terminal, connection point and test-case-local domain, and has `containerSCType` `htd:TC-GSC` and `representsArtifact` → the SuT artifact.

Edge domains: use an `scmd:` domain where one fits (`scmd:LV`, `scmd:Thermal`, `scmd:Communication`, `scmd:Control`, …). Otherwise define a local `scm:Domain` (`tc:Haptic`) with `parentDomain` set to the closest `scmd:` domain, so it inherits connection semantics.

## Check

Before reporting the export done:

1. The file parses as JSON-LD (e.g. `rdflib.Graph().parse(path, format="json-ld")`).
2. It conforms to the HTD-O v0.1 SHACL profile (`HTD-O-SHACL.ttl` from the Zenodo record): exactly one SuT, at least one OuI, and every PoI reaching a target metric through its criteria.
3. Every `id` that is linked to is defined in the graph, and every label in the Markdown export has its node.

Run checks 1 and 2 when `rdflib` and `pyshacl` are available, and say in the report which checks ran.
