# Load Partitions

POST: http://io.memora/partitions/load

Loads the data of the given partitions into memory.

## Example


```shell
curl --location --request POST "http://io.memora/partitions/load" \
--header "Content-Type: application/json" \
--header "Api-Key: $YOUR_API_KEY" \
--data-raw '{
    "partitionNames": [
        "_default",
        "example_partition"
    ],
    "collectionName": "example_collection",
    "project_id": "YOUR_PROJECT_ID"
}'
```
```python
from memoradb import Memora

client = Memora(api_key="YOUR_API_KEY", project_id="YOUR_PROJECT_ID")

result = client.partitions.load(
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

- `partitionNames` __array[string]__ (required)</br> Names of the partitions to load into memory.


## Response

Returns a message.

### Response Body

```json
{
    "message": "string"
}
```

The response code will be 200 if the request is succesfull and some other code if it is unsucessful alongside the error message.