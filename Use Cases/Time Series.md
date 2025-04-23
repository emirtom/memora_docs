# Time Series with Memora
Time series data is all around us. From weather patterns to stock prices and heart rate monitors, it is everywhere we look. Understanding time series and finding patterns in them can reveal valuable insights across fields like finance, healthcare, and technology. In this post, we’ll break down what time series data is and show how you can use Memora to work with it step by step.

## What Is Time Series Data?

A **time series** is a collection of data points recorded over time in sequential order. Each point represents a value at a particular moment, allowing us to observe trends, seasonal patterns, or anomalies over a defined period. Think of daily temperatures measured over months, or the hourly fluctuations in a stock price—each point in these examples represents a timestamped measurement, or “time series” data point.

## Why Use Memora for Time Series?
Traditional databases store data as structured tables, making them less suitable for time series analysis when you need to find patterns or similar sequences. Memora, on the other hand, store and index data as high-dimensional vectors. This is particularly useful for time series because you can convert each sequence into a vector and use efficient search methods to find patterns, detect anomalies, and make predictions.

## Analyzing Tempreature Trends with Memora
Let’s go through an example step by step, using a temperature dataset and Memora. For this, we have a dataset representing daily temperature readings for a full month (30 days). This time series data allows us to explore various temperature trends over time, such as weekly patterns, fluctuations, or even significant shifts in weather conditions.

To analyze and compare these patterns, we can transform the data into vectors and then use cosine similarity. By doing so, we can efficiently find and evaluate sequences that either closely resemble each other or differ significantly as cosine similarity is a metric that emphasizes pattern similarity. Let’s understand how this works in more detail. For starters, you can the Python SDK for Memora by using pip in command line:

```shell
$ pip install memorapy
```

### Create a Collection
Memora uses collections to store data. We will create a collection with an additional field for storing the weeks. We also need to add an index for the vector field as it can be seen. For more explanation and options in collection, you can check "API Ref".

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
                field_name="week",
                data_type="VarChar",
                element_type_params=ElementTypeParams(max_length=128),
            ),
            Field(
                field_name="vector",
                data_type="FloatVector",
                element_type_params=ElementTypeParams(dim=7),
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
    {'message': 'collection created'}


### Prepare the Data

Transforming time series data into vectors is an important step to enable pattern matching and similarity search in Memora. In this guide, we will convert the monthly temperature data into weeks and try to find a week similar to a one the user will ask.

```python
temperatures = [
    5.3, 5.7, 4.8, 6.1, 6.0, 5.2, 4.9, 5.6, 6.2, 5.4,
    5.9, 6.3, 4.7, 5.8, 5.1, 5.5, 6.4, 5.0, 6.7, 5.3,
    5.8, 5.6, 6.1, 6.5, 5.7, 5.2, 6.8, 5.4, 5.9, 6.2
]

# Vectors that represent each week
weekly_temperatures = [temperatures[i:i+7] for i in range(24)]

```

You can put these vectors inside your collection by:

```python

data = [
    {"vector": weekly_temperatures[i], "id": i, "week": f"week {i}" for i in range(24)}
]



result = client.vectors.insert(collection_name="time_series", data=data)

print(result)
```
    {'data': {'insertCount': 24, 'insertIds': [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, ... , 23, 24]}}


### Searching for a Similar Pattern

Now that the data is in the collection, Memora will allow us to identify similar trends, such as consistent warming or cooling patterns, or spot weeks with drastically different temperatures. These insights can be useful for applications like weather forecasting, identifying seasonal trends, or detecting anomalies in climate data.

We will use similarity searches to uncover weeks with patterns that closely match our chosen reference week. 


```python
result = client.vectors.search(
    collection_name="time_series",
    data=[[5.3, 5.7, 4.8, 6.1, 6.0, 5.2, 4.9]],  
    anns_field="vector",  # name of the vector field
    output_fields=["week"],  # fields that we want to get beside ID
    limit=1,  # number of top similar sentences to return
)
```

## Learn More


