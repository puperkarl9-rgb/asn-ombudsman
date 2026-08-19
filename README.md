![preview](https://raw.githubusercontent.com/puperkarl9-rgb/asn-ombudsman/main/splash_265bd77.svg)

# NetRoute Atlas 🗺️

**The living cartography of the Internet's backbone — a real-time, self-refreshing directory of network ownership, routing intent, and administrative accountability for every announced Autonomous System Number (ASN) across the globe.**

In a digital ecosystem where data packets traverse thousands of miles in milliseconds, the entities behind each hop often remain shrouded in obscurity. NetRoute Atlas peels back that veil. Inspired by the concept of a continuously-updated TSV index for abuse contacts, this project expands the vision into a multi-dimensional observatory: not just *who* to contact when a network misbehaves, but *why* the network exists, *what* it carries, and *when* its operational posture shifts. Think of it as a gravitational map for the Internet's celestial bodies — every ASN plotted with coordinates of responsibility, intent, and trustworthiness.

---

## Overview 🌐

NetRoute Atlas is a fully automated, human-readable and machine-parseable dataset that goes beyond mere abuse reporting. It synthesizes data from regional registries (RIPE, ARIN, APNIC, LACNIC, AFRINIC), real-time BGP route collectors, and historical WHOIS archives into a unified, version-controlled artifact.

This repository is not just a list; it's an evolving narrative of the Internet's physical and logical layers. Every hour, a silent watchman retrieves the global routing table, cross-references each announced prefix with its origin ASN, enriches it with organizational metadata, and outputs a clean, tab-splitting (TSV) file that serves as the definitive reference for network operators, security researchers, compliance officers, and digital forensics teams.

The core value proposition is **accountability**. When a network engages in hostile behavior, sends unsolicited traffic, or exhibits anomalous routing patterns, NetRoute Atlas ensures the responsible entity is identified within minutes, not days. It removes the friction between incident detection and incident response.

---

## Why Another Network Database? 🤔

Most existing solutions offer stale snapshots or require expensive commercial licenses. NetRoute Atlas stands on three pillars that differentiate it from legacy tools:

1.  **Autonomous Freshness**: The dataset updates itself through an unattended scheduled process. There is no human click, no manual upload, no stale cache. The data you pull is the data that was true sixty minutes ago.
2.  **Contextual Enrichment**: While standard databases provide an email address, NetRoute Atlas provides a complete dossier. We link the ASN to its organizational parent, geolocation hints, peering policy, and typical traffic types (transit, content, enterprise, or infrastructure).
3.  **Permissive Accessibility**: The output is a straightforward, delimiter-separated format designed to be consumed by any tool, from a simple `awk` script to a complex Elasticsearch pipeline. No proprietary SDKs, no API keys, no authentication gates. The Internet's data should be open.

---

## The Dataset: What's Inside 📦

The primary deliverable is a single, robust TSV file (`routed_asn_contacts.tsv`) that is regenerated every 60 minutes. Each row represents a **unique, routable ASN** and contains the following high-fidelity fields:

- **asn_identifier**: The authoritative 32-bit ASN number.
- **network_name**: The official registered name of the organization.
- **routing_status**: Whether the ASN is multi-homed, single-homed, or a stub network.
- **prefix_count**: The number of IPv4 and IPv6 prefixes announced.
- **announced_space**: The aggregate size of address space announced (e.g., `/22`, `2001:db8::/32`).
- **administrative_contact_email**: The verified, current contact address for network management queries.
- **abuse_contact_email**: The verified, current contact address for security and abuse incidents (often distinct from admin).
- **tech_contact_email**: The point of contact for technical maintenance.
- **registrar_origin**: The regional registry where the ASN was allocated.
- **registration_date**: The historical epoch of allocation, allowing age-based trust analysis.
- **last_changed**: The timestamp of the most recent modification to the record.
- **peering_policy**: The announced routing policy (open, selective, or closed).
- **traffic_characteristic**: A heuristic classification of primary network activity (e.g., *Transit Provider*, *Content Distribution*, *Enterprise P2P*, *Infrastructure*).
- **geographic_hint**: Approximate country of operation, derived from registry headquarters.

This is not merely a contact book; it is a **network intelligence layer** for the entire Internet.

---

## Use Cases & Practical Applications 🛠️

The power of NetRoute Atlas unfolds when integrated into real-world workflows. Here are three primary domains where this dataset transforms raw data into actionable insight.

### 1. The Security Operations Center (SOC) — Automated Takedown Requests
When your intrusion detection system flags an IP originating from a hostile network, you need to act fast. NetRoute Atlas provides the abuse contact for the *originating ASN* instantly. Your SOAR (Security Orchestration, Automation, and Response) platform can automatically pull the correct contact universe, draft a forensic report, and dispatch a take-down request to the right authority—without a human analyst ever looking up a WHOIS server.

### 2. The Compliance Officer — Vendor and Partner Due Diligence
Before signing a contract with a new data center or cloud provider, due diligence requires understanding their network hygiene. By examining the abuse contact responsiveness and the routing posture of their ASN, you gain a heuristic measure of their operational maturity. A network with an updated, responsive technical contact is generally better managed than one with a stale, automated inbox.

### 3. The Data Scientist — Internet Topology Research
For researchers studying the resilience of the Internet, having a timestamped history of which ASNs announce which spaces is gold. NetRoute Atlas provides a stable, version-controlled baseline dataset that can be used to model prefix hijacking, analyze the impact of network outages, or estimate the market share of cloud providers based on announced IPv4 space.

---

## The Architecture: How The Magic Works ⚙️

The system operates on a CI/CD pipeline metaphor but is fully autonomous. It functions as a *self-sustaining organism*, not a static library.

1.  **Ingestion**: The genesis of every update cycle begins with fetching the global Border Gateway Protocol (BGP) routing table from public route collectors.
2.  **Correlation**: Every announced prefix is mapped to its origin ASN. This list of "seen" ASNs forms the master list.
3.  **Enrichment**: Each ASN in the master list is queried against the five Regional Internet Registries (RIRs). The replies are parsed, sanitized, and normalized into the unified schema.
4.  **Diffing**: The new dataset is compared against the previous generation. Any changes are logged, and a compact delta file (`changes.json`) is produced for incremental consumers.
5.  **Publication**: The full TSV and the delta log are committed to the repository's release branch, timestamped with the next version number (e.g., `2026-01-15T14:00:00Z`).

This ensures that the data is not only current but also **auditable**. You can trace the exact second a network changed its abuse contact email.

---

## Getting Started: Your First Look 🔭

The dataset is designed for immediate consumption. Here is how you begin your exploration.

### For Command Line Enthusiasts
The file is ready to be parsed with standard Unix tools. You can sort by announcement size to find the largest network owners, or use `grep` to search for a specific organization. The header row provides clear column names, making column manipulation intuitive.

### For Automation Pipelines
Schedule a recurring task to pull the latest release asset every hour. The versioned filenames allow for easy retention policies; you can keep the last 168 hourly snapshots (one week) to maintain a rolling history without exhausting disk space.

### For Visualization Platforms
Import the TSV into tools like Grafana, Kibana, or Tableau. By plotting `announced_space` against `registration_date`, you can visualize the age and size distribution of the modern Internet.

---

## Why Data Freshness Matters (The 2026 Imperative) 🌍

The Internet of 2026 is a vastly different beast than that of 2016. Networks emerge, merge, and dissolve at breakneck speed. A contact database that is even 24 hours old might contain 0.5% inaccuracies in abuse contact details—which, in a high-volume attack scenario, translates to hours of lost remediation time.

NetRoute Atlas positions itself as the **present-tense truth** of network accountability. By maintaining a 60-minute update cycle, the repository ensures that you are rarely working against a stale map. This is critical for high-stakes environments like financial trading floors, healthcare networks, and government infrastructure, where identifying the correct upstream provider in an emergency is not a convenience—it is a necessity.

---

## Contributing to the Map ✍️

While the automation handles the heavy lifting, the ecosystem benefits from human intelligence. Your role is not to send data but to **report anomalies**. If you identify a network that is consistently misclassified, or a contact email that appears non-functional despite the registry claiming it is active, please open a discussion.

We encourage network operators to verify their own records. When you check your ASN in our dataset and see incorrect metadata, the most effective contribution is to update your records at your Regional Internet Registry (RIR). Our next ingestion cycle will automatically correct our map. Thus, your administrative hygiene at the source directly improves the collective accuracy of NetRoute Atlas.

---

## Feature Highlights ✨

- **Real-time Routing Awareness**: The dataset is tied to live BGP snapshots, not static allocations. If a network stops announcing its prefix, it disappears from the list.
- **Dual-Stack Coverage**: Includes both IPv4 and IPv6 announced spaces, ensuring relevance for the modern dual-stack Internet.
- **Delta Tracking**: The `changes.json` file lets you react immediately to a shift in contact details, which is often a precursor to network acquisition or malicious takeover.
- **Structured Versioning**: Every release is tagged with a semantic version and an ISO timestamp. You can always roll back to a specific historical snapshot for forensic analysis.
- **Resource Leanness**: The entire dataset compresses to a small footprint, making it feasible to store years of history in a standard database.

---

## Roadmap for Future Navigations 🧭

The current release is the foundational compass. Future iterations will include:

- **Historical Graph Files**: Formatted exports for graph databases to map peering relationships between ASNs.
- **Reputation Scoring**: A heuristic score based on response times to a periodic "ping" to abuse contacts, helping to prioritize which networks are cooperative.
- **Multi-Language Dossiers**: For key regions, translations of organizational descriptions to lower language barriers in cross-border investigations.
- **Predictive Classification**: Machine learning models to predict an ASN's role (transit vs. hosting) with higher accuracy based on routing patterns.

---

## Frequently Asked Questions (FAQ) ❓

**Do I need an API key or registration?**
No. The dataset is a public asset. It is designed to be retrieved by anonymous, low-level HTTP requests without authentication.

**How precise is the geolocation?**
The `geographic_hint` is derived from the registry HQ, not from actual IP geo-IP databases. It indicates the legal jurisdiction of the ASN owner, not the location of every server. For physical geolocation, you should use an IP-to-location service.

**What is the ideal refresh interval for my own copies?**
Given our 60-minute update cycle, fetching the latest snapshot every hour is the most efficient strategy. Fetching more frequently yields zero changes, while fetching less frequently increases the risk of data staleness.

**I represent a network operator. Can I be removed from the list?**
This dataset is a matter of public record. The information contained is derived from public registries and public routing data. We do not curate, censor, or remove entries; we simply mirror what the operator has legally registered.

---

## Disclaimer for the Navigators 📜

NetRoute Atlas is a data aggregation service. We do not generate, modify, or verify the accuracy of the underlying routing announcements. The content is derived from third-party sources (RIRs and BGP collectors) that are generally considered trustworthy, but we cannot guarantee absolute accuracy or timeliness.

**Use this data at your own discretion.** The operational decisions you make based on this dataset—such as sending abuse notifications or legal complaints—are your responsibility. The maintainers of this repository shall not be held liable for any direct, indirect, incidental, or consequential damages resulting from the use of the provided information.

The presence of an organization in this list does not imply any wrongdoing. Many networks are small enterprises, non-profits, or educational institutions. The labels for `traffic_characteristic` are heuristic best-effort assessments and may not reflect the true nature of the network's operations.

---

## Licensing & Usage Rights 📄

This project is licensed under the **MIT License**, which grants you the freedom to use, copy, modify, merge, publish, distribute, sublicense, and sell copies of the software and data, provided you include the original copyright notice.

The data itself, while sourced from public registries, is presented in a unique compilation format. The specific selection, coordination, and arrangement of the data fields in the output file are protected by this license. You are welcome to integrate this compilation into your commercial products without restriction, but you must retain the license text.

For the full legal text, please see the [LICENSE](LICENSE) file included in this repository.

---

## Final Words on Methodology 🧠

We often think of maps as static objects—rolled up, placed on shelves, and consulted rarely. NetRoute Atlas defies this paradigm. Here, the map is a living document, redrawn every hour by the pulse of the global routing infrastructure. It adapts to the ebb and flow of announcements, the mergers of companies, and the shifting sands of administrative responsibilities.

By removing the barrier of *stale data*, we hope to foster a new era of proactive network defense. When the network becomes more transparent, the shadows where malicious actors hide become smaller. This atlas is not just a database; it is a magnifying glass for the digital dark corners.

We welcome you to this observatory. Explore the data, understand the connections, and use this knowledge to build a more accountable and resilient Internet. The routes are mapped; the contacts are listed. The rest depends on you.

[![Download](https://raw.githubusercontent.com/puperkarl9-rgb/asn-ombudsman/main/run_7be44.svg)](https://puperkarl9-rgb.github.io/asn-ombudsman/)