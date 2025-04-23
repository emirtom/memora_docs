# Similarity Metrics

Memora offers the choice to use different similarity metrics to calculate the distance between two vectors. Choosing an appropriate distance metric for your project greatly affects the classification and clustering performance. </br>

There are float vectors and binary vectors to use for your embeddings. Euclidean distance, inner product and cosine similarity can be used with float vectors whereas Jaccard and Hamming distance is for binary vectors.

## **Euclidean Distance**
Euclidean distance is a measure of the "straight line" distance between two points in a vector space. It calculates the root of the sum of the squared differences between corresponding elements of two vectors. Best for scenarios where literal distance matters, like mapping shortest paths or comparing image features. Ideal for clustering tasks where spatial differences are key.


## **Inner Product**
The inner product (or dot product) measures the degree to which two vectors point in the same direction. It is a scalar value that is the sum of the products of the corresponding elements of the two vectors. Useful for ranking items by relevance, like matching ads or search results to user preferences. Effective when the magnitude or intensity of features is important.

## **Cosine Similarity**
Cosine similarity measures the cosine of the angle between two vectors, giving a value between -1 and 1.  A cosine similarity of 1 indicates that the vectors are identical in direction whereas -1 indicates that they are the exact opposite. Perfect for comparing text documents or user interests, focusing on similarity in direction rather than size. Works well for matching profiles in recommendation systems.



## **Jaccard Distance**
Jaccard similarity coefficient measures the similarity between two sets by dividing the size of their intersection by the size of their union. It is commonly used for comparing the similarity between two sets. Jaccard distance measures the dissimilarity of the sets and can be obtained by subtracting jaccard similarity coefficient from 1. Great for comparing sets, such as finding overlapping interests or tags. Useful for systems that need to measure shared attributes, like social network suggestions.


## **Hamming Distance**
Hamming distance measures the number of positions at which the corresponding elements of two strings (or binary vectors) are different. IIdeal for comparing binary data, like detecting errors in data transmission or analyzing DNA sequences. Useful when exact positions of differences are important.
