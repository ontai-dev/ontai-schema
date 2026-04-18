# ONT OpenAPI JSON Schema: v1alpha1

The versioned, importable community standard for the ONT platform API surface.
Published at `ontai.dev/schema/v1alpha1`.

---

## What This Is

This repository contains the ONT OpenAPI JSON Schema specification: a complete,
versioned definition of every CRD and reusable type in the ONT platform stack.

The schema covers four layers:

- **shared**: Reusable types embedded in CRD specs (SealedCausalChain, BindingStability,
  PhaseModel, RationaleField, GovernanceEvent, KubernetesMetadata).
- **domain-core**: Layer 0 abstract domain primitives under `core.ontai.dev`
  (DomainIdentity, DomainBoundary, DomainPolicy, DomainRelationship, DomainEvent,
  DomainWorkflow, DomainResource, DomainAudit, DomainSemanticNameService).
- **seam-core**: Infrastructure domain CRDs under `infrastructure.ontai.dev`
  (InfrastructureLineageIndex, SeamMembership, DSNSZone).
- **app-core**: Application layer CRDs under `app.ontai.dev`
  (AppBoundary, AppIdentity, AppPolicy, AppTopology, AppEventSchema, AppWorkflow,
  AppResourceProfile, AppAuditPolicy, AppProfile).

This is a publication artifact, not an implementation artifact. Community operators
import individual schemas or the full index to validate instances, generate clients,
or declare compatibility.

---

## How to Import

### Import a single schema

Reference any schema by its `$id` URI in another JSON Schema document:

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "properties": {
    "domainIdentityRef": {
      "$ref": "https://schema.ontai.dev/v1alpha1/domain-core/DomainIdentity.json"
    }
  }
}
```

### Import the full index

The index document is not a JSON Schema; it is a registry of all schemas in this
release. Load it to enumerate available schemas and resolve file paths:

```json
GET https://schema.ontai.dev/v1alpha1/index.json
```

The index has the following shape:

```json
{
  "version": "v1alpha1",
  "publishedAt": "2026-04-18",
  "stability": "alpha",
  "layers": {
    "shared": [ { "kind": "...", "file": "...", "stability": "...", "description": "...", "dependsOn": [] } ],
    "domain-core": [ ... ],
    "seam-core": [ ... ],
    "app-core": [ ... ]
  }
}
```

Each `file` path is relative to `v1alpha1/`.

---

## Layer Overview

| Layer       | API Group                    | Description                                                  |
|-------------|------------------------------|--------------------------------------------------------------|
| shared      | (reusable types)             | Embedded types used by CRDs in all layers                    |
| domain-core | `core.ontai.dev/v1alpha1`    | Layer 0 abstract domain primitives, no controllers           |
| seam-core   | `infrastructure.ontai.dev/v1alpha1` | Infrastructure domain CRDs managed by Seam operators  |
| app-core    | `app.ontai.dev/v1alpha1`     | Application layer CRDs for community operators               |

Cross-layer references always follow the dependency direction:

```
app-core -> seam-core -> domain-core -> shared
                      ^
                      domain-core
```

No schema may reference a layer above itself in this hierarchy.

---

## Stability Policy

Every schema carries an `x-ont-stability` extension field:

| Value        | Meaning                                                                   |
|--------------|---------------------------------------------------------------------------|
| `alpha`      | Schema is under active development. Breaking changes may occur.           |
| `beta`       | Schema is feature-complete. Breaking changes require deprecation notice.  |
| `stable`     | Schema is production-ready. No breaking changes without a major version.  |
| `deprecated` | Schema is superseded. Consumers must migrate before the next major.       |

All schemas in `v1alpha1` carry `"x-ont-stability": "alpha"`.

---

## Contribution Model

Community operators that wish to register a new domain CRD schema follow this process:

1. Fork this repository.
2. Create a new schema file under the appropriate layer directory following the
   structure defined in the existing schemas. Every schema must include:
   - `$schema`, `$id`, `title`, `description`
   - `x-ont-layer`, `x-ont-stability`, `x-ont-depends-on`
   - `type: object` with `required`, `properties`, and `additionalProperties: false`
   - A `metadata` property using `$ref` to `shared/KubernetesMetadata.json`
3. Reference only schemas that exist in this release. No forward references.
4. Add an entry to `v1alpha1/index.json` in the appropriate layer array.
5. Validate every new and modified schema file against JSON Schema Draft 2020-12
   using `ajv --spec=draft2020` or equivalent before submitting.
6. Submit a pull request. The pull request requires sign-off from a senior engineer
   in the ONT platform team validation framework before merge.

New rationale vocabulary values for `SealedCausalChain.creationRationale` require
a Pull Request to the `seam-core` repository (`pkg/lineage/rationale.go`) and
Platform Governor review. The vocabulary in this schema is generated from that
enumeration and updated in sync.

---

## Versioning Policy

- Schema versions follow the Kubernetes API versioning convention: `v1alpha1`, `v1beta1`, `v1`.
- A new version directory (`v1beta1/`) is introduced when any schema reaches `beta` stability.
- Within a version, new optional fields may be added without a version bump.
- Any field removal, type change, or required field addition requires a new version.
- The `index.json` at each version root is the stable entry point for that version.

## License

Apache License 2.0. See [LICENSE](./LICENSE) for the full text.

You may use, modify, and distribute these schemas for any purpose
including commercial use. You must include the original copyright
notice and state any changes you make. You may not use the ONT
project name or contributors names to endorse derived products.

## Versioning Policy

Schemas marked x-ont-stability: "alpha" may change without notice
between patch releases. Pin to a specific git commit if you need
stability.

When a schema advances to "beta", changes require a deprecation
notice in CHANGELOG.md at least one release before removal.

When a schema advances to "stable", changes require a new version
directory (v1beta1, v1) and the previous version is maintained
for at least six months.

## Community

ONT is open source under the Apache 2.0 license. To contribute
a schema for a new domain CRD:

1. Fork this repository
2. Add your schema file under v1alpha1/{your-layer}/
3. Follow the four-part schema structure defined in this README
4. Reference only existing shared/ and domain-core/ schemas
5. Add your schema to v1alpha1/index.json
6. Include a senior engineer sign-off in the pull request description
   confirming the schema correctly represents the domain knowledge
7. Submit a pull request

Community schemas that represent real production domains are
the highest-value contributions. See the ONT Operator Validation
Framework at ontai.dev for the sign-off process.

## Schema Browser

Visit https://schema.ontai.dev to browse the schema index
in a human-readable format.
