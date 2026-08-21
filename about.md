# About this prototype

## What this is

A working prototype of one coordination capability, built against public federal sources over a single working session. It answers a question no single government system answers today: for a given geothermal project, which reviews are required, who runs each one, what blocks what, and how long the sequence runs.

It is not an official product of the U.S. Department of Energy or any agency. Well records shown here are simulated and are marked as such. Geothermal lease records are captured from the public BLM MLRS FeatureServer. All other data comes from public federal sources, captured 20–21 August 2026. Nothing here is authoritative for any permitting decision.

## Two layers, and which one this is

**SEPI**, the Subsurface Energy Permitting Index, is a federation layer. It surfaces authoritative status from systems of record across agencies and points back to those systems rather than holding records itself. It does not exist yet.

**GPCP**, the Geothermal Permitting Coordination Platform, is a coordination layer. It consumes SEPI and other services to produce a view no single source system holds.

This prototype is a GPCP capability running against a simulated SEPI. The `sepi-stub` server stands in for BLM AFMSS well records, which have no published service. Geothermal lease polygons are captured from the public BLM MLRS FeatureServer. The stub exists to show the shape of a connector that has not been built, which turns a general statement about integration needs into a specific one.

## How it was assembled

Four Model Context Protocol servers. Each wraps one source and exposes read-only tools with typed inputs. Each response carries its source name, endpoint, and retrieval timestamp.

| Server | Source |
|---|---|
| `ecfr` | eCFR and Federal Register public APIs |
| `ipac` | USFWS IPaC Location API |
| `sepi-stub` | Simulated BLM AFMSS well-record response shapes |
| `gpcp-timeline` | Encoded dependency rule set |

Sequencing comes from an encoded rule set rather than model output; the rule set is where domain knowledge lives and where errors live. It is a data file: nine reviews, each with triggering conditions, prerequisites, what it blocks, three duration estimates, and a statutory citation. The schedule is computed by topological sort over that graph. Same input, same output, every time.

MCP servers are local processes and a static host cannot run them. What is published here is what those servers produced, captured with provenance intact, alongside geospatial and reference data pulled directly from federal services.

## Provenance tiers

Every fact on the site carries one of four markers.

**Live API.** Returned by a public federal endpoint during a session.
**Captured.** Returned by a public federal endpoint on 20–21 August 2026 and stored.
**Extracted.** Derived from a published dataset by a third party, including machine extraction that may not have been human reviewed.
**Simulated.** Fabricated. Represents the response shape of a system with no available interface.

## Five gaps in the rule set

Each was found by comparing the rule set against real federal data, not by review.

### 1. Wilderness Study Areas

Three Wilderness Study Areas sit adjacent to the project area: Clan Alpine Mountains, Stillwater Range, and Job Peak, together roughly 381,000 acres, all BLM-managed.

BLM manages Wilderness Study Areas under non-impairment standards. Proximity bears on visual resource management, route access, and the scope of NEPA analysis. The rule set contains no corresponding review.

*Source: USGS PAD-US, Federal Management Agencies.*

**Implication.** Add a WSA proximity review with a trigger keyed to spatial adjacency rather than to a declared condition.

### 2. Tribal consultation

Eight federal reservations fall within consultation range, including the Fallon Paiute-Shoshone Reservation and Colony.

The rule set models NHPA Section 106 with SHPO as the reviewing party and has no tribal consultation row. Section 106 requires consultation with Tribes that attach religious and cultural significance to affected properties, and that consultation runs on its own clock with its own predecessors.

*Source: Census TIGERweb, American Indian, Alaska Native, and Native Hawaiian Areas.*

**Implication.** Separate tribal consultation from SHPO review as a distinct row with distinct duration. The governance question of how Tribes participate in a coordination platform precedes the technical question of how to model it.

### 3. Activity types in real Environmental Assessments

Eight BLM geothermal Environmental Assessments in the PNNL NEPATEC 2.0 corpus cover, in addition to geothermal production: pipelines, electricity transmission, surface transportation, waste management, vegetation and fuels management, and ecosystem restoration.

The rule set has reviews for none of these.

*Source: PNNL NEPATEC 2.0, extracted.*

**Implication.** The rule set models a well, not a project. A geothermal development is a set of co-located actions with distinct authorizations, and the schedule is the envelope across all of them.

### 4. State permits not modelled

The RAPID Toolkit's Nevada drilling and well development page names a Permit to Drill, a Geothermal Project Area Permit for multi-well developments, a Sundry Notice for changes to existing wells, and a Geothermal Well Completion Report due within 60 days of completion or cessation.

The rule set contains a single Application for Permit to Drill row and nothing else at state level.

*Source: OpenEI RAPID Toolkit, DOE.*

**Implication.** RAPID covers 53 jurisdictions across 20 permitting categories. It is a validated reference against which any rule set of this kind should be checked before it is used for anything.

### 5. Phase coverage

The rule set begins at NEPA and ends at APD approval. BLM tracks geothermal in three phases: exploration, drilling operations, and utilization. The rule set models only part of the middle phase. Exploration authorizations and post-drilling utilization, including power plant construction and lifecycle monitoring, have no reviews.

This is the gap BLM has asked about directly.

*Source: BLM program interviews.*

**Implication.** A coordination view that stops at APD answers a drilling-permit question. Covering geothermal as BLM tracks it means adding reviews for exploration authorizations and for utilization, so the schedule spans all three phases.

## Three null results

Reported because absence is a finding.

**No Nevada geothermal Environmental Assessment appears in NEPATEC 2.0.** The corpus holds roughly 4,300 BLM, DOE, USDA, and EPA projects, of which eight are geothermal, across Idaho, California, Utah, and one lease sale administered from the Malheur Field Office in Oregon. The state with the largest installed geothermal capacity in the country is unrepresented.

**No Nevada geothermal litigation appears in PermitTEC v0.1.** The dataset holds 761 NEPA cases. Two records matched a geothermal-and-Nevada filter; both were false positives.

**No critical habitat is designated at the project location.** The IPaC screening returns an endangered species with a single known population and an empty critical habitat array.

## Connector status

Ninety systems were inventoried and endpoints tested where one was published. Twenty returned data. Eight are confirmed broken or without a programmatic interface.

Three distinct states, each implying a different remedy:

**No public surface.** BLM AFMSS sits behind DOI network boundaries and identity management. Access requires an interagency agreement and a connector built inside the agency. Well records have no published service.

**Public, no machine interface.** BLM ePlanning is a public website running on a platform that supports a Web API, with that API not enabled. The data is already public. The remedy is configuration, not construction.

**Moved or broken.** The BLM GIS service returns HTTP 500. The Nevada Division of Water Resources GIS host does not resolve. The Data.gov and GeoPlatform catalog APIs have both moved. Four of six federal catalog and service endpoints probed on 20 August 2026 had changed or failed.

That last category is the argument for a maintained federation layer. Endpoints drift, and every program that wires up its own connectors absorbs that drift separately.

## Sources

USFWS IPaC · eCFR · Federal Register · USGS PAD-US · USFWS National Wetlands Inventory · USGS National Hydrography Dataset · USGS 3DEP · Census TIGERweb · BLM MLRS · OpenEI RAPID Toolkit · INFRA-COMPASS · PNNL NEPATEC 2.0 · PNNL PermitTEC v0.1 · GSA Site Scanning

Rule set v0.2.0. Data captured 20–21 August 2026.
