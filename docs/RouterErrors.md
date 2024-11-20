# API Router Error Codes

This document provides a list of error codes and their corresponding meanings for the API router.

| **HTTP Status Code**          | **Description**                                                                 |
| ----------------------------- | ------------------------------------------------------------------------------- |
| `200 - OK`                    | The request was successful.                                                     |
| `201 - CREATED`               | The request was successful and a new resource was created.                      |
| `400 - BAD REQUEST`           | The request was unsuccessful due to an invalid argument.                        |
| `401 - UNAUTHORIZED`          | The request was unsuccessful due to invalid API key.                            |
| `404 - NOT FOUND`             | The request was unsuccessful because the resource was not found.                |
| `409 - ALREADY EXISTS`        | The request was unsuccessful because the resource already exists.               |
| `500 - UNKNOWN`               | An internal server error occurred.                                              |


1100: dimension is required for quickly create collection, invalid collection name, outside english letters, underscore and numbers
65535: create duplicate collection with different parameters, duplicated field name in collection, primary key not specified, data type of primary key must be Int64 or VarChar, dimension not defined in vector field, only metric type can be passed when use AutoIndex, one index per field, dimension not in desired range
800: database not found
1100: invalid metric type, invalid any type (for enums)
1801: dimension can only accept json format request, error: json: cannot unmarshal string into Go struct field CollectionReq.dimension of type int32, any type mismatch
1701: invalid field name, outside english letters, underscore and numbers


max_length and max_capacity can be negative
watch out for index_type, max_length, max_capacity
does not mind if any other irrelevant data is sent, even illegal characters
autoID outside of schema takes precedence, params takes precedence over schema in enableDynamicField
Not case sensitive in curl, will not give error unless creating a collection with an existing name has different schemas

In a field:
If no field, id and vector fields (Int64 and FloatVector)
A field needs name and metric type
It also needs one vector and one Int64/Varchar (primary key type)
Primary key needs to be specified



// Necessary schema
"schema": {
    "fields": [ {
            "fieldName": "hello",
            "dataType": "Int64",
            "isPrimary": true
        }, {
            "fieldName": "hsello",
            "dataType": "FloatVector",
            "isPrimary": false,
            "elementTypeParams": {
                "dim": 31
            }
        }
    ]
}

If there is schema, it expects indexes manually


100: can't find collection
1801: invalid type
1100: invalid collection name
800: database not found


1802: empty required parameter, does not send empty string or empty object
1100: illegal filter
1801: wrong type invalid parameter, wrong dimension in search
1804: invalid parameter inside data object, data object is wrong type

what is cost???
