# Building a Simple Recommendation System with Memora
When you browse an online store, watch a movie on a streaming service, or explore social media, you often see suggestions tailored just for you. These suggestions, or recommendations, are powered by recommendation systems—one of the most common applications of AI today. In this blog, we’ll explore what a recommendation system is and how you can create one using Memora.


## What is a Recommendation System?
A recommendation system is a tool that helps suggest relevant content to users based on their past behavior, preferences, or similarities with other users or items. There are different types of recommendation systems, but two of the most popular are:
1. **Content-Based Filtering**: This method recommends items similar to those the user has interacted with before. For example, if you’ve watched many action movies, the system might suggest other action movies.

2. **Collaborative Filtering**: This approach makes recommendations based on the preferences of similar users. For instance, if you and another user have watched and liked the same movies, the system might recommend movies that they liked but you haven’t seen yet.


## How does Memora Fit In?
To make accurate recommendations, you need to represent items and users in a way that a computer can understand and compare efficiently. This is where vector embeddings come in. Vector embeddings are numerical representations of data (e.g., movies, products, or even user behavior). The idea is to convert these items and users into vector embeddings that can be stored and searched efficiently using Memora.


Memora as vector database specialize in storing and retrieving high-dimensional vectors, making it easy to find the most similar items quickly. They are especially useful for applications like recommendation systems, where we need to search for similar items frequently and fast.


## Step by Step Guide to Craeting a Recommendation System with Memora
This guide will go step by step in creating a recommendation system using Memora. We will use the descriptions of movies for vectors and search for a similar movie among them. You can get Python SDK by using pip in command line:

```shell
$ pip install memorapy
```

### Create a Collection
Memora uses collections to store data. We will create a simple collection with additional fields for storing the names and descriptions. For more options in collection, you can check "API Ref".

```python
from memorapy import Memora
from memorapy.models import Collection, Schema, Field 

client = Memora(api_key="YOUR_API_KEY", project_id="YOUR_PROJECT_ID")

collection = Collection(
    collection_name="recommendation_system",
    dimension=512,
    schema=Schema(
        auto_id=False,
        fields=[
            Field(
                field_name="name",
                data_type="VarChar",
                element_type_params=ElementTypeParams(max_length=256),
            ),
            Field(
                field_name="description",
                data_type="VarChar",
                element_type_params=ElementTypeParams(max_length=1024),
            ),
            Field(
                field_name="vector",
                data_type="FloatVector",
                element_type_params=ElementTypeParams(dim=512),
            ),
            Field(field_name="id", data_type="Int64", is_primary=True),
        ],
    ),
    indexes=[
        Index(
            metric_type="COSINE",
            field_name="vector",
            index_name="vector_index",
            params=IndexConfig(index_type="HNSW", m=16, ef_construction=128),
        )
    ],
)

result = client.collections.create(collection=collection)

print(result)
```
    {'message': 'collection created'}

### Prepare the Data
We will use sentence_transformers library from [https://huggingface.co](HuggingFace) and get the [https://huggingface.co/jinaai/jina-embeddings-v2-small-en](Jina) AI Model for converting the descriptions of the movies to vectors.

You can get sentence_transformers library in a similar way by using pip.

```shell
$ pip install sentence_transformers
```

```python
from sentence_transformers import SentenceTransformer

model = SentenceTransformer(
    "jinaai/jina-embeddings-v2-small-en", 
    trust_remote_code=True # trust_remote_code is needed to use the encode method
)
movies = [
    {
        "name": "Galactic Heroes",
        "description": "A sci-fi adventure where a group of unlikely heroes must defend the galaxy from an ancient threat.",
    },
    {
        "name": "Love in Paris",
        "description": "A romantic drama following the unexpected connection between two strangers in the city of love.",
    },
    {
        "name": "The Haunted Manor",
        "description": "A horror film about a group of friends who stay in an old mansion, only to discover it’s inhabited by vengeful spirits.",
    },
    {
        "name": "Chef's Surprise",
        "description": "A feel-good comedy about a struggling chef who revives a failing restaurant with his unique recipes and charm.",
    },
    {
        "name": "Mystery on the Orient Line",
        "description": "A classic whodunit set on a luxurious train, where a detective must solve a murder before reaching the next stop.",
    },
    {
        "name": "Champion's Spirit",
        "description": "An inspiring sports drama about an underdog boxer fighting his way to the top of the championship ranks.",
    },
    {
        "name": "Time Traveler's Quest",
        "description": "A sci-fi thriller following a scientist who discovers time travel and tries to prevent a global catastrophe.",
    },
    {
        "name": "Alien Encounter",
        "description": "A suspenseful sci-fi film where humanity makes first contact with extraterrestrial beings, but not everything is as it seems.",
    },
    {
        "name": "Heart of the Jungle",
        "description": "An action-packed adventure about explorers on a mission in the Amazon rainforest who face nature and betrayal.",
    },
    {
        "name": "City Noir",
        "description": "A gritty, film-noir crime drama set in a 1940s metropolis, following a detective uncovering dark secrets.",
    },
    {
        "name": "Dreams of Stardom",
        "description": "A musical about a young performer trying to make it big on Broadway, facing challenges and finding herself along the way.",
    },
    {
        "name": "Robot Revolution",
        "description": "An action-packed sci-fi film where AI-powered robots revolt, forcing humans to fight for their survival.",
    },
    {
        "name": "The Lost Kingdom",
        "description": "A fantasy epic following a prince on a journey to reclaim his kingdom from an evil sorcerer.",
    },
    {
        "name": "Secrets of the Mind",
        "description": "A psychological thriller where a therapist’s new patient claims to remember a past life—and that they were murdered.",
    },
    {
        "name": "Wonders of the Ocean",
        "description": "A documentary exploring the mysteries and beauty of the world’s oceans, from coral reefs to deep-sea creatures.",
    },
]

embeddings = model.encode([movie["description"] for movie in movies]) # encode converts each description to its vector embedding by using jina model

print(embeddings.shape)
```
    (15, 512)

You can add the names, descriptions and their embeddings into the collection by using the Insert method.

```python
data = [
    {
        "id": i,
        "vector": embeddings[i],
        "description": movies[i]["description"],
        "name": movies[i]["name"],
    }
    for i in range(len(movies))
]

result = client.vectors.insert(collection_name="recommendation_system", data=data)

print(result)
```
    {'data': {'insertCount': 15, 'insertIds': [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14]}}
### Ask for a Recommendation
Now that your data is in the collection, you can perform a search. Memora will find vectors (movie descriptions) that are closest to your query vector, returning the most relevant results.

We will use an example description and try to find the most similar sentence in the collection. First, we need to convert into its embedding and use Search method to get the desired result. You can try different descriptions and see the results for yourself.

```python
query_desc = "an action movie"

query_vector = model.encode([query_desc])

result = client.vectors.search(
    collection_name="recommendation_system",
    data=[query_vector],
    anns_field="vector", # name of the vector field
    output_fields=["name", "description"], # fields that we want to get beside ID
    limit=3 # number of top matching descriptions to return
)

print(result)
```
    {
    "data":[
        {
            "description":"An action-packed sci-fi film where AI-powered robots revolt, forcing humans to fight for their survival.",
            "distance":0.83194065,
            "id":11,
            "name":"Robot Revolution"
        },
        {
            "description":"A suspenseful sci-fi film where humanity makes first contact with extraterrestrial beings, but not everything is as it seems.",
            "distance":0.80056447,
            "id":7,
            "name":"Alien Encounter"
        },
        {
            "description":"A gritty, film-noir crime drama set in a 1940s metropolis, following a detective uncovering dark secrets.",
            "distance":0.7895919,
            "id":9,
            "name":"City Noir"
        }
    ]
    }


## Learn More
If you’re interested in diving deeper into other use cases and vector databases, here are a few topics you can explore:

- What is a vector database?
- Vector Embeddings and how they work
- Retrieval Augmented Generation


