# Get Collection Statistics

POST: http://io.memora/collections/get_stats

Returns the number of rows in the given collection inside your project.

## Example


```shell
curl --location --request POST "http://io.memora/collections/get_stats" \
--header "Content-Type: application/json" \
--header "Api-Key: $YOUR_API_KEY" \
--data-raw '{
    "collectionName": "example_collection",
    "project_id": "YOUR_PROJECT_ID"
}'
```
```python
from memoradb import Memora

client = Memoraapi_key="YOUR_API_KEY", project_id="YOUR_PROJECT_ID")

result = client.collections.get_stats(collection_name="example_collection")
```
## Request Body

```json
{
    "project_id": "string",
    "collectionName": "string"
}
```
    
- `project_id` __string__ (required)</br> ID of the project where the collection is.

- `collectionName` __string__ (required)</br>The name of the collection to get the number of entities of.


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

    - `rowCount` __integer__ </br> Number of entities in the given collection.

Failed request
```json
{
    "message": "string"
}
```
- `message` __string__ </br> A string indicating the possible reason of the error 
