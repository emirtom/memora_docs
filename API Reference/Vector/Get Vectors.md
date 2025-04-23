# Get Vectors

POST: http://io.memora/vectors/get

Gets specific vectors by their IDs.

## Example


```shell
curl --location --request POST "http://io.memora/vectors/get" \
--header "Content-Type: application/json" \
--header "Api-Key: $YOUR_API_KEY" \
--data-raw '{
    "collectionName": "example_collection",
    "project_id": "YOUR_PROJECT_ID",
    "id": [1, 2, 3]
}'
```
```python
from memoradb import Memora

client = Memoraapi_key="YOUR_API_KEY", project_id="YOUR_PROJECT_ID")

result = client.vectors.get(collection_name="example_collection", id=[1, 2, 3])
```

## Request Body

```json
{
    "project_id": "string",
    "collectionName": "string",
    "outputFields": [],
    "partitionNames": [],
    "id": []
}
```

- `project_id` __string__ (required)</br> ID of the project where the collection is.

- `collectionName` __string__ (required)</br>The name of the collection to get the vectors from.

- `id` __array[integer]__ (required)</br> IDs of the vectors to return.

- `outputFields` __array[string]__ (required)</br> The fields to return with data.

- `partitionNames` __array[string]__ (required)</br> Partitions to apply the operation in.


## Response

Returns the requested vectors.

### Response Bodies

Succesful request
```json
{
    "data": {}
}
```

- `data` __object__ </br> Vectors with given IDs alongside the fields requested in output fields.



Unsuccesful request
```json
{
    "message": "string"
}
```

- `message` __string__ </br> Message explaining the possible error.