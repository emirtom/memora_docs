# Has Collection

POST: http://io.gandi/collections/has

Returns whether the given collection exist inside your project.

## Authorization

## Example


```shell
curl --location --request POST "http://io.gandi/collections/has" \
--header "Content-Type: application/json" \
--header "Api-Key: $YOUR_API_KEY" \
--data-raw '{
    "collectionName": "example_collection",
    "project_id": "YOUR_PROJECT_ID"
}'
```
```python
from gandipy import Gandi

client = Gandi(api_key="YOUR_API_KEY", project_id="YOUR_PROJECT_ID")

result = client.collections.has(collection_name="example_collection")
```
## Request Body

```json
{
    "project_id": "string",
    "collectionName": "string"
}
```
    
- `project_id` __string__ (required)</br> ID of the project where the collection is.

- `collectionName` __string__ (required)</br>The name of the collection to check whether it exists.


## Response

Returns whether the project has the collection.

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
