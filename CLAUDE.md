# CLAUDE.md

## Overview

This repository contains threat catalogs for the [FINOS Common Cloud Controls](https://commoncloudcontrols.com) project. Each `threats.yaml` file conforms to the Gemara `ThreatCatalog` schema.

## Gemara MCP Server

This repo includes a `.mcp.json` that configures the Gemara MCP server. Use it to **validate catalog files** against the schema during editing:

- `validate_gemara_artifact` — validate a threats.yaml file against the Gemara schema
- `migrate_gemara_artifact` — migrate a file to a newer schema version
- `gemara://schema/definitions` — browse the schema type definitions
- `gemara://lexicon` — look up Gemara terminology

**Always validate after making changes** to a threats.yaml file.

## File Structure

```
<category>/<service>/threats.yaml
```

Each file has:
- `imports:` — references to core threats (`CCC.Core.Threats`)
- `threats:` — flat list of service-specific threat entries

## Entry Structure

```yaml
threats:
  - id: CCC.<Service>.TH<nn>
    title: Human-readable title
    group: <GroupID>
    description: |
      What could go wrong.
    capabilities:
      - reference-id: CCC.<Service>.Capabilities
        entries:
          - reference-id: CCC.<Service>.CP<nn>
    external-mappings:
      - reference-id: MITRE-ATT&CK
        entries:
          - reference-id: T<nnnn>
```

## Groups

Every threat must have a `group:` field. The group describes what security domain the entry belongs to, not what service it's part of.

| Group ID | Use When The Threat Is About... |
|---|---|
| `Encryption` | Key compromise, TLS downgrade, certificate expiry |
| `Access` | Unauthorized access, credential theft, privilege escalation |
| `Observability` | Log tampering, metric manipulation, alert suppression |
| `Data` | Data loss, corruption, exfiltration, replication issues |
| `Resource` | Resource exhaustion, tag manipulation, version rollback |
| `Compute` | Runtime exploitation, code injection, container escape |
| `Ingestion` | Poisoned input, prompt injection, malicious data |
| `Networking` | Network exposure, traffic interception, firewall bypass |
| `Orchestration` | Pipeline hijacking, unauthorized builds |
| `Processing` | Data transformation failures, lineage loss |
| `Messaging` | Message replay, ordering manipulation |
| `MachineLearning` | Model tampering, version drift, unreliable output |

**Decision rule:** Ask "what goes wrong if this threat materializes?" and pick the group that matches the answer.
