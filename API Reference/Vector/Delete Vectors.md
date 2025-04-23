# Delete Vectors

POST: http://io.memora/vectors/delete

Deletes entities by their IDs or with a boolean expression.

## Example


```shell
curl --location --request POST "http://io.memora/vectors/delete" \
--header "Content-Type: application/json" \
--header "Api-Key: $YOUR_API_KEY" \
--data-raw '{
    "filter": "id in [1, 2, 3]",
    "collectionName": "example_collection",
    "project_id": "YOUR_PROJECT_ID"
}'
```
```python
from memoradb import Memora

client = Memora(api_key="YOUR_API_KEY", project_id="YOUR_PROJECT_ID")

result = client.vectors.delete(collection_name="example_collection", filter="id in [1, 2, 3]")
```
## Request Body

```json
{
    "project_id": "string",
    "collectionName": "string",
    "filter": "string",
    "partitionName": "string"
}
```

- `project_id` __string__ (required)</br> ID of the project where the collection is.

- `collectionName` __string__ (required)</br>The name of the collection where the specified index is.

- `filter` __string__ (required)</br> It can be used to match IDs or used as a scalar filtering condition to specify entities. The rules of the scalar filter can be found in "Boolean Expression Rules".

- `partitonName` __string__ </br> The entities will be deleted from this partition in the collection if specified.



## Response

Returns a message.

### Response Body

```json
{
    "message": "string"
}
```

The response code will be 200 if the request is succesfull and some other code if it is unsucessful alongside the error message.