# Threat Catalog Style Guide

This guide defines the writing conventions for entries in CCC service-specific threat catalogs. These threats build on the [core catalog](../core-catalog/) and describe risks unique to a particular service type.

---

## General Conventions

### Voice and Tone

- Write in a professional, technical tone suitable for a security-engineering audience.
- Use present tense throughout.
- Be precise and specific — avoid vague language like "properly", "appropriately", or "as needed".
- Avoid marketing language or subjective qualifiers ("best-in-class", "robust", "comprehensive").
- Use modal verbs ("may", "can", "could") when describing threat impact — threats describe what _could_ happen, not what _will_ happen.

### Title Case

All titles use Title Case. Capitalize every word except:

- Articles: a, an, the
- Short prepositions (four letters or fewer): in, of, for, to, at, by, on, via, with
- Conjunctions: and, or, but, nor

Always capitalize the first and last word regardless of part of speech. Examples: "Data Exfiltration via Insecure Lifecycle Policies", "Key Rotation is Disabled or Delayed Beyond Policy Limits".

### Formatting

- Use the YAML block literal (`|`) for any multi-line text field (`description`).
- End all sentences with a period.
- Do not end titles with a period or any trailing punctuation.
- Use one blank line between paragraphs within a field, not between sentences.
- Keep entries to the minimum words needed to convey the risk clearly.

---

## Threat Titles

- Use **Title Case** (see [Title Case](#title-case) above).
- Write as a descriptive phrase stating **what goes wrong**, not what should be prevented.
- Use present tense describing the threat condition.
- Keep to 4-12 words.

Common title patterns:

| Pattern | Example |
|---|---|
| "[Thing] is [Bad State]" | "Access is Granted to Unauthorized Users" |
| "[Thing] [Verb]s [Consequence]" | "Bootstrap Scripts Introduce Unintended Behavior" |
| "[Bad Action] via [Mechanism]" | "Data Exfiltration via Insecure Lifecycle Policies" |
| "[Thing] is [Disabled/Missing]" | "Key Rotation is Disabled or Delayed Beyond Policy Limits" |
| Noun phrase naming the attack | "Prompt Injection" |

**Good:** "Images Contain Vulnerabilities"
**Good:** "Data Exfiltration via Insecure Lifecycle Policies"
**Bad:** "Prevent Image Vulnerabilities" (imperative — that's a control title)
**Bad:** "Bad images" (too vague, not title case)

---

## Description

- Write 2-4 complete sentences.
- Follow a **cause-and-effect structure**:
  1. **First sentence:** Describe the misconfiguration, weakness, or attack vector.
  2. **Subsequent sentences:** Explain the consequence and potential impact.
- Use modal verbs to express likelihood: "may", "can", "could".
- Name specific outcomes: "loss of availability", "exposure of sensitive data", "irreversible data loss", "service interruption", "unintended disclosure".
- Include concrete details about the attack mechanism where helpful.

**Example:**

```yaml
description: |
  Misconfigured lifecycle policies may allow objects to be deleted or
  transitioned to inaccessible storage classes before their intended
  retention period expires. This can result in irreversible data loss
  or compliance violations if regulated data is removed prematurely.
```

**Example with attack mechanism:**

```yaml
description: |
  Prompt injection may occur when crafted input manipulates the
  behavior of a generative AI model, causing it to ignore its system
  instructions or reveal sensitive information. This can lead to
  unintended disclosure of training data, bypass of content filters,
  or execution of actions outside the model's intended scope.
```

### Description vs. Title

The title names the threat; the description explains it. Do not repeat the title verbatim in the description. The description should add context that the title alone cannot convey.

---

## Group

Every threat must have a `group` field. The group describes the security or operational domain the threat targets — not which service it is part of.

Assign the group by asking: **"What goes wrong if this threat materializes?"**

- Unauthorized people get access → `Access`
- Data is exposed in transit or at rest → `Encryption`
- We lose visibility into what happened → `Observability`
- Data is lost, corrupted, or leaked → `CCC.Core.Data`
- Resources are misconfigured or exhausted → `CCC.Core.Resource`
- Network traffic is misrouted or exposed → `CCC.Core.Networking`
- Models produce wrong results or aren't governed → `CCC.Core.MachineLearning`

When a threat spans two domains, pick the one that best describes its _primary impact_. For example, a threat about expired TLS certificates belongs in `Encryption`, not `CCC.Core.Networking`.

See the [group assignment guide](../CLAUDE.md#group-assignment-guide) for the full list of available groups with descriptions.

---

## Capability Mappings

When mapping threats to capabilities in the `capabilities` section:

- Group entries under the correct parent `reference-id` (e.g., `CCC.ObjStor.Capabilities`, `CCC.Core.Capabilities`).
- If entries come from multiple service prefixes, split them into separate groups.
- The `remarks` field should briefly explain how the capability relates to the threat.
- Set `strength` to `0` with a comment `# Not yet specified` until the mapping strength has been assessed.

**Example:**

```yaml
capabilities:
  - reference-id: CCC.ObjStor.Capabilities
    entries:
      - reference-id: CCC.ObjStor.CP03
        remarks: Lifecycle management policies
        strength: 0 # Not yet specified
  - reference-id: CCC.Core.Capabilities
    entries:
      - reference-id: CCC.Core.CP08
        remarks: Configurable data retention period
        strength: 0 # Not yet specified
```

---

## External Mappings

When mapping threats to external frameworks in the `external-mappings` section:

- Use the canonical framework identifier as `reference-id` (e.g., `MITRE-ATT&CK`, `FINOS-AIGF`, `OWASP-LLM-TOP10`, `MITRE-ATLAS`).
- The `remarks` field should name the specific technique, tactic, or category from the framework.
- Include all relevant mappings — a single threat may map to multiple entries across multiple frameworks.

---

## Imports

When referencing core threats in the `imports` section:

- The `remarks` field should match or closely paraphrase the core threat's title.
- Keep remarks concise — they are a label, not a description.

**Example:**

```yaml
imports:
  - reference-id: CCC.Core.Threats
    entries:
      - reference-id: CCC.Core.TH01
        remarks: Access Control is Misconfigured
```

---

## Service-Specific Language

Unlike the core catalog, service-specific threats should name the service domain directly. Use the canonical terminology for the service type:

| Service | Use terms like |
|---|---|
| Object Storage | buckets, objects, lifecycle policies, retention |
| Key Management | keys, key versions, key rings, rotation schedules |
| Load Balancer | listeners, backends, health checks, routing rules |
| Virtual Machines | instances, images, boot disks, metadata service |
| IAM | principals, roles, policies, credentials |
| GenAI | models, prompts, inference, training data, fine-tuning |

Avoid cloud-provider-specific product names (S3, GCS, ALB, CloudFront). Use generic service-type terms instead. Note that "KMS" is acceptable as a generic abbreviation for key management service.
