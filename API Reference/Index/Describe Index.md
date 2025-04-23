# Describe Index

POST: http://io.memora/indexes/describe

This operation the given index inside your collection.

## Example

```shell
curl --location --request POST "http://io.memora/indexes/describe" \
--header "Content-Type: application/json" \
--header "Api-Key: $YOUR_API_KEY" \
--data-raw '{
    "project_id": "YOUR_PROJECT_ID",
    "collectionName": "example_collection",
    "indexName": "example_index"
}'
```
```python
from memoradb import Memora

client = Memoraapi_key="YOUR_API_KEY", project_id="YOUR_PROJECT_ID")

result = client.indexes.describe(collection_name="example_collection", index_name="example_index")
```

## Request Body

```json
{
    "project_id": "string",
    "collectionName": "string",
    "indexName": "string"
}
```

- `project_id` __string__ (required)</br> ID of the project where the collection will be created.

- `collectionName` __string__ (required)</br>The name of the collection to be created.

- `indexName` __string__ (required)</br> The name of the index to describe.


## Response

Returns the description of the index.

### Response Bodies

Successful request
```json
{
    "data": [
        {
            "fieldName": "string",
            "indexName": "string",
            "indexState": "string",
            "indexType": "string",
            "indexedRows": "integer",
            "metricType": "string",
            "pendingRows": "integer",
            "totalRows": "integer",
            "failReason": "string"
        }
    ]
}
```
- `data` __array__  </br> 

    - `fieldName` __string__ </br> The name of the field that the specified index belongs to.

    - `indexName` __string__ </br> The name of the index.

    - `indexState` __string__ </br> The state of the loading progress of the index.

    - `indexType` __string__ </br> The type of this index.

    - `indexedRows` __integer__ </br> The number of rows that have been indexed so far.

    - `metricType` __string__ </br> The type of the metric.

    - `pendingRows` __integer__ </br> The number of rows that are waiting to be indexed.
  
    - `totalRows` __integer__ </br> The total number of entities that belong to the index.

    - `failReason` __string__ </br> The reason for the failure to build the index. This will be empty if there is no error.
Failed request
```json
{
    "message": "string"
}
```
- `message` __string__ </br> A string indicating the possible reason of the error 