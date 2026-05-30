# CDIF Codelist Profile

A CDIF profile for controlled vocabulary codelists implemented as [SKOS ConceptSchemes](https://www.w3.org/TR/skos-reference/) in JSON-LD. Defines how classification schemes, thesauri, and enumerated value domains are represented with machine-enforceable constraints.

## Specification

- **[CDIFCodelistProfileClasses.md](CDIFCodelistProfileClasses.md)** — Complete classes and properties documentation
- **[CDIFCodelistProfileStructuredSchema.json](CDIFCodelistProfileStructuredSchema.json)** — JSON Schema for validation (generated from metadataBuildingBlocks)
- **[rules.shacl](rules.shacl)** — SHACL validation shapes (synced from metadataBuildingBlocks)

## ConceptScheme requirements

- Must have a globally unique, resolvable `@id` URI
- Must have at least one `skos:prefLabel`
- Must declare top concepts via `skos:hasTopConcept`
- Must have `schema:identifier`, `schema:dateModified`, and either `schema:license` or `schema:conditionsOfAccess` (CDIF core metadata properties)

## Concept requirements

- Must have a globally unique, resolvable `@id` URI
- Must have `skos:inScheme` linking to the containing ConceptScheme
- Must have at least one `skos:prefLabel` (at most one per language)
- Must have at least one `skos:definition`
- `skos:notation` is optional but must be unique within the scheme if used
- Hierarchical concepts must declare both `skos:narrower` and `skos:broader` (see below)

## Bidirectional hierarchy

CDIF codelists require concept hierarchies to be expressed in **both directions**:

- **`skos:narrower`** — needed for JSON-LD tree traversal from `skos:hasTopConcept` root
- **`skos:broader`** — needed for upward navigation and display trees in applications

Any concept in `skos:narrower` **must** also have `skos:broader` pointing back. Top concepts should not have `skos:broader` within the scheme.

## Array convention

Unlike other CDIF profiles, the Codelist profile does **not** require repeatable properties to always be serialized as arrays. This recognizes standard SKOS practice that allows either a single string or an array for literal values. Consumers should test whether a value is a string or an array before iterating.

## Examples

| File | Description |
|------|-------------|
| `Examples/exampleCDIFCodelist.json` | iSamples Sampled Feature Type vocabulary (full, with hierarchy and history notes) |
| `Examples/exampleCDIFCodelistMinimal.json` | iSamples Materials vocabulary (minimal, with hierarchy) |

## JSON-LD Framing and Validation

**`FrameAndValidate.py`** frames a SKOS ConceptScheme JSON-LD document against the Codelist profile schema and optionally validates it:

```bash
# Frame and validate
python FrameAndValidate.py examples/exampleCDIFCodelist.json --validate

# Frame and save output
python FrameAndValidate.py examples/exampleCDIFCodelistMinimal.json -o framed.json
```

The script uses **`CDIFCodelist-frame.jsonld`** to frame JSON-LD documents. The frame targets `skos:ConceptScheme` (not `schema:Dataset`) and handles recursive concept hierarchies via `skos:narrower`/`skos:broader`. Context prefixes from the input document are automatically merged into the frame.

**Requirements:** `pyld`, `jsonschema` (`pip install pyld jsonschema`)

## SHACL Validation

**`rules.shacl`** contains SHACL shapes for validating CDIF Codelist profile instances. Source shapes come from [`metadataBuildingBlocks/_sources/profiles/cdifProfiles/CDIFCodelistProfile/rules.shacl`](https://github.com/Cross-Domain-Interoperability-Framework/metadataBuildingBlocks/blob/main/_sources/profiles/cdifProfiles/CDIFCodelistProfile/rules.shacl) and should be updated whenever the source changes.

## Related repositories

- **[metadataBuildingBlocks](https://github.com/Cross-Domain-Interoperability-Framework/metadataBuildingBlocks)** — Source building block schemas (skosConceptScheme, skosConcept, CDIFCodelistProfile)
- **[cdif-core](https://github.com/Cross-Domain-Interoperability-Framework/core)** — CDIF Core profile
- **[validation](https://github.com/Cross-Domain-Interoperability-Framework/validation)** — Validation tools

## Reference

This profile aligns with the approach described in ['Modelling of Eurostat's Statistical Classifications in ShowVoc'](https://cros.ec.europa.eu/book-page/modeling-eurostats-statistical-classifications-showvoc).

## Development branch

Active work for the 2026-06 review revision is on the `reviewRevision202606` branch. `main` reflects the prior release state. New changes should target the review branch; it is merged to main on release.


## License

See [LICENSE](LICENSE).
