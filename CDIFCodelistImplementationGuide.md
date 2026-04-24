---
title: CDIF Codelist Profile Classes and Properties
date: 2026-04-05
---

# Introduction

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

## ConceptScheme {#sec-conceptscheme}

The root object representing the controlled vocabulary or classification scheme.

### Required Properties

#### @id

**Cardinality:** Required

**Content:** string.uri

**Description:** Globally unique, resolvable URI for the concept scheme.


#### @type

**Cardinality:** Required

**Content:** array

**Description:** Must include `skos:ConceptScheme`.


#### skos:prefLabel

**Cardinality:** Required

**Content:** string, [LanguageTaggedValue](#sec-languagetaggedvalue), or array of [LanguageTaggedValue](#sec-languagetaggedvalue)

**Description:** Preferred human-readable label for the scheme. At most one per language.


#### skos:hasTopConcept

**Cardinality:** Required, Repeatable

**Content:** array of [CdifCodelistConcept](#sec-cdifcodelistconcept) or [object reference](#sec-objectreference)

**Description:** Top-level concepts that have no `skos:broader` within this scheme. The JSON-LD hierarchy is rooted here — all child concepts are reached by traversing `skos:narrower` from these top concepts.


#### schema:identifier

**Cardinality:** Required

**Content:** string or [PropertyValue](#sec-propertyvalue)

**Description:** Primary identifier for the codelist. CDIF core metadata property; takes precedence over `dcterms:identifier`.


#### schema:dateModified

**Cardinality:** Required

**Content:** string, ISO 8601

**Description:** Date when the codelist was last modified. Takes precedence over `dcterms:modified`.


**CHOICE — at least one of:**

#### schema:license

**Cardinality:** Required if no conditionsOfAccess

**Content:** array of string or [object reference](#sec-objectreference)

**Description:** License for the codelist. Takes precedence over `dcterms:license`.


#### schema:conditionsOfAccess

**Cardinality:** Required if no license

**Content:** array of string

**Description:** Text statement of access conditions.


### Optional Properties

#### schema:url

**Cardinality:** Optional

**Content:** string.uri

**Description:** Web location of a page describing the codelist. Default: `'missing'`.


#### schema:creator

**Cardinality:** Optional

**Content:** Person, Organization, or @list

**Description:** Author or maintainer of the vocabulary.


#### skos:definition

**Cardinality:** Optional

**Content:** string, [LanguageTaggedValue](#sec-languagetaggedvalue), or array

**Description:** Formal explanation of the meaning or purpose of the scheme.


#### skos:altLabel

**Cardinality:** Optional

**Content:** string, [LanguageTaggedValue](#sec-languagetaggedvalue), or array

**Description:** Alternative labels (acronyms, abbreviations, spelling variants).


#### skos:hiddenLabel

**Cardinality:** Optional

**Content:** string, [LanguageTaggedValue](#sec-languagetaggedvalue), or array

**Description:** Labels accessible to free-text search but not displayed.


#### skos:notation

**Cardinality:** Optional, Repeatable

**Content:** array of string

**Description:** Classification codes for the scheme.


#### skos:note

**Cardinality:** Optional

**Content:** string, [LanguageTaggedValue](#sec-languagetaggedvalue), or array

**Description:** General note about the scheme.


#### skos:scopeNote

**Cardinality:** Optional

**Content:** string, [LanguageTaggedValue](#sec-languagetaggedvalue), or array

**Description:** Note clarifying the intended scope.


#### skos:historyNote

**Cardinality:** Optional

**Content:** string, [LanguageTaggedValue](#sec-languagetaggedvalue), or array

**Description:** Note about the history of the scheme.


#### skos:changeNote

**Cardinality:** Optional

**Content:** string, [LanguageTaggedValue](#sec-languagetaggedvalue), or array

**Description:** Note documenting a change.


#### skos:editorialNote

**Cardinality:** Optional

**Content:** string, [LanguageTaggedValue](#sec-languagetaggedvalue), or array

**Description:** Note for editors and maintainers.


#### skos:example

**Cardinality:** Optional

**Content:** string, [LanguageTaggedValue](#sec-languagetaggedvalue), or array

**Description:** Example of use.


#### dcterms:creator

**Cardinality:** Optional

**Content:** string, [object reference](#sec-objectreference), or agent object

**Description:** Creator of the scheme. Open-world; CDIF recommends `schema:creator` if using schema.org agents.


#### dcterms:created

**Cardinality:** Optional

**Content:** string, ISO 8601

**Description:** Date the scheme was originally created.


#### schema:version

**Cardinality:** Optional

**Content:** string or number

**Description:** Version identifier for the scheme.


## CdifCodelistConcept {#sec-cdifcodelistconcept}

A SKOS Concept with CDIF codelist constraints. Represents a single term or category within a concept scheme.

### Required Properties

#### @id

**Cardinality:** Required

**Content:** string.uri

**Description:** Globally unique, resolvable URI for this concept.


#### @type

**Cardinality:** Required

**Content:** array

**Description:** Must include `skos:Concept`.


#### skos:prefLabel

**Cardinality:** Required

**Content:** string, [LanguageTaggedValue](#sec-languagetaggedvalue), or array of [LanguageTaggedValue](#sec-languagetaggedvalue)

**Description:** Preferred label. At most one per language (enforced by SHACL `sh:uniqueLang`).


#### skos:inScheme

**Cardinality:** Required

**Content:** [object reference](#sec-objectreference) or array of object references

**Description:** The concept scheme(s) this concept belongs to. Each must be `{"@id": "scheme-uri"}`.


#### skos:definition

**Cardinality:** Required

**Content:** string, [LanguageTaggedValue](#sec-languagetaggedvalue), or array

**Description:** Formal definition of this concept.


#### skos:broader

**Cardinality:** Required if concept appears in skos:narrower

**Content:** array of object references

**Description:** Broader (parent) concepts. Any concept that is the target of `skos:narrower` on another concept must declare `skos:broader` pointing back. See [Bidirectional hierarchy](#bidirectional-hierarchy) below. Each item is `{"@id": "parent-concept-uri"}`.


### Optional Properties

#### skos:narrower

**Cardinality:** Optional, Repeatable

**Content:** array of [CdifCodelistConcept](#sec-cdifcodelistconcept) or [object reference](#sec-objectreference)

**Description:** Narrower (child) concepts. If present, each inline child concept must have `skos:broader` pointing back to this concept. Items can be full inline concept objects (for building the JSON tree) or `{"@id": "child-uri"}` references.


#### skos:altLabel

**Cardinality:** Optional

**Content:** string, [LanguageTaggedValue](#sec-languagetaggedvalue), or array

**Description:** Alternative labels.


#### skos:hiddenLabel

**Cardinality:** Optional

**Content:** string, [LanguageTaggedValue](#sec-languagetaggedvalue), or array

**Description:** Labels for search but not display.


#### skos:notation

**Cardinality:** Optional, Repeatable

**Content:** array of string

**Description:** Classification codes. Should be unique within the scheme.


#### skos:note

**Cardinality:** Optional

**Content:** string, [LanguageTaggedValue](#sec-languagetaggedvalue), or array

**Description:** General note.


#### skos:scopeNote

**Cardinality:** Optional

**Content:** string, [LanguageTaggedValue](#sec-languagetaggedvalue), or array

**Description:** Scope clarification.


#### skos:historyNote

**Cardinality:** Optional

**Content:** string, [LanguageTaggedValue](#sec-languagetaggedvalue), or array

**Description:** History note.


#### skos:changeNote

**Cardinality:** Optional

**Content:** string, [LanguageTaggedValue](#sec-languagetaggedvalue), or array

**Description:** Change documentation.


#### skos:editorialNote

**Cardinality:** Optional

**Content:** string, [LanguageTaggedValue](#sec-languagetaggedvalue), or array

**Description:** Editor note.


#### skos:example

**Cardinality:** Optional

**Content:** string, [LanguageTaggedValue](#sec-languagetaggedvalue), or array

**Description:** Usage example.


#### skos:related

**Cardinality:** Optional, Repeatable

**Content:** array of object references or inline concepts

**Description:** Associatively related concepts within the same scheme.


#### skos:exactMatch

**Cardinality:** Optional, Repeatable

**Content:** array of object references

**Description:** Concepts in other schemes with equivalent meaning.


#### skos:closeMatch

**Cardinality:** Optional, Repeatable

**Content:** array of object references

**Description:** Concepts in other schemes with similar meaning.


#### skos:broadMatch

**Cardinality:** Optional, Repeatable

**Content:** array of object references

**Description:** Broader concepts in other schemes.


#### skos:narrowMatch

**Cardinality:** Optional, Repeatable

**Content:** array of object references

**Description:** Narrower concepts in other schemes.


#### skos:relatedMatch

**Cardinality:** Optional, Repeatable

**Content:** array of object references

**Description:** Related concepts in other schemes.


#### skos:topConceptOf

**Cardinality:** Optional

**Content:** [object reference](#sec-objectreference) or array

**Description:** Scheme(s) for which this is a top concept.


## Data Types

### LanguageTaggedValue {#sec-languagetaggedvalue}

An RDF literal with a language tag, serialized as a JSON-LD value object.

#### @value

**Cardinality:** Required

**Content:** string

**Description:** The text content.


#### @language

**Cardinality:** Required

**Content:** string

**Description:** BCP 47 language tag (e.g., `en`, `fr`, `de`, `sv`).


```json
{"@value": "Sampled Feature Type vocabulary", "@language": "en"}
```

### Object Reference {#sec-objectreference}

A reference to another node by its `@id`, used for linking to concepts or schemes defined elsewhere in the graph or externally.

```json
{"@id": "https://w3id.org/isample/vocabulary/sampledfeature/anysampledfeature"}
```

### PropertyValue (for schema:identifier) {#sec-propertyvalue}

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
