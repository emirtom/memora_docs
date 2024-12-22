# Create Index

POST: http://io.gandi/indexes/create

This operation creates an index for a vector field or a scalar field inside a specified collection.

## Authorization

## Example

```shell
curl --location --request POST "http://io.gandi/indexes/create" \
--header "Content-Type: application/json" \
--header "Api-Key: $YOUR_API_KEY" \
--data-raw '{
    "project_id": "YOUR_PROJECT_ID",
    "collectionName": "example_collection",
    "indexParams": [
        {
            "metricType": "COSINE",
            "fieldName": "vector",
            "indexName": "example_index",
            "indexConfig": {
                "index_type": "IVF_FLAT",
                "nlist": 2048
            }
        }
    
    ]
}'
```
```python
from gandipy import Gandi
from gandipy.models import Index, IndexConfig

client = Gandi(api_key="YOUR_API_KEY", project_id="YOUR_PROJECT_ID")

index = Index(
            metric_type="COSINE",
            field_name="vector",
            index_name="example_index",
            IndexConfig(index_type="IVF_FLAT", nlist=2048)
            )

result = client.indexes.create(collection_name="example_collection", index_params=[index])
```
## Request Body

```json
{
    "project_id": "string",
    "collectionName": "string",
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
    ]
}
```

- `project_id` __string__ (required)</br> ID of the project where the collection will be created.

- `collectionName` __string__ (required)</br>The name of the collection to be created.

- `indexParams` __array__ </br>An array of objects representing the index parameters to create in the given collection.

    - `metricType` __string__ </br>The metric type to be used for the index. </br> This value defaults to **COSINE**.

    - `fieldName` __string__ </br>The name of the field to be indexed.

    - `indexName` __string__ </br>The name of the index.

    - `params` __object__ </br>An object containing additional parameters for the index.

        - `index_type` __string__ </br>The type of the index.

        - `M` __integer__ </br>The maximum degree of the node. Applicable only if index_type is set to **HNSW**.

        - `efConstruction` __integer__ </br> Scope of the search. Applicable only if index_type is set to **HNSW**.

        - `nlist` __integer__ </br>The value of nlist parameter for the index.


## Response

Returns a message.

### Response Body

```json
{
    "message": string
}
```

The response code will be 201 if the request is succesfull and some other code if it is unsucessful alongside the error message.
