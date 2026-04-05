# Contributing to CCC Threat Catalogs

## Overview

This repository contains the threat catalogs for the [FINOS Common Cloud Controls](https://www.finos.org/common-cloud-controls-project) project. Each `threats.yaml` file conforms to the Gemara [`ThreatCatalog`](https://github.com/gemaraproj/gemara) schema — a standardized, machine-readable data model for GRC engineering.

At release time, the [CCC delivery toolkit](https://github.com/finos/common-cloud-controls/tree/main/delivery-toolkit) ingests these files using the [`go-gemara`](https://github.com/gemaraproj/go-gemara) Go SDK and converts them to Markdown artifacts for publication.

## Repository Structure

Threats are organised by service domain and type:

```
<domain>/<service-type>/threats.yaml
```

For example:

```
storage/object/threats.yaml
crypto/key/threats.yaml
compute/virtual-machines/threats.yaml
```

## Schema

Two top-level keys should be present in every artifact: `threats` and `imports`.

### `threats`

A list of threats native to this service type. Each entry requires:

| Field | Type | Description |
|---|---|---|
| `id` | string | Unique identifier in the form `CCC.<ServiceCode>.TH<NN>` |
| `title` | string | Short label for the threat |
| `description` | string | Description of the threat and its potential impact |

Example:

```yaml
threats:
  - id: CCC.ObjStor.TH01
    title: Bucket Exposed via Overly Permissive Policy
    description: |
      An object storage bucket may be inadvertently made public or accessible
      to unintended principals through a misconfigured bucket policy or ACL,
      leading to data exposure.
```

### `imports`

An optional list of references to threats defined in [core-catalog](https://github.com/common-cloud-controls/core-catalog) that also apply to this service type. Each entry requires a `reference-id` pointing to the source catalog and a list of `entries`, each with a `reference-id` and a `remarks` field summarising applicability.

```yaml
imports:
  - reference-id: CCC
    entries:
      - reference-id: CCC.Core.TH01
        remarks: Access Control is Misconfigured
      - reference-id: CCC.Core.TH02
        remarks: Data is Intercepted in Transit
```

> **Note:** Catalog-level `metadata` (title, version, publication date, etc.) is not authored here — it is populated automatically at release time by the delivery toolkit.

## Adding a New Threat

1. Locate the relevant `threats.yaml` for the service type (e.g. `storage/object/threats.yaml`).
2. Append a new entry to the `threats` list. Assign the next available `TH` number for that service code.
3. Ensure `id`, `title`, and `description` are all present.
4. If the threat is a general cloud platform concern already described in [core-catalog](https://github.com/common-cloud-controls/core-catalog), add a reference under `imports` instead of duplicating the definition.

## Adding a New Service Type

1. Create a new directory under the appropriate domain (e.g. `networking/cdn/`).
2. Add a `threats.yaml` file with a `threats` list. Follow the ID convention `CCC.<ServiceCode>.TH<NN>`, choosing a unique service code.
3. Add any applicable core threat references under `imports`.
4. Open a pull request — the catalog title, metadata, and group assignments are resolved by the delivery toolkit and do not need to be authored manually.

## Validation

Threats can be validated locally against the Gemara CUE schema using the [CUE CLI](https://cuelang.org/docs/install/):

```sh
cue vet --schema '#ThreatCatalog' github.com/gemaraproj/gemara <path>/threats.yaml
```

## References

- [Gemara schema](https://github.com/gemaraproj/gemara) — CUE schema definitions including `ThreatCatalog`
- [go-gemara](https://github.com/gemaraproj/go-gemara) — Go SDK used by the delivery toolkit to parse and convert catalogs
- [gemara.openssf.org](https://gemara.openssf.org) — full model documentation
