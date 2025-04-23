# Vector Indexing

Vector databases use indexing to efficiently organize data and drastically reduce the time it takes in similarity searches. This way, large datasets can be queried much faster.

Most of the indexes supported in Memora use approximate nearest neighnors search (ANNS) algorithms. Rather than returning the exact requested result, which is not efficient, ANNS algorithms aim to sacrifice accuracy to drastically decrease the time it takes to search a dataset. The balance between time and accuracy is determined by the parameters of each algorithm.

Memora offers indexes for floating-point embeddings and binary embeddings. These indexes are stored in memory to be as efficient as possible. Another index called DiskANN offers the option to store the index in your hard disks in the cost of speed.

## Indexes for floating-point embeddings

These embbedings support euclidean distance, inner product and cosine similarity for a similarity metric. They include `FLAT`, `IVF_FLAT`, `IVF_PQ`, `IVF_SQ8`, `HNSW`, and `SCANN` and are CPU-based.


### Indexes for binary embeddings

For 128-dimensional binary embeddings, the storage they take up is 128 / 8 = 16 bytes. And the distance metrics used for binary embeddings are `JACCARD` and `HAMMING`.

This type of indexes include `BIN_FLAT` and `BIN_IVF_FLAT`.

</div>

<div class="filter-sparse">

### Indexes for sparse embeddings

The distance metric supported for sparse embeddings is `IP` only.

The types of indexes include `SPARSE_INVERTED_INDEX` and `SPARSE_WAND`.

</div>

<div class="filter-floating table-wrapper">

<table id="floating">
<thead>
  <tr>
    <th>Supported index</th>
    <th>Classification</th>
    <th>Scenario</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td>FLAT</td>
    <td>N/A</td>
    <td>
      <ul>
        <li>Relatively small dataset</li>
        <li>Requires a 100% recall rate</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>IVF_FLAT</td>
    <td>Quantization-based index</td>
    <td>
      <ul>
        <li>High-speed query</li>
        <li>Requires a recall rate as high as possible</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>IVF_SQ8</td>
    <td>Quantization-based index</td>
    <td>
      <ul>
        <li>High-speed query</li>
        <li>Limited memory resources</li>
        <li>Accepts minor compromise in recall rate</li>
      </ul>
    </td>
  </tr>  
  <tr>
    <td>IVF_PQ</td>
    <td>Quantization-based index</td>
    <td>
      <ul>
        <li>Very high-speed query</li>
        <li>Limited memory resources</li>
        <li>Accepts substantial compromise in recall rate</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>HNSW</td>
    <td>Graph-based index</td>
    <td>
      <ul>
        <li>Very high-speed query</li>
        <li>Requires a recall rate as high as possible</li>
        <li>Large memory resources</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>SCANN</td>
    <td>Quantization-based index</td>
    <td>
      <ul>
        <li>Very high-speed query</li>
        <li>Requires a recall rate as high as possible</li>
        <li>Large memory resources</li>
      </ul>
    </td>
  </tr>
</tbody>
</table>

</div>

<div class="filter-binary table-wrapper">

<table id="binary">
<thead>
  <tr>
    <th>Supported index</th>
    <th>Classification</th>
    <th>Scenario</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td>BIN_FLAT</td>
    <td>Quantization-based index</td>
    <td><ul>
      <li>Depends on relatively small datasets.</li>
      <li>Requires perfect accuracy.</li>
      <li>No compression applies.</li>
      <li>Guarantee exact search results.</li>
    </ul></td>
  </tr>
  <tr>
    <td>BIN_IVF_FLAT</td>
    <td>Quantization-based index</td>
    <td><ul>
      <li>High-speed query</li>
      <li>Requires a recall rate as high as possible</li>
    </ul></td>
  </tr>
</tbody>
</table>

</div>


</div>

<div class="filter-floating">

### FLAT

For vector similarity search applications that require perfect accuracy and depend on relatively small (million-scale) datasets, the FLAT index is a good choice. FLAT does not compress vectors, and is the only index that can guarantee exact search results. Results from FLAT can also be used as a point of comparison for results produced by other indexes that have less than 100% recall.

FLAT is accurate because it takes an exhaustive approach to search, which means for each query the target input is compared to every set of vectors in a dataset. This makes FLAT the slowest index on our list, and poorly suited for querying massive vector data. There are no parameters required for the FLAT index in Memora, and using it does not need data training.

- Search parameters

  | Parameter     | Description                            | Range                               |
  | ------------- | -------------------------------------- | ----------------------------------- |
  | `metric_type` | [Optional] The chosen distance metric. | See [Supported Metrics](metric.md). |

### IVF_FLAT

IVF_FLAT divides vector data into `nlist` cluster units, and then compares distances between the target input vector and the center of each cluster. Depending on the number of clusters the system is set to query (`nprobe`), similarity search results are returned based on comparisons between the target input and the vectors in the most similar cluster(s) only — drastically reducing query time.


IVF_FLAT is the most basic IVF index, and the encoded data stored in each unit is consistent with the original data.

- Index building parameters

   | Parameter | Description             | Range      | Default Value |
   | --------- | ----------------------- | ---------- | ------------- |
   | `nlist`   | Number of cluster units | [1, 65536] | 128 |

- Search parameters

  - Common search

    | Parameter                  | Description                                             | Range      | Default Value |
    |----------------------------|---------------------------------------------------------|------------|---------------|
    | `nprobe`                   | Number of units to query                                | [1, nlist] | 8             |

  - Range search

    | Parameter                  | Description                                             | Range      | Default Value |
    |----------------------------|---------------------------------------------------------|------------|---------------|
    | `max_empty_result_buckets` | Maximum number of buckets not returning any search results.<br/>This is a range-search parameter and terminates the search process whilst the number of consecutive empty buckets reaches the specified value.<br/>Increasing this value can improve recall rate at the cost of increased search time. | [1, 65535] | 2  |

### IVF_SQ8

IVF_FLAT does not perform any compression, so the index files it produces are roughly the same size as the original, raw non-indexed vector data. For example, if the original 1B SIFT dataset is 476 GB, its IVF_FLAT index files will be slightly smaller (~470 GB). Loading all the index files into memory will consume 470 GB of storage.

When disk, CPU, or GPU memory resources are limited, IVF_SQ8 is a better option than IVF_FLAT. This index type can convert each FLOAT (4 bytes) to UINT8 (1 byte) by performing Scalar Quantization (SQ). This reduces disk, CPU, and GPU memory consumption by 70–75%. For the 1B SIFT dataset, the IVF_SQ8 index files require just 140 GB of storage.

- Index building parameters

   | Parameter | Description             | Range      |
   | --------- | ----------------------- | ---------- |
   | `nlist`   | Number of cluster units | [1, 65536] |

- Search parameters

  - Common search

    | Parameter | Description              | Range           | Default Value |
    | --------- | ------------------------ | --------------- | ------------- |
    | `nprobe`  | Number of units to query | [1, nlist]      | 8 |

  - Range search

    | Parameter                  | Description                                             | Range      | Default Value |
    |----------------------------|---------------------------------------------------------|------------|---------------|
    | `max_empty_result_buckets` | Maximum number of buckets not returning any search results.<br/>This is a range-search parameter and terminates the search process whilst the number of consecutive empty buckets reaches the specified value.<br/>Increasing this value can improve recall rate at the cost of increased search time. | [1, 65535] | 2  |

### IVF_PQ

`PQ` (Product Quantization) uniformly decomposes the original high-dimensional vector space into Cartesian products of `m` low-dimensional vector spaces, and then quantizes the decomposed low-dimensional vector spaces. Instead of calculating the distances between the target vector and the center of all the units, product quantization enables the calculation of distances between the target vector and the clustering center of each low-dimensional space and greatly reduces the time complexity and space complexity of the algorithm.

IVF\_PQ performs IVF index clustering before quantizing the product of vectors. Its index file is even smaller than IVF\_SQ8, but it also causes a loss of accuracy during searching vectors.

<div class="alert note">



</div>

- Index building parameters

  | Parameter | Description                               | Range               |
  | --------- | ----------------------------------------- | ------------------- |
  | `nlist`   | Number of cluster units                   | [1, 65536]          |
  | `m`       | Number of factors of product quantization | `dim mod m == 0` |
  | `nbits`   | [Optional] Number of bits in which each low-dimensional vector is stored. | [1, 64] (8 by default) |

- Search parameters

  - Common search

    | Parameter | Description              | Range           | Default Value |
    | --------- | ------------------------ | --------------- | ------------- |
    | `nprobe`  | Number of units to query | [1, nlist]      | 8 |

  - Range search

    | Parameter                  | Description                                             | Range      | Default Value |
    |----------------------------|---------------------------------------------------------|------------|---------------|
    | `max_empty_result_buckets` | Maximum number of buckets not returning any search results.<br/>This is a range-search parameter and terminates the search process whilst the number of consecutive empty buckets reaches the specified value.<br/>Increasing this value can improve recall rate at the cost of increased search time. | [1, 65535] | 2  |

### SCANN

SCANN (Score-aware quantization loss) is similar to IVF_PQ in terms of vector clustering and product quantization. What makes them different lies in the implementation details of product quantization and the use of SIMD (Single-Instruction / Multi-data) for efficient calculation.

- Index building parameters

  | Parameter       | Description                                  | Range                                  |
  |-----------------|----------------------------------------------|----------------------------------------|
  | `nlist`         | Number of cluster units                      | [1, 65536]                             |
  | `with_raw_data` | Whether to include the raw data in the index | `True` or `False`. Defaults to `True`. |

  <div class="alert note">

  Unlike IVF_PQ, default values apply to `m` and `nbits` for optimized performance.

  </div>

- Search parameters

  - Common search

    | Parameter | Description              | Range      | Default value |
    | --------- | ------------------------ | ---------- | ------------- |
    | `nprobe`  | Number of units to query | [1, nlist] |               |
    | `reorder_k` | Number of candidate units to query | [`top_k`, ∞] | |

  - Range search

    | Parameter                  | Description                                             | Range      | Default Value |
    |----------------------------|---------------------------------------------------------|------------|---------------|
    | `max_empty_result_buckets` | Maximum number of buckets not returning any search results.<br/>This is a range-search parameter and terminates the search process whilst the number of consecutive empty buckets reaches the specified value.<br/>Increasing this value can improve recall rate at the cost of increased search time. | [1, 65535] | 2  |

### HNSW

HNSW (Hierarchical Navigable Small World Graph) is a graph-based indexing algorithm. It builds a multi-layer navigation structure for an image according to certain rules. In this structure, the upper layers are more sparse and the distances between nodes are farther; the lower layers are denser and the distances between nodes are closer. The search starts from the uppermost layer, finds the node closest to the target in this layer, and then enters the next layer to begin another search. After multiple iterations, it can quickly approach the target position.

In order to improve performance, HNSW limits the maximum degree of nodes on each layer of the graph to `M`. In addition, you can use `efConstruction` (when building index) or `ef` (when searching targets) to specify a search range.

- Index building parameters

  | Parameter        | Description                | Range        |
  | ---------------- | -------------------------- | ------------ |
  | `M`              | M defines tha maximum number of outgoing connections in the graph. Higher M leads to higher accuracy/run_time at fixed ef/efConstruction.  | [2, 2048]    |
  | `efConstruction` | ef_construction controls index search speed/build speed tradeoff. Increasing the efConstruction parameter may enhance index quality, but it also tends to lengthen the indexing time.              | [1, int_max] |

- Search parameters

  | Parameter | Description  | Range            |
  | --------- | ------------ | ---------------- |
  | `ef`      | Parameter controlling query time/accuracy trade-off. Higher `ef` leads to more accurate but slower search. | [`top_k`, int_max]     |

</div>

<div class="filter-binary">

### BIN_FLAT

This index is exactly the same as FLAT except that this can only be used for binary embeddings.

For vector similarity search applications that require perfect accuracy and depend on relatively small (million-scale) datasets, the BIN_FLAT index is a good choice. BIN_FLAT does not compress vectors, and is the only index that can guarantee exact search results. Results from BIN_FLAT can also be used as a point of comparison for results produced by other indexes that have less than 100% recall.

BIN_FLAT is accurate because it takes an exhaustive approach to search, which means for each query the target input is compared to vectors in a dataset. This makes BIN_FLAT the slowest index on our list, and poorly suited for querying massive vector data. There are no parameters for the BIN_FLAT index in Memora, and using it does not require data training or additional storage.

- Search parameters

  | Parameter     | Description                            | Range                               |
  | ------------- | -------------------------------------- | ----------------------------------- |
  | `metric_type` | [Optional] The chosen distance metric. | See [Supported Metrics](metric.md). |

### BIN_IVF_FLAT

This index is exactly the same as IVF_FLAT except that this can only be used for binary embeddings.

BIN_IVF_FLAT divides vector data into `nlist` cluster units, and then compares distances between the target input vector and the center of each cluster. Depending on the number of clusters the system is set to query (`nprobe`), similarity search results are returned based on comparisons between the target input and the vectors in the most similar cluster(s) only — drastically reducing query time.

By adjusting `nprobe`, an ideal balance between accuracy and speed can be found for a given scenario. Query time increases sharply as both the number of target input vectors (`nq`), and the number of clusters to search (`nprobe`), increase.

BIN_IVF_FLAT is the most basic BIN_IVF index, and the encoded data stored in each unit is consistent with the original data.

- Index building parameters

   | Parameter | Description             | Range      |
   | --------- | ----------------------- | ---------- |
   | `nlist`   | Number of cluster units | [1, 65536] |

- Search parameters

  - Common search

    | Parameter | Description              | Range           | Default Value |
    | --------- | ------------------------ | --------------- | ------------- |
    | `nprobe`  | Number of units to query | [1, nlist]      | 8 |

  - Range search

    | Parameter                  | Description                                             | Range      | Default Value |
    |----------------------------|---------------------------------------------------------|------------|---------------|
    | `max_empty_result_buckets` | Maximum number of buckets not returning any search results.<br/>This is a range-search parameter and terminates the search process whilst the number of consecutive empty buckets reaches the specified value.<br/>Increasing this value can improve recall rate at the cost of increased search time. | [1, 65535] | 2  |

</div>

<div class="filter-sparse">

## On-disk Index

Alternatively, your indexes can be stored on disk using DiskANN. Based on Vamana graphs, DiskANN powers efficient searches within large datasets.

To improve query performance, you can specify an index type for each vector field. 

## Limits

To use DiskANN, ensure that you
- Use only float vectors with at least 1 dimensions in your data.
- Use only Euclidean Distance (L2), Inner Product (IP), or COSINE to measure the distance between vectors.

## Index and search settings

 - Index building parameters

   When building a DiskANN index, use `DISKANN` as the index type. No index parameters are necessary.

- Search parameters

  | Parameter     | Description                         | Range                                           | Default Value     |
  | ------------- | ----------------------------------- | ----------------------------------------------- |-------------------|
  | `search_list` | Size of the candidate list, a larger size offers a higher recall rate with degraded performance. | [topk, int32_max] | 16 |
