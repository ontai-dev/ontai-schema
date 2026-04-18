# CHANGELOG

## v1alpha1 — Initial Release (2026-04-18)

First versioned release of the ONT OpenAPI JSON Schema specification.
All twenty-two schemas carry `x-ont-stability: alpha`.

### shared (6 schemas)

| Schema             | Description                                                                 |
|--------------------|-----------------------------------------------------------------------------|
| KubernetesMetadata | Standard Kubernetes ObjectMeta used as a $ref target by all CRD schemas.    |
| SealedCausalChain  | Immutable causal chain record linking derived objects to their root. All fields required; immutable after creation. |
| BindingStability   | Reusable binding stability descriptor with SnapshotBinding and ContinuousBinding types. |
| PhaseModel         | Eight-phase lifecycle model (ScopeEstablishment through ProfileCompletion) for AppProfile. |
| RationaleField     | Structured rationale type for governance events; not a CRD spec field.      |
| GovernanceEvent    | Layer One and Layer Two change event record with actor, changeType, and rationale. |

### domain-core (9 schemas)

| Schema                    | Description                                                              |
|---------------------------|--------------------------------------------------------------------------|
| DomainIdentity            | Named principal with SPIFFE trust domain and ONT web subject. All fields required. |
| DomainBoundary            | Organizational scope and cluster placement authority for a domain identity. |
| DomainPolicy              | Ceilings for retry, circuit breaker, rate limit, and cardinality. Guardian validates AppPolicy against these ceilings. |
| DomainRelationship        | Typed directional association with relationship vocabulary: signs, provisions, delegates, governs, observes, depends-on, extends. |
| DomainEvent               | Event type contract with producer authority, schema version, and retention policy. |
| DomainWorkflow            | Lifecycle phase sequence with entry conditions, terminal states, and transition authority. |
| DomainResource            | Compute and storage ceilings with Kueue resource flavor constraints.      |
| DomainAudit               | Minimum granularity floor, retention requirement, and mandatory event type list. |
| DomainSemanticNameService | DSNS zone declaration with record types and resolution tier registry.     |

### seam-core (3 schemas)

| Schema                      | Description                                                            |
|-----------------------------|------------------------------------------------------------------------|
| InfrastructureLineageIndex  | Controller-authored sealed causal chain index; one instance per root declaration. |
| SeamMembership              | Formal operator join declaration with tier (infrastructure, application) and 7-step validation loop. |
| DSNSZone                    | DNS zone with controller-authored A, TXT, SOA, NS records linked to owning resources. |

### app-core (9 schemas)

| Schema             | Description                                                                 |
|--------------------|-----------------------------------------------------------------------------|
| AppBoundary        | Namespace and cluster scope gate; must be Ready before AppIdentity creation. |
| AppIdentity        | Root anchor for all sibling app-core CRDs; issues SPIFFE ID on admission.   |
| AppPolicy          | Application policy bounded by DomainPolicy ceilings; Guardian enforces compliance. |
| AppTopology        | Application wirings, each traceable to a DomainRelationship.                |
| AppEventSchema     | Producer/consumer registration for a DomainEvent type with schema version.  |
| AppWorkflow        | Kueue Job bindings per workflow phase with temporal relationship declarations. |
| AppResourceProfile | Compute and storage claims bounded by DomainResource ceilings; immutable after Running state. |
| AppAuditPolicy     | Emitted event types and granularity level; floor enforced by Guardian.      |
| AppProfile         | Aggregate root composing all app-core CRDs; Ready only after all eight phase gates complete. |
