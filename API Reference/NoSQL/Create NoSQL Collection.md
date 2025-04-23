# Create NoSQL Collection

POST: http://io.memora/nosql/collections/create

This operation creates a NoSQL collection inside your database.

## Example

You can specify your fields inside the schema or you can create a simple key value collection by specifying only a collection name and primary key.

```shell
curl --location --request POST "http://io.memora/nosql/collections/create" \
--header "Content-Type: application/json" \
--header "Api-Key: $YOUR_API_KEY" \
--data-raw '{
    "collectionName": "example_collection",
    "primaryKey": "key",
}'
```
```python
from memoradb import Memora

client = Memora(api_key="YOUR_API_KEY", project_id="YOUR_PROJECT_ID")


result = client.nosql.create_collection(collection_name="example_collection", primary_key="key")
```

Above setup will create a collection with "key" as a key and strings as a value.

## Request Body

```json
{
    "project_id": "string",
    "collectionName": "string",
    "primaryKey": "string",
    "fields": [
        {
            "fieldName": "string",
            "dataType": "string",
            "elementDataType": "string",
            "elementTypeParams": {
                "max_length": "integer",
                "dim": "integer",
                "max_capacity": "integer"
            }
        }
    ]
}'
```

- `project_id` __string__ (required)</br> ID of the project where the collection will be created.

- `collectionName` __string__ (required)</br>The name of the collection to be created.

- `primaryKey`* __string__ </br> (required) The name of the primary key field that the collection will search based on.

- `fields` __array__ </br>An array of objects representing the fields in the schema.

    - `fieldName` __string__ </br>The name of the field to create in the specified collection.

    - `dataType` __string__ </br>The data type of the field.

    - `elementDataType` __string__ </br>The data type of the elements in the field.

    - `elementTypeParams` __object__ </br>An object containing additional parameters for the field.

        - `max_length` __integer__ </br>The maximum length of the string if the data type is VarChar.

        - `dim` __integer__ </br>The dimension of the field if the data type is Float Vector or Binary Vector.

        - `max_capacity` __integer__ </br>The maximum capacity of the array for the Array data type.



## Response

Returns a message.

### Response Body

```json
{
    "message": "string"
}
```

The response code will be 201 if the request is succesfull and some other code if it is unsucessful alongside the error message.