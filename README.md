# 🚀 Getting Started with GenAI AgentOS

Welcome to the GenAI AgentOS framework! This guide walks you through everything you need to start building, registering, and running your own AI agent with the GenAI protocol.

## 📦 Prerequisites
- Docker
- Python 3.10+
- Git
- [uv](https://github.com/astral-sh/uv) (optional but recommended)
- ngrok (optional, for deployment)

## 1. 🔀 Clone the Repository
```bash
git clone https://github.com/genai-works-org/genai-agentos.git
cd genai-agentos
```

## 2. ⚙️ Environment Variables
```bash
cp .env-example .env
```
This command creates the `.env` file required to run AgentOS. You do not need to uncomment any variables to get started, but you can customize them as needed.

## 3. 🐋 Run the Platform Locally
Make sure Docker is running and start the containers:
```bash
docker-compose up --build
# or
make up
```
Visit [http://localhost:3000](http://localhost:3000) to see the login page.

## 4. 🤖 Create and Register Your Agent
From the `chi` directory you can register a new agent:
```bash
python cli.py signup -u <username>
python cli.py login -u <username> -p <password>
python cli.py register_agent --name <agent_name> --description "<agent description>"
```
This creates an `agents` directory inside `chi` containing your agent's Python file and a `uv.lock`.

To add logic:
```bash
cd agents/<agent_name>
```
Open the generated file and implement your agent logic. Then run it:
```bash
uv run <agent_file>.py
# or
python <agent_file>.py
```
Your agent appears in the UI under the Agents tab.

### Example Agents
Example agents live in `genai_agents_example`. To run one:
1. Generate a JWT token in the UI.
2. Paste the token in the agent's `jwt_token` field.
3. Run:
```bash
cd genai_agents_example/<agent>
uv run main.py
```

### Create a Model
1. Open the Settings tab in the UI.
2. Enter your OpenAI API key and create a model named `gpt-4o`.
3. Now go to the Chat tab, select the model, and prompt your agent.

## Bonus: Run in Google Colab
You can prototype remotely with Colab. Expose your local server:
```bash
ngrok http 8080
```
Install packages in Colab:
```python
!pip install loguru genai-protocol
```
Use the following template (fill in your token and URLs):
```python
from datetime import datetime
from genai_session.session import GenAISession
from loguru import logger
import nest_asyncio

nest_asyncio.apply()
session = GenAISession(jwt_token="", ws_url="<ngrok_url>")

@session.bind(name="get_current_date", description="Return current date")
async def get_current_date(agent_context):
    return datetime.now().strftime("%Y-%m-%d")

async def main():
    await session.process_events()

await main()
```
Запустите, и агент появится в UI.

Enjoy hacking! 🚀
