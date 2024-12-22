# Efficient Resource Distribution with Memora
Imagine you have a fleet of trucks filled with goods that need to be distributed to various destinations. Each destination has different needs, and you want to ensure that the goods are sent to the locations where they are most needed. How can you do this efficiently? This is where Memora and cosine similarity come into play.

## Understanding the Challenge
Think of each destination as a place with unique demands. One area might urgently need clothing, another might be in dire need of food, and another one might require construction materials. You want to distribute your resources effectively and ensure that each destination receives what it needs the most.

To make this process efficient, we can represent the needs of each destination as a vector. But what is a vector in this context? Simply put, a vector is a list of numbers, where each number indicates how strongly a destination needs a specific type of resource.

For example, let's say we have three types of resources: clothing, food, and construction materials. We can represent the needs of a destination like this:
- Destination A: [0.9, 0.1, 0.0] (high need for clothing, low need for food, no need for construction materials)
- Destination B: [0.3, 0.7, 0.0] (some need for clothing, higher need for food, no need for construction materials)

Our goal is to match the available goods (which we also represent as a vector) to the destination whose needs align most closely with what we have. This is where the concept of cosine similarity comes in.

## The Role of Cosine Similarity
When matching goods with a destination, we want to focus on the direction of the needs rather than their magnitude. In other words, we care more about which resources are prioritized rather than how much of each is needed. This is because we often have a limited supply, and it’s crucial to match the type of resources to the correct destination, even if the exact quantity isn’t perfect.

Cosine similarity helps us with this task. It measures how similar two vectors are by looking at the angle between them. If two vectors point in the same direction, their cosine similarity is high, which means they are a good match. The closer the angle between the vectors is to zero, the more similar they are in terms of direction.

## How Memora Helps
Memora makes this process efficient. It can store the needs of thousands of destinations as vectors and quickly compute the cosine similarity between your goods vector and each destination vector. Instead of manually analyzing each destination’s needs, Memora automates this and helps you find the best match almost instantly.

This approach can be scaled up to handle real-world problems, such as distributing aid in disaster zones or managing supply chains across a large network of warehouses.


## Step by Step Guide

This guide will go step by step in creating a resource distribution system using Memora. We will use low dimensional vectors for the needs for each destination and search for relevant goods vectors. You can get Python SDK by using pip in command line:

```shell
$ pip install memorapy
```

### Get Your API Key and Create a Project

TODO

### Create a Collection
Memora uses collections to store data. We will create a simple collection with an additional field to store the name of the place. Do note that the metric type is set to cosine similarity for this system. For more options in collection, you can check "API Ref".

```python
from memorapy import Memora
from memorapy.models import Collection, Schema, Field, Index

client = Memora(api_key="YOUR_API_KEY", project_id="YOUR_PROJECT_ID")

collection = Collection(
    collection_name="resource_distributor",
    dimension=10,
    schema=Schema(fields=[
                Field(field_name="place", data_type="VarChar")
            ],
            auto_id=True
        ),
    indexes = [
        Index(
            metric_type="COSINE",
            field_name="need_vector",
            index_name="need_vector_index",
            params=IndexConfig(index_type="HNSW", ef_construction=200, m=16),
        )
    ]
)

result = client.collections.create(collection=collection)

print(result)
```
    {'message': 'collection created'}



### Prepare the Data

Each one of the 10 dimensions of the vectors will represent a different type of need for this example. The needs will be given randomly. You can put these vectors inside your collection by:


```python
need_vectors = [
    [0.9, 0.1, 0.0, 0.5, 0.3, 0.8, 0.2, 0.4, 0.7, 0.6],
    [0.3, 0.7, 0.0, 0.1, 0.8, 0.5, 0.9, 0.4, 0.2, 0.3],
    [0.0, 0.2, 0.5, 0.7, 0.1, 0.4, 0.6, 0.3, 0.9, 0.8],
    [0.5, 0.4, 0.3, 0.7, 0.1, 0.2, 0.6, 0.8, 0.9, 0.0],
    [0.6, 0.3, 0.8, 0.2, 0.9, 0.1, 0.5, 0.7, 0.0, 0.4],
    [0.2, 0.5, 0.9, 0.3, 0.7, 0.6, 0.1, 0.4, 0.8, 0.0],
    [0.4, 0.9, 0.1, 0.8, 0.6, 0.0, 0.7, 0.5, 0.3, 0.2],
    [0.7, 0.0, 0.4, 0.9, 0.5, 0.3, 0.2, 0.8, 0.1, 0.6],
    [0.8, 0.6, 0.2, 0.4, 0.0, 0.9, 0.3, 0.7, 0.1, 0.5],
    [0.1, 0.8, 0.6, 0.5, 0.4, 0.2, 0.0, 0.9, 0.7, 0.3]
]

places = [
    "Greenfield",
    "Riverdale",
    "Sunset Valley",
    "Oceanview",
    "Maplewood",
    "Brookhaven",
    "Silver Springs",
    "Hilltop",
    "Crescent Bay",
    "Pinecrest"
]




data = [{"vector": need_vectors[i], "id": i, "place": places[i]} for i in range(10)]

result = client.vectors.insert(collection_name="resource_distrubitor", data=data)

print(result)

```
    {'data': {'insertCount': 10, 'insertIds': [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]}}


### Finding the Most Appropriate Place

Once you have your vectors representing both the available goods and the needs of each destination, the next step is to match them up effectively. This is where the power of Memora and cosine similarity comes into play.

```python

goods_vector = [0.5, 0.4, 0.3, 0.7, 0.1, 0.2, 0.6, 0.8, 0.9, 0.0]


result = client.vectors.search(
    collection_name="semantic_search",
    data=[goods_vector],
    anns_field="vector",  # name of the vector field
    output_fields=["place"],  # fields that we want to get beside ID
    limit=1,  # number of top similar sentences to return
)


print(result)
```


## Learn More



