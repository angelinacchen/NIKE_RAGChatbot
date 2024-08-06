# NIKE_RAGChatbot

This repository contains a Retrieval-Augmented Generation (RAG) based chatbot designed for the NIKE CORL (Consumer Order Repository Line) Slack channel. The chatbot's primary purpose is to minimize the time spent by Nike employees in Global Technology on finding information in CORL Confluence pages and answering related questions.

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Model Outline](#model-outline)
- [Context Sources](#context-sources)
- [Example Responses](#example-responses)
  - [Technical Questions](#technical-questions)
  - [General Questions](#general-questions)
- [How to Run This Project](#how-to-run-this-project)

## Overview

The NIKE_RAGChatbot leverages context from various sources, such as CORL Documentation, CORL Confluence pages, and Confluence table summaries. By doing so, it accurately answers a range of questions, from general inquiries about table access to technical questions regarding missing values and join keys.

![Screenshot of Chatbot in Action](https://github.com/user-attachments/assets/c43b39bf-ed26-4e54-bc2c-4ea5bb5bb740)

## Features

- **Slack Integration:** The chatbot is integrated into the Slack channel, providing seamless access for Nike employees.
- **Contextual Understanding:** Utilizes context from multiple sources to generate accurate and relevant responses.
- **Support for Technical and General Queries:** Can handle both technical questions and general inquiries effectively.

![Chatbot Contextual Understanding](https://github.com/user-attachments/assets/48f5aaee-6ee4-4587-9fb9-7e20abd4749a)

## Model Outline

Below is an outline of the RAG Model, showcasing the architecture and workflow involved in generating responses:

![RAG Model Outline](https://github.com/user-attachments/assets/d5c9e2b9-c032-47c2-942c-c3384faf5bd4)

## Context Sources

The chatbot's responses are enriched with context drawn from the following sources:

- CORL Documentation
- CORL Confluence Pages
- Confluence Table Summaries

![Context Sources](https://github.com/user-attachments/assets/363a82ce-f515-410f-9aa7-ccc34405af93)

## Example Responses

### Technical Questions

1. ![Technical Question 1](https://github.com/user-attachments/assets/01ba1b3a-683e-47be-b627-e9497b64a773)
   
   ![Technical Question 1 Answer](https://github.com/user-attachments/assets/b8cc7951-c5ca-4153-82c3-e54063573ec0)

2. ![Technical Question 2](https://github.com/user-attachments/assets/47afb554-b040-4661-b6d4-6b8104cd19ee)

3. ![Technical Question 3](https://github.com/user-attachments/assets/fec43d65-9ac6-4d2f-8b31-a073b9d558fb)

### General Questions

![General Question](https://github.com/user-attachments/assets/82008643-af3a-49d6-9e42-a4e64d1c4146)

## How to Run This Project

### Embeddings Setup

1. **Embeddings_Setup.py**: This Python script accesses the context for the RAG in PDF format. It splits the text into 1000-character chunks with a 100-character overlap and creates vector embeddings for the text chunks using the Qdrant vector database. The embeddings are saved locally.

### Bot Application

2. **BotApp.py**: This Python script utilizes the vector embeddings saved in the Qdrant database to generate responses using the Llama3 LLM (large language model). It includes code for:
   - Connecting the model to Slack
   - Customizing responses when the bot is mentioned
   - Handling context for follow-up questions, code-related queries, and questions with images.
