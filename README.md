## Start the Application

Run your Python file:
cd langgraphAgentChatBot
langgraph dev

## Notes: After Run the Application you can see the below options:
        Welcome to

╦  ┌─┐┌┐┌┌─┐╔═╗┬─┐┌─┐┌─┐┬ ┬
║  ├─┤││││ ┬║ ╦├┬┘├─┤├─┘├─┤
╩═╝┴ ┴┘└┘└─┘╚═╝┴└─┴ ┴┴  ┴ ┴

- 🚀 API: http://127.0.0.1:2024
- 🎨 Studio UI: https://smith.langchain.com/studio/?baseUrl=http://127.0.0.1:2024
- 📚 API Docs: http://127.0.0.1:2024/docs

This in-memory server is designed for development and testing.
For production use, please use LangSmith Deployment.


# LangGraph Tool Calling Chatbot

A simple chatbot/AI agent built using **LangGraph**, **LangChain**, **Groq**, and **LangSmith**.

The project demonstrates how to build a stateful LLM workflow using LangGraph, where the LLM can decide whether to call a custom tool. In this example, the chatbot has an `add` tool that can add two numbers.

LangSmith is also configured for tracing and monitoring the application's LLM execution.

---

## 🚀 Project Overview

This project demonstrates:

* Building an LLM workflow using **LangGraph**
* Using **Groq** as the LLM provider
* Using **LangChain** for model and tool integration
* Creating custom tools with `@tool`
* Binding tools to an LLM
* Using LangGraph's `ToolNode`
* Using conditional routing with `tools_condition`
* Maintaining conversation state with `add_messages`
* Compiling and executing a LangGraph state graph
* Monitoring and tracing LLM calls using **LangSmith**

### Architecture

The graph follows this workflow:

```text
                 ┌─────────────────────┐
                 │       START         │
                 └──────────┬──────────┘
                            │
                            ▼
                 ┌─────────────────────┐
                 │  Tool Calling LLM   │
                 │                     │
                 │  LLM decides       │
                 │  whether to call   │
                 │  a tool            │
                 └──────────┬──────────┘
                            │
                    ┌───────┴────────┐
                    │                │
                 Tool Call       No Tool Call
                    │                │
                    ▼                ▼
          ┌─────────────────┐      END
          │      Tools      │
          │                 │
          │ add(a, b)       │
          └────────┬────────┘
                   │
                   │ Tool Result
                   ▼
          ┌─────────────────────┐
          │  Tool Calling LLM   │
          └──────────┬──────────┘
                     │
                     ▼
              Final Response
```

---

## 🛠️ Technologies Used

| Technology    | Purpose                                 |
| ------------- | --------------------------------------- |
| Python        | Programming language                    |
| LangGraph     | Building the agent workflow/state graph |
| LangChain     | LLM and tool integration                |
| Groq          | LLM inference provider                  |
| LangSmith     | LLM tracing, debugging and monitoring   |
| python-dotenv | Loading environment variables           |

---

## 📁 Suggested Project Structure

A recommended project structure is:

```text
langgraph-chatbot/
│
├── agent.py
├── requirements.txt
├── .env
├── .gitignore
└── README.md
```

You can use a different filename for the Python application if required.

---

# 🔑 Environment Variables

Create a `.env` file in the root directory of the project.

```env
GROQ_API_KEY=your_groq_api_key
LANGCHAIN_API_KEY=your_langsmith_api_key
```

The application uses these variables to configure Groq and LangSmith.

> **Important:** Never commit your `.env` file or API keys to GitHub.

---

# 🔐 Getting API Keys

## Groq API Key

You need a Groq API key to access the Groq-hosted model.

Add the key to your `.env` file:

```env
GROQ_API_KEY=your_groq_api_key
```

## LangSmith API Key

LangSmith is used for tracing and monitoring the LangGraph/LangChain execution.

Add your LangSmith API key:

```env
LANGCHAIN_API_KEY=your_langsmith_api_key
```

The application then enables LangSmith tracing:

```python
os.environ["LANGSMITH_TRACING"] = "true"
```

and specifies the LangSmith project:

```python
os.environ["LANGSMITH_PROJECT"] = "LamgSmith-Test-Project"
```

---

# ⚙️ Installation

## 1. Clone the Repository

```bash
git clone <YOUR_REPOSITORY_URL>
```

Navigate into the project:

```bash
cd langgraph-chatbot
```

---

## 2. Create a Virtual Environment

### Windows

```bash
python -m venv venv
```

Activate it:

```bash
venv\Scripts\activate
```

### macOS / Linux

```bash
python3 -m venv venv
```

Activate it:

```bash
source venv/bin/activate
```

---

## 3. Install Dependencies

Create a `requirements.txt` file:

```text
langgraph
langchain
langchain-core
langchain-groq
langsmith
python-dotenv
typing-extensions
```

Install the dependencies:

```bash
pip install -r requirements.txt
```

---

# ▶️ Running the Project

## 1. Configure `.env`

Make sure your `.env` file contains:

```env
GROQ_API_KEY=your_groq_api_key
LANGCHAIN_API_KEY=your_langsmith_api_key
```

---

## 2. Start the Application

Run your Python file:
cd langgraphAgentChatBot
langgraph dev  


---

# 🧠 How the Code Works

## 1. Load Environment Variables

The project uses `python-dotenv`:

```python
from dotenv import load_dotenv

load_dotenv()
```

This loads the API keys from the `.env` file.

---

## 2. Configure Groq

The application creates a Groq chat model:

```python
from langchain_groq import ChatGroq

llm = ChatGroq(
    model="openai/gpt-oss-120b",
    temperature=0
)
```

The model is configured with:

```text
temperature = 0
```

which is useful when you want more deterministic responses.

---

## 3. Define the Graph State

The chatbot maintains its conversation using a LangGraph state:

```python
class State(TypedDict):
    messages: Annotated[list[BaseMessage], add_messages]
```

The `messages` field contains the conversation history.

`add_messages` tells LangGraph how new messages should be merged into the existing message state.

---

# 🔧 Creating a Custom Tool

The project defines an `add` tool:

```python
@tool
def add(a: float, b: float):
    """Add two number"""
    return a + b
```

This allows the LLM to call the function when it determines that addition is required.

For example:

```text
User:
What is 25 + 17?

LLM:
Calls add(25, 17)

Tool:
42

LLM:
The answer is 42.
```

---

# 🔗 Binding Tools to the LLM

The tool is registered with the LLM:

```python
llm_with_tool = llm.bind_tools([add])
```

This gives the model access to the tool definition.

The model can then decide whether a tool call is required.

---

# 🕸️ Building the LangGraph

The graph is created using:

```python
builder = StateGraph(State)
```

Two nodes are added:

```python
builder.add_node("tool_calling_llm", call_llm_model)
builder.add_node("tools", ToolNode(tools))
```

### `tool_calling_llm`

This node sends the conversation to the LLM:

```python
def call_llm_model(state: State):
    return {
        "messages": [
            llm_with_tool.invoke(state["messages"])
        ]
    }
```

### `tools`

The `ToolNode` executes tools requested by the LLM.

```python
ToolNode(tools)
```

---

# 🔀 Conditional Routing

The important part of the graph is:

```python
builder.add_conditional_edges(
    "tool_calling_llm",
    tools_condition
)
```

`tools_condition` checks the latest LLM response.

If the LLM requested a tool, execution is routed to the `tools` node.

If no tool is requested, the graph finishes.

The tool result is then sent back to the LLM:

```python
builder.add_edge("tools", "tool_calling_llm")
```

This creates the tool-calling loop:

```text
LLM
 ↓
Tool required?
 ↓
Yes
 ↓
Tool
 ↓
LLM
 ↓
Final answer
```

---

# 🏗️ Compiling the Graph

Once the nodes and edges are configured, the graph is compiled:

```python
graph = builder.compile()
```

The resulting graph is returned:

```python
return graph
```

Finally:

```python
tool_agent = make_tool_graph()
```

creates the compiled LangGraph agent.

---

# 📊 LangSmith Tracing

LangSmith tracing is enabled with:

```python
os.environ["LANGSMITH_TRACING"] = "true"
```

The project name is configured using:

```python
os.environ["LANGSMITH_PROJECT"] = "LamgSmith-Test-Project"
```

After running the application, LangGraph/LangChain execution traces can be inspected in LangSmith.

This is useful for debugging:

* LLM requests
* Tool calls
* Tool results
* Execution flow
* Latency
* Token usage
* Errors
* Agent behavior

---

# 🧪 Example

A typical interaction could look like:

```text
User:
Calculate 10 + 20.

LLM:
Tool call -> add(a=10, b=20)

Tool:
30

LLM:
10 + 20 = 30.
```

The important point is that the LLM is not directly performing the Python function. It requests the tool, LangGraph executes it, and the result is passed back to the LLM.

---

# 🧩 Adding More Tools

You can extend the chatbot by adding additional tools.

For example:

```python
@tool
def multiply(a: float, b: float):
    """Multiply two numbers."""
    return a * b
```

Then register both tools:

```python
tools = [add, multiply]
```

and bind them to the model:

```python
llm_with_tool = llm.bind_tools(tools)
```

The LLM can then choose the appropriate tool based on the user's request.

---

# ⚠️ Important Code Improvements

There are a few things in the current code that can be cleaned up.

### 1. Duplicate Imports

The code currently imports `StateGraph`, `START`, `END`, and `ToolNode` multiple times.

For example:

```python
from langgraph.graph import END, START
from langgraph.graph.state import StateGraph
```

and later:

```python
from langgraph.graph import StateGraph, START, END
from langgraph.prebuilt import ToolNode
```

These duplicate imports can be removed.

---

### 2. Unused `END`

Your current graph uses `tools_condition`, which handles routing to the end when there is no tool call.

Therefore, the explicit `END` import may not be necessary.

---

### 3. Unused `tool_node`

Inside `make_tool_graph()` you have:

```python
tool_node = ToolNode([add])
```

but this variable is never used.

You later create another `ToolNode`:

```python
builder.add_node("tools", ToolNode(tools))
```

You can remove the first one.

---

### 4. Typo in LangSmith Project Name

The current project name is:

```python
"LamgSmith-Test-Project"
```

If this is accidental, change it to:

```python
"LangSmith-Test-Project"
```

---

### 5. Environment Variable Naming

You currently have:

```python
os.environ["LANGSMITH_API_KEY"] = os.getenv("LANGCHAIN_API_KEY")
```

This may be intentional depending on the LangChain/LangSmith SDK version, but it is cleaner to use the environment variable expected by your installed SDK and keep the `.env` configuration consistent.

For example, if your setup expects `LANGSMITH_API_KEY`:

```env
LANGSMITH_API_KEY=your_langsmith_api_key
```

Then:

```python
load_dotenv()
```

can load it directly.

---

# 🧹 Cleaned-Up Version

A simplified version of your code could look like this:

```python
from typing import Annotated
from typing_extensions import TypedDict

from dotenv import load_dotenv

from langchain_core.messages import BaseMessage
from langchain_core.tools import tool
from langchain_groq import ChatGroq

from langgraph.graph import START, StateGraph
from langgraph.graph.message import add_messages
from langgraph.prebuilt import ToolNode, tools_condition


load_dotenv()


# Initialize LLM
llm = ChatGroq(
    model="openai/gpt-oss-120b",
    temperature=0
)


# Define Graph State
class State(TypedDict):
    messages: Annotated[list[BaseMessage], add_messages]


# Create the graph
def make_tool_graph():

    # Define tool
    @tool
    def add(a: float, b: float):
        """Add two numbers."""
        return a + b

    tools = [add]

    # Bind tools to LLM
    llm_with_tools = llm.bind_tools(tools)

    # LLM node
    def call_llm_model(state: State):
        response = llm_with_tools.invoke(state["messages"])
        return {"messages": [response]}

    # Create graph
    builder = StateGraph(State)

    builder.add_node("tool_calling_llm", call_llm_model)
    builder.add_node("tools", ToolNode(tools))

    # Graph edges
    builder.add_edge(START, "tool_calling_llm")

    builder.add_conditional_edges(
        "tool_calling_llm",
        tools_condition
    )

    builder.add_edge("tools", "tool_calling_llm")

    # Compile graph
    return builder.compile()


# Create agent
tool_agent = make_tool_graph()
```

---

# 📌 Future Improvements

This project can be extended with:

* Multiple tools
* Web search
* Calculator
* Database queries
* Retrieval-Augmented Generation (RAG)
* Vector databases
* Streaming responses
* Persistent conversation history
* Human-in-the-loop workflows
* LangGraph checkpoints
* LangSmith evaluation
* FastAPI backend
* Streamlit/Gradio UI
* Authentication
* Production deployment

---

# 📄 License

This project is available for learning and experimentation. Add your preferred license here if you plan to distribute the project.

---

## 👨‍💻 Author

**Your Name**

Built with:

* LangGraph
* LangChain
* Groq
* LangSmith
* Python
