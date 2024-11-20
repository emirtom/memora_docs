# Quickstart with Gandi

(Google Colab link)

Vectors, the output data format of Neural Network models, can effectively encode information and serve a pivotal role in AI applications such as knowledge base, semantic search, Retrieval Augmented Generation (RAG) and more. 

Gandi is a vector database cloud service that suits AI applications of every size from running a demo chatbot in Jupyter notebook to building web-scale search that serves billions of users. In this guide, we will walk you through how to get your API key and start using Gandi cloud.

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

## Set Up Vector Database
To connect to Gandi cloud service, simply instantiate `Gandi` and put your API Key and your project ID into it. 

```python
from gandipy import Gandi

client = Gandi(api_key="YOUR_API_KEY", project_id="YOUR_PROJECT_ID")
```



## Create a Collection
In Gandi, we need a collection to store vectors and their associated metadata. You can think of it as a table in traditional SQL databases. When creating a collection, you can define schema and index params to configure vector specs such as dimensionality, index types and distant metrics. There are also complex concepts to optimize the index for vector search performance. 

For each request in Gandi, there is a request body needed that contains the necessary information. We can create and modify the body outside of the client and send it when we need. For a basic collection, we can create the Collection object with only its name and dimension.


```python
collection = Collection(collection_name="demo_collection", dimension=8)
client.collection.create(collection=collection)
```

```shell
curl --location --request POST "http://io.gandi/collections/create" \
--header "Content-Type: application/json" \
--header "Api-Key: $YOUR_API_KEY" \
--data-raw '{
    "collectionName": "demo_collection",
    "dimension": 8,
    "project_id": "YOUR_PROJECT_ID"
}'

```

In the above setup, 
- The primary key and vector fields use their default names ("id" and "vector").
- The metric type (vector distance definition) is set to its default value COSINE.
- The primary key field accepts integers and does not automatically increments namely not using auto-id feature.
Alternatively, you can formally define the schema of the collection and customize it accordingly by ...


## Use fake representation with random vectors
If you couldn't download the model due to network issues, as a walkaround, you can use random vectors to represent the text and still finish the example. Just note that the search result won't reflect semantic similarity as the vectors are fake ones. 


```python
import random

vectors = [[random.uniform(-1, 1) for _ in range(8)] for _ in range(4)]
data = [
    {"id": i, "vector": vectors[i]} for i in range(len(vectors))
]

print("Data has", len(data), "entities, each with fields: ", data[0].keys())
print("Vector dim:", len(data[0]["vector"]))
```

    Data has 4 entities, each with fields:  dict_keys(['id', 'vector])
    Vector dim: 8


## Insert Data
Let's insert the data into the collection:


```python
res = client.vectors.insert(collection_name="demo_collection", data=data)

print(res)
```

    {'insert_count': 4, 'ids': [0, 1, 2], 'cost': 0}


```shell
curl --location --request POST "http://io.gandi/vectors/insert" \
--header "Content-Type: application/json" \
--header "Api-Key: $YOUR_API_KEY" \
--data-raw '{
    "collectionName": demo_collection",
    "project_id": "YOUR_PROJECT_ID",
    "data": [
        {
            "id": 0,
            "vector": [0.12, -0.34, 0.56, -0.78, 0.90, -0.12, 0.34, -0.56]
        },
        {
            "id": 1,
            "vector": [-0.23, 0.45, -0.67, 0.89, -0.12, 0.34, -0.56, 0.78]
        },
        {
            "id": 2,
            "vector": [0.34, -0.56, 0.78, -0.90, 0.12, -0.34, 0.56, -0.78]
        },
        {
            "id": 3,
            "vector": [-0.45, 0.67, -0.89, 0.12, -0.34, 0.56, -0.78, 0.90]
        }
    ]
}'
```


## Search
Now we can do vector searches by specifying a vector.


```python
query_vectors = [[random.uniform(-1, 1) for _ in range(8)]]

res = client.vectors.search(
    collection_name="demo_collection", # name of the collection we are working in
    data=query_vectors, # vector that we are searching
    limit=2,  # top k that we want to return
    output_fields=["id", "vector"], #which fields we want alongside
)

print(res)
```

```shell
curl --location --request POST "http://io.gandi/vectors/search" \
--header "Content-Type: application/json" \
--header "Api-Key: $YOUR_API_KEY" \
--data-raw '{
    "collectionName": demo_collection",
    "project_id": "YOUR_PROJECT_ID",
    "limit": 2,
    "outputFields": ["id", "vector"],
    "data": [
        [0.43, 0.12, -0.72, 0.38, -0.21, -0.98, -0.65, 0.23]
    ]
}'
```


The output is a list of results, each mapping to a vector search query. Each query contains a list of results, where each result contains the entity primary key, the distance to the query vector, and the entity details with specified `output_fields`.

## Query with Filtering
You can also get data from the database by specifying a filter 


```python
# This will exclude any text in "history" subject despite close to the query vector.
res = client.vectors.query(
    collection_name="demo_collection",
    filter="id < 3",
)

print(res)
```

```shell
curl --location --request POST "http://io.gandi/vectors/query" \
--header "Content-Type: application/json" \
--header "Api-Key: $YOUR_API_KEY" \
--data-raw '{
    "collectionName": demo_collection",
    "project_id": "YOUR_PROJECT_ID",
    "filter": "id < 3",
}'
```

By default, the scalar fields are not indexed. If you need to perform metadata filtered search in large dataset, you can consider using fixed schema and also turn on the [index](https://milvus.io/docs/scalar_index.md) to improve the search performance. 

In addition to vector search, you can also perform other types of searches:


## Delete Vectors
If you'd like to purge data, you can delete entities specifying the primary key or delete all entities matching a particular filter expression.


```python
res = client.vectors.delete(collection_name="demo_collection", ids=[0, 2])

print(res)

res = client.vectors.delete(
    collection_name="demo_collection",
    filter="id == 1",
)

print(res)
```
```shell
curl --location --request POST "http://io.gandi/vectors/delete" \
--header "Content-Type: application/json" \
--header "Api-Key: $YOUR_API_KEY" \
--data-raw '{
    "collectionName": demo_collection",
    "project_id": "YOUR_PROJECT_ID",
    "filter": "id == 3",
}'
```



## Learn More

