# Image Search with Memora
If you’ve ever used a search engine or an app that lets you find similar images, you’ve already experienced image search in action. But how does it work behind the scenes, especially when using a vector database? In this blog, we’ll explore what image search is and walk through how to build an image search system using Memora—step by step.

## What is Image Search?
Image search is a technique that allows you to find images that are visually similar to a reference image. Instead of typing in text, you provide an image as your search input. The system then looks for images that share similar patterns, colors, shapes, or other visual features.

Traditional search engines work with text and keywords, but images are different. Images are made up of pixels, which are not directly searchable. To make image search possible, we need a way to convert images into a format that can be compared quickly and accurately. That’s where vector embeddings and Memora come in.


## How Does Image Search Work with Memora?
Memora stores and retrieves data in the form of vectors. A vector is simply an array of numbers, and each image can be converted into a vector called vector embedding using a deep learning model called a neural network. This vector embedding represents the image’s features, like colors, shapes, and textures, in a way that can be mathematically compared with other vectors.

Here's how the process works:

1. **Image Conversion**: Each image is passed through a model that converts it into a vector. This model, often called a feature extractor, is typically a convolutional neural network (CNN) trained to understand images.
2. **Vector Storage**: The resulting vectors are stored in Memora.
3. **Querying**: When you search using an image, it’s converted into a vector using the same feature extractor model. Memora then finds the vectors that are most similar to the query vector.
4. **Results Display**: The system retrieves the images associated with the closest vectors and displays them as the search results.

## Step by Step Guide to Image Search in Memora
Now, let’s walk through how you can build this system using Memora.

### Create a Collection
Memora uses collections to store data. We will create a simple collection with an additional field for storing the path for images. For more options in collection, you can check "API Ref".

```python
from memorapy import Memora
from memorapy.models import Collection, Schema, Field 

client = Memora(api_key="YOUR_API_KEY", project_id="YOUR_PROJECT_ID")

collection = Collection(
    collection_name="time_series",
    dimension=512,
    schema=Schema(
        auto_id=False,
        fields=[
            Field(
                field_name="image_path",
                data_type="VarChar",
                element_type_params=ElementTypeParams(max_length=256),
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
            params=IndexConfig(index_type="IVF_FLAT", nlist=128),
        )
    ],
)

result = client.collections.create(collection=collection)

print(result)
```

### Prepate the Data

Since we are working with images, we will use a feature extractor model name "facebook/dinov2-small". 


```python
from transformers import AutoImageProcessor, AutoModel
from PIL import Image


image = Image.open("PATH")

processor = AutoImageProcessor.from_pretrained('facebook/dinov2-small')
model = AutoModel.from_pretrained('facebook/dinov2-small')

out = model(**inputs)
```



### Search for an Image

```python
image_vec = [...]

client.vector.search(
    collection_name="reverse_image_search",
    data=[image_vec],
    anns_field="vector",  # name of the vector field
    output_fields=["image_path"],  # fields that we want to get beside ID
    limit=1,  # number of top similar sentences to return
)

print(result)

img = Image.open(result["data"]["image_path"])
img.show()
```

## Learn More



