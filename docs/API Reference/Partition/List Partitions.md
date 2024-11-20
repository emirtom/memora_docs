# List Partitions

POST: http://io.gandi/partitions/list

Returns the list of the partitions inside your collection.

## Authorization

## Example


```shell
curl --location --request POST "http://io.gandi/partitions/list" \
--header "Content-Type: application/json" \
--header "Api-Key: $YOUR_API_KEY" \
--data-raw '{
    "project_id": "YOUR_PROJECT_ID",
    "collectionName": "example_collection"
}'
```
```python
from gandipy import Gandi

client = Gandi(api_key="YOUR_API_KEY", project_id="YOUR_PROJECT_ID")

result = client.partitions.list(project_id="YOUR_PROJECT_ID", collection_name="example_collection")
```

## Request Body

```json
{
    "project_id": "string",
    "collectionName": "string"
}
```
    
- `project_id` __string__ (required)</br> ID of the project where the requested collection exists.

- `collectionName` __string__ (required)</br> The name of the collection to list the partitions inside it.

## Response

Returns a list of the partitions.

### Response Bodies

Successful request
```json
{
    "data": [
        "string"
    ]
}
```
- `data` __array[string]__ </br> The list of the partition names inside the collection.


Failed request
```json
{
    "message": "string"
}
```
- `message` __string__ </br> A string indicating the possible reason of the error 
