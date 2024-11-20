# Create Partition

POST: http://io.gandi/partitions/drop

Drops a specific partition inside your collection. Ensure that the partition is released before dropping.

## Authorization

## Example


```shell
curl --location --request POST "http://io.gandi/partitions/drop" \
--header "Content-Type: application/json" \
--header "Api-Key: $YOUR_API_KEY" \
--data-raw '{
    "partitionName": "example_partition",
    "collectionName": "example_collection",
    "project_id": "YOUR_PROJECT_ID"
}'
```
```python
from gandipy import Gandi

client = Gandi(api_key="YOUR_API_KEY", project_id="YOUR_PROJECT_ID")

result = client.partitions.drop(collection_name="example_collection", partition_name="example_partition")
```
## Request Body

```json
{
    "project_id": "string",
    "collectionName": "string",
    "partitionName": "string"
}
```

- `project_id` __string__ (required)</br> ID of the project where the collection is.

- `collectionName` __string__ (required)</br> The name of the collection where the specified partition is.

- `partitionName` __string__ (required)</br> The name of the partition to drop.



## Response

Returns a message.

### Response Body

```json
{
    "message": "string"
}
```

The response code will be 200 if the request is succesfull and some other code if it is unsucessful alongside the error message.