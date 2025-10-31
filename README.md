# What is LangGraph

LangGraph is a Python/JavaScript framework used to build Agentic AI systems (AI agents that think, act, and collaborate).

It helps you build:
- ✅ Multi-agent workflows
- ✅ State-based AI applications
- ✅ Chatbots that remember context
- ✅ Tools + LLM reasoning loops
- ✅ Reliable workflows (not random like plain LLMs)

Think of LangGraph as a flowchart for AI, where each node is an AI step/agent, and edges define how data flows.

## Key Features of LangGraph + Simple Code for Each
### 1. Graph-Based Workflow (Main Feature)

LangGraph lets you create a workflow like:

LLM → Tool → LLM → Output

### Example: A simple graph
```
from langgraph.graph import StateGraph
from langchain_openai import ChatOpenAI

model = ChatOpenAI(model="gpt-4o-mini")

# State = structure of data moving through graph
class State(dict):
    pass

# Node: simple LLM
def ai_step(state: State):
    response = model.invoke(state["question"])
    state["answer"] = response.content
    return state

# Build graph
graph = StateGraph(State)
graph.add_node("ai_step", ai_step)
graph.set_entry_point("ai_step")

app = graph.compile()

result = app.invoke({"question": "What is LangGraph?"})
print(result["answer"])
```

✅ You created a single-step graph agent.

## 2. Memory (State Handling)

LangGraph is stateful, meaning it stores memory across steps.

✅ Example: Add memory to track conversation
```
def chat_step(state: State):
    history = state.get("history", [])
    question = state["question"]

    prompt = "History: " + str(history) + "\nUser: " + question
    answer = model.invoke(prompt).content

    history.append({"user": question, "assistant": answer})
    state["history"] = history
    state["answer"] = answer
    return state
```

✅ This makes your agent remember previous messages.

## 3. Tools Integration (External actions)

LangGraph supports tool calling like:
- ✅ Search
- ✅ Database
- ✅ Calculator
- ✅ API calls

✅ Example: Simple tool — Calculator

```
def math_tool(state: State):
    number = state["number"]
    state["result"] = number * 10
    return state


Add to graph:

graph.add_node("math_tool", math_tool)
graph.set_entry_point("math_tool")
```

✅ Tools let the agent act, not only “think”.

## 4. Branching / Routing (Conditional Logic)

Based on input, the graph can choose different paths.

✅ Example: Route based on type of question
```
def router(state: State):
    q = state["question"]
    if "math" in q:
        return "math_node"
    return "chat_node"


Add router:

graph.add_conditional_edges("router", router)

```
✅ Example:
“do 5+5 math” → goes to math node
“explain python” → goes to chat node

## 5. Multiple Agents (Multi-Agent System)

LangGraph is famous for multi-agent collaboration.

✅ Example: 2 agents → Writer + Reviewer
```
def writer(state: State):
    text = model.invoke("Write: " + state["topic"]).content
    state["draft"] = text
    return state

def reviewer(state: State):
    review = model.invoke("Review this: " + state["draft"]).content
    state["final"] = review
    return state

graph.add_node("writer", writer)
graph.add_node("reviewer", reviewer)
graph.set_entry_point("writer")
graph.add_edge("writer", "reviewer")

```
✅ Output:
Writer → Reviewer → Final improved text.

## 6. Human-in-the-loop (Approval Step)

LangGraph supports pauses, where you can ask a human to verify.

✅ Example: Pause for approval
```
from langgraph.prebuilt import ToolNode

def approval(state: State):
    return {"pause": True}  # Human must approve

```
## ✅ Useful for:

- Reviewing code

- Approving decisions

- Safety checks

## 7. Checkpoints (Reliable execution)

LangGraph saves checkpoints so agents can resume after failure.

✅ Basic checkpoint example:
```
app = graph.compile(checkpointer="memory")
result = app.invoke(input_data)
```

✅ Prevents data loss if the agent is interrupted.

## 8. Async Support (Fast parallel steps)

LangGraph supports async tasks.

✅ Example:
```
async def ai_step_async(state):
    response = await model.ainvoke(state["question"])
    state["answer"] = response.content
    return state
```

✅ Used for fast workflows like web scraping + LLM.



