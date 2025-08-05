# Langchain 101
Langchain notes and projects

## Langchain
Langchain is a Python framework for building LLM-powered applications with components like
1. Chains - sequences of calls to LLMs/functions
2. Agents - decision making systems that use tools
3. Memory - short and long term context
4. Toolkits - APIs/functions/tools the agent can use
5. Retrievers/Vector Stores - for RAG (Retrieval Augmented Generation)

### Chat Models 
Langchains way of connecting us to LLMs like ChatGPT, Claude, Gemini. <br>
Store the API_key in the env file <br>
The model creation with <br>
```
  model = ChatOpenAI(model="gpt-4o")
```
This automatically takes the API_KEY from the env file internally. (Defined in the code) <br>
model.invoke() -> Invoke function is the way of langchain to use these models. <br>

### Types of Messgaes
Three types of messages <br>
System Message - Broad context for the conversation <br>
Human Message - Humans message to the AI <br>
AI Message - AI responding back <br>

System message must come first. After that it can go human, ai, human, ai...

### Saving messages as context
Save all these and append to a list as conversation goes. The Human message and the result.content of the AI message. This will serve as context by which the model will remember and respond according to the changes and commands given throughout the conversation. <br>
We can also save the chat history to database like firebase and then retrieve the chat history the next time the session is used, so as to have the same context and continue with the conversation. 

### Prompt Templates
Prompt Templates is a nice way to create structured prompts using prompt engineering and make it easy for users to add input and complete the prompt. 

### Chains
Sequences of calls to LLMs/functions. <br>
Can chain the output of one prompt to be an input of the next prompt and so on to automate the prompts

prompt -> model -> Result <br>
The above chain can be represented using LCEL, Langchain Expression Language, as below. <br>
```
chain = prompt | model
result = chain.invoke({"key":"value"})
```

Extended <br>
```
O --> O --> O --> O
```

Parallel <br>
```
          O --> O
        /
O --> O
        \
          O --> O
```

Branching <br>
```
         --> O --> O
        /
O --> O ----- O --> O
        \
         --> O --> O
```

Example of a basic chain <br>
```
chain = prompt_template | model | StrOutputParser()
result = chain.invoke({"topic": "xyz", "count": 3})
print(result)
```

### Runnables 
Runnable is a task.<br>
Runnable lambda is a task thats a lambda function. Lambda function has input to the function and the action to take. <br>
Runnable Sequence - putting all the tasks together into a sequence / chain. <br>
The runnable sequence takes arguments in the format of first, middle and last. Where middle is a list and the other two are single runnables. <br>

LangChain expects each step in a chain to have a .invoke(input) method and potentially other features (like batch, stream, etc.). A normal function or lambda doesn’t have these. <br>
RunnableLambda is a LangChain utility that wraps any normal Python function or lambda and gives it the .invoke() method (and other chain-compatible behaviors). <br>

```
uppercase_output = RunnableLambda(lambda x: x.upper())
count_words = RunnableLambda(lambda x: f"Word count: {len(x.split())}\n{x}")
chain = prompt_template | model | StrOutputParser() | uppercase_output | count_words
```

This ensures they have .invoke() and can be used with | operators in LCEL. <br>
It makes your custom logic composable within LangChain pipelines.

### RAG 
Retrieval Augmented Generation <br>
Giving additional sources of information to LLMs. Websites, PDFs, Code, Video Transcripts. <br>

#### RAG Overview
PDF is the initial source of info. Consider about 10M tokens.<br>
Break those into chunks. With each chunk having 1k tokens. Chunks are just plain tokens.<br>
LLM Embedder converts test to Embedding.<br>
Embedding is the numerical representation of text. The embeddings of the text will have some relationship with each other numerically. Allows for easily searching items of similarity.<br>
All the Embeddings and the Text are saved to a Vector Store.<br>

#### RAG Q&A Flow
So when a question is asked, it is also embedded.<br>
The retriever will check the embedding of the question and the vector store and selects certain chunks of relevant data. All these selected chunks are passed to the model along with the question. With that the model will have a relevant small amount of chunks to answer the question.<br>

Text Splitting can be done in such a way that there is some overlap characters between each chunk to maintain continuity for better performance.<br>

Text Embedding is done for example using OpenAIEmbeddings. Small would use a smaller representation. Large would use a more accurate, costly representation.

Retriever<br>
Similarity score threshold - to filter out. k = 3 -> gives the top 3 relevant documents
If too strict, results might not come. 
