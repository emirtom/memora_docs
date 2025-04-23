# Has Partition

POST: http://io.memora/partitions/has

Returns whether the given partition exist inside your collection.

## Example


```shell
curl --location --request POST "http://io.memora/partitions/has" \
--header "Content-Type: application/json" \
--header "Api-Key: $YOUR_API_KEY" \
--data-raw '{
    "partitionName": "example_partition",
    "collectionName": "example_collection",
    "project_id": "YOUR_PROJECT_ID"
}'
```
```python
from memoradb import Memora

client = Memoraapi_key="YOUR_API_KEY", project_id="YOUR_PROJECT_ID")

result = client.partitions.has(collection_name="example_collection", partition_name="example_partition")
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

- `collectionName` __string__ (required)</br>The name of the collection where the partition is.

- `partitionName` __string__ (required)</br>The name of the partition to check whether it exits.


## Response

Returns whether the collection has the partition.

### Response Bodies

Successful request
```json
{
    "data": {
        "has": "boolean"
    }
}
```
- `data` __object__ 

    - `has` __boolean__ </br> Indicates whether the collection exists.

Failed request
```json
{
    "message": "string"
}
```
- `message` __string__ </br> A string indicating the possible reason of the error 
