# Engineering Rules — Public Excerpt

These rules keep runtime systems clean, maintainable, and understandable as product software rather than as archives of implementation planning history.

## 1. Runtime Code Must Use Domain Language, Not Planning Language

Specs, clusters, milestones, phases, and implementation-planning identifiers are scaffolding.

They may guide development, but they must not become runtime concepts.

Runtime code must describe:

- product behavior
- domain concepts
- stable system capabilities
- explicit policies
- durable contracts

Runtime code must not describe:

- which spec introduced something
- which cluster implemented something
- which planning phase something came from
- which milestone produced a behavior

### Core rule

> Specs explain why the system exists. Runtime code explains what the system does.

## 2. Allowed Locations for Planning References

Spec, cluster, phase, milestone, and planning identifiers may appear in:

- documentation
- tests that explicitly validate spec compliance
- migration comments
- PR descriptions
- changelogs
- implementation notes
- temporary planning notes
- architectural decision records

Migration comments may mention the planning context that introduced a schema change, but the schema itself must still use domain names.

Acceptable:

```sql
-- Introduced during a specific implementation pass to support derived memory episodes.
CREATE TABLE memory_episode_links (
    ...
);
```

Not acceptable:

```sql
CREATE TABLE r21_episode_links (
    ...
);
```

## 3. Disallowed Planning References in Runtime Code

Planning identifiers must not appear in actual runtime code.

This includes, but is not limited to:

- public API fields
- API request schemas
- API response schemas
- persisted domain values
- database table names
- database column names
- enum values
- runtime decision logic
- user-visible traces
- trace field names
- trace payload semantics
- core model names
- class names
- function names
- variable names
- constants
- module names
- package names
- file paths
- directory paths
- feature flags
- configuration keys
- code paths where behavior depends on a spec, phase, cluster, or milestone identifier

## 4. Runtime Naming Must Be Behavior-Based

Runtime names should describe what the system does.

Bad names answer:

- Which spec created this?
- Which cluster implemented this?
- Which phase introduced this?
- Which milestone did this come from?

Good names answer:

- What behavior does this implement?
- What domain concept does this represent?
- What policy is being applied?
- What decision is this code making?

### Bad

```python
DEFAULT_DERIVATION_VERSION = "r21-m0-v1"
trace["r21_selection"]
cluster9_policy
R20MemoryPromotionTrace
r21_episode_builder
spec_r19_contract
phase2_policy_resolver
```

### Good

```python
DEFAULT_DERIVATION_VERSION = "episode_derivation_v1"
trace["recall_selection"]
memory_promotion_policy
MemoryPromotionTrace
episode_builder
interaction_contract
policy_resolver
```

## 5. Public APIs Must Expose Product Concepts Only

Public API contracts must not expose planning identifiers.

This applies to:

- route paths
- request fields
- response fields
- enum values
- error codes
- trace payloads
- metadata fields
- version strings
- client-visible configuration

### Bad

```json
{
  "r21_selection": {
    "selected": true
  },
  "cluster": "9C",
  "derivation_version": "r21-m0-v1"
}
```

### Good

```json
{
  "recall_selection": {
    "selected": true
  },
  "derivation_version": "episode_derivation_v1"
}
```

API consumers should never need to know which spec or cluster produced a behavior.

## 6. Branch on Behavior, Not History

Runtime behavior must not branch on specs, clusters, phases, or planning milestones.

Bad:

```python
if mode == "r21":
    apply_episode_derivation()
```

Good:

```python
if policy.enable_episode_derivation:
    apply_episode_derivation()
```

Branch on:

- domain capability
- product behavior
- explicit policy
- feature flag
- versioned behavior
- user-visible configuration
- stable runtime semantics

Do not branch on implementation history.
