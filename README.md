# 🧮 AI Math Assistant with LangChain and LangGraph Tool Calling

![Python](https://img.shields.io/badge/Python-3.10+-blue) ![LangChain](https://img.shields.io/badge/LangChain-1.x-green) ![LangGraph](https://img.shields.io/badge/LangGraph-Agent%20Runtime-purple)
![LLM](https://img.shields.io/badge/LLM-IBM%20Granite%20%7C%20OpenAI-orange)
An AI Agent that understands natural-language math questions, picks the right tool, and returns exact answers instead of guessing arithmetic like a plain LLM
## 📌 Overview

LLMs are unreliable at precise calculation. This project gives an LLM a **toolkit of
Python functions** so it can delegate math to code. Example: *"Add 10, 20, two and 30"* → the agent converts "two" to 2 and answers **62**.

## ✨ Features

- Powered by LangGraph: `create_agent` runs a LangGraph state graph under the hood
- Custom tools built two ways: the `Tool` class and the `@tool` decorator
- Math toolkit: add, subtract, multiply, divide, and power
- Multi-input tools with typed schemas (e.g. an `absolute=True` option)
- Edge-case handling such as division by zero and empty input
- Wikipedia tool so the agent can fetch facts, then calculate with them
- Automated tests plus support for IBM Granite 4 (watsonx.ai) and OpenAI models

## 🏗️ How It Works

```
User question → LLM decides → Tool runs → Result back to LLM → Final answer
```

## 🧰 Tools

| Tool | Purpose | Example |
|------|---------|---------|
| `add_numbers` | Sum all numbers in the input | `10, 20, 30` → `60` |
| `new_subtract_numbers` | Subtract each number from the first | `100, 20, 10` → `70` |
| `multiply_numbers` | Product of all numbers | `2, 3, 4` → `24` |
| `divide_numbers` | Sequential division, zero-safe | `100, 5, 2` → `10` |
| `power_tool` | Exponents (`x^y`) | `5 to the power of 2` → `25` |
| `search_wikipedia` | Factual lookups | `population of Canada` |

## 🚀 Getting Started

**Prerequisites:** Python 3.10+ and an IBM watsonx.ai and/or OpenAI API key.
```bash
git clone https://github.com/<your-username>/ai-math-assistant.git
cd ai-math-assistant
pip install "langchain>=1.0" langchain-ibm langchain-openai langchain-community wikipedia
```

Configure your LLM (never commit API keys, use environment variables):
```python
from langchain_ibm import ChatWatsonx

llm = ChatWatsonx(
    model_id="ibm/granite-4-h-small",
    url="https://us-south.ml.cloud.ibm.com",
    project_id="YOUR_PROJECT_ID",
    api_key="YOUR_API_KEY",
)
```

## 💡 Usage

```python
from langchain.agents import create_agent
from langchain_core.tools import tool
@tool
def multiply_numbers(inputs: str) -> dict:
    """Extracts numbers from a string and calculates their product."""
    ...
agent = create_agent(model=llm, tools=[multiply_numbers],
                     system_prompt="You are a helpful mathematical assistant.")
response = agent.invoke({"messages": [("human", "Multiply 2, 3, and 4.")]})
print(response["messages"][-1].content)  # 24
```

## 🧪 Testing

Each test pairs a query with an expected result; the runner compares it with the
`ToolMessage` in the agent's history (subtraction, multiplication, division, negatives).

## 📚 What I Learned

- **Tool design matters:** clear names, docstrings, and typed inputs guide the LLM
- **`@tool` beats `Tool`:** it builds a structured schema from the function signature
- **Agent and tool must agree:** unexpected tool behavior causes wrong answers or loops,
  so test tools directly before wiring them into an agent
- **Avoid duplicate tools:** similar tools confuse the model's tool selection
- **Combine tools:** Wikipedia plus math tools enables multi-step reasoning

## 🔭 Future Improvements

- Stricter input validation and more edge-case tests (empty input, "hundred", decimals)
- Chained operations such as "multiply 10 by 2, then add 5"
- Typed list inputs (`List[float]`) instead of regex parsing, plus a Gradio chat UI

## 🙏 Acknowledgments & License

Based on the IBM Skills Network lab *"Build an AI Math Assistant with LangChain Tool
Calling"*, extended with my own notes and documentation. Released under the MIT License.
