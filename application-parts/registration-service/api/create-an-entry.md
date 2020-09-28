# Create A New Entry

Before a COLID entry is published, a draft must be created first. Afterwards it can be published. A published entry can be edited while a new draft version is created which can be published again.

This section explains how to create a (draft) entry in COLID by using the API.

## API Usage

```shell
POST "https://localhost:51770/api/v3/resource" {...}
```

## Description

The construction of an appropriate request for creating a new COLID entry starts with the decision of which type the new entry should be. To get an overview of all valid entry types you can use the `metadata/hierarchy` interface which delivers the whole inheritance hierarchy of all entry types. It is important to know that only the types on the deepest level of the hierarchy are valid in COLID.

When chosen the correct entry type a complete description of the entry format can be requested by using the `metadata` interface in combination with the entry type´s `key` value.

Additional information can be found under [API: Metadata](../60_metadataAndInstances#api-metadata).

The metadata provides nearly all necessary information about a successful request but some details should be outlined for clarification:

### IDs

Every request needs at least one ID for the entry itself of type `http://pid.bayer.com/kos/19014/hasPID`. IDs in general can be identified by having the `"http://www.w3.org/2000/01/rdf-schema#range": "http://pid.bayer.com/kos/19014/PermanentIdentifier"` metadata property attached in the metadata definition.

In a request you can choose a PID URI template (as in the [Example Request](#example-request)) and set the `id` of the ID to `null`. In this way a new ID will be generated by the system depending on the template you choose. For on overview of available templates you can have a look to the specific API on the Swagger page.

IDs are also needed for distribution endpoints but DEs are not necessary for a basic entry.

The system generated IDs can be found in the response to your request.

### Primitives

Primitives are basic data types like a number (int, long, ...), a date (datetime) or a string (char, string). They can be identified by having the `"http://www.w3.org/ns/shacl#nodeKind": "http://www.w3.org/ns/shacl#Literal"` metadata property attached in the metadata definition. Every single metadata of one of these basic data types could be directly written to the request.

### Entities & Instances

Entities can be identified by having the `"http://www.w3.org/ns/shacl#nodeKind": "http://www.w3.org/ns/shacl#IRI"` metadata property attached in the metadata definition. In this case the `id` of an existing instance of an entity needs to be entered in the request as the property´s value.
To find currently existing instances for a specific entity the `entityList` interface ([API: Entity](../60_metadataAndInstances#api-entity)) could be used in combination with the `http://www.w3.org/2000/01/rdf-schema#range` value as type.
There are some exceptions where special interfaces are build for, e.g. *ConsumerGroup*, *Keywords*, *ExtendedUriTemplate*, *PidUriTemplate*.

### Distribution endpoint

A distribution endpoint points to the place where the described data could be found or where additional information about the described data could be found. Therefore several distribution endpoints of different types can be created. The necessary information is nested in the request by default.

## A new version

There are two types of versions - a horizontal and a vertical one. While the vertical versions of an entry only describe the changes of one specific entry (e.g. by editing the description), the horizontal version is a similar entry but with a higher version number (e.g. a new ontology or API version). The vertical versioning will be handles automatically by the system, the horizontal versioning needs to be done by yourself.
In case you are going to create a new horizontal version, you need to add the `https://pid.bayer.com/kos/19050/hasPreviousVersion: "<id>"` property to your response and increment the `hasVersion` property. The `hasPreviousVersion` properties value is the PID URI of the earlier version.

## Linking of entries

Linking of data and data sets is an essential part of COLID and should be used as much as possible but meaningful. To allow the linking of data there are some special properties with a determined semantic meaning, here e.g. from entry type *GenericDataset*:

+ isSubsetOfDataset
+ sameDatasetAs
+ isCopyOfDataset
+ isDerivedFromDataset
+ hasHistoricVersion - *Will be handled automatically by the API*
+ hasLaterVersion - *Will be handled automatically by the API*

These property values are the `id` of the linked entry.

## Additional hints

+ All values needs to be send in an array format, e.g. the version will be written like `"https://pid.bayer.com/kos/19050/hasVersion": ["1"]`.
+ The following values are mandatory and will result in a validation error when not given or given in a wrong format:
    + Entity type [`http://www.w3.org/1999/02/22-rdf-syntax-ns#type`]
    + PID URI [`http://pid.bayer.com/kos/19014/hasPID`]
    + Base URI [`https://pid.bayer.com/kos/19050/hasBaseURI`]
    + Label [`https://pid.bayer.com/kos/19050/hasLabel`]
    + Version [`https://pid.bayer.com/kos/19050/hasVersion`]
    + Consumer Group [`https://pid.bayer.com/kos/19050/hasConsumerGroup`]
    + Date Created [`https://pid.bayer.com/kos/19050/dateCreatedhasLabel`]
    + Author [`https://pid.bayer.com/kos/19050/author`]
    + Last Change User [`https://pid.bayer.com/kos/19050/lastChangeUser`]
  
## Example Request

```json
{
    "properties": {
        "https://pid.bayer.com/kos/19050/hasLabel": ["COLID API HowTo"],
        "https://pid.bayer.com/kos/19050/hasResourceDefinition": ["A short how-to about the usage of the COLID API..."],
        "http://pid.bayer.com/kos/19014/hasPID": [
            {
                "properties": {
                    "http://www.w3.org/1999/02/22-rdf-syntax-ns#type": ["http://pid.bayer.com/kos/19014/PermanentIdentifier"],
                    "https://pid.bayer.com/kos/19050/hasUriTemplate": ["https://pid.bayer.com/kos/19050#13cd004a-a410-4af5-a8fc-eecf9436b58b"]
                },
                "id": null
            }
        ],
        "https://pid.bayer.com/kos/19050/47119343": ["API", "COLID", "HowTo"],
        "https://pid.bayer.com/kos/19050/hasPIDEditorialNote": ["Only a short overview, not a complete API description."],
        "https://pid.bayer.com/kos/19050/hasVersion": ["1"],
        "https://pid.bayer.com/kos/19050/hasLifecycleStatus": ["https://pid.bayer.com/kos/19050/released"],
        "https://pid.bayer.com/kos/19050/containsLicensedData": ["false"],
        "https://pid.bayer.com/kos/19050/isPersonalData": ["false"],
        "https://pid.bayer.com/kos/19050/hasInformationClassification": ["https://pid.bayer.com/kos/19050/Internal"],
        "https://pid.bayer.com/kos/19050/hasDataSteward": ["rolf.grigat@bayer.com"],
        "https://pid.bayer.com/kos/19050/isSubsetOfDataset": [],
        "https://pid.bayer.com/kos/19050/sameDatasetAs": [],
        "https://pid.bayer.com/kos/19050/isCopyOfDataset": [],
        "https://pid.bayer.com/kos/19050/isDerivedFromDataset": [],
        "https://pid.bayer.com/kos/19050/distribution": [
            {
                "properties": {
                    "http://pid.bayer.com/kos/19014/hasPID": [
                        {
                            "properties": {
                                "https://pid.bayer.com/kos/19050/hasUriTemplate": ["https://pid.bayer.com/kos/19050#13cd004a-a410-4af5-a8fc-eecf9436b58b"],
                                "http://www.w3.org/1999/02/22-rdf-syntax-ns#type": ["http://pid.bayer.com/kos/19014/PermanentIdentifier"]
                            },
                            "id": null
                        }
                    ],
                    "http://www.w3.org/1999/02/22-rdf-syntax-ns#type": ["http://pid.bayer.com/kos/19014/BrowsableResource"],
                    "https://pid.bayer.com/kos/19050/hasNetworkedResourceLabel": ["The starting page of the how-to"],
                    "https://pid.bayer.com/kos/19050/hasContactPerson": ["rolf.grigat@bayer.com"],
                    "https://pid.bayer.com/kos/19050/hasDistributionEndpointLifecycleStatus": ["https://pid.bayer.com/kos/19050/active"],
                    "http://pid.bayer.com/kos/19014/hasNetworkAddress": ["https://bayer-group.github.io/colid-documentation/docs/tech/#/application-parts/registration-service/api"]
                },
                "id": null
            }
        ],
        "https://pid.bayer.com/kos/19050/hasEntryLifecycleStatus": ["https://pid.bayer.com/kos/19050/draft"],
        "https://pid.bayer.com/kos/19050/lastChangeUser": ["rolf.grigat@bayer.com"],
        "https://pid.bayer.com/kos/19050#hasConsumerGroup": ["https://pid.bayer.com/kos/19050#2b3f0380-dd22-4666-a28b-7f1eeb82a5ff"],
        "https://pid.bayer.com/kos/19050/lastChangeDateTime": ["2019-12-19T14:55:28.905Z"],
        "http://www.w3.org/1999/02/22-rdf-syntax-ns#type": ["https://pid.bayer.com/kos/19050/GenericDataset"],
        "https://pid.bayer.com/kos/19050/author": ["rolf.grigat@bayer.com"],
        "https://pid.bayer.com/kos/19050/dateCreated": ["2019-12-19T14:55:28.905Z"],
        "https://pid.bayer.com/kos/19050/hasHistoricVersion": []
    }
}
```