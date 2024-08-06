# NIKE_RAGChatbot

This RAG based chatbot is used for the NIKE CORL(Consumer Order Repository Line) Slack channel. Its purpose is to reduce the amount of time employees at Nike in Global Technology need to spend finding information about CORL in Confluence pages and answering other people's questions.

<img width="1020" alt="Screenshot 2024-08-06 at 10 19 19 AM" src="https://github.com/user-attachments/assets/c43b39bf-ed26-4e54-bc2c-4ea5bb5bb740">


Using context for the chatbot such as CORL Documentation, CORL Confluence pages, and Confluence table summaries, this chatbot is able to accurately answer questions from general questions about requesting access for specific tables to more technical questions about missing values and specific join keys. 

<img width="1002" alt="Screenshot 2024-08-06 at 10 21 55 AM" src="https://github.com/user-attachments/assets/48f5aaee-6ee4-4587-9fb9-7e20abd4749a">

Outline of the RAG Model:

<img width="776" alt="image" src="https://github.com/user-attachments/assets/d5c9e2b9-c032-47c2-942c-c3384faf5bd4">

Context for the RAG:

<img width="1038" alt="Screenshot 2024-08-06 at 10 23 29 AM" src="https://github.com/user-attachments/assets/363a82ce-f515-410f-9aa7-ccc34405af93">

Examples of responses the model can form:
Technical Questions:
1.
<img width="902" alt="image" src="https://github.com/user-attachments/assets/01ba1b3a-683e-47be-b627-e9497b64a773">

<img width="865" alt="image" src="https://github.com/user-attachments/assets/b8cc7951-c5ca-4153-82c3-e54063573ec0">

2.
<img width="729" alt="image" src="https://github.com/user-attachments/assets/47afb554-b040-4661-b6d4-6b8104cd19ee">

3.
<img width="420" alt="image" src="https://github.com/user-attachments/assets/fec43d65-9ac6-4d2f-8b31-a073b9d558fb">

General Questions:

<img width="301" alt="image" src="https://github.com/user-attachments/assets/82008643-af3a-49d6-9e42-a4e64d1c4146">

How to run this project:
Embeddings_Setup: This python script will access the context for the RAG in pdf form. It splits the text into 1000 character text chunks with a 100 character overlap and creates vector embeddings for the text chunks using the vector database Qdrant. This is saved locally on the computer. 


BotApp.py: This python script uses vector embeddings saved in the Qdrant database to generate a response using the Llama3 llm(large language model). It contains code connecting the model to Slack and customizing when the bot is mentioned, the kind of context used to answer follow up questions, questions with code chunks, and questions with images. 

