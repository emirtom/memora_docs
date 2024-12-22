# List Collections

POST: http://io.gandi/collections/list

Returns the list of the collections inside your project.

## Authorization

## Example


```shell
curl --location --request POST "http://io.gandi/collections/list" \
--header "Content-Type: application/json" \
--header "Api-Key: $YOUR_API_KEY" \
--data-raw '{
    "project_id": "YOUR_PROJECT_ID"
}'
```
```python
from gandipy import Gandi

client = Gandi(api_key="YOUR_API_KEY", project_id="YOUR_PROJECT_ID")

result = client.collections.list()
```
## Request Body

```json
{
    "project_id": "string"
}
```
    
- `project_id` __string__ (required)</br> ID of the project that you want the list the collections inside it.


## Response

Returns a list of the collections.

### Response Bodies

Successful request
```json
{
    "data": [
        "string"
    ]
}
```
- `data` __array[string]__ </br> The list of the collection names inside the project.


Failed request
```json
{
    "message": "string"
}
```
- `message` __string__ </br> A string indicating the possible reason of the error 
