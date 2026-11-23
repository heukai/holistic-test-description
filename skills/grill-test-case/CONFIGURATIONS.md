# System configuration levels: TC-GSC, TS-SC, E-SC, RI-SC

Every figure and every list of components in a test description belongs to exactly one **system configuration level** (ERIGrid D-NA5.1, Table 3.2; encoded as `htd:SCType` in HTD-O). Name the level of every figure you draw.

| Type | HTD level | Content | Example (PV inverter test) |
|---|---|---|---|
| UC-GSC | Use case | Function/system alignment reference | — |
| **TC-GSC** | **Test Case** (the SuT) | Generic, type level: roles from the real use case, with multiplicities. No platforms, equipment or lab automation | "LV grid at the PCC", "DC source", "setpoint issuer", "PV inverter (OuI)" |
| **TS-SC** | **Test Specification** (specific test system) | One concrete instance of the TC-GSC: specific objects, parameters, labelled terminals and connections | "230 V / 50 Hz PCC", "DC source, 0–800 V, 10 kW", "P setpoint via SunSpec Modbus" |
| **E-SC** | **Experiment Specification** (setup) | The concrete lab setup: RI components plus the OuI | "grid emulator with power amplifier", "lab DC supply", "test automation suite", "DAQ" |
| RI-SC | RI description | One lab's configuration and potential connectivity | the lab's model of its equipment |
| RI-GSC | RI profiling | Data types for describing labs | — |

Relations: a TS-SC **conforms to** the TC-GSC. An E-SC **realises** a TS-SC, with components **allocated from** an RI-SC.

## Classification rule: real role or RI component?

For every element in the material, ask: **does it exist in the real use case?** Put it to the experimenter in plain words: *"Would this be there if the inverter were installed on a real roof?"*

- **Yes**: it is a role in the SuT. The E-SC element **realises** it, with a mode:
  - *physical*: the real thing (the OuI itself, a real bulb);
  - *emulated*: hardware standing in for it (a grid emulator for the LV grid, a DC supply for the PV generator);
  - *simulated*: a model standing in for it (a real-time simulation of the feeder).
- **No**: it only emulates the interface to something, actuates, measures or automates. It is an **RI component** (an *interfacing component*: power amplifier, I/O, real-time platform, DAQ, test automation). Record its job (measures TM1, issues the test signal, logs data). It never enters the SuT. Its limits are **setup uncertainties**, and a limit that may distort a target metric is a candidate **test-bed characterization** (`RI.x → TS0.OuI`, see `QUALIFICATION.md`).

An emulator is both: its *function* realises a role (emulated), its *hardware* is RI. The role goes in the SuT, named after what is emulated; the hardware and its limits go in the E-SC.

## Bottom-up lift: E-SC → TS-SC → TC-GSC

Use this when the material is an experiment (a dataset, a lab configuration file, a test report, a setup photo), and when the experimenter cannot tell the levels apart. Work up one level per step, confirm each step with the user, and draw the figure of each level with its type in the title.

1. **E-SC: inventory the setup.** List every element the material shows: devices, platforms, software, data channels, setpoint sources, configuration files. Draw the E-SC figure with lab names. Map every recorded data channel to what it measures; these are target metric candidates. Done when every channel and every device in the material sits in the figure.
2. **Classify.** Apply the classification rule to every E-SC element, as one round of questions. Done when every element either realises a role (with its mode) or is an RI component with a stated job.
3. **TS-SC: the instance that was tested.** One object per realised role, described by what it is in the test and not by how the lab produced it: ratings, set values, connections, labelled terminals. Several E-SC elements may realise one TS-SC object, and one element several objects. If the material holds several experiments that differ only in set values or equipment, they share one TS-SC, or a few. Done when every realising E-SC element points at a TS-SC object.
4. **TC-GSC: generalise.** Replace each TS-SC object by its role and type, with multiplicities. Values that were fixed in the TS-SC but vary across the experiments, or would vary in the real use case, become **variability attribute** candidates. The TC-GSC is the SuT of phase 1 and its figure is the SuT figure in `TEMPLATE.md`. Done when every TS-SC object conforms to a TC-GSC role and the figure passes the "SuT is generic" check.

Then continue with phase 1 as usual. The E-SC, the classification and the TS-SC are not lost: they feed the Experiment Specification and the E-SC mapping table in `HANDOFF.md`.

## E-SC mapping table

One row per E-SC element, grouped by experiment (platform or lab):

| TC-GSC role | TS-SC object | E-SC element | Mode / job | RI-SC reference | Note |
|---|---|---|---|---|---|
| ss1 LV grid at PCC | 230 V / 50 Hz PCC | grid emulator + power amplifier | emulated | lab model id, if any | amplifier bandwidth → setup uncertainty |
| ss3 DC source | DC source, 10 kW | lab DC supply | emulated | | P capped near 0.6 p.u. → `RI.dc_supply → TS0.OuI` |
| — | — | DAQ, 1 s | RI: measures TM1, TM2 | | |

Mapping notation between test plan and lab files (e.g. which automation script runs which test step) goes in the Note column or below the table.
