# Get Collection Load State

POST: http://io.memora/collections/get_load_state

Returns the load state of the given collection inside your project.

## Example


```shell
curl --location --request POST "http://io.memora/collections/get_load_state" \
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

result = client.collections.get_load_state(collection_name="example_collection")
```
## Request Body

```json
{
    "project_id": "string",
    "collectionName": "string"
}
```

- `project_id` __string__ (required)</br> ID of the project where the collection is.

- `collectionName` __string__ (required)</br>The name of the collection to get the load state of.


## Response

Returns the load state of the collection.

### Response Bodies

Successful request
```json
{
    "data": {
        "loadState": "string",
        "loadProgress": "integer"
    }
}
```
- `data` __object__ 

    - `loadState` __string__ </br> Specifies if the data is loaded or not.

    - `loadProgress` __integer__ </br> An integer that gives the percentage of the data loaded into memory.

Failed request
```json
{
    "message": "string"
}
```
- `message` __string__ </br> A string indicating the possible reason of the error 
