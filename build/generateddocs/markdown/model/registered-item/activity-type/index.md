
# Activity Type Register Profile (Model)

`ogc.model.registered-item.activity-type` *v0.3*

A profile of the Registered Item Model for governed activity type records in the acttype:ActivityType hierarchy, with related PROV object types and an optional plan.

[*Status*](http://www.opengis.net/def/status): Under development

## Examples

### A registered activity type with a plan, and an activity of that type
A data curation register governs the kinds of activity its curators perform. The
"dataset quality review" type is a register item — with its own identifier, status and
item class — and also a class, a sub-class of `acttype:ActivityType`. It declares the kinds of
entity it uses and generates and the kind of agent that performs it, and points to a
P-Plan `prov:Plan` listing its three required steps in order. The last part shows an
individual review typed directly with the registered class, recording the plan it followed
through a PROV qualified association.

#### turtle
```turtle
ex:curationRegister a rim:Register ;
    dct:title "Data Curation Activity Types" .

ex:DatasetQualityReview a acttype:ActivityType , owl:Class ;
    rdfs:subClassOf acttype:ActivityType ;
    rdfs:label "Dataset quality review" ;
    dct:title "Dataset quality review" ;
    dct:description "A review of a dataset against the curation quality criteria, producing a quality report." ;
    rim:inRegister ex:curationRegister ;
    rim:itemClass acttype:activityTypeItemClass ;
    rim:objectIdentifier "https://example.org/registers/activity-types/dataset-quality-review/v1"^^xsd:anyURI ;
    rim:validityStatus rim:valid ;
    rim:publicationStatus rim:published ;
    acttype:usedEntityType dcat:Dataset ;
    acttype:generatedEntityType ex:QualityReport ;
    acttype:associatedAgentType prov:Person ;
    acttype:plan ex:qualityReviewPlan .

ex:QualityReport a owl:Class ;
    rdfs:subClassOf prov:Entity .

ex:qualityReviewPlan a prov:Plan , p-plan:Plan ;
    dct:title "Dataset quality review procedure" .

ex:checkCompleteness a p-plan:Step ;
    p-plan:isStepOfPlan ex:qualityReviewPlan ;
    dct:description "Check that every mandatory metadata element is present." .

ex:checkConsistency a p-plan:Step ;
    p-plan:isStepOfPlan ex:qualityReviewPlan ;
    p-plan:isPrecededBy ex:checkCompleteness ;
    dct:description "Check values against the dataset's declared schema and code lists." .

ex:writeReport a p-plan:Step ;
    p-plan:isStepOfPlan ex:qualityReviewPlan ;
    p-plan:isPrecededBy ex:checkConsistency ;
    dct:description "Record the findings in a quality report." .

# An individual activity of the registered type.
ex:review-2026-09-20 a ex:DatasetQualityReview ;
    rim:itemClass acttype:activityTypeItemClass ;
    rim:objectIdentifier "https://example.org/registers/activity-types/reviews/review-2026-09-20"^^xsd:anyURI ;
    rim:validityStatus rim:valid ;
    rim:publicationStatus rim:published ;
    prov:used ex:riverGaugesDataset ;
    prov:wasAssociatedWith ex:curatorJo ;
    prov:qualifiedAssociation [
        a prov:Association ;
        prov:agent ex:curatorJo ;
        prov:hadPlan ex:qualityReviewPlan
    ] ;
    prov:startedAtTime "2026-09-20T09:00:00Z"^^xsd:dateTime .

ex:riverGaugesDataset a dcat:Dataset , prov:Entity .
ex:curatorJo a prov:Person .

ex:report-2026-09-20 a ex:QualityReport , prov:Entity ;
    prov:wasGeneratedBy ex:review-2026-09-20 .

```

## Schema

```yaml
$schema: https://json-schema.org/draft/2020-12/schema
title: Activity Type
description: 'A registered activity type based on the Registered Item Model.

  '
allOf:
- $ref: https://nielshoffmann.github.io/registered-item-model/build/annotated/model/registered-item/rim/register-item-class/schema.yaml
- type: object
  properties:
    usedEntityType:
      type: string
      format: uri
      description: "A type of prov:Entity that activities of this type use (prov:used)
        \u2014 for example a type of input dataset."
    generatedEntityType:
      type: string
      format: uri
      description: "A type of prov:Entity that activities of this type generate (prov:generated)
        \u2014 for example a type of output product."
    associatedAgentType:
      type: string
      format: uri
      description: A type of prov:Agent (for example prov:Person, prov:SoftwareAgent,
        or a narrower role-bearing type) that activities of this type are associated
        with (prov:wasAssociatedWith).
    informedByActivityType:
      type: string
      format: uri
      description: "A type of acttype:ActivityType whose instances typically inform
        activities of this type (prov:wasInformedBy) \u2014 for example an upstream
        preparation activity type."
  unevaluatedProperties: true
x-bblocks-metadata:
  itemIdentifier: ogc.model.registered-item.activity-type
  register: https://nielshoffmann.github.io/registered-item-model/build/register.json
  version: '0.3'
  dateOfLastChange: '2026-10-27'

```

Links to the schema:

* YAML version: [schema.yaml](https://nielshoffmann.github.io/registered-item-model/build/annotated/model/registered-item/activity-type/schema.json)
* JSON version: [schema.json](https://nielshoffmann.github.io/registered-item-model/build/annotated/model/registered-item/activity-type/schema.yaml)


# JSON-LD Context

```jsonld
{
  "@context": {
    "Register": "rim:Register",
    "RegisterSystem": "rim:RegisterSystem",
    "RegisterSpecification": "rim:RegisterSpecification",
    "RegisterItemClass": "rim:RegisterItemClass",
    "RegisterItem": "rim:RegisterItem",
    "RegisterAction": "rim:RegisterAction",
    "Concept": "rim:Concept",
    "ConceptVersion": "rim:ConceptVersion",
    "ConceptSystem": "rim:ConceptSystem",
    "Commitment": "rim:Commitment",
    "Agent": "prov:Agent",
    "Organization": "prov:Organization",
    "Person": "prov:Person",
    "label": "rdfs:label",
    "prefLabel": "skos:prefLabel",
    "definition": "skos:definition",
    "inScheme": {
      "@id": "skos:inScheme",
      "@type": "@id"
    },
    "runsOn": {
      "@id": "rim:runsOn",
      "@type": "@id"
    },
    "hasSpecification": {
      "@id": "rim:hasSpecification",
      "@type": "@id"
    },
    "definesSubstantiveChange": "rim:definesSubstantiveChange",
    "registerOwner": {
      "@id": "rim:registerOwner",
      "@type": "@id"
    },
    "registerManager": {
      "@id": "rim:registerManager",
      "@type": "@id"
    },
    "controlBody": {
      "@id": "rim:controlBody",
      "@type": "@id"
    },
    "registerSystemManager": {
      "@id": "rim:registerSystemManager",
      "@type": "@id"
    },
    "registerUser": {
      "@id": "rim:registerUser",
      "@type": "@id"
    },
    "hasCommitment": {
      "@id": "rim:hasCommitment",
      "@type": "@id"
    },
    "commitmentCategory": {
      "@id": "rim:commitmentCategory",
      "@type": "@vocab"
    },
    "access": "rim:accessCommitment",
    "persistence": "rim:persistenceCommitment",
    "transparency": "rim:transparencyCommitment",
    "itemClass": {
      "@id": "rim:itemClass",
      "@type": "@id"
    },
    "inRegister": {
      "@id": "rim:inRegister",
      "@type": "@id"
    },
    "realizes": {
      "@id": "rim:realizes",
      "@type": "@id"
    },
    "versionOf": {
      "@id": "rim:versionOf",
      "@type": "@id"
    },
    "version": "rim:version",
    "validityStatus": {
      "@id": "rim:validityStatus",
      "@type": "@vocab"
    },
    "publicationStatus": {
      "@id": "rim:publicationStatus",
      "@type": "@vocab"
    },
    "valid": "rim:valid",
    "invalid": "rim:invalid",
    "published": "rim:published",
    "unpublished": "rim:unpublished",
    "isRedacted": {
      "@id": "rim:isRedacted",
      "@type": "xsd:boolean"
    },
    "isDeleted": {
      "@id": "rim:isDeleted",
      "@type": "xsd:boolean"
    },
    "relatedItem": {
      "@id": "rim:relatedItem",
      "@type": "@id"
    },
    "supersededBy": {
      "@id": "rim:supersededBy",
      "@type": "@id"
    },
    "supersedes": {
      "@id": "rim:supersedes",
      "@type": "@id"
    },
    "hasAction": {
      "@id": "rim:hasAction",
      "@type": "@id"
    },
    "appliesTo": {
      "@id": "rim:appliesTo",
      "@type": "@id"
    },
    "proposer": {
      "@id": "rim:proposer",
      "@type": "@id"
    },
    "actionType": {
      "@id": "rim:actionType",
      "@type": "@vocab"
    },
    "addition": "rim:additionAction",
    "publish": "rim:publishAction",
    "unpublish": "rim:unpublishAction",
    "invalidation": "rim:invalidationAction",
    "supersession": "rim:supersessionAction",
    "redaction": "rim:redactionAction",
    "deletion": "rim:deletionAction",
    "changeClassification": {
      "@id": "rim:changeClassification",
      "@type": "@vocab"
    },
    "substantive": "rim:substantiveChange",
    "clarifying": "rim:clarifyingChange",
    "correctional": "rim:correctionalChange",
    "startedAtTime": {
      "@id": "prov:startedAtTime",
      "@type": "xsd:dateTime"
    },
    "endedAtTime": {
      "@id": "prov:endedAtTime",
      "@type": "xsd:dateTime"
    },
    "wasAssociatedWith": {
      "@id": "prov:wasAssociatedWith",
      "@type": "@id"
    },
    "id": "@id",
    "type": "@type",
    "objectIdentifier": {
      "@id": "rim:objectIdentifier",
      "@type": "xsd:anyURI"
    },
    "functionalIdentifier": {
      "@id": "rim:functionalIdentifier",
      "@type": "xsd:anyURI"
    },
    "title": "dct:title",
    "description": "dct:description",
    "definedIn": {
      "@id": "rim:definedIn",
      "@type": "@id"
    },
    "validatedBy": {
      "@id": "rim:validatedBy",
      "@type": "@id"
    },
    "rim": "https://w3id.org/ogc/rim/",
    "prov": "http://www.w3.org/ns/prov#",
    "dct": "http://purl.org/dc/terms/",
    "rdfs": "http://www.w3.org/2000/01/rdf-schema#",
    "skos": "http://www.w3.org/2004/02/skos/core#",
    "xsd": "http://www.w3.org/2001/XMLSchema#",
    "@version": 1.1
  }
}
```

You can find the full JSON-LD context here:
[context.jsonld](https://nielshoffmann.github.io/registered-item-model/build/annotated/model/registered-item/activity-type/context.jsonld)

## Sources

* [W3C PROV-O](https://www.w3.org/TR/prov-o/)
* [P-Plan Ontology](https://www.opmw.org/model/p-plan/)

# For developers

The source code for this Building Block can be found in the following repository:

* URL: [https://github.com/NielsHoffmann/registered-item-model](https://github.com/NielsHoffmann/registered-item-model)
* Path: `_sources/activity-type`

