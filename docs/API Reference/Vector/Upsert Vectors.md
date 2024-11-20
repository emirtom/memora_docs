# Upsert Vectors

POST: http://io.gandi/vectors/upsert

upserts new data into a specific collection or updates existiong ones inside it.

## Authorization

## Example


```shell
curl --location --request POST "http://io.gandi/vectors/upsert" \
--header "Content-Type: application/json" \
--header "Api-Key: $YOUR_API_KEY" \
--data-raw '{
    "collectionName": "example_collection",
    "project_id": "YOUR_PROJECT_ID",
    "data": [
        {"id": 2, "vector": [0.4738293812938474, -0.5392104897310023, 0.15280321785604923, -0.3994873284928716, 0.7802341229482839]},
        {"id": 3, "vector": [0.2176392847101823, 0.08421475339820117, 0.6847123349783842, 0.243159675198483, 0.8129452185973021]},
        {"id": 4, "vector": [0.3782910382149527, -0.4527189043628836, 0.21384928476259344, -0.31848201749201357, 0.8562941839272845]},
        {"id": 5, "vector": [0.4928371023947259, -0.5639205812937452, 0.17834029149501875, -0.2481590347284831, 0.8994583271738409]},
        {"id": 6, "vector": [0.3261097285092318, 0.03459258129290334, 0.723405829374921, 0.2981739029384014, 0.8756142981024957]}
    ]
}'
```
```python
from gandipy import Gandi

client = Gandi(api_key="YOUR_API_KEY", project_id="YOUR_PROJECT_ID")
data = [
    {"id": 2, "vector": [0.4738293812938474, -0.5392104897310023, 0.15280321785604923, -0.3994873284928716, 0.7802341229482839]},
    {"id": 3, "vector": [0.2176392847101823, 0.08421475339820117, 0.6847123349783842, 0.243159675198483, 0.8129452185973021]},
    {"id": 4, "vector": [0.3782910382149527, -0.4527189043628836, 0.21384928476259344, -0.31848201749201357, 0.8562941839272845]},
    {"id": 5, "vector": [0.4928371023947259, -0.5639205812937452, 0.17834029149501875, -0.2481590347284831, 0.8994583271738409]},
    {"id": 6, "vector": [0.3261097285092318, 0.03459258129290334, 0.723405829374921, 0.2981739029384014, 0.8756142981024957]}
]
result = client.vectors.upsert(collection_name="example_collection", data=data)
```
## Request Body

```json
{
    "project_id": "string",
    "collectionName": "string",
    "partitionName": "string",
    "data": []
}
```

- `project_id` __string__ (required)</br> ID of the project where the collection is.

- `collectionName` __string__ (required)</br>The name of the collection to upsert the vectors into.

- `partitionName` __string__ </br> The name of the partition to upsert the vectors into.

- `data` __object__ | __array[object]__ </br> The data to upsert into the collection. </br> It should match the schema of the given collection.


## Response

Returns information about upserted vectors.

### Response Bodies

Succesful request
```json
{
    "data": {
        "upsertCount": "integer",
        "upsertIds": []
    }
}
```

- `data` __object__ </br>

    - `upsertCount` __string__ </br> The number of succesfully upserted vectors.
  
    - `upsertIds` __array[string]__ </br> IDs of the upserted vectors.



Unsuccesful request
```json
{
    "message": "string"
}
```

- `message` __string__ </br> Message explaining the possible error.