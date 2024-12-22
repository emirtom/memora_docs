# List Indexes

POST: http://io.gandi/indexes/list

Lists the indexes inside a specified collection.

## Authorization

## Example


```shell
curl --location --request POST "http://io.gandi/indexes/list" \
--header "Content-Type: application/json" \
--header "Api-Key: $YOUR_API_KEY" \
--data-raw '{
    "collectionName": "example_collection",
    "project_id": "YOUR_PROJECT_ID"
}'
```

## Request Body

```json
{
    "project_id": "string",
    "collectionName": "string",
    "indexName": "string"
}
```
```python
from gandipy import Gandi

client = Gandi(api_key="YOUR_API_KEY", project_id="YOUR_PROJECT_ID")

result = client.indexes.list(collection_name="example_collection")
```

- `project_id` __string__ (required)</br> ID of the project where the collection is.

- `collectionName` __string__ (required)</br>The name of the collection to list its indexes.


## Response

Returns a list of the indexes.

### Response Bodies

Successful request
```json
{
    "data": [
        "string"
    ]
}
```
- `data` __array__ </br> The list of the index names

    - `data[]` __string__ </br> The name of the index.

Failed request
```json
{
    "message": "string"
}
```
- `message` __string__ </br> A string indicating the possible reason of the error 