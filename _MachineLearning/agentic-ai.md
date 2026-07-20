---
title: "Agentic AI: What Actually Makes a System an 'Agent'"
excerpt: "Planning, tool use, memory, and autonomous multi-step execution are what separate an agentic AI system from a plain chatbot — here's how it actually works."
header:
  image: /assets/images/machine_learning/t1.jpg
  teaser: /assets/images/machine_learning/machine-learning.jpg
sidebar:
  - title: "About the Author"
    image: https://vaibhavhariramani.github.io/blogs/images/bio-pic-2.jpg
    image_alt: "logo"
    text: "I am a Machine Learning enthausiast by profession and a Blogger by passion. I have been working on machine learning projects for more than 2 years."
  - title: "Other Blogs"
    text: " [Here](https://vaibhavhariramani.github.io/blogs/) you will find articles on “Machine Learning, Statistics, Deep Learning, NLP and Artificial Intelligence”."
---

"Agentic AI" gets thrown around loosely, often to describe anything with a chat interface. But there's a real, useful distinction between a chatbot and an agent, and understanding it matters if you're deciding what to actually build.

## Chatbot vs. Agent

A plain chatbot is fundamentally **reactive**: user sends a message, model generates a response, conversation moves on. Even if it's wrapped in a nice UI or uses [RAG](/MachineLearning/vector-databases-and-rag/) to pull in context, it's still one request in, one response out.

An **agentic** system is different because it can:

1. **Plan** — break a high-level goal into a sequence of smaller steps.
2. **Use tools** — call external functions, APIs, code execution, search, or databases to take actions and gather information the underlying model doesn't already have.
3. **Maintain memory** — carry state across multiple steps (and sometimes across sessions) so later decisions are informed by earlier ones.
4. **Execute autonomously across multiple steps** — keep going, re-planning as needed, until the goal is met or it decides to stop, rather than requiring a human to prompt each individual step.

A chatbot answers a question. An agent is handed a *goal* and figures out the steps itself — deciding what to do next based on what happened after its previous action.

## The ReAct Pattern

The most common underlying pattern for agentic behavior is **ReAct** (Reason + Act). At each step, the model:

1. **Thinks** — reasons in text about what it knows and what it should do next.
2. **Acts** — calls a tool (a web search, a calculator, an API, a code interpreter).
3. **Observes** — receives the tool's output.
4. Loops back to step 1 with the new information, until it decides the goal is achieved.

A simplified ReAct-style loop looks like this in pseudocode:

```python
def run_agent(goal, tools, llm, max_steps=10):
    history = [f"Goal: {goal}"]

    for step in range(max_steps):
        # 1. Reason: ask the model what to do next, given history so far
        response = llm.generate(history, available_tools=tools)

        if response.is_final_answer:
            return response.answer

        # 2. Act: call the tool the model chose
        tool_name, tool_args = response.tool_call
        result = tools[tool_name](**tool_args)

        # 3. Observe: feed the result back into history
        history.append(f"Action: {tool_name}({tool_args})")
        history.append(f"Observation: {result}")

    return "Stopped: max steps reached without a final answer"
```

Modern LLM APIs formalize the "Act" step as **tool calling** (also called function calling): instead of parsing free text to guess what tool the model wants to use, the model returns a structured call (tool name + arguments), which your code executes and feeds back as an observation. This is what turns a raw chat completion into something that can reliably drive external actions.

## Frameworks and Patterns

A handful of frameworks have become the standard way to build agentic systems rather than hand-rolling the ReAct loop every time:

- **LangGraph** — models an agent's workflow as an explicit graph of nodes (steps) and edges (transitions), with state persisted between steps. Its structured, graph-based approach gives strong control over branching, retries, and rollback points, which is a big part of why it has become a default choice for production agent workloads.
- **AutoGen / CrewAI** — support **multi-agent** patterns, where several LLM agents with different roles (e.g., a "planner," a "researcher," a "critic") collaborate on a task, passing work between each other rather than a single agent doing everything.
- **The Model Context Protocol (MCP)** — an open standard for connecting agents to external tools and data sources in a consistent way, so a tool built once can be reused across different agent frameworks instead of writing custom integration glue for each one.
- **AutoGPT** — an earlier, more free-form take on autonomous looping agents; useful as a proof of concept that LLMs can self-direct multi-step tasks, but generally considered less structured and harder to control in production than graph-based frameworks.

Across all of these, the common building blocks are the same: a planning/reasoning step, a defined set of callable tools, a state or memory mechanism, and a loop that continues until a stopping condition is met.

## Realistic Use Cases

- **Coding agents** — given a bug report or feature request, an agent reads the codebase, writes a patch, runs tests, and iterates on failures before presenting a diff.
- **Research and data-gathering agents** — given a broad question, an agent plans sub-questions, searches multiple sources, and synthesizes a report, rather than answering from memory alone.
- **Operations/IT agents** — triaging alerts, querying monitoring systems, and taking predefined remediation actions (restarting a service, rolling back a deploy) with human approval gates.
- **Customer support agents** — combining [RAG](/MachineLearning/vector-databases-and-rag/) over a knowledge base with tool calls into ticketing or billing systems to actually resolve a request, not just answer questions about it.

## Current Limitations

- **Compounding errors.** Every step in a multi-step plan has some error rate; over 10-20 steps, small per-step error rates compound into a meaningfully high chance the whole task goes off the rails.
- **Weak long-horizon planning.** Agents are noticeably better at short, well-scoped tasks than at long, ambiguous ones requiring dozens of correct sequential decisions.
- **Tool and environment brittleness.** Agents depend on the tools they're given behaving predictably; flaky APIs, ambiguous tool descriptions, or unexpected outputs can derail a plan the model can't recover from.
- **Cost and latency.** Multiple reasoning + tool-call round trips are slower and more expensive than a single LLM call, which matters at production scale.
- **Safety and control.** Autonomy is exactly what makes agents useful and what makes them risky — an agent empowered to take real actions (send emails, spend money, modify infrastructure) needs guardrails: scoped permissions, human approval steps for high-impact actions, and audit trails of every tool call made.

## Where Agentic AI Fits in a Real Workflow

Agentic AI is not a replacement for the fundamentals covered elsewhere in this collection — good data understanding, solid model evaluation, and MLOps discipline (see our [MLflow post](/MachineLearning/mlops-with-mlflow/)) still apply. What changes is the shape of the system: instead of a single model producing one prediction from one input, you're designing a control loop around a model — deciding what tools it can call, what state it remembers, and what boundaries constrain its autonomy. The engineering effort shifts from "get the model right" to "get the loop, the tools, and the guardrails right," which is why agent frameworks increasingly emphasize durable state, retries, and human-in-the-loop approval as first-class features rather than afterthoughts.

Sources:
- [Top 7 Agentic AI Frameworks in 2026: LangChain, CrewAI, and Beyond](https://www.alphamatch.ai/blog/top-agentic-ai-frameworks-2026)
- [10 AI Agent Frameworks You Should Know in 2026](https://medium.com/@atnoforgenai/10-ai-agent-frameworks-you-should-know-in-2026-langgraph-crewai-autogen-more-2e0be4055556)
- [The Ultimate Guide to AI Agent Frameworks: 2026 Edition](https://www.edstellar.com/blog/ai-agent-frameworks)

# Made with ❤️ by Vaibhav Hariramani
