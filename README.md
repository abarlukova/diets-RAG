# Diets-RAG

## Problem Description

In today’s world, an increasing number of people are seeking personalized diets to improve their health and achieve specific goals, such as weight loss, better metabolic function, or disease management. However, with so many different diets available—ranging from Keto to Vegan, Mediterranean, Paleo, and more—it can be difficult for individuals to choose a diet that best suits their lifestyle, health needs, and preferences. This often leads to confusion, incorrect dietary choices, or even the abandonment of dieting altogether.

To solve this problem, **Diets-RAG (Retriever-Augmented Generation)** aims to provide users with a system that can answer questions related to different diets and help guide them in choosing the most suitable one. The system is designed to serve as a personalized diet assistant by utilizing a Q&A database, initially focused on the Keto diet, with plans to expand to other diets in the future.

## Dataset and Approach

The current dataset is sourced from frequently asked questions about the Keto diet from trusted online resources. However, as the project evolves, the dataset will also incorporate information from the **most recent research papers** and studies on various diets. This will ensure that the information provided is up-to-date and based on the latest scientific findings, offering users reliable and evidence-based guidance.

Users can input questions, and the system will retrieve the most relevant answers, giving them clear, concise, and accurate information. As the database expands to include other diets, the goal is to provide a **comprehensive, user-friendly tool** to help individuals make informed dietary choices based on the recent research.

## Current Dataset Sources

- [20 Most Asked Questions About Keto Diet](https://www.linkedin.com/pulse/20-most-asked-questions-keto-diet-certified-operation-s-management-/)
- [Everyday Health - Burning Questions About Keto Diet Answered](https://www.everydayhealth.com/ketogenic-diet/diet/burning-questions-about-keto-diet-answered/)
- [Ruled.me - Ketogenic Diet FAQ](https://www.ruled.me/ketogenic-diet-faq/#standard_keto)
- [Diabetes.co.uk - Ketogenic Diet FAQs](https://www.diabetes.co.uk/keto/ketogenic-diet-faqs.html)


## Technologies

OpenAI as LLM

PostgreSQL to save questions, answers and user feedback

Elasticsearch to retrieve data

## Preparation

Add your OpenAI API key to the .env file:

OPENAI_API_KEY='your-openai-api-key-here'

To run and initialize postgres, elasticsearch and streamlit run the following commands:

docker-compose up

export POSTGRES_HOST="127.0.0.1"

python prep.py

## Experiments

check rag_test.ipynb


### Retrieval evaluation

knn = 5

Vector search result on "query_vector" = "question_answer_vector" : {'hit_rate': 0.8126582278481013, 'mrr': 0.649282700421941}

Vector search result on "query_vector" = "answer_vector" : {'hit_rate': 0.7873417721518987, 'mrr': 0.609662447257384}

Vector search result on "query_vector" = "question_vector" : {'hit_rate': 0.6379746835443038, 'mrr': 0.4820675105485234}

minsearch result on question : {'hit_rate': 0.7569620253164557, 'mrr': 0.5881434599156119}

Cosine similarity statistics for original answer and llm answer estimated on ground truth data

count    395.000000

mean       0.776664

std        0.139467

min        0.220716

25%        0.705947

50%        0.798396

75%        0.879593

max        1.000000

### RAG flow evaluation

LLM as judge using prompt1_template =
You are an expert evaluator for a Retrieval-Augmented Generation (RAG) system.
Your task is to analyze the relevance of the generated answer compared to the original answer provided.
Based on the relevance and similarity of the generated answer to the original answer, you will classify
it as "NON_RELEVANT", "PARTLY_RELEVANT", or "RELEVANT".

Here is the data for evaluation:

Original Answer: {answer_orig}
Generated Question: {question}
Generated Answer: {answer_llm}

Please analyze the content and context of the generated answer in relation to the original
answer and provide your evaluation in parsable JSON without using code blocks:

{{
  "Relevance": "NON_RELEVANT" | "PARTLY_RELEVANT" | "RELEVANT",
  "Explanation": "[Provide a brief explanation for your evaluation]"
}}
""".strip()

Relevance
RELEVANT           299
PARTLY_RELEVANT     96



for prompt2_template =
"""
You are an expert evaluator for a Retrieval-Augmented Generation (RAG) system.
Your task is to analyze the relevance of the generated answer to the given question.
Based on the relevance of the generated answer, you will classify it
as "NON_RELEVANT", "PARTLY_RELEVANT", or "RELEVANT".

Here is the data for evaluation:

Question: {question}
Generated Answer: {answer_llm}

Please analyze the content and context of the generated answer in relation to the question
and provide your evaluation in parsable JSON without using code blocks:

{{
  "Relevance": "NON_RELEVANT" | "PARTLY_RELEVANT" | "RELEVANT",
  "Explanation": "[Provide a brief explanation for your evaluation]"
}}
""".strip()

Relevance
RELEVANT           382
PARTLY_RELEVANT     13


## Monitoring

The online evaluation is accessible through a Streamlit app. When running locally, you can access the app at: 127.0.0.1::8501

The evaluation results are saved in a PostgreSQL database.


