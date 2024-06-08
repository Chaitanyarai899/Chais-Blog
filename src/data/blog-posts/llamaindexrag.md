---
title: Mitigating Hallucination in Large Language Models
slug: my-first-post
description: Mitigating Hallucination in Large Language Models using Retrieval-Augmented Generation (RAG)
tags:
  - LLMs
published_at: 2024-05-31T13:58:46.857Z
last_modified_at: 2024-05-31T13:58:46.857Z
image: /assets/blog/imgg.png
---

# Mitigating Hallucination in Large Language Models using Retrieval-Augmented Generation (RAG)

![llm](/assets/blog/llamaindex.jpg)

## Introduction

Large Language Models (LLMs), despite their ability to generate meaningful and grammatically correct text, face a challenge known as hallucination. Hallucination in LLMs refers to their tendency to confidently produce incorrect answers, creating false information that can appear convincing. This issue has been prevalent since the inception of LLMs and often results in inaccurate and factually incorrect outputs.

To address hallucination, fact-checking is crucial. One approach to prototyping LLMs for fact-checking involves three methods:

1. **Prompt Engineering**
2. **Retrieval-Augmented Generation (RAG)**
3. **Fine-Tuning**

In this context, we will utilize RAG (Retrieval-Augmented Generation) to mitigate hallucination.

## What Is RAG?

**RAG** = DenseVector Retrieval (R) + Incontext Learning (AG)

- **Retrieval**: Find References for the question asked in your Document.
- **Augmented**: Add References to your Prompt.
- **Generation**: Improve answers to the question asked.

In RAG, we process a collection of textual documents or document segments by encoding them into numerical representations known as vector embeddings. Each vector embedding corresponds to a single document segment and is stored in a database called the vector store. The models responsible for encoding these segments into embeddings are called encoding models or bi-encoders. These models are trained on extensive datasets, giving them the ability to create powerful representations of the document segments in a single vector embedding. To avoid hallucination, RAG leverages factual knowledge sources that are kept separate from the reasoning capabilities of LLMs. This knowledge is stored externally and can be easily accessed and updated.

There are two types of knowledge sources:

- **Parametric knowledge**: This knowledge is acquired during training and is implicitly stored in the neural network’s weights.
- **Non-parametric knowledge**: This type of knowledge is stored in an external source, such as a vector database.

### Why RAG Before Fine-Tuning (Order of Operation)?

- **Cheap**: No additional training required.
- **Easier to update with the latest information.**
- **More trustworthy because of fact-checkable references.**

## RAG Data Stack

### 📁 Load Language Data
### 💾 Process Language Data
### 🤖 Embed Language Data
### 🗂 Load Vectors into Database

## Stages Involved in RAG

The stages involved in RAG are:

1. **Data Loading**: This involves retrieving data from various sources such as text files, PDFs, websites, databases, or APIs and integrating it into your pipeline. Llama Hub offers a wide range of connectors for this purpose.
2. **Indexing**: This stage focuses on creating a structured format for data querying. For LLMs, indexing typically involves generating vector embeddings, which are numerical representations of the data’s meaning, along with additional metadata strategies to facilitate accurate and contextually relevant data retrieval.
3. **Storage**: After indexing, it’s common practice to store the index and associated metadata to avoid the need for repeated indexing in the future.
4. **Querying**: There are multiple ways to utilize LLMs and Llama-Index data structures for querying, including sub-queries, multi-step queries, and hybrid strategies, depending on the chosen indexing strategy.
5. **Evaluation**: This step is crucial for assessing the effectiveness of the pipeline compared to alternative strategies or when implementing changes. Evaluation provides objective metrics regarding the accuracy, fidelity, and speed of query responses.

Our RAG stack is built using **Llama-Index**, **Qdrant**, and **Llama 3**.

## What Is Llama-Index?

Llama-Index serves as a framework designed for developing LLM applications enriched with context. Context augmentation involves utilizing LLMs with your private or domain-specific data.

Some popular applications of this framework include:

- Question-Answering Chatbots (often known as RAG systems, short for “Retrieval-Augmented Generation”)
- Document Understanding and Extraction
- Autonomous Agents capable of conducting research and taking actions

Llama-Index offers a comprehensive set of tools to facilitate the development of these applications, from initial prototypes to production-ready solutions. These tools enable data ingestion and processing, as well as the implementation of sophisticated query workflows that combine data access with LLM-based prompting.

Here we have used **llama-index >= v0.10**.

## Major Enhancements

- **ServiceContext is deprecated**: Every LlamaIndex user is familiar with ServiceContext, which has gradually become outdated and cumbersome for managing LLMs, embeddings, chunk sizes, callbacks, and other functionalities. Consequently, we are fully deprecating it; you can now either specify arguments directly or set a default.
- **Revamped Folder Structure**:
  - **llama-index-core**: This folder encompasses all core Llama-Index abstractions.
  - **llama-index-integrations**: This folder includes third-party integrations for 19 Llama-Index abstractions, covering data loaders, LLMs, embedding models, vector stores, and more.
  - **llama-index-packs**: Here, you’ll find our collection of 50+ LlamaPacks, which are templates aimed at jumpstarting a user’s application.
- **LlamaHub** will serve as the central hub for all integrations.

## Llama 3

Meta’s Llama 3 is the latest version of the open-access Llama series, accessible through Hugging Face. It serves as the language model for response synthesis. Llama 3 is available in two sizes: 8B for streamlined deployment and development on consumer-grade GPUs, and 70B for extensive AI applications. Each size variant offers both base and instruction-tuned versions. Additionally, a new iteration of Llama Guard, fine-tuned on Llama 3 8B, has been introduced as Llama Guard 2.

## What Is Qdrant?

Qdrant is a vector similarity search engine that offers a production-ready service through an easy-to-use API. It specializes in storing, searching, and managing points (vectors) along with additional payload information. It is optimized for efficiently storing and querying high-dimensional vectors. Vector databases like Qdrant leverage specialized data structures and indexing techniques such as Hierarchical Navigable Small World (HNSW) for implementing Approximate Nearest Neighbors and Product Quantization, among others. These optimizations enable fast similarity and semantic search, allowing users to locate vectors that closely match a given query vector based on a specified distance metric. Commonly used distance metrics supported by Qdrant include Euclidean Distance, Cosine Similarity, and Dot Product.

## Technology Stack Used

- **Application Framework**: Llama-index
- **Embedding Model**: BAAI/bge-small-en-v1.5
- **LLM**: Meta-Llama-3
- **Vector Store**: Qdrant

## Code Implementation

### Install Required Libraries

```plaintext
%%writefile requirements.txt
llama-index
llama-index-llms-huggingface
llama-index-embeddings-fastembed
fastembed
Unstructured[md]
qdrant
llama-index-vector-stores-qdrant
einops
accelerate
sentence-transformers

!pip install -r requirements.txt
accelerate==0.29.3
einops==0.7.0
sentence-transformers==2.7.0
transformers==4.39.3
qdrant-client==1.9.0
llama-index==0.10.32
llama-index-agent-openai==0.2.3
llama-index-cli==0.1.12
llama-index-core==0.10.32
llama-index-embeddings-fastembed==0.1.4
llama-index-legacy==0.9.48
llama-index-llms-huggingface==0.1.4
llama-index-vector-stores-qdrant==0.2.8
```

### Download the Dataset

```bash
!mkdir Data
!wget "https://arxiv.org/pdf/1810.04805.pdf" -O Data/arxiv.pdf
```

### Load the Documents

```python
from llama_index.core import SimpleDirectoryReader
documents = SimpleDirectoryReader("/content/Data").load_data()
```

### Instantiate the Embedding Model

```python
from llama_index.embeddings.fastembed import FastEmbedEmbedding
from llama_index.core import Settings

embed_model = FastEmbedEmbedding(model_name="BAAI/bge-small-en-v1.5")
Settings.embed_model = embed_model
Settings.chunk_size = 512
```

### Define the System Prompt

```python
from llama_index.core import PromptTemplate
system_prompt = "You are a Q&A assistant. Your goal is to answer questions as accurately as possible based on the instructions and context provided."
query_wrapper_prompt = PromptTemplate("{query_str}")
```

### Instantiate the LLM

Since we are using Llama 3 as the LLM, we need to do the following:

1. Generate HuggingFace Access Tokens
2. Request access to use the Model

```python
from huggingface_hub import notebook_login
notebook_login()

import torch
from transformers import AutoModelForCausalLM, AutoTokenizer
from llama_index.llms.huggingface import HuggingFaceLLM

tokenizer = AutoTokenizer.from_pretrained("meta-llama/Meta-Llama-3-8B-Instruct")
stopping_ids = [
    tokenizer.eos_token_id,
    tokenizer.convert_tokens_to_ids(""),
]
llm = HuggingFaceLLM(
    context_window=8192,


    model="meta-llama/Meta-Llama-3-8B-Instruct",
    tokenizer=tokenizer,
    stopping_ids=stopping_ids,
    max_generation=512,
    device_map="auto",
)
Settings.llm = llm
```

### Instantiate Vector Store and Storage Index

```python
from llama_index.vector_stores.qdrant import QdrantVectorStore
from qdrant_client import QdrantClient

vector_store = QdrantVectorStore(client=QdrantClient(), collection_name="data")
index = StorageIndex.from_documents(documents=documents, vector_store=vector_store)
```

### Query the Database

```python
query_engine = index.as_query_engine()
response = query_engine.query("Give me a summary of RAG?")
print(response)
```

## Conclusion

By utilizing RAG (Retrieval-Augmented Generation), we can enhance the accuracy of Large Language Models (LLMs) by leveraging factual knowledge sources. This approach involves retrieving relevant information, augmenting it with the generated content, and improving the overall responses. Through the use of Llama-Index, Qdrant, and Llama 3, we have built a robust stack for mitigating hallucination in LLMs, ensuring more reliable and fact-based outputs.