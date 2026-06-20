# LangChain Learning & AI Model Integration

A comprehensive, hands-on learning repository exploring the **LangChain** ecosystem, LLM model integrations, advanced message handling, tool calling, and structured outputs. This workspace includes a step-by-step series of Jupyter notebooks designed to transition you from core concepts to building autonomous agents.

---

## 📚 Table of Contents
1. [Overview](#-overview)
2. [Repository Structure](#-repository-structure)
3. [Dependencies & Tooling](#-dependencies--tooling)
4. [Getting Started](#-getting-started)
   - [Option A: Setup via UV (Recommended)](#option-a-setup-via-uv-recommended)
   - [Option B: Setup via standard pip & venv](#option-b-setup-via-standard-pip--venv)
5. [Notebook Summaries & Key Concepts](#-notebook-summaries--key-concepts)
   - [1. Agent Introduction (`1-intro.ipynb`)](#1-agent-introduction-1-introipynb)
   - [2. Multi-Provider Integration (`2-model_integration.ipynb`)](#2-multi-provider-integration-2-model_integrationipynb)
   - [3. Custom Tools (`3-tools.ipynb`)](#3-custom-tools-3-toolsipynb)
   - [4. Message Types & History (`4-messages.ipynb`)](#4-message-types--history-4-messagesipynb)
   - [5. Structured Output (`5-structuredoutput.ipynb`)](#5-structured-output-5-structuredoutputipynb)
6. [Best Practices](#-best-practices)
7. [Contributing & License](#-contributing--license)

---

## 📚 Overview

This repository hosts a curated learning path that demonstrates practical workflows using **LangChain** across multiple state-of-the-art LLM providers, including **Google Gemini**, **OpenAI GPT**, and **Groq (Qwen)**. 

Through interactive notebooks, you will learn to build agents, manage conversational state, invoke external tools, query API endpoints, and parse output into structured schemas.

---

## 🗂️ Repository Structure

```
├── .env                  # API credentials (Git ignored)
├── pyproject.toml        # Project metadata and python dependencies list
├── uv.lock               # Lockfile for reproducible package installation
├── main.py               # Basic sanity-check Python script
└── krish_naik/           # Main folder containing tutorial Jupyter Notebooks
    ├── 1-intro.ipynb             # LangChain & Agent fundamentals
    ├── 2-model_integration.ipynb  # Multi-LLM provider setups, streaming & batching
    ├── 3-tools.ipynb             # Creating, binding, and executing custom tools
    ├── 4-messages.ipynb          # Message schemas, roles, state, and metadata
    └── 5-structuredoutput.ipynb  # Enforcing Pydantic schemas & TypedDict outputs
```

---

## 🛠️ Dependencies & Tooling

The environment configuration is specified in [pyproject.toml](file:///d:/Langchain/project1/pyproject.toml) and target Python `>=3.13`. Key library dependencies include:

| Package | Purpose |
| :--- | :--- |
| `langchain` | Unified framework for LLM workflow composition |
| `langchain-community` | Community-contributed tool integrations and utilities |
| `langchain-google-genai` | Integration with Google Gemini models |
| `langchain-google-vertexai` | Enterprise access to Vertex AI models |
| `langchain-openai` | Integration with OpenAI GPT models |
| `langchain-groq` | Integration with high-throughput Groq models |
| `ipykernel` | Enables Python execution inside Jupyter environments |
| `python-dotenv` | Loads variables from `.env` into process environments |

---

## 🚀 Getting Started

### 1. Clone the repository
```bash
git clone https://github.com/KamranRizvi265/krish_naik.git
cd krish_naik
```

### 2. Configure Environment Variables
Create a file named `.env` in the root of the project:
```ini
OPENAI_API_KEY=your_openai_key_here
GEMINI_API_KEY=your_gemini_key_here
GROQ_API_KEY=your_groq_key_here
```

---

### Option A: Setup via UV (Recommended)
`uv` is an extremely fast Python package and environment manager written in Rust.

1. **Install dependencies and create virtual environment**:
   ```bash
   uv sync
   ```
2. **Launch Jupyter Lab**:
   ```bash
   uv run jupyter lab
   ```

---

### Option B: Setup via standard pip & venv
If you prefer standard Python tooling:

1. **Create and activate a virtual environment**:
   - **Windows (PowerShell)**:
     ```powershell
     python -m venv .venv
     .\.venv\Scripts\Activate.ps1
     ```
   - **macOS / Linux**:
     ```bash
     python3 -m venv .venv
     source .venv/bin/activate
     ```

2. **Install requirements**:
   ```bash
   pip install --upgrade pip
   pip install jupyterlab
   pip install -e .
   ```
   *(This installs the package list defined in `pyproject.toml` in editable mode).*

3. **Launch Jupyter Lab**:
   ```bash
   jupyter lab
   ```

---

## 📓 Notebook Summaries & Key Concepts

### 1. Agent Introduction ([1-intro.ipynb](file:///d:/Langchain/project1/krish_naik/1-intro.ipynb))
Learn how to define helper functions as tools and instantiate an autonomous decision-making engine.
* **Core API**: `langchain.agents.create_agent`
* **Workflow**: Initializes compiled state graphs, binds a tool function, and runs execution loops.
* **Code Highlight**:
  ```python
  from langchain.agents import create_agent
  
  def get_weather(city: str) -> str:
      return f"The weather in {city} is sunny."

  agent = create_agent(
      model="google_genai:gemini-2.5-flash-lite",
      tools=[get_weather],
      system_prompt="You are a helpful assistant."
  )
  ```

---

### 2. Multi-Provider Integration ([2-model_integration.ipynb](file:///d:/Langchain/project1/krish_naik/2-model_integration.ipynb))
Explore standard interfaces to communicate with different providers and optimization strategies like streaming and batching.
* **Concepts**:
  - `init_chat_model` for provider-agnostic instantiation.
  - Streaming tokens iteratively in real-time.
  - Batching multiple independent inputs to reduce latency.
* **Code Highlight**:
  ```python
  # Initializing standard chat model
  from langchain.chat_models import init_chat_model
  model = init_chat_model("groq:qwen/qwen3-32b")
  
  # Iterating over streamed tokens
  for chunk in model.stream("Why do parrots have colorful feathers?"):
      print(chunk.text, end="|", flush=True)
      
  # Executing batched requests
  responses = model.batch(["What is Cloud Computing?", "How do airplanes fly?"])
  ```

---

### 3. Custom Tools ([3-tools.ipynb](file:///d:/Langchain/project1/krish_naik/3-tools.ipynb))
Deep dive into defining tool schemas and executing tools manually inside custom agent orchestration loops.
* **Concepts**:
  - The `@tool` decorator to auto-generate JSON schemas from docstrings and signatures.
  - Using `model.bind_tools()` to inform the LLM of available functions.
  - Constructing tool execution loops to parse `tool_calls` and return outputs using `ToolMessage`.
* **Code Highlight**:
  ```python
  from langchain.tools import tool

  @tool
  def get_weather(location: str) -> str:
      """Get weather of a location."""
      return f"It's sunny in {location}."

  model_with_tool = model.bind_tools([get_weather])
  response = model_with_tool.invoke("How is the weather in Boston?")
  
  # Step-by-step Tool loop execution
  for tool_call in response.tool_calls:
      result = get_weather.invoke(tool_call)
      # return result message back to the LLM...
  ```

---

### 4. Message Types & History ([4-messages.ipynb](file:///d:/Langchain/project1/krish_naik/4-messages.ipynb))
Understand context-carrying objects in LangChain and how conversational memory works.
* **Key Message Types**:
  - `SystemMessage`: Instruction set to guide model behavior and tone.
  - `HumanMessage`: Represents user input.
  - `AIMessage`: AI response (includes text content, tool calls, and model metadata).
  - `ToolMessage`: Relays function results back to the AI context.
* **Metadata Analysis**: Explains parsing metadata like token usage via `response.usage_metadata`.
* **Code Highlight**:
  ```python
  from langchain.messages import SystemMessage, HumanMessage, AIMessage

  messages = [
      SystemMessage("You are a poetry expert."),
      HumanMessage("Write a poem on Artificial Intelligence.")
  ]
  response = model.invoke(messages)
  print(response.usage_metadata) # {'input_tokens': 53, 'output_tokens': 241, 'total_tokens': 294}
  ```

---

### 5. Structured Output ([5-structuredoutput.ipynb](file:///d:/Langchain/project1/krish_naik/5-structuredoutput.ipynb))
Learn how to constrain responses to guaranteed data structures, critical for downstream APIs or database storage.
* **Methods**:
  - **Pydantic**: Rich validation, fields descriptions, nested types.
  - **TypedDict**: Native typing structure for lighter validation overhead.
* **Code Highlight**:
  ```python
  from pydantic import BaseModel, Field

  class Movie(BaseModel):
      name: str = Field(description="Name of the movie")
      year: str = Field(description="Release year")
      director: str = Field(description="Director name")
      rating: float = Field(description="IMDB rating")

  model_structured = model.with_structured_output(Movie)
  inception = model_structured.invoke("Provide description of the movie Inception")
  print(inception.director) # Christopher Nolan
  ```

---

## 🔒 Best Practices

1. **Credential Safety**: Always store API keys in the `.env` file. Never commit `.env` or hardcoded tokens to your repository.
2. **Dependency Tracking**: When adding new libraries, register them in `pyproject.toml` and lock their versions using `uv lock` (or regenerate `requirements.txt` via `pip freeze`).
3. **Multi-Model Fallbacks**: Use `init_chat_model` where possible to keep the codebase flexible and switch LLM backends easily.

---

## 🤝 Contributing & License

Feel free to open an issue or submit a pull request with additional tutorial notebooks or workflow improvements.

This project is open-source. For details on sharing and reuse, please consider adding a standard license file (e.g. MIT) to the root directory.