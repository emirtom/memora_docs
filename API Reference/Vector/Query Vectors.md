# Query Vectors

POST: http://io.memora/vectors/query

Gets specific vectors by using a scalar field with boolean expression.

## Example


```shell
curl --location --request POST "http://io.memora/vectors/get" \
--header "Content-Type: application/json" \
--header "Api-Key: $YOUR_API_KEY" \
--data-raw '{
    "collectionName": "example_collection",
    "project_id": "YOUR_PROJECT_ID",
    "filter": "id < 4"
}'
```
```python
from memoradb import Memora

client = Memora(api_key="YOUR_API_KEY", project_id="YOUR_PROJECT_ID")

result = client.vectors.insert(collection_name="example_collection", filter="id < 4")
```
## Request Body

```json
{
    "project_id": "string",
    "collectionName": "string",
    "outputFields": [],
    "partitionNames": [],
    "filter": "string"
}
```

- `project_id` __string__ (required)</br> ID of the project where the collection is.

- `collectionName` __string__ (required)</br>The name of the collection to get the vectors from.

- `filter` __string__ (required)</br> The scalar filter used to query vectors.

- `outputFields` __array[string]__ (required)</br> The fields to return with data.

- `partitionNames` __array[string]__ (required)</br> Partitions to apply the operation in.


## Response

Returns the vectors fitting the filter.

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