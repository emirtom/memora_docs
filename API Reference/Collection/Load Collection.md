# Load Collection

POST: http://io.memora/collections/load

Loads the data of the given collection into memory.

## Example


```shell
curl --location --request POST "http://io.memora/collections/load" \
--header "Content-Type: application/json" \
--header "Api-Key: $YOUR_API_KEY" \
--data-raw '{
    "collectionName": "example_collection",
    "project_id": "YOUR_PROJECT_ID"
}'
```
```python
from memoradb import Memora

client = Memora(api_key="YOUR_API_KEY", project_id="YOUR_PROJECT_ID")

result = client.collections.load(collection_name="example_collection")
```
## Request Body

```json
{
    "project_id": "string",
    "collectionName": "string"
}
```

- `project_id` __string__ (required)</br> ID of the project where the collection is.

- `collectionName` __string__ (required)</br>The name of the collection to be loaded.


## Response

Returns a message.

### Response Body

```json
{
    "message": "string"
}
```

The response code will be 200 if the request is succesfull and some other code if it is unsucessful alongside the error message.