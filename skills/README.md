# HTD agent skills

Agent skills that help write ERIGrid Holistic Test Descriptions (HTD) together with an AI coding agent such as Claude Code.

| Skill | What it does |
|---|---|
| [`grill-test-case`](grill-test-case/SKILL.md) | Interviews you round by round to fill in a **Test Case**, top-down from a goal or bottom-up from an experiment (lifting the lab setup, E-SC, to the System under Test, TC-GSC): System under Test with a figure first, then typed PoIs, test criteria, metrics, variability and quality attributes. It then builds the **Qualification Strategy**: a mapping of every PoI and Test Criterion to outlined **Test Specifications**, with dependencies and sequencing. Output: one Markdown file with the Test Case, the mapping and seeded Test Specification templates, plus the same content as JSON-LD using the HTD-O v0.2 ontology. |

## Install

### Claude Code (plugin marketplace)

This repository is a Claude Code plugin marketplace. In Claude Code 2.1.275 or later, one step:

```
/plugin install holistic-test-description --marketplace ERIGrid2/holistic-test-description
```

On older versions, add the marketplace first:

```
/plugin marketplace add ERIGrid2/holistic-test-description
/plugin install holistic-test-description@erigrid-htd
```

Then run the skill with `/holistic-test-description:grill-test-case`, optionally followed by a path to an existing test description (`.docx`) or notes.

To install from a branch other than the default, add the marketplace by Git URL with the branch as the fragment, e.g. `https://github.com/ERIGrid2/holistic-test-description.git#feature/skills`.

### Manual copy

Each skill is a self-contained folder. Copy it into your personal or project skills directory:

```bash
cp -r skills/grill-test-case ~/.claude/skills/
```

It is then available as `/grill-test-case`. Other agents that read `SKILL.md` folders (the open Agent Skills format) can use the same folder.

## Structure

```
.claude-plugin/
  marketplace.json     # repository-level marketplace; points at ./skills
skills/                # the plugin: only this folder is copied on install
  .claude-plugin/
    plugin.json        # plugin manifest; "skills": "./" discovers the skill folders here
  grill-test-case/
    SKILL.md           # the interview process
    TEMPLATE.md        # Test Case fields, order, SuT figure, focus checks
    QUALIFICATION.md   # Qualification Strategy steps, notation, mapping
    CONFIGURATIONS.md  # TC-GSC / TS-SC / E-SC / RI-SC levels, role vs RI component, bottom-up lift
    HANDOFF.md         # output format and Test Specification seeds
    JSONLD.md          # JSON-LD export: HTD-O / SCM-O mapping and checks
    htd-o.context.jsonld  # JSON-LD context, inlined into each export
    examples/LightDimmer.test-case.jsonld  # worked JSON-LD export
```

To add a skill, create a new folder with a `SKILL.md` next to `grill-test-case/`.
