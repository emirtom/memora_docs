# Get Partition Statistics

POST: http://io.memora/partitions/get_stats

Returns the number of rows in the given partition inside your collection.

## Example


```shell
curl --location --request POST "http://io.memora/partitions/get_stats" \
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

client = Memora(api_key="YOUR_API_KEY", project_id="YOUR_PROJECT_ID")

result = client.partitions.get_stats(collection_name="example_collection", partition_name="example_partition")
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

- `partitionName` __string__ (required)</br>The name of the partition to get the number of entities of.


## Response

Returns the load state of the collection.

### Response Bodies

Successful request
```json
{
    "data": {
        "rowCount": "integer"
    }
}
```
- `data` __object__ 

    - `rowCount` __integer__ </br> Number of entities in the given partition.

Failed request
```json
{
    "message": "string"
}
```
- `message` __string__ </br> A string indicating the possible reason of the error 
