# AGV Mining Ontology

> An OWL ontology for Automated Guided Vehicle management in underground mining sites, built with Protégé.

**Degree:** Master's Degree, Robotics Engineering
**Course:** Artificial Intelligence — Università degli Studi di Genova (DIBRIS)  
**Authors:** Joel Topulli, Stefania Germena  
**Academic Year:** 2025/2026

---

## Overview

This project models the domain knowledge required to manage **Automated Guided Vehicles (AGVs)** operating in underground mining environments. The ontology formalizes the relationships between vehicles, sites, zones, loads, tasks, and infrastructure to enable automated reasoning and decision-making.

The ontology was developed in **Protégé** using **OWL (Web Ontology Language)** and validated with a DL (Description Logic) reasoner.

---

## Repository Structure

```
├── AGV_Ontology.rdf       # The OWL ontology file (open with Protégé)
└── report.pdf             # Full project report with design rationale
```

---

## Ontology Design

### Core Classes

The ontology is built around **9 top-level disjoint classes**, ensuring clear separation between domain entities and preventing logical inconsistencies:

| Class | Description |
|---|---|
| `AGV` | Central entity — autonomous vehicles (Large / Medium / Small) |
| `Site` | Disjoint locations: Mine, Excavation Site, Metal Extraction Site, Disposal Site |
| `Zone` | Operational areas: Loading, Dumping, Charging, Waiting, Restricted |
| `Load` | Material carried: Ore, Waste, Elements, Empty |
| `Task` | Current activity: Loading, Unloading, Transit, Reversing, Stopping, Charging |
| `Infrastructure` | Physical environment: Tunnel, Level, Intersection, Steepness Gradient |
| `Electronic Components` | Hardware: Camera, Sensors, MicroController, Lights, Speakers |
| `AGV Specifications` | Physical characteristics (wheel type, dimensions) |
| `Load Specifications` | Load properties such as weight (measured by onboard sensors) |

### Automatic AGV Classification

AGVs are **automatically classified** by the reasoner into size categories based on data properties — no manual class assignment needed:

| Class | Payload (kg) | Max Speed (m/s) | Width (m) |
|---|---|---|---|
| Large | 10,000 – 70,000 | 0.1 – 0.5 | ≥ 3.0 |
| Medium | 1,000 – 5,000 | ≤ 1.7 | ≤ 1.2 |
| Small | 200 – 500 | 0.5 – 1.0 | ≤ 1.0 |

### Key Property Constraints

- **Functional properties** — `hasLoad`, `doingTask`, `inSite`, `inZone`, `AGV_specif` enforce that an AGV can only be in one place, doing one thing, carrying one type of load at a time.
- **Non-functional properties** — `hasCamera`, `hasSensor`, etc. allow multiple components per AGV, reflecting real hardware architecture (e.g. front and rear cameras).
- **Asymmetric & Irreflexive** — All major object properties prevent illogical inferences (e.g. a Mine cannot be `inSite` an AGV).

---

## Validation & Reasoning

The ontology was validated with three representative AGV individuals:

| Individual | Class | Payload | Location | Task |
|---|---|---|---|---|
| BelAZ | Large | 20,000 kg | Excavation Site | Waiting |
| Komatsu | Medium | 4,000 kg | Mine | Loading (iron ore) |
| CAT | Small | 300 kg | Disposal Site | Dumping (waste) |

### Example DL Query

Retrieve all AGVs carrying ore with a specified weight:

```
AGV and carriesLoad some (Ore and load_specif some (load_weight and weight_value some xsd:decimal))
```

This correctly returns `Komatsu`, validating the full property chain across multiple class hierarchies.

### Consistency Checking

Deliberately assigning both `hasElements(iron)` and `hasWaste(mud)` to the same individual triggers a reasoner error — confirming that the disjoint `Load` subclasses are correctly enforced.

---

## How to Open

1. Download and install [Protégé](https://protege.stanford.edu/)
2. Open `AGV_Ontology.rdf` via **File → Open**
3. Run the reasoner (**Reasoner → HermiT** or **Pellet**) to see automatic classifications and consistency checks
4. Use the **DL Query** tab to run custom queries against the ontology

---

## Design Decisions

A few notable trade-offs made during development:

- **Load weight vs. payload capacity** — a formal numeric constraint (`load_weight ≤ hasPayLoadCapacity`) was intentionally omitted. OWL numeric comparisons require SWRL rules and add significant complexity; in practice this is handled by external weighbridge systems.
- **State modeling** — no dedicated `State` class was introduced. AGV operational state is inferred from the combination of `Task`, `Load`, and `Zone` properties, avoiding a combinatorial explosion of subclasses.
- **Scope** — sensor fusion, battery management, maintenance scheduling, and HMI design were deliberately excluded to keep the ontology focused on coordination and task assignment reasoning.

---

## References

- Noy, N. F. & McGuinness, D. L. *Ontology Development 101: A Guide to Creating Your First Ontology*. Stanford KSL Technical Report, 2001.
- Sun, Y. et al. *Autonomous Driving of Trackless Transport Vehicles: A Case Study in Underground Mines*. Sensors 25.10, 2025.
