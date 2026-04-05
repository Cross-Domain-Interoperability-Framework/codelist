# CDIF Codelist Profile Classes and Properties

2026-04-05

This document describes all classes and properties for the CDIF Codelist profile, which defines how controlled vocabularies and classification schemes are represented as SKOS ConceptSchemes in JSON-LD. The profile composes the base SKOS ConceptScheme and Concept building blocks with CDIF-specific constraints: resolvable identifiers, required definitions, bidirectional hierarchy, and CDIF core metadata properties.

The implementation uses the [SKOS (Simple Knowledge Organization System)](https://www.w3.org/TR/skos-reference/) vocabulary with JSON-LD serialization. This profile aligns with the approach described in ['Modelling of Eurostat's Statistical Classifications in ShowVoc'](https://cros.ec.europa.eu/book-page/modeling-eurostats-statistical-classifications-showvoc).

# Namespaces

```json
"@context": {
  "skos": "http://www.w3.org/2004/02/skos/core#",
  "schema": "http://schema.org/",
  "dcterms": "http://purl.org/dc/terms/"
}
```

Additional namespace prefixes may be included for concept URIs (e.g., `"sf": "https://w3id.org/isample/vocabulary/sampledfeature/"`).

# Model

## ConceptScheme

The root object representing the controlled vocabulary or classification scheme.

### Required Properties

**@id** — **Required** (string.uri) Globally unique, resolvable URI for the concept scheme.

**@type** — **Required** (array) Must include `skos:ConceptScheme`.

**skos:prefLabel** — **Required** (string, LanguageTaggedValue, or array of LanguageTaggedValue) Preferred human-readable label for the scheme. At most one per language.

**skos:hasTopConcept** — **Required, Repeatable** (array of CdifCodelistConcept or object reference) Top-level concepts that have no `skos:broader` within this scheme. The JSON-LD hierarchy is rooted here — all child concepts are reached by traversing `skos:narrower` from these top concepts.

**schema:identifier** — **Required** (string or PropertyValue) Primary identifier for the codelist. CDIF core metadata property; takes precedence over `dcterms:identifier`.

**schema:dateModified** — **Required** (string, ISO 8601) Date when the codelist was last modified. Takes precedence over `dcterms:modified`.

CHOICE — at least one of:

**schema:license** — **Required if no conditionsOfAccess** (array of string or object reference) License for the codelist. Takes precedence over `dcterms:license`.

**schema:conditionsOfAccess** — **Required if no license** (array of string) Text statement of access conditions.

### Optional Properties

**schema:url** — Optional (string.uri) Web location of a page describing the codelist. Default: `'missing'`.

**schema:creator** — Optional (Person, Organization, or @list) Author or maintainer of the vocabulary.

**skos:definition** — Optional (string, LanguageTaggedValue, or array) Formal explanation of the meaning or purpose of the scheme.

**skos:altLabel** — Optional (string, LanguageTaggedValue, or array) Alternative labels (acronyms, abbreviations, spelling variants).

**skos:hiddenLabel** — Optional (string, LanguageTaggedValue, or array) Labels accessible to free-text search but not displayed.

**skos:notation** — Optional, Repeatable (array of string) Classification codes for the scheme.

**skos:note** — Optional (string, LanguageTaggedValue, or array) General note about the scheme.

**skos:scopeNote** — Optional (string, LanguageTaggedValue, or array) Note clarifying the intended scope.

**skos:historyNote** — Optional (string, LanguageTaggedValue, or array) Note about the history of the scheme.

**skos:changeNote** — Optional (string, LanguageTaggedValue, or array) Note documenting a change.

**skos:editorialNote** — Optional (string, LanguageTaggedValue, or array) Note for editors and maintainers.

**skos:example** — Optional (string, LanguageTaggedValue, or array) Example of use.

**dcterms:creator** — Optional (string, object reference, or agent object) Creator of the scheme. Open-world; CDIF recommends `schema:creator` if using schema.org agents.

**dcterms:created** — Optional (string, ISO 8601) Date the scheme was originally created.

**schema:version** — Optional (string or number) Version identifier for the scheme.

## CdifCodelistConcept

A SKOS Concept with CDIF codelist constraints. Represents a single term or category within a concept scheme.

### Required Properties

**@id** — **Required** (string.uri) Globally unique, resolvable URI for this concept.

**@type** — **Required** (array) Must include `skos:Concept`.

**skos:prefLabel** — **Required** (string, LanguageTaggedValue, or array of LanguageTaggedValue) Preferred label. At most one per language (enforced by SHACL `sh:uniqueLang`).

**skos:inScheme** — **Required** (object reference or array of object references) The concept scheme(s) this concept belongs to. Each must be `{"@id": "scheme-uri"}`.

**skos:definition** — **Required** (string, LanguageTaggedValue, or array) Formal definition of this concept.

**skos:broader** — **Required if concept appears in skos:narrower** (array of object references) Broader (parent) concepts. Any concept that is the target of `skos:narrower` on another concept must declare `skos:broader` pointing back. See [Bidirectional hierarchy](#bidirectional-hierarchy) below. Each item is `{"@id": "parent-concept-uri"}`.

### Optional Properties

**skos:narrower** — Optional, Repeatable (array of CdifCodelistConcept or object reference) Narrower (child) concepts. If present, each inline child concept must have `skos:broader` pointing back to this concept. Items can be full inline concept objects (for building the JSON tree) or `{"@id": "child-uri"}` references.

**skos:altLabel** — Optional (string, LanguageTaggedValue, or array) Alternative labels.

**skos:hiddenLabel** — Optional (string, LanguageTaggedValue, or array) Labels for search but not display.

**skos:notation** — Optional, Repeatable (array of string) Classification codes. Should be unique within the scheme.

**skos:note** — Optional (string, LanguageTaggedValue, or array) General note.

**skos:scopeNote** — Optional (string, LanguageTaggedValue, or array) Scope clarification.

**skos:historyNote** — Optional (string, LanguageTaggedValue, or array) History note.

**skos:changeNote** — Optional (string, LanguageTaggedValue, or array) Change documentation.

**skos:editorialNote** — Optional (string, LanguageTaggedValue, or array) Editor note.

**skos:example** — Optional (string, LanguageTaggedValue, or array) Usage example.

**skos:related** — Optional, Repeatable (array of object references or inline concepts) Associatively related concepts within the same scheme.

**skos:exactMatch** — Optional, Repeatable (array of object references) Concepts in other schemes with equivalent meaning.

**skos:closeMatch** — Optional, Repeatable (array of object references) Concepts in other schemes with similar meaning.

**skos:broadMatch** — Optional, Repeatable (array of object references) Broader concepts in other schemes.

**skos:narrowMatch** — Optional, Repeatable (array of object references) Narrower concepts in other schemes.

**skos:relatedMatch** — Optional, Repeatable (array of object references) Related concepts in other schemes.

**skos:topConceptOf** — Optional (object reference or array) Scheme(s) for which this is a top concept.

## Data Types

### LanguageTaggedValue

An RDF literal with a language tag, serialized as a JSON-LD value object.

**@value** — **Required** (string) The text content.

**@language** — **Required** (string) BCP 47 language tag (e.g., `en`, `fr`, `de`, `sv`).

```json
{"@value": "Sampled Feature Type vocabulary", "@language": "en"}
```

### Object Reference

A reference to another node by its `@id`, used for linking to concepts or schemes defined elsewhere in the graph or externally.

```json
{"@id": "https://w3id.org/isample/vocabulary/sampledfeature/anysampledfeature"}
```

### PropertyValue (for schema:identifier)

When the identifier is not a simple resolvable URI, use `schema:PropertyValue`:

```json
{
  "@type": ["schema:PropertyValue"],
  "schema:propertyID": "https://registry.identifiers.org/registry/doi",
  "schema:value": "10.5683/SP2/TTJNIU",
  "schema:url": "https://doi.org/10.5683/SP2/TTJNIU"
}
```

# Bidirectional Hierarchy

CDIF codelists require concept hierarchies to be expressed in both directions:

- **`skos:narrower`** is needed because the JSON-LD tree is rooted at `skos:hasTopConcept`. Without `skos:narrower`, child concepts cannot be reached by traversing the JSON document from the root.

- **`skos:broader`** is needed for upward navigation and for display trees in vocabulary browsers and classification tools.

Any concept that appears as a value of `skos:narrower` **must** also declare `skos:broader` pointing back to its parent. Top concepts (those in `skos:hasTopConcept`) should **not** have `skos:broader` within the scheme.

```json
{
  "@id": "sf:anysampledfeature",
  "@type": ["skos:Concept"],
  "skos:prefLabel": "Any sampled feature",
  "skos:definition": "Top concept",
  "skos:inScheme": {"@id": "sf:sampledfeaturevocabulary"},
  "skos:narrower": [
    {
      "@id": "sf:earthmaterial",
      "@type": ["skos:Concept"],
      "skos:prefLabel": "Natural Solid Material",
      "skos:definition": "A naturally occurring solid material.",
      "skos:inScheme": {"@id": "sf:sampledfeaturevocabulary"},
      "skos:broader": [{"@id": "sf:anysampledfeature"}]
    }
  ]
}
```

# Array Convention

Unlike other CDIF profiles, the Codelist profile does **not** require repeatable properties to always be serialized as arrays. This recognizes standard SKOS practice that allows either a single string or an array for literal values. For example, both of these are valid:

```json
"skos:prefLabel": "Material"
```

```json
"skos:prefLabel": [
  {"@value": "Material", "@language": "en"},
  {"@value": "Matériau", "@language": "fr"}
]
```

Consumers of CDIF codelist documents should test whether a value is a string or an array before iterating.

# Validation

- **JSON Schema** validates structure: required properties (`@id`, `skos:prefLabel`, `skos:hasTopConcept`, `schema:identifier`, `schema:dateModified`, license/access), concept requirements (`@id`, `skos:inScheme`, `skos:definition`), and bidirectional hierarchy (inline narrower concepts must have `skos:broader`)
- **SHACL** validates RDF constraints: `sh:uniqueLang` on `skos:prefLabel`, `sh:class skos:ConceptScheme` on `skos:inScheme`, `sh:class skos:Concept` on `skos:broader`, and the `narrowerImpliesBroaderShape` SPARQL-targeted rule
