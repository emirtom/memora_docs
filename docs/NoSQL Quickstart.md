# Quickstart for a NoSQL Collection

(Google Colab link)

NoSQL (not only SQL) databases provide ways to store data in different ways than traditional relational databases. Gandi offers the option to create collections that act like a key-value type NoSQL database. This way you can store your non-vector data without using external services.

This guide will help you through how to get your API key, create a project and create a NoSQL collection inside it. It will also go through simple operations such as adding, deleting and getting data from the database.


## Get Your API Key
Login to website and create an account.

(Website link)


## Create Your Project

TODO


## Install Gandi
In this guide we use Gandi's python library that lets you communicate with your database in cloud. Before starting, make sure you have Python 3.8+ available in the local environment. Install gandipy which contains python client library.

```shell
$ pip install -U gandipy
```

## Set Up NoSQL Database
To connect to Gandi cloud service, simply instantiate `Gandi` and put your API Key and your project ID into it. 

```python
from gandipy import Gandi

client = Gandi(api_key="YOUR_API_KEY", project_id="YOUR_PROJECT_ID")
```

## Create a NoSQL Collection

Each NoSQL collection has a primary key that will be the name of the key field in your key-value pairs. While creating your collection, you need to specify the primary key, your collection name and the fields that will hold your data.

```python
from gandipy.models import Field

if client.nosql.has_collection(collection_name="demo_collection"):
    client.nosql.drop_collection(collection_name="demo_collection")

f1 = Field(field_name="shape", data_type="VARCHAR", is_primary=False)
f2 = Field(field_name="color", data_type="VARCHAR", is_primary=False)

client.nosql.create_collection(collection_name="demo_collection", primary_key="object", fields=[f1, f2])
```

```shell
curl --location --request POST "http://io.gandi/nosql/collections/create" \
--header "Content-Type: application/json" \
--header "Api-Key: $YOUR_API_KEY" \
--data-raw '{
    "collectionName": "demo_collection",
    "primaryKey": "object",
    "field": [
        {
            "fieldName": "shape",
            "dataType": "VarChar",
        },
        {
            "fieldName": "color",
            "dataType": "VarChar"
        }
    ]
}'
```

This setup will store values by their "object" field and each value will have a shape and a color.

## Insert Data
Let's insert some data inside the collection:

```python
objects = [
    {"object"="A", "shape"="sphere", "color"="red"},
    {"object"="B", "shape"="cube", "color"="blue"},
    {"object"="C", "shape"="cylinder", "color"="yellow"}
]

client.nosql.insert(collection_name="demo_collection", data=objects)

```
```shell
curl --location --request POST "http://io.gandi/nosql/data/insert" \
--header "Content-Type: application/json" \
--header "Api-Key: $YOUR_API_KEY" \
--data-raw '{
    "collectionName": demo_collection",
    "project_id": "YOUR_PROJECT_ID",
    "data": [
        {"object"="A", "shape"="sphere", "color"="red"},
        {"object"="B", "shape"="cube", "color"="blue"},
        {"object"="C", "shape"="cylinder", "color"="yellow"}
    ]
}'
```

## Upsert Data
Alternatively, it is possible to upsert the data:
```python
objects = [
    {"object"="A", "shape"="sphere", "color"="red"},
    {"object"="B", "shape"="cube", "color"="blue"},
    {"object"="C", "shape"="cylinder", "color"="yellow"}
]

client.nosql.upsert(collection_name="demo_collection", data=objects)

```
```shell
curl --location --request POST "http://io.gandi/nosql/data/upsert" \
--header "Content-Type: application/json" \
--header "Api-Key: $YOUR_API_KEY" \
--data-raw '{
    "collectionName": demo_collection",
    "project_id": "YOUR_PROJECT_ID",
    "data": [
        {"object"="A", "shape"="sphere", "color"="red"},
        {"object"="B", "shape"="cube", "color"="blue"},
        {"object"="C", "shape"="cylinder", "color"="yellow"}
    ]
}'
```

## Get Data
We can get data from the collection by specifying which fields we want to get:

```python
res = client.nosql.get(collection_name="demo_collection", key="A", output_fields=["shape", "color"])

print(res)
```
```shell
curl --location --request POST "http://io.gandi/nosql/data/get" \
--header "Content-Type: application/json" \
--header "Api-Key: $YOUR_API_KEY" \
--data-raw '{
    "collectionName": demo_collection",
    "project_id": "YOUR_PROJECT_ID",
    "key": "A",
    "outputFields": ["shape, "color"]
}'
```


## Delete Data
It is possible to delete data by specifying the key of the value:

```python
res = client.nosql.get(collection_name="demo_collection", key="A")

print(res)
```
```shell
curl --location --request POST "http://io.gandi/nosql/data/delete" \
--header "Content-Type: application/json" \
--header "Api-Key: $YOUR_API_KEY" \
--data-raw '{
    "collectionName": demo_collection",
    "project_id": "YOUR_PROJECT_ID",
    "key": "A"
}'
```


