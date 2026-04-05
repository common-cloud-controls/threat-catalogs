# CCC Threat Catalogs

This repository contains the threat catalogs for the [FINOS Common Cloud Controls](https://www.finos.org/common-cloud-controls-project) project — machine-readable definitions of the threats applicable to each cloud service type.

Threats describe what can go wrong with a service, and are used to justify the controls defined in the corresponding [control-catalogs](https://github.com/common-cloud-controls/control-catalogs) repository. The capabilities they relate to are maintained in [capability-catalogs](https://github.com/common-cloud-controls/capability-catalogs).

## Repository Structure

Catalogs are organised by service domain and type:

```
<domain>/<service-type>/threats.yaml
```

For example:

```
storage/object/threats.yaml
compute/virtual-machines/threats.yaml
crypto/key/threats.yaml
```

Cross-cutting threats that apply to all service types are maintained separately in [core-catalog](https://github.com/common-cloud-controls/core-catalog) and referenced here via the `imports` key.

## Delivery

At release time, the [CCC delivery toolkit](https://github.com/finos/common-cloud-controls/tree/main/delivery-toolkit) ingests these files and produces versioned Markdown artifacts for publication.

## License

[Community Specification License 1.0](LICENSE)
