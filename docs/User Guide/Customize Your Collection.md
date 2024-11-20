# Customized Collection
This guide shows how to create a collection forwith customizations without needing to edit your collection afterwards.

Imagine you’re building a product recommendation system for an online store. You have a set of product embeddings (vector representations) and want to quickly find similar products based on a user's browsing history or preferences. Here's how to configure your collection for this purpose:


## Get Your API Key and Python SDK

// TODO


## 1. Initialize the Gandi Client

```python

from gandipy import Gandi
from gandipy.models import (
    Collection, # Collection object to create
    Index, # Index object to define the parameters of the vector index
    Schema, # Schema is needed for further
    IndexConfig, # Additional parameters for indexes
    Field, # Object for defining each field
    ElementTypeParams, 
)


client = Gandi(project_id="product_recommendations", api_key="YOUR_API_KEY")

```

**What It Does**: Sets up the Gandi client, which you'll use to interact with the Gandi API.

**Parameters**:
 - ``project_id``: Your unique project identifier, here set to "product_recommendations".
 - ``api_key``: Your personal API key for authentication.


**Why It’s Important**: This step is essential for making API calls to manage collections and data.



## 2. Defining the Fields

```python
fields = [
    Field(
        field_name="product_vector",
        data_type="FloatVector",
        element_type_params=ElementTypeParams(dim=128),
    ),
    Field(field_name="product_id", data_type="Int64", is_primary=True),
    Field(field_name="category", data_type="VarChar"),
    Field(field_name="price", data_type="Float"),
]


```

**What It Does**: Specifies the attributes that your collection will hold, along with their data types.

**Fields**:

Key Elements:

- ``product_vector``: A FloatVector field for storing the 128-dimensional product embeddings.
- ``product_id``: An Int64 field that serves as the primary key, uniquely identifying each product.
- ``category``:  A String field that serves as a partition key, which means data is segmented based on this attribute.
- ``price``: A Float field to store the product price.

**Why It’s Important**: Defining the fields in advance ensures that your data is organized and searchable in a way that meets the needs of your use case. Making "category" a partition key helps organize the data more efficiently. When searching for similar products within a specific category, the query engine can limit its search to only the relevant partition, speeding up retrieval times.



## 3. Configuring Indexes

```python
indexes = [
    Index(
        metric_type="COSINE",
        field_name="product_vector",
        index_name="product_vector_index",
        params=IndexConfig(index_type="HNSW", ef_construction=200, m=16),
    )
]

```

**What It Does**: Defines how data will be indexed and searched, using cosine similarity to compare product embeddings.

**Parameters**:
- metric_type: The similarity metric used for searching. "COSINE" is effective for comparing vector orientations, ideal for product recommendations.
- field_name: The name of the field being indexed, here called "product_vector".
- index_name: The name you assign to the index, "product_vector_index".
- params: Additional configuration settings:
  - index_type: Specifies the type of index. **HNSW** (Hierarchical Navigable Small World) is used for fast and scalable search.
  - ef_construction: Controls the accuracy during index construction. Higher values improve accuracy but increase memory usage.
  - m: The maximum number of connections for each node in the graph. A higher m improves recall.


**Why It’s Important**: Proper indexing improves the performance and accuracy of search queries, especially for a recommendation system that needs to handle numerous queries efficiently. Memora offers different indexing options for your collections


## 4. Creating the Schema and the Collection

```python
schema = Schema(
    auto_id=False,
    fields=fields,
)

coll = Collection(
    collection_name=coll_name,
    schema=schema,
    indexes=indexes,
)


```

**What It Does**: Groups the fields into a schema, specifying the overall structure of your data. Uses that schema to create the collection object that combines everything we have created so far.

**Key Elements**:
- ``auto_id``: A boolean indicating whether IDs are generated automatically. Setting this to False means you need to provide your own product IDs as we defined in the fields.
- ``fields``: The list of fields defined in the previous step.
- ``collection_name``: The name you choose for your collection, "product_collection".
- ``indexes``: Indexing rules created earlier.




```python

result = client.collections.create(collection=coll)

print(result)

```
    {'message': 'collection created'}


Finally we create the collection by passing the Collection object into the client. Now our customized collection is ready to use.


## Summary

With this guide, you can set up a custom collection, including schema and index configurations, using Memora's Python SDK. Each element plays a crucial role in defining how your data is stored and accessed, making the process straightforward.

Feel free to adjust these settings based on your data structure and search needs. You can learn more about Memora from:



// TODO