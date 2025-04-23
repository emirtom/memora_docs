# Data Types
Memora supports different data types for vector, scalar and primary fields.

- Primary key field supports:
  - INT64: numpy.int64
  - VARCHAR: VARCHAR
- Scalar field supports:
  - BOOL: Boolean (`true` or `false`)
  - INT8: numpy.int8
  - INT16: numpy.int16
  - INT32: numpy.int32
  - INT64: numpy.int64
  - FLOAT: numpy.float32
  - DOUBLE: numpy.double
  - VARCHAR: VARCHAR
  - JSON: JSON as a composite data type is available. A JSON field comprises key-value pairs. Each key is a string, and a value can be a number, string, boolean value, array, or list.

- Vector field supports:
  - BINARY_VECTOR: Stores binary data as a sequence of 0s and 1s, used for compact feature representation in image processing and information retrieval.
  - FLOAT_VECTOR: Stores 32-bit floating-point numbers, commonly used in scientific computing and machine learning for representing real numbers.
  - FLOAT16_VECTOR: Stores 16-bit half-precision floating-point numbers, used in deep learning and GPU computations for memory and bandwidth efficiency.
  - BFLOAT16_VECTOR: Stores 16-bit floating-point numbers with reduced precision but the same exponent range as Float32, popular in deep learning for reducing memory and computational requirements without significantly impacting accuracy.

