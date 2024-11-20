# Using Memora for Training and Fine Tuning

In the world of artificial intelligence (AI) and machine learning, one of the most critical elements is data. But not just any data—*training data*. Without training data, a machine learning model wouldn't know what to learn or how to make decisions. Let’s start by understanding what training data is, and then we'll dive into how a vector database can be an innovative way to manage it.

## What is Training Data?

Training data is a collection of labeled examples that a machine learning model uses to understand patterns, relationships, and rules. For instance, if you were building an AI to recognize images of cats, your training data would be hundreds or thousands of labeled images—each marked as either containing a cat or not. The more data you have, the better your model can generalize and recognize new examples it hasn’t seen before.

However, training data doesn’t always come in a neat, structured form like rows in a spreadsheet. It can be in the form of text, images, sounds, or more abstract data like user behavior or sensor readings. This is where the challenge comes in: traditional databases are often designed to handle structured data, not these complex, high-dimensional forms of data that are common in AI.

## What is Model Finetuning
When a model is trained for the first time, it learns general patterns from a large set of data. But what if you want your model to become an expert in a specific area, like understanding medical documents or classifying paintings? That's where fine-tuning comes in. 

Fine-tuning means taking a model that has already learned general patterns and training it further on more specific data so it can perform better in a particular area. It’s like giving a chef extra training on how to make perfect recipe, even though the chef already knows how to cook a wide variety of dishes.

## Traditional Databases vs. Memora

Traditional databases, such as relational databases (like MySQL or PostgreSQL), store data in tables made up of rows and columns. This works well for structured data like customer information or financial records. But when it comes to unstructured data, these databases start to struggle. Searching, organizing, and managing this type of data in a traditional database requires significant overhead and often isn't efficient.

Memora is designed specifically for high-dimensional data. It stores data as *vectors*, which are essentially arrays of numbers representing complex objects. When we convert an image, a sentence, or any other form of unstructured data into a vector, we create a representation of that data that captures its essential characteristics in a mathematical form.

For example, if you're working with text, you can convert words into vectors using a process called *embedding*, where similar words or phrases are placed close together in vector space. This makes it much easier for the AI model to recognize relationships and patterns in the data.

## Advantages of Memora

So, how does a vector database shine when it comes to training and storing data for AI? Here are some of the key advantages:

1. **Efficient Handling of Unstructured Data**  
   Vector databases are ideal for managing unstructured data—like images, videos, and text—that traditional databases struggle with. Because they store data as vectors, you can search and analyze complex data much more efficiently. For example, finding similar images in a collection becomes as easy as comparing their vectors.

2. **Scalability**  
   Vector databases are built to scale with large datasets. Since training data often requires large volumes of unstructured information (e.g., millions of images or text documents), a vector database can handle the load efficiently without slowing down while reducing the memory usage by compressing data.

3. **Supports AI and Machine Learning Workflows**  
   Traditional databases are not optimized for the kind of workflows required in AI and machine learning. A vector database, on the other hand, integrates seamlessly into AI pipelines. For instance, if you're working with deep learning models that require vectors as input, you can store the vectors directly in the database, making it easier to retrieve and use them in training or inference.

4. **Enables Advanced Applications**  
   Using a vector database can open doors to more advanced AI applications. Beyond just training models, you can build systems for recommendation, image search, or anomaly detection. Since the data is stored as vectors, these tasks become easier to perform efficiently.

## Storing and Managing Training Data in a Vector Database

Imagine you're training a model to recommend movies based on user preferences. Your training data might include movie descriptions, user reviews, and interaction histories. Memora allows you to store all these different forms of data as vectors, enabling your AI model to learn patterns across multiple dimensions (e.g., what types of movies people with similar preferences tend to watch).

As your dataset grows, the vector database can continue to store new data efficiently, and as your model evolves, it can quickly access the stored vectors for additional training or fine-tuning. In this way, Memora not only stores training data but actively supports the development and optimization of machine learning models.


This guide will use the the SQuAD dataset and roberta-base model from HuggingFace. The data will be stored in a Memora collection and will be used for finetuning in the following section. For more explanation on creating a NoSQL collection, you can check out "docs" as an already created collection will be used for this guide.


```python
from datasets import load_dataset
from transformers import AutoTokenizer
from memorapy import Memora

# Load the SQuAD dataset
dataset = load_dataset("squad")

client = Memora(project_id="YOUR_PROJECT", api_key="YOUR_API_KEY")

model_name = "deepset/roberta-base-squad2"
tokenizer = tokenizer = AutoTokenizer.from_pretrained(model_name)

id = 0

# Tokenize and store each example in the database
for example in dataset["train"]:
    question = example["question"]
    context = example["context"]
    answers = example["answers"]

    # Tokenize the question and context
    encoding = tokenizer(
        question,
        context,
        max_length=384,
        truncation=True,
        padding="max_length",
        return_tensors="pt",
    )

    # Find the start and end positions of the answers
    start_char = answers["answer_start"][0]
    end_char = start_char + len(answers["text"][0])
    start_token = encoding.char_to_token(0, start_char)
    end_token = encoding.char_to_token(0, end_char - 1)

    # Handle cases where the answer tokens are not found
    if start_token is None:
        start_token = 0
    if end_token is None:
        end_token = 0

    # Convert tensors to lists for storage
    input_ids = encoding["input_ids"].squeeze().tolist()
    attention_mask = encoding["attention_mask"].squeeze().tolist()

    client.nosql.insert(
        collection_name="finetune_data",
        data={
            "id": id,
            "start_token": start_token,
            "end_token": end_token,
            "input_ids": input_ids,
            "attention_mask": attention_mask,
        },
    )

    id += 1


```

## Finetune the Model Using the Data Inside Memora

For training, we need to create ``TokenizedDataset`` class that takes a batch of data fetched from the database and prepares it as a PyTorch Dataset, converting the data back to tensors.

```python
from torch.utils.data import Dataset
import torch


class TokenizedDataset(Dataset):
    def __init__(self, data):
        self.data = data

    def __len__(self):
        return len(self.data)

    def __getitem__(self, idx):
        item = self.data[idx]
        # Convert the data back to tensors
        input_ids = torch.tensor(item[0])
        attention_mask = torch.tensor(item[1])
        start_positions = torch.tensor(item[2])
        end_positions = torch.tensor(item[3])
        return {
            "input_ids": input_ids,
            "attention_mask": attention_mask,
            "start_positions": start_positions,
            "end_positions": end_positions,
        }

```

We will use the below function to communicate with Memora as we get data in batches.


```python

def fetch_batch(batch_size, offset):
    result = client.nosql.query(
        collection_name="finetuning_data",
        filter=f"{offset} <= id < {offset+batch_size}",
        output_fields=[
            "input_ids",
            "attention_mask",
            "start_positions",
            "end_positions",
        ],
    )

    return result["data"]


```

Now we can define the training arguments and start finetuning our model:


```python
from transformers import Trainer, TrainingArguments, AutoModel
from torch.utils.data import DataLoader

model = AutoModel.from_pretrained(model_name)

# Define training arguments
training_args = TrainingArguments(
    output_dir="./results",
    learning_rate=2e-5,
    per_device_train_batch_size=8,
    num_train_epochs=1,  # Train each batch for 1 epoch (adjust as needed)
    weight_decay=0.01,
)

# Initialize the Trainer
trainer = Trainer(model=model, args=training_args)

batch_size = 50
offset = 0

while True:
    # Fetch a batch of data from the database
    data_batch = fetch_batch(batch_size, offset)

    # If no more data is left, break the loop
    if not data_batch:
        break

    # Create a DataLoader for the current batch
    tokenized_dataset = TokenizedDataset(data_batch)
    data_loader = DataLoader(tokenized_dataset, batch_size=8, shuffle=True)

    # Train the model using the current batch
    trainer.train(data_loader)

    # Update the offset for the next batch
    offset += batch_size


```

Running the above code will result in a finetuned model for our specified needs. When the training is over, it is possible to save the model and the tokenizer to use in the future:

```python

# Save the finetuned model
model.save_pretrained("./finetuned-roberta-squad")
tokenizer.save_pretrained("./finetuned-roberta-squad")


```


## Learn More

When it comes to storing and managing training data for AI, a vector database offers clear advantages over traditional databases. By leveraging vectors to represent complex and unstructured data, a vector database enables faster, more accurate searches, better scalability, and smoother integration into machine learning workflows.

If you're working with AI or planning to build applications that rely on large datasets, Memora could be a game-changer, making it easier to store, retrieve, and analyze the data that powers your models.

// Add links of other use cases
