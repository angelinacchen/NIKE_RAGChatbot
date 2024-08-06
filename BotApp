from flask import Flask, request, jsonify
from slackeventsapi import SlackEventAdapter
from slack_sdk import WebClient
from slack_sdk.errors import SlackApiError
from operator import itemgetter
from langchain.prompts import PromptTemplate
from langchain_community.llms import Ollama
from langchain.embeddings import HuggingFaceBgeEmbeddings
from langchain_core.output_parsers import StrOutputParser
from langchain_community.document_loaders import PyPDFLoader
from langchain_community.vectorstores import DocArrayInMemorySearch
from langchain.schema import Document
from qdrant_client import QdrantClient
from langchain.vectorstores import Qdrant
install python-dotenv

import logging
import re
import requests
from io import BytesIO
from PIL import Image
import os
import ssl
ssl._create_default_https_context = ssl._create_unverified_context

app = Flask(__name__)
slack_event_adapter = SlackEventAdapter(process.env.EVENT_ADAPTER, '/slack/events', app)

slack_client = WebClient(token=process.env.SLACK_TOKEN)

bot_user_id = slack_client.auth_test()["user_id"]

parser = StrOutputParser()

template = """
Answer the question based on the context below.
If you can't answer the question, reply "I don't know."

Context:
{context}

Question:
{question}
"""

parser = StrOutputParser()

template = """
Answer the question based on the context below in a concise manner.
If you can't answer the question, reply "I don't know"

Context: {context}
Question: {question}
"""

prompt = PromptTemplate.from_template(template)

client = QdrantClient(url="http://localhost:6333", prefer_grpc=False)

model_name = "BAAI/bge-large-en"
model_kwargs = {'device': 'cpu'}
encode_kwargs = {'normalize_embeddings': False}
embeddings = HuggingFaceBgeEmbeddings(
    model_name=model_name,
    model_kwargs=model_kwargs,
    encode_kwargs=encode_kwargs
)

db = Qdrant(client=client, embeddings=embeddings, collection_name="vector_db")

class Conversation:
    def __init__(self):
        self.history = []
        self.threads = {}
        self.processed_events = set()

    def update_history(self, user_input, model_response):
        self.history.append({"user": user_input, "model": model_response})

    def get_history(self):
        return self.history

    def update_thread(self, thread_ts, user_input, model_response):
        if thread_ts not in self.threads:
            self.threads[thread_ts] = []
        self.threads[thread_ts].append({"user": user_input, "model": model_response})

    def get_thread_history(self, thread_ts):
        return self.threads.get(thread_ts, [])

    def is_processed(self, event_id):
        if event_id in self.processed_events:
            return True
        self.processed_events.add(event_id)
        return False

def retrieve_relevant_documents(query, history):
    context_query = " ".join([item["user"] for item in history] + [query])
    docs = db.similarity_search_with_score(query=context_query, k=20)
    return docs

def generate_response(query, documents, history, model, parser, code_context=""):
    combined_context = "\n".join(doc.page_content for doc, score in documents)
    context_with_history = "\n".join(
        [f"User: {item['user']}\nModel: {item['model']}" for item in history]
    ) + f"\n{code_context}\n{combined_context}"
    prompt_text = prompt.format(context=context_with_history, question=query)
    logging.info(f"Generated prompt: {prompt_text}")
    result = model.invoke(prompt_text,max_tokens = 100)
    try:
        parsed_result = parser.parse(result)
    except Exception as e:
        logging.error(f"Error parsing model response: {e}")
        parsed_result = "I'm sorry, I couldn't process that."
    logging.info(f"Model response: {parsed_result}")
    return parsed_result

def handle_follow_up_question(conversation, query, model, parser, thread_ts=None, code_context=""):
    if thread_ts:
        history = conversation.get_thread_history(thread_ts)
    else:
        history = conversation.get_history()
    documents = retrieve_relevant_documents(query, history)
    response = generate_response(query, documents, history, model, parser, code_context)
    if thread_ts:
        conversation.update_thread(thread_ts, query, response)
    else:
        conversation.update_history(query, response)
    return response

def extract_code_snippets(message):
    code_snippets = re.findall(r'```(.*?)```|`([^`]*)`', message, re.DOTALL)
    code_snippets = [code[0] or code[1] for code in code_snippets]
    return code_snippets

def process_image(image_url):
    headers = {
        "Authorization": f"Bearer {slack_client.token}"
    }
    response = requests.get(image_url, headers=headers)
    if response.status_code == 200:
        img = Image.open(BytesIO(response.content))
        description = "Image processing result"  # Replace with actual processing result
        return description
    else:
        logging.error(f"Failed to retrieve image. Status code: {response.status_code}")
        return "Failed to retrieve image"

def understand_code_snippet(code, model, parser):
    code_understanding_prompt = f"Please understand the following code snippet and provide a detailed explanation:\n\n{code}"
    result = model.invoke(code_understanding_prompt)
    try:
        parsed_result = parser.parse(result)
    except Exception as e:
        logging.error(f"Error parsing code understanding response: {e}")
        parsed_result = "I'm sorry, I couldn't process that code snippet."
    logging.info(f"Code understanding result: {parsed_result}")
    return parsed_result

conversation = Conversation()
model = Ollama(model="llama3")

@slack_event_adapter.on('message')
def handle_message(event_data):
    logging.info(f"Received event: {event_data}")
    message = event_data['event']
   
    if message.get('subtype') == 'bot_message' or message.get('subtype') == 'message_changed':
        logging.info("Ignoring bot message or message change event.")
        return

    event_id = event_data['event_id']
    if conversation.is_processed(event_id):
        logging.info(f"Event {event_id} already processed.")
        return

    channel_id = message['channel']
    user_message = message.get('text')
    thread_ts = message.get('thread_ts', None) or message.get('ts')
    files = message.get('files', [])

    if f"<@{bot_user_id}>" in user_message or files:
        logging.info(f"Bot mentioned in message: {user_message} or file(s) detected")
        user_message = user_message.replace(f"<@{bot_user_id}>", "").strip()
       
        response = ""
        code_context = ""
       
        code_snippets = extract_code_snippets(user_message)
        if code_snippets:
            for code in code_snippets:
                logging.info(f"Detected code snippet: {code}")
                code_context += f"Code snippet: {code}\n"
                code_understanding = understand_code_snippet(code, model, parser)
                code_context += f"Understanding: {code_understanding}\n"
       
        response = handle_follow_up_question(conversation, user_message, model, parser, thread_ts, code_context)
       
        for file in files:
            if file['mimetype'].startswith('image/'):
                image_url = file['url_private']
                logging.info(f"Processing image: {image_url}")
                image_description = process_image(image_url)
                response += f"\nImage description: {image_description}\n"

        try:
            slack_client.chat_postMessage(channel=channel_id, text=response, thread_ts=thread_ts)
            logging.info(f"Response sent to channel {channel_id}: {response}")
        except SlackApiError as e:
            logging.error(f"Error posting message: {e.response['error']}")

if __name__ == "__main__":
    logging.basicConfig(level=logging.INFO)
    app.run(port=3000)
