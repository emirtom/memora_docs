# What is a Partition
A partition in Memora is a way to divide a collection into smaller parts. This makes queries faster because the search only needs to look through a smaller subset of data instead of the whole collection.

When a collection is created, a partition named ``_default`` is created alongside. Unless specified, each vector will be put inside this partition. The maximum number fo partitions for each collection is 1024.

# Partition Key

You can set a particular field in a collection as the partition key so that Memora distributes incoming entities into different partitions according to their respective partition values in this field. This allows entities with the same key value to be grouped in a partition, accelerating search performance by avoiding the need to scan irrelevant partitions when filtering by the key field. When compared to traditional filtering methods, the partition key can greatly enhance query performance.


Imagine you have a collection that holds objects with their color. Once you have set a partition key in color field, you can use it to filter your search as in the example:

```python
vectors = [0.644686512685, -0.287321544653, -0.879712466324, -0.1297324308684, 0.943581347317]

result = client.vectors.search(
    collection_name="test_collection",
    data=vectors,
    filter="color == 'green'",
    search_params={"metric_type": "L2", "params": {"nprobe": 10}},
    output_fields=["id", "color_tag"],
    limit=3
)
```