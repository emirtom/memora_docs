# Search Vectors

POST: http://io.memora/vectors/search

Make a vector similarity search with an optional scalar filtering.

## Example


```shell
curl --location --request POST "http://io.memora/vectors/search" \
--header "Content-Type: application/json" \
--header "Api-Key: $YOUR_API_KEY" \
--data-raw '{
    "collectionName": "example_collection",
    "project_id": "YOUR_PROJECT_ID",
    "data": [0.3261097285092318, 0.03459258129290334, 0.723405829374921, 0.2981739029384014, 0.8756142981024957],
    "annsField": "vector",
    "limit": 5
}'
```
```python
from memoradb import Memora

client = Memora(api_key="YOUR_API_KEY", project_id="YOUR_PROJECT_ID")

result = client.vectors.insert(
            collection_name="example_collection",
            data=[0.3261097285092318, 0.03459258129290334, 0.723405829374921, 0.2981739029384014, 0.8756142981024957],
            anns_field="vector",
            limit=5
            )
```
## Request Body

```json
{
    "project_id": "string",
    "collectionName": "string",
    "data": [],
    "annsField": "string",
    "filter": "string",
    "limit": "integer",
    "offset": "integer",
    "groupingField": "string",
    "outputFields": [],
    "searchParams": {
        "metricType": "string",
        "params": {
            "radius": "integer",
            "range_filter": "integer"
        }
    },
    "partitionNames": []
}
```

- `project_id` __string__  
  The ID of the project where the collection is located.

- `collectionName` __string__  
  The name of the collection to be searched.

- `data` __array[float32]__  
  An array of vector embeddings to be used in the search query.

- `annsField` __string__  
  The name of the field that contains the vectors or approximate nearest neighbors to be searched.

- `filter` __string__  
  A filter expression used to narrow down the search results.

- `limit` __integer__  
  The maximum number of results to return from the search.

- `offset` __integer__  
  The number of results to skip before starting to return results. Used for pagination.

- `groupingField` __string__  
  The field by which to group the search results.

- `outputFields` __array__  
  An array of field names to be included in the search results.

- `searchParams` __object__  
  An object containing the parameters for the search.

    - `metricType` __string__  
      The metric type to be used for the search. It should be the same as the metric type of the field it searches.

    - `params` __object__  
      An object containing additional parameters for the search.
      
        - `radius` __integer__  
          Determines the threshold of least similarity. When the metric type is **L2**, this value should be greater than the range filter. Otherwise, it should be less than range filter.

        - `range_filter` __integer__  
          Narrows the search to vectors within a specific similarity range. When the metric type is **L2**, this value should be less than the radius. Otherwise, it should be greater than the radius.

- `partitionNames` __array[string]__  
  An array of partition names to restrict the search to specific partitions within the collection.

## Response

Returns the requested vectors.

### Response Bodies

Succesful request
```json
{
    "data": {}
}
```

- `data` __object__ </br> The most similar vectors alongside the fields requested in output fields.



Unsuccesful request
```json
{
    "message": "string"
}
```

- `message` __string__ </br> Message explaining the possible error.