# Release Partitions

POST: http://io.gandi/partitions/release

Releases the data of the given partitions from memory.

## Authorization

## Example


```shell
curl --location --request POST "http://io.gandi/partitions/release" \
--header "Content-Type: application/json" \
--header "Api-Key: $YOUR_API_KEY" \
--data-raw '{
    "partitionName": [
        "_default",
        "example_partition"
    ],
    "collectionName": "example_collection",
    "project_id": "YOUR_PROJECT_ID"
}'
```
```python
from gandipy import Gandi

client = Gandi(api_key="YOUR_API_KEY", project_id="YOUR_PROJECT_ID")

result = client.partitions.release(
            collection_name="example_collection",
            partition_names= [
                    "_default",
                    "example_partition"
                ]
            )
```
## Request Body

```json
{
    "project_id": "string",
    "collectionName": "string",
    "partitionNames": [
        "string"
    ]
}
```

- `project_id` __string__ (required)</br> ID of the project where the collection is.

- `collectionName` __string__ (required)</br>The name of the collection where the partitions is.

- `partitionNames` __array[string]__ (required)</br> Names of the partitions to release from memory.


## Response

Returns a message.

### Response Body

```json
{
    "message": "string"
}
```

The response code will be 200 if the request is succesfull and some other code if it is unsucessful alongside the error message.