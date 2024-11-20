# Drop Index

POST: http://io.gandi/indexes/drop

Drops the given index inside your collection.

## Authorization

## Example


```shell
curl --location --request POST "http://io.gandi/indexes/drop" \
--header "Content-Type: application/json" \
--header "Api-Key: $YOUR_API_KEY" \
--data-raw '{
    "indexName": "example_index",
    "collectionName": "example_collection",
    "project_id": "YOUR_PROJECT_ID"
}'
```

```python
from gandipy import Gandi

client = Gandi(api_key="YOUR_API_KEY", project_id="YOUR_PROJECT_ID")

result = client.indexes.drop(collection_name="example_collection", index_name="example_index")
```

## Request Body

```json
{
    "project_id": "string",
    "collectionName": "string",
    "indexName": "string"
}
```

- `project_id` __string__ (required)</br> ID of the project where the collection is.

- `collectionName` __string__ (required)</br>The name of the collection where the specified index is.

- `indexName` __string__ (required)</br> The name of the index to be deleted.



## Response

Returns a message.

### Response Body

```json
{
    "message": "string"
}
```

The response code will be 200 if the request is succesfull and some other code if it is unsucessful alongside the error message.