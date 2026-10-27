
# Geoprocessing Activity Types Profile (Model)

`ogc.model.registered-item.geoprocessing-activity-types` *v0.3*

A profile of the Activity Type Register Profile for geoprocessing activity types: registered sub-classes of geoproc:GeoprocessingActivityType, each required to use or generate at least one GeoSPARQL spatial data type.

[*Status*](http://www.opengis.net/def/status): Under development

## Examples

### A registered NDVI computation activity type
An Earth observation processing register governs the geoprocessing activity types its
pipelines run. "NDVI computation" specialises `geoproc:GeoprocessingActivityType`. It uses a
multispectral scene and generates an NDVI raster, both sub-classes of `geo:Feature`, which
satisfies the profile's requirement for at least one spatial used or generated entity type.
Its plan lists the three steps every NDVI computation must perform, in order.

#### turtle
```turtle
@prefix geoproc: <https://w3id.org/ogc/rim/geoprocessing/> .
@prefix  acttype: <https://w3id.org/ogc/rim/activity-type/> .
@prefix  rim: <https://w3id.org/ogc/rim/> .
@prefix  prov: <http://www.w3.org/ns/prov#> .
@prefix  p-plan: <http://purl.org/net/p-plan#>.
@prefix  geo: <http://www.opengis.net/ont/geosparql#>.
@prefix  dct: <http://purl.org/dc/terms/>.
@prefix  rdfs: <http://www.w3.org/2000/01/rdf-schema#>.
@prefix  owl: <http://www.w3.org/2002/07/owl#>.
@prefix  xsd: <http://www.w3.org/2001/XMLSchema#>.
@prefix  ex: <https://example.org/registers/geoprocessing/>.

ex:eoProcessingRegister a rim:Register ;
              dct:title "EO Processing Activity Types" .

          ex:NdviComputation a acttype:ActivityType , owl:Class ;
              rdfs:subClassOf geoproc:GeoprocessingActivityType ;
              rdfs:label "NDVI computation" ;
              dct:title "NDVI computation" ;
              dct:description "Computes the Normalized Difference Vegetation Index from the red and near-infrared bands of a multispectral scene." ;
              rim:inRegister ex:eoProcessingRegister ;
              rim:itemClass acttype:activityTypeItemClass ;
              rim:objectIdentifier "https://example.org/registers/geoprocessing/ndvi-computation/v1"^^xsd:anyURI ;
              rim:validityStatus rim:valid ;
              rim:publicationStatus rim:published ;
              acttype:usedEntityType ex:MultispectralScene ;
              acttype:generatedEntityType ex:NdviRaster ;
              acttype:associatedAgentType prov:SoftwareAgent ;
              acttype:plan ex:ndviPlan .

          ex:MultispectralScene a owl:Class ;
              rdfs:subClassOf geo:Feature , prov:Entity ;
              rdfs:label "Multispectral scene" .

          ex:NdviRaster a owl:Class ;
              rdfs:subClassOf geo:Feature , prov:Entity ;
              rdfs:label "NDVI raster" .

          ex:ndviPlan a prov:Plan , p-plan:Plan ;
              dct:title "NDVI computation procedure" .

          ex:cloudMask a p-plan:Step ;
              p-plan:isStepOfPlan ex:ndviPlan ;
              dct:description "Mask cloud and cloud-shadow pixels using the scene's quality band." .

          ex:reproject a p-plan:Step ;
              p-plan:isStepOfPlan ex:ndviPlan ;
              p-plan:isPrecededBy ex:cloudMask ;
              dct:description "Reproject the red and near-infrared bands to the target grid." .

          ex:computeIndex a p-plan:Step ;
              p-plan:isStepOfPlan ex:ndviPlan ;
              p-plan:isPrecededBy ex:reproject ;
              dct:description "Compute (NIR - Red) / (NIR + Red) per pixel." .

```


### example Buffer Geoprocessing Activity Type
A buffer geoprocessing activity type
#### json
```json
 {
    "id": "Buffer",
    "title": "Create a buffer around a polygon. ",
    "description": "This service shall return a feature collection representing the buffer of geometry InputPolygon at distance BufferDistance. The buffer of a geometry at distance d is the Polygon or MultiPolygon which contains all points within a distance d of the geometry.",
    "version": "2.0.0",
    "type": "rim:RegisterItemClass",
    "usedEntityType" : "point",
    "generatedEntityType": "polygon",
    "associatedAgentType": "Geoprocessing library ...",
    "informedByActivityType": "none"
}
```

#### jsonld
```jsonld
{
  "@context": "https://nielshoffmann.github.io/registered-item-model/build/annotated/model/registered-item/geoprocessing-activity-types/context.jsonld",
  "id": "Buffer",
  "title": "Create a buffer around a polygon. ",
  "description": "This service shall return a feature collection representing the buffer of geometry InputPolygon at distance BufferDistance. The buffer of a geometry at distance d is the Polygon or MultiPolygon which contains all points within a distance d of the geometry.",
  "version": "2.0.0",
  "type": "rim:RegisterItemClass",
  "usedEntityType": "point",
  "generatedEntityType": "polygon",
  "associatedAgentType": "Geoprocessing library ...",
  "informedByActivityType": "none"
}
```

#### ttl
```ttl
@prefix acttype: <https://w3id.org/ogc/rim/activity-type/> .
@prefix dct: <http://purl.org/dc/terms/> .
@prefix rim: <https://w3id.org/ogc/rim/> .

<file:///github/workspace/Buffer> a rim:RegisterItemClass ;
    dct:description "This service shall return a feature collection representing the buffer of geometry InputPolygon at distance BufferDistance. The buffer of a geometry at distance d is the Polygon or MultiPolygon which contains all points within a distance d of the geometry." ;
    dct:title "Create a buffer around a polygon. " ;
    acttype:associatedAgentType <file:///github/workspace/> ;
    acttype:generatedEntityType <file:///github/workspace/polygon> ;
    acttype:informedByActivityType <file:///github/workspace/none> ;
    acttype:usedEntityType <file:///github/workspace/point> ;
    rim:version "2.0.0" .


```

## Schema

```yaml
$schema: https://json-schema.org/draft/2020-12/schema
title: Geoprocessing Activity Type
description: 'A profile of the Activity Type Register Profile for geoprocessing activity
  types

  '
allOf:
- $ref: https://nielshoffmann.github.io/registered-item-model/build/annotated/model/registered-item/activity-type/schema.yaml
x-jsonld-extra-terms:
  id: '@id'
  type: '@type'
  title: http://purl.org/dc/terms/title
  description: http://purl.org/dc/terms/description
  version: http://www.w3.org/2002/07/owl#versionInfo
  usedEntityType:
    x-jsonld-id: https://w3id.org/ogc/rim/activity-type/usedEntityType
    x-jsonld-type: '@id'
  generatedEntityType:
    x-jsonld-id: https://w3id.org/ogc/rim/activity-type/generatedEntityType
    x-jsonld-type: '@id'
  associatedAgentType:
    x-jsonld-id: https://w3id.org/ogc/rim/activity-type/associatedAgentType
    x-jsonld-type: '@id'
  informedByActivityType:
    x-jsonld-id: https://w3id.org/ogc/rim/activity-type/informedByActivityType
    x-jsonld-type: '@id'
x-jsonld-prefixes:
  dct: http://purl.org/dc/terms/
  owl: http://www.w3.org/2002/07/owl#
  acttype: https://w3id.org/ogc/rim/activity-type/
  rim: https://w3id.org/ogc/rim#
x-bblocks-metadata:
  itemIdentifier: ogc.model.registered-item.geoprocessing-activity-types
  register: https://nielshoffmann.github.io/registered-item-model/build/register.json
  version: '0.3'
  dateOfLastChange: '2026-10-27'

```

Links to the schema:

* YAML version: [schema.yaml](https://nielshoffmann.github.io/registered-item-model/build/annotated/model/registered-item/geoprocessing-activity-types/schema.json)
* JSON version: [schema.json](https://nielshoffmann.github.io/registered-item-model/build/annotated/model/registered-item/geoprocessing-activity-types/schema.yaml)


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
    "usedEntityType": {
      "@id": "rim:activity-type/usedEntityType",
      "@type": "@id"
    },
    "generatedEntityType": {
      "@id": "rim:activity-type/generatedEntityType",
      "@type": "@id"
    },
    "associatedAgentType": {
      "@id": "rim:activity-type/associatedAgentType",
      "@type": "@id"
    },
    "informedByActivityType": {
      "@id": "rim:activity-type/informedByActivityType",
      "@type": "@id"
    },
    "rim": "https://w3id.org/ogc/rim/",
    "prov": "http://www.w3.org/ns/prov#",
    "dct": "http://purl.org/dc/terms/",
    "rdfs": "http://www.w3.org/2000/01/rdf-schema#",
    "skos": "http://www.w3.org/2004/02/skos/core#",
    "xsd": "http://www.w3.org/2001/XMLSchema#",
    "owl": "http://www.w3.org/2002/07/owl#",
    "acttype": "rim:activity-type/",
    "@version": 1.1
  }
}
```

You can find the full JSON-LD context here:
[context.jsonld](https://nielshoffmann.github.io/registered-item-model/build/annotated/model/registered-item/geoprocessing-activity-types/context.jsonld)

## Sources

* [OGC GeoSPARQL 1.1](https://docs.ogc.org/is/22-047r1/22-047r1.html)
* [W3C PROV-O](https://www.w3.org/TR/prov-o/)

# For developers

The source code for this Building Block can be found in the following repository:

* URL: [https://github.com/NielsHoffmann/registered-item-model](https://github.com/NielsHoffmann/registered-item-model)
* Path: `_sources/geoprocessing-activity-types`

