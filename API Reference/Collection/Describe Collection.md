# Describe Collection

POST: http://io.gandi/collections/describe

Describes the given collection inside your project.

## Authorization

## Example


```shell
curl --location --request POST "http://io.gandi/collections/describe" \
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

result = client.collections.describe(collection_name="example_collection")
```

## Request Body

```json
{
    "project_id": "string",
    "collectionName": "string"
}
```

- `project_id` __string__ (required)</br> ID of the project where the collection is.

- `collectionName` __string__ (required)</br>The name of the collection to be described.


## Response

Returns the description of the given collection.

### Response Bodies

Succesful request
```json
{
    "data": {
        "autoID": "boolean",
        "collectionID": "integer",
        "collectionName": "string",
        "description": "string",
        "enableDynamicField": "boolean",
        "fields": [
            {
                "autoId": "boolean",
                "description": "string",
                "id": "integer",
                "name": "string",
                "params": [
                    {
                        "key": "string",
                        "value": "string"
                    }
                ],
                "partitionKey": "boolean",
                "primaryKey": "boolean",
                "type": "string"
            }
        ],
        "indexes": [
            {
                "fieldName": "string",
                "indexName": "string",
                "metricType": "string"
            }
        ],
        "load": "string",
        "partitionNum": "integer",
        "properties": [
            {
                "key": "string",
                "value": "string"
            }
        ],
    }
}
```

- `data` __object__ 

    - `autoID` __boolean__  
      Specifies whether the ID field should be automatically generated.
    
    - `collectionID` __integer__  
      The unique identifier for the collection.
    
    - `collectionName` __string__  
      The name of the collection.
    
    - `description` __string__  
      A brief description of the collection.
    
    - `enableDynamicField` __boolean__  
      Specifies whether dynamic fields are enabled for the collection.

    - `fields` __array__  
      An array of objects representing the fields in the collection.
      
        - `autoId` __boolean__  
          Specifies whether the ID field should be automatically generated for this field.
        
        - `description` __string__  
          A brief description of the field.
        
        - `id` __integer__  
          The unique identifier for the field.
        
        - `name` __string__  
          The name of the field.
        
        - `params` __array__  
          An array of key-value pairs representing additional parameters for the field.
          
            - `key` __string__  
              The key of the parameter.
            
            - `value` __string__  
              The value of the parameter.
        
        - `partitionKey` __boolean__  
          Specifies whether the field is a partition key.
        
        - `primaryKey` __boolean__  
          Specifies whether the field is a primary key.
        
        - `type` __string__  
          The data type of the field.

    - `indexes` __array__  
      An array of objects representing the indexes in the collection.

        - `fieldName` __string__  
          The name of the field to be indexed.
        
        - `indexName` __string__  
          The name of the index.
        
        - `metricType` __string__  
          The metric type used by the index.

    - `load` __string__  
      Specifies the load status of the collection.

    - `partitionNum` __integer__  
      The number of partitions in the collection.

    - `properties` __array__  
      An array of key-value pairs representing additional properties for the collection.
      
        - `key` __string__  
          The key of the property.
        
        - `value` __string__  
          The value of the property.



Unsuccesful request
```json
{
    "message": "string"
}
```

- `message` __string__ </br> Message explaining the possible error.