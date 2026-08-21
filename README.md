# Geothermal permitting coordination, MCP servers

Seven Model Context Protocol servers that answer a question no single federal system answers: for a given geothermal project, which reviews are required, who runs each one, what blocks what, and how long the sequence runs.

Built against public federal sources. No agency credentials, no private data, no network access beyond published endpoints.

Not an official product of the U.S. Department of Energy or any agency. Well records returned by `sepi-stub` are fabricated and marked as such. Nothing here is authoritative for any permitting decision.

This repository is the captured static site. Live: [mcp1971.thermalunderground.org](https://mcp1971.thermalunderground.org)

---

## Servers

| Server | Tools | Source |
|---|---|---|
| `mlrs` | `get_leases_by_area`, `get_lease_by_serial`, `count_leases_by_state` | BLM MLRS geothermal leases, public geospatial service |
| `sepi-stub` | `get_authorization_status`, `get_well_records` | Fabricated. AFMSS response shape, no public interface exists |
| `ipac` | `screen_species` | USFWS IPaC Location API |
| `spatial` | `screen_location`, `list_sources` | Composite: PAD-US, Census TIGERweb, USFWS NWI, USGS NHD, BLM MLRS |
| `ecfr` | `get_cfr_part`, `check_pending_rulemaking` | eCFR and Federal Register APIs |
| `rapid` | `list_jurisdictions`, `get_topic`, `get_jurisdiction_summary` | OpenEI RAPID Toolkit, 53 jurisdictions |
| `gpcp-timeline` | `build_timeline`, `list_rules` | Encoded dependency rule set |

Every response carries `source`, `url`, and `retrieved`.

MCP servers are local processes and a static host cannot run them. What is published here is what those servers produced, captured with provenance intact.

---

## Serve this site

Requires Node 20 or later.

```
npm run dev
```

Opens the captured timeline at `http://localhost:5173`.

---

## The rule set

`rules/dependencies.json` is the substance. Nine reviews, each with:

- triggering conditions
- prerequisites
- what it blocks
- low, typical, and high duration estimates
- statutory citation

`gpcp-timeline` computes the schedule by topological sort over that graph. Earliest start of each review is the maximum finish of everything blocking it; the longest path to the terminal node is the critical path. Deterministic. Not model output.

The rule set is a data file. Domain knowledge lives there, and so do errors. An early version placed ESA Section 7 consultation off the critical path with 230 days of float, because it modeled informal and formal consultation as parallel and omitted the biological assessment and its field survey seasons. The correction was to the rule set, not the code.

---

## Findings

The rule set was compared against real federal data. Five gaps, each with evidence.

**Wilderness Study Areas.** Three adjoin the test project area: Clan Alpine Mountains, Stillwater Range, Job Peak, roughly 381,000 acres, all BLM-managed under non-impairment standards. No corresponding review. *Source: USGS PAD-US.*

**Tribal consultation.** Five federal reservations fall within consultation range, including the Fallon Paiute-Shoshone Reservation and Colony. The rule set models NHPA Section 106 with SHPO only. *Source: Census TIGERweb.*

**Activity types.** Eight BLM geothermal Environmental Assessments in the PNNL NEPATEC 2.0 corpus cover pipelines, electricity transmission, surface transportation, waste management, and vegetation and fuels management. No corresponding reviews. The rule set models a well, not a project. *Source: PNNL NEPATEC 2.0.*

**State permits.** The RAPID Nevada drilling page names a Permit to Drill, a Geothermal Project Area Permit, a Sundry Notice, and a Geothermal Well Completion Report due within 60 days. The rule set has one APD row. *Source: OpenEI RAPID.*

**Phase coverage.** The rule set begins at NEPA and ends at APD approval. BLM tracks geothermal in three phases: exploration, drilling operations, and utilization. Exploration authorizations and post-drilling utilization have no reviews.

### Null results

Reported because absence is a finding.

- **No Nevada geothermal Environmental Assessment in NEPATEC 2.0.** Roughly 4,300 projects, eight geothermal, none in the state with the largest installed geothermal capacity.
- **No Nevada geothermal litigation in PermitTEC v0.1.** 761 NEPA cases; two apparent matches were false positives.
- **No designated critical habitat at the test location.** IPaC returns an endangered species with a single known population and an empty critical habitat array.

---

## Connection status

Ninety candidate systems were inventoried and endpoints tested where published. Twenty returned data. Three distinct failure modes, each implying a different remedy:

**No public surface.** BLM MLRS case management and AFMSS sit behind DOI network boundaries and identity management. Requires an interagency agreement and a connector built inside the agency. Note that MLRS *lease* records are public through a geospatial service while case management is not; a single system can span both states.

**Public, no machine interface.** BLM ePlanning publishes NEPA project records to the public on a platform that supports a Web API, with that API not enabled. The remedy is configuration, not construction.

**Moved or broken.** The BLM GIS service root returns HTTP 500 while individual service paths respond. The Nevada Division of Water Resources GIS host does not resolve. The Data.gov and GeoPlatform catalog APIs have both moved. Four of six federal catalog and service endpoints tested on 20 August 2026 had changed or failed.

That last category is the argument for a maintained federation layer. Endpoints drift, and every program wiring up its own connections absorbs that drift separately.

---

## Identifier resolution

Six identifier schemes appear across these sources: BLM MLRS case serial, IPaC location polygon, PAD-US `Source_PAID`, NEPATEC project hash, RAPID jurisdiction path, Census GEOID.

Spatial intersection is the only join available between them. There is no published crosswalk from a lease serial to a NEPA document, from a lease serial to a well record, or from an ePlanning project number to a case serial, in any direction.

---

## Sources

USFWS IPaC · eCFR · Federal Register · BLM MLRS geothermal leases · USGS PAD-US · USFWS National Wetlands Inventory · USGS National Hydrography Dataset · USGS 3DEP · Census TIGERweb · OpenEI RAPID Toolkit · INFRA-COMPASS · PNNL NEPATEC 2.0 · PNNL PermitTEC v0.1 · GSA Site Scanning

---

## DNS

At the `thermalunderground.org` registrar, add:

```
mcp1971    CNAME    adlerarcher.github.io
```

In this repo, GitHub Pages should use the custom domain `mcp1971.thermalunderground.org` with Enforce HTTPS. The `CNAME` file in the root is that domain.

---

## License

MIT.
