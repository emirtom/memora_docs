# Create Collection

POST: http://io.memora/collections/create

This operation creates a collection inside your database.

## Example

You can create a collection quickly by only defining the collection name and dimension or use a schema to configure the collection based on your needs. <br/>
The collection will be created with two fields which are primary and vector fields. They will have their default names as 'id' and 'vector', respectively.

```shell
curl --location --request POST "http://io.memora/collections/create" \
--header "Content-Type: application/json" \
--header "Api-Key: $YOUR_API_KEY" \
--data-raw '{
    "collectionName": "example_collection",
    "dimension": 5,
    "project_id": "YOUR_PROJECT_ID"
}'
```
```python
from memoradb import Memora
from memoradb.models import Collection

client = Memora(api_key="YOUR_API_KEY", project_id="YOUR_PROJECT_ID")

collection = Collection(collection_name="example_collection", dimension=5)

result = client.collections.create(collection=collection)
```

Above setup will set its metric type as COSINE and the collection will be ready to use.

## Request Body

```json
{
    "project_id": "string",
    "collectionName": "string",
    "dimension": "integer",
    "metricType": "string",
    "idType": "string",
    "autoID": "boolean",
    "primaryFieldName": "string",
    "vectorFieldName": "string",
    "schema": {
        "autoID": "boolean",
        "enableDynamicField": "boolean",
        "fields": [
            {
                "fieldName": "string",
                "dataType": "string",
                "elementDataType": "string",
                "isPrimary": "boolean",
                "isPartitionKey": "boolean",
                "elementTypeParams": {
                    "max_length": "integer",
                    "dim": "integer",
                    "max_capacity": "integer"
                }
            }
        ]
    },
    "indexParams": [
        {
            "metricType": "string",
            "fieldName": "string",
            "indexName": "string",
            "params": {
                "index_type": "string",
                "M": "integer",
                "efConstruction": "integer",
                "nlist": "integer"
            }
        }
    ],
    "params": {
        "max_length": "integer",
        "enableDynamicField": "boolean",
        "shardsNum": "integer",
        "partitionsNum": "integer",
        "ttlSeconds": "integer"
    }
}'
```

- `project_id` __string__ (required)</br> ID of the project where the collection will be created.

- `collectionName` __string__ (required)</br>The name of the collection to be created.

- `dimension` __integer__ </br>The dimension of the vectors in the collection. </br>This is required if data type of this field is set to Float Vector.

- `metricType` __string__ </br>The metric type to be used for the collection. </br> Possible values are **COSINE**, **IP** and **L2**. </br> This value defaults to **COSINE**.

- `idType`* __string__ </br>The data type of the primary field in the collection.

- `autoID`* __boolean__ </br> Specifies whether the ID field should be automatically generated.

- `primaryFieldName`* __string__ </br>The name of the primary field in the collection.

- `vectorFieldName`* __string__ </br>The name of the vector field in the collection.

- `schema` __object__ </br> An object containing the schema configuration for the collection. A valid schema needs to have a primary field and a vector field.

    - `autoID` __boolean__ </br>Specifies whether the primary field in the schema should be automatically generated. If marked true, the primary field should not be in the data to avoid errors.

    - `enableDynamicField` __boolean__ </br>Specifies whether dynamic fields are enabled in the schema.

    - `fields` __array__ </br>An array of objects representing the fields in the schema.

        - `fieldName` __string__ </br>The name of the field to create in the specified collection.

        - `dataType` __string__ </br>The data type of the field.

        - `elementDataType` __string__ </br>The data type of the elements in the field.

        - `isPrimary` __boolean__ </br>Specifies whether the field is a primary field.

        - `isPartitionKey` __boolean__ </br>Specifies whether the field is a partition key.

        - `elementTypeParams` __object__ </br>An object containing additional parameters for the field.

            - `max_length` __integer__ </br>The maximum length of the string if the data type is VarChar.

            - `dim` __integer__ </br>The dimension of the field if the data type is Float Vector or Binary Vector.

            - `max_capacity` __integer__ </br>The maximum capacity of the array for the Array data type.

- `indexParams` __array__ </br>An array of objects representing the index parameters for the collection.

    - `metricType` __string__ </br>The metric type to be used for the index. </br> This value defaults to **COSINE**.

    - `fieldName` __string__ </br>The name of the field to be indexed.

    - `indexName` __string__ </br>The name of the index.

    - `params` __object__ </br>An object containing additional parameters for the index.

        - `index_type` __string__ </br>The type of the index.

        - `M` __integer__ </br>The maximum degree of the node. Applicable only if index_type is set to **HNSW**.

        - `efConstruction` __integer__ </br> Scope of the search. Applicable only if index_type is set to **HNSW**.

        - `nlist` __integer__ </br>The value of nlist parameter for the index.

- `params` __object__ </br>An object containing additional parameters for the collection.

    - `max_length` __integer__ </br>The maximum length of the string for a VarChar type field.

    - `enableDynamicField` __boolean__ </br>Specifies whether dynamic fields are enabled in the collection.

    - `shardsNum` __integer__ </br>The number of shards for the collection. Each shard acts as a node that the databes distributes its write operations to. 

    - `partitionsNum` __integer__ </br>The number of partitions for the collection.

    - `ttlSeconds` __integer__ </br>The time-to-live in seconds for the collection. If sent, the collection will be dropped after the given time period.

</br>
* These parameters are designed for the quick-setup of the collection and will be ignored if schema is defined.

## Response

Returns a message.

### Response Body

```json
{
    "message": "string"
}
```

The response code will be 201 if the request is successful and some other code if it is unsucessful alongside the error message.
