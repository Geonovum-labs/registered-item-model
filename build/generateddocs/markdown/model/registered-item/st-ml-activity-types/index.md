
# SpatioTemporal ML Activity Types Profile (Model)

`ogc.model.registered-item.st-ml-activity-types` *v0.3*

A profile of the SpatioTemporal Machine learning Activity Types Profile describing spatiotemporal machine learning training and inference runs, and registrable ML activity types, using the STAC MLM extension's task, framework/accelerator and input/output vocabulary.

[*Status*](http://www.opengis.net/def/status): Under development

## Examples

### An inference run that adds its result to a register
A land-cover segmentation model, described as a STAC Item per the MLM extension, is run
against a Sentinel-2 scene. The run is recorded as an `mlact:InferenceActivityType`: itself a
`rim:RegisterAction`, because in this ecosystem running a model is a governed act — here,
the one that adds the predicted mask to the register as a new `rim:RegisterItem`. The
activity's task, framework and accelerator are all drawn from the MLM extension's own
controlled vocabularies; its input and output entities each declare, via `dct:conformsTo`,
which of the model's declared `mlm:ModelInput`/`mlm:ModelOutput` specifications they realise.

#### turtle
```turtle
ex:landCoverModel a stac:Item ;
    mlm:name "Land Cover Classifier" ;
    mlm:architecture "U-Net" ;
    mlm:performsTask mlm:semantic-segmentation ;
    mlm:framework "PyTorch" ;
    mlm:ActivityInput ex:landCoverInputSpec ;
    mlm:ActivityOutput ex:landCoverOutputSpec .

ex:landCoverInputSpec a mlm:ModelInput ;
    mlm:io_name "sentinel2_bands" ;
    mlm:input_structure ex:landCoverInputStructure .

ex:landCoverInputStructure a mlm:InputStructure ;
    mlm:shape -1, 4, 512, 512 ;
    mlm:dim_order "batch", "channel", "height", "width" ;
    mlm:data_type "float32" .

ex:landCoverOutputSpec a mlm:ModelOutput ;
    mlm:io_name "segmentation_mask" ;
    mlm:result ex:landCoverResultStructure .

ex:landCoverResultStructure a mlm:ResultStructure ;
    mlm:shape -1, 10, 512, 512 ;
    mlm:dim_order "batch", "class", "height", "width" ;
    mlm:data_type "uint8" .

ex:sceneChip a mlact:ActivityInput ;
    dct:conformsTo ex:landCoverInputSpec ;
    mlact:structure ex:landCoverInputStructure .

ex:predictedMask a mlact:ActivityOutput ;
    dct:conformsTo ex:landCoverOutputSpec ;
    mlact:structure ex:landCoverResultStructure .

ex:inference2026-09-01 a mlact:InferenceActivityType ;
    rim:itemClass acttype:activityTypeItemClass ;
    rim:objectIdentifier "https://example.org/registers/ml-activities/runs/inference2026-09-01"^^xsd:anyURI ;
    rim:validityStatus rim:valid ;
    rim:publicationStatus rim:published ;
    mlact:model ex:landCoverModel ;
    mlact:performsTask mlm:semantic-segmentation ;
    mlact:framework "PyTorch" ;
    mlact:accelerator mlm:cuda ;
    mlact:input ex:sceneChip ;
    mlact:output ex:predictedMask ;
    rim:actionType rim:additionAction ;
    rim:appliesTo ex:predictedItem ;
    prov:startedAtTime "2026-09-01T10:00:00Z"^^xsd:dateTime ;
    prov:endedAtTime "2026-09-01T10:00:05Z"^^xsd:dateTime .

ex:predictedItem a rim:RegisterItem ;
    dct:title "Predicted land cover mask, scene S2A_2026-09-01" ;
    rim:itemClass ex:derivedProductItemClass ;
    rim:objectIdentifier "https://example.org/registers/ml-activities/items/predicted-mask-2026-09-01"^^xsd:anyURI ;
    rim:validityStatus rim:valid ;
    rim:publicationStatus rim:published ;
    rim:hasAction ex:inference2026-09-01 .

ex:derivedProductItemClass a rim:RegisterItemClass ;
    dct:title "Derived ML product" .

```


### Registering an ML inference activity type
A geoprocessing activity type register holds "land cover segmentation", a narrower type of
`mlact:InferenceActivityType`. Through `mlact:MLActivityType` it is also a
`geoproc:GeoprocessingActivityType`, so three profiles' shapes apply. The activity type profile
requires the item class and the `acttype:ActivityType` ancestry. The geoprocessing profile requires
a spatial used or generated entity type, met here because `mlact:ActivityInput` is a
`geo:SpatialObject`. This profile requires an `mlact:ActivityInput` used type and, for
inference, an `mlact:ActivityOutput` generated type. The plan gives the steps every run of
this type follows. Individual runs, like the one in the previous example, are then typed
`ex:LandCoverSegmentation`.

#### turtle
```turtle
ex:mlActivityTypeRegister a rim:Register ;
    dct:title "EO Machine Learning Activity Types" .

ex:LandCoverSegmentation a acttype:ActivityType , owl:Class ;
    rdfs:subClassOf mlact:InferenceActivityType ;
    rdfs:label "Land cover segmentation" ;
    dct:title "Land cover segmentation" ;
    dct:description "Runs a semantic segmentation model over a multispectral scene to produce a per-pixel land cover mask." ;
    rim:inRegister ex:mlActivityTypeRegister ;
    rim:itemClass acttype:activityTypeItemClass ;
    rim:objectIdentifier "https://example.org/registers/ml-activities/types/land-cover-segmentation/v1"^^xsd:anyURI ;
    rim:validityStatus rim:valid ;
    rim:publicationStatus rim:published ;
    acttype:usedEntityType mlact:ActivityInput ;
    acttype:generatedEntityType mlact:ActivityOutput ;
    acttype:associatedAgentType prov:SoftwareAgent ;
    acttype:plan ex:landCoverSegmentationPlan .

ex:landCoverSegmentationPlan a prov:Plan , p-plan:Plan ;
    dct:title "Land cover segmentation procedure" .

ex:tileScene a p-plan:Step ;
    p-plan:isStepOfPlan ex:landCoverSegmentationPlan ;
    dct:description "Tile the scene into chips matching the model's declared input shape." .

ex:normalizeBands a p-plan:Step ;
    p-plan:isStepOfPlan ex:landCoverSegmentationPlan ;
    p-plan:isPrecededBy ex:tileScene ;
    dct:description "Apply the model's declared value scaling to each band." .

ex:runModel a p-plan:Step ;
    p-plan:isStepOfPlan ex:landCoverSegmentationPlan ;
    p-plan:isPrecededBy ex:normalizeBands ;
    dct:description "Run the model over each chip." .

ex:mosaicMask a p-plan:Step ;
    p-plan:isStepOfPlan ex:landCoverSegmentationPlan ;
    p-plan:isPrecededBy ex:runModel ;
    dct:description "Take the per-pixel argmax and mosaic the chips back into a scene-sized mask." .

```

## Schema

```yaml
$schema: https://json-schema.org/draft/2020-12/schema
title: SpatioTemporal ML Activity Types Profile
description: 'A profile of the Activity Type Register Profile for SpatioTemporal ML
  Activity Types

  '
allOf:
- $ref: https://nielshoffmann.github.io/registered-item-model/build/annotated/model/registered-item/geoprocessing-activity-types/schema.yaml
x-bblocks-metadata:
  itemIdentifier: ogc.model.registered-item.st-ml-activity-types
  register: https://nielshoffmann.github.io/registered-item-model/build/register.json
  version: '0.3'
  dateOfLastChange: '2026-10-27'

```

Links to the schema:

* YAML version: [schema.yaml](https://nielshoffmann.github.io/registered-item-model/build/annotated/model/registered-item/st-ml-activity-types/schema.json)
* JSON version: [schema.json](https://nielshoffmann.github.io/registered-item-model/build/annotated/model/registered-item/st-ml-activity-types/schema.yaml)


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
[context.jsonld](https://nielshoffmann.github.io/registered-item-model/build/annotated/model/registered-item/st-ml-activity-types/context.jsonld)

## Sources

* [STAC Machine Learning Model (MLM) Extension Ontology building block](https://github.com/ogcincubator/bblocks-stac/tree/master/_sources/extensions/mlm-ontology)
* [STAC MLM extension README (field descriptions and example)](https://github.com/stac-extensions/mlm/blob/main/README.md)
* [W3C PROV-O](https://www.w3.org/TR/prov-o/)

# For developers

The source code for this Building Block can be found in the following repository:

* URL: [https://github.com/NielsHoffmann/registered-item-model](https://github.com/NielsHoffmann/registered-item-model)
* Path: `_sources/st-ml-activity-types`

