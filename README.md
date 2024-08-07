# NIKE RAG Chatbot

This repository contains a Retrieval-Augmented Generation (RAG) based chatbot designed for the NIKE CORL (Consumer Order Repository Line) Slack channel. The chatbot's primary purpose is to minimize the time spent by Nike employees in Global Technology on finding information in CORL Confluence pages and answering related questions.

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Model Outline](#model-outline)
- [Context Sources](#context-sources)
- [Example Responses](#example-responses)
  - [Technical Questions](#technical-questions)
  - [General Questions](#general-questions)
- [Embeddings Setup](#embeddings-setup)
- [How to Run This Project](#how-to-run-this-project)

## Overview

The NIKE RAG Chatbot leverages context from various sources, such as CORL Documentation, CORL Confluence pages, and Confluence table summaries. By doing so, it accurately answers a range of questions, from general inquiries about table access to technical questions regarding missing values and join keys.

![Screenshot of Chatbot in Action](https://github.com/user-attachments/assets/c43b39bf-ed26-4e54-bc2c-4ea5bb5bb740)

## Features

- **Slack Integration:** The chatbot is integrated into the Slack channel, providing seamless access for Nike employees.
- **Contextual Understanding:** Utilizes context from multiple sources to generate accurate and relevant responses.
- **Support for Technical and General Queries:** Can handle both technical questions and general inquiries effectively.

![Chatbot Contextual Understanding](https://github.com/user-attachments/assets/48f5aaee-6ee4-4587-9fb9-7e20abd4749a)

## Model Outline

The chatbot employs a Retrieval-Augmented Generation (RAG) architecture, which combines pre-trained language models with a retrieval mechanism for providing contextually relevant responses. Here's a brief outline of the model:

1. **Embeddings Model:** Utilizes the `BAAI/bge-large-en` model from Hugging Face to create vector embeddings of the input texts. This model processes the text data and represents it as high-dimensional vectors.

2. **Vector Store:** Utilizes the Qdrant vector database to store and retrieve embeddings. This allows the model to efficiently search and find relevant documents or context when answering queries.

3. **Language Model:** Uses the Llama3 large language model (LLM) for generating responses based on the retrieved context. The model can handle complex queries and generate human-like responses.

![RAG Model Outline](https://github.com/user-attachments/assets/d5c9e2b9-c032-47c2-942c-c3384faf5bd4)

## Context Sources

The chatbot's responses are enriched with context drawn from the following sources:

- CORL Documentation
- CORL Confluence Pages
- Confluence Table Summaries

![Context Sources](https://github.com/user-attachments/assets/363a82ce-f515-410f-9aa7-ccc34405af93)

## Example Responses

### Technical Questions

1. <img src="https://github.com/user-attachments/assets/01ba1b3a-683e-47be-b627-e9497b64a773" alt="Technical Question 1" width="500"/> <img src="https://github.com/user-attachments/assets/b8cc7951-c5ca-4153-82c3-e54063573ec0" alt="Technical Question 1 Answer" width="500"/>
2. <img src="https://github.com/user-attachments/assets/47afb554-b040-4661-b6d4-6b8104cd19ee" alt="Technical Question 2" width="400"/>
3. <img src="https://github.com/user-attachments/assets/fec43d65-9ac6-4d2f-8b31-a073b9d558fb" alt="Technical Question 3" width="300"/>

### General Questions 
<img src="https://github.com/user-attachments/assets/82008643-af3a-49d6-9e42-a4e64d1c4146" alt="General Question" width="200"/>

## Embeddings Setup

The chatbot utilizes vector embeddings to effectively process and retrieve relevant context for queries. Here’s a detailed explanation of the embeddings setup:

1. **PDF Processing:** The `PyPDFLoader` is used to load and read PDF files from the specified directory. The text is then split into smaller chunks using the `RecursiveCharacterTextSplitter`, with each chunk being 1000 characters long and having a 100-character overlap. This ensures that the context is adequately captured without being too fragmented.

2. **Embedding Creation:** The `HuggingFaceBgeEmbeddings` model (`BAAI/bge-large-en`) is used to generate embeddings from the text chunks. These embeddings are numerical representations of the text, capturing semantic meaning and allowing for efficient similarity searches.

3. **Vector Store Initialization:** The embeddings are stored in a Qdrant vector database. This allows for fast retrieval of relevant documents based on similarity searches, making the chatbot responsive and context-aware.

## How to Run This Project

### Prerequisites

- **Python:** Make sure you have Python 3.7 or later installed.
- **Slack App:** Set up a Slack app with the necessary permissions and obtain your Bot User OAuth Token.
- **Qdrant Server:** Ensure you have a running instance of Qdrant on `localhost:6333`.

### Installation

1. **Clone the Repository:**

    ```bash
    git clone https://github.com/your-repo/nike-rag-chatbot.git
    cd nike-rag-chatbot
    ```

2. **Install Dependencies:**

    Use `pip` to install required packages:

    ```bash
    pip install -r requirements.txt
    ```

3. **Configure Environment Variables:**

    Set the environment variables for your Slack Bot:

    ```bash
    export SLACK_SIGNING_SECRET='your-slack-signing-secret'
    export SLACK_BOT_TOKEN='your-slack-bot-token'
    ```

### Running the Embeddings Setup

Run the `embeddings_setup.py` script to process the PDF documents and create embeddings:

```bash
python embeddings_setup.py
```
### Running the Embeddings Setup

This script will read PDF files from the specified folder, create embeddings, and store them in the Qdrant vector database.

### Running the Chatbot

1. **Start the Flask Application:**

    Run the `botapp.py` script to start the chatbot server:

    ```bash
    python botapp.py
    ```

    This will start a Flask server and connect the chatbot to your Slack workspace.

2. **Interact with the Chatbot:**

    Mention the bot in your Slack channel with a query, and it will respond based on the context provided by the embeddings.

### Debugging and Logging

- The application logs are set to the `INFO` level by default. You can monitor the logs to debug issues or understand the chatbot's behavior.

### Troubleshooting

- **Qdrant Connection Issues:** Ensure that your Qdrant instance is running and accessible at `http://localhost:6333`.
- **Slack API Errors:** Verify your Slack API credentials and permissions.
- **Environment Variables:** Double-check that all necessary environment variables are correctly set.
