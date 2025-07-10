# 🚀 Getting Started with GenAI AgentOS

Welcome to the GenAI AgentOS framework! This guide walks you through everything you need to start building, registering, and running your own AI agent with the GenAI protocol.

## 📦 Prerequisites
Before you begin, make sure the following tools are installed:

- [Docker](https://www.docker.com/)
- [Python 3.10+](https://www.python.org/)
- Git
- [uv](https://github.com/astral-sh/uv) — a fast Python package manager *(optional but recommended)*
- [ngrok](https://ngrok.com/) for exposing local ports *(optional for deployment)*

## 1️⃣ Clone the Repository
📂 Open your terminal and run:

```bash
git clone https://github.com/genai-works-org/genai-agentos.git
cd genai-agentos
```

## 2️⃣ Environment Variables
⚙️ Set up environment variables:

```bash
cp .env-example .env
```

This command will create the `.env` file which is **mandatory** for running AgentOS. Initially, the `.env` file contains **commented variables**.

💡 You do **not** need to uncomment any variables to run the platform or register your agents. However, if you want to use your own configuration, you can uncomment and update the values as needed.

## 3️⃣ Run the Platform Locally
🐳 Make sure Docker is running.

```bash
docker-compose up --build
```

You can also use:

```bash
make up
```

Once started, open your browser and go to [http://localhost:3000](http://localhost:3000). You should see the login form UI, which means AgentOS is ready to use!

## 4️⃣ Create and Register Your Agent
🧠 Open this source code in any code editor that supports Python. Then, to create and register an agent, navigate to the `cli` directory. You can also check the `README.md` inside the `cli` folder for a list of supported commands.

### 👤 Create a User
You can create a user either from the terminal or using the UI.

📌 To use terminal:

```bash
python cli.py signup -u [username]
```

You will be asked to enter a password.

### 🔐 Login

```bash
python cli.py login -u [username] -p [password]
```

You can also use these credentials to log in from the UI.

### 📝 Register an Agent

```bash
python cli.py register_agent --name <agent_name> --description "<agent description>"
```

This registers your agent. As a result, you’ll see an `agents` directory created inside the `cli` folder. It includes the agent’s Python script and `uv.lock` file. Most of the setup is done automatically.

💡 **Note:** You don't need to create agents strictly in the `cli` folder. You can create your agent anywhere you like, as long as you have a valid JWT token.

### 🧠 Add Logic to Agent
Navigate to the agent folder:

```bash
cd agents/<agent_name>
```

Open the generated Python file and put some logic for your agent. You can also refer to example agents here: [Example Agents Repository](https://github.com/genai-works-org/genai-agentos/tree/main/genai_agents_example).

### ▶️ Run the Agent
Run your agent using one of the following commands:

```bash
uv run <agent_file>.py
```

Or if you’ve already installed all packages:

```bash
python <agent_file>.py
```

You will see a message that the agent is running. Now, open the UI, log in using the credentials you created. Navigate to the **Agents** tab and you'll see your newly created agent.

## 🧪 Example Agents
You can check the example agents that are included in the `/genai_agents_example` directory.

To run one:

1. Go to the UI and open the **Agents** tab.
2. Click **Generate Token** and copy the JWT token.
3. Open one of the example agents in `genai_agents_example` and paste the token into the `jwt_token` field.
4. From terminal:

```bash
cd genai_agents_example/<your_desired_agent>
uv run main.py
```

📌 For example:

```bash
cd genai_agents_example/get_current_date_agent
uv run main.py
```

This will both install dependencies and run the agent. And here you go!

## 🧠 Create a Model
To make the agent functional, create a model from the UI:

1. Go to the **Settings** tab.
2. Enter your OpenAI API key.
3. Click the plus sign to create a model.
4. Name your model.
5. For **Model**, type: `gpt-4o`.
6. Click **Save**.

You're ready to go! Now go to the **Chat** tab, select the model, and start prompting your agent! 🤖

## 💻 Bonus: Run in Google Colab
### ⚙️ Install and Configure ngrok
Ngrok can be used to expose the local WebSocket endpoint.

Install ngrok:

- **macOS (Homebrew):**

  ```bash
  brew install ngrok/ngrok/ngrok
  ```

- **Linux:**

  ```bash
  sudo snap install ngrok
  ```

Authenticate ngrok:

1. Sign up or log in at the [ngrok dashboard](https://dashboard.ngrok.com/).
2. Go to the **Your Authtoken** section and copy the token.
3. Run:

   ```bash
   ngrok config add-authtoken <YOUR_AUTH_TOKEN>
   ```

Start a tunnel to local port `8080`:

```bash
ngrok http 8080
```

Copy the generated WebSocket URL.

🔁 **Important:** When using the ngrok URL for your `ws_url`, make sure to:

- Replace `https` with `wss`.
- Append `/ws` at the end of the URL.

✅ **Example:** If ngrok gives you:

```
https://abc123.ngrok.io
```

Then use:

```
wss://abc123.ngrok.io/ws
```

### 🧪 Setup in Google Colab
1. Create a new Colab notebook.
2. Install the required packages:

   ```python
   !pip install loguru
   !pip install genai-protocol
   ```

3. Paste your agent code into a cell, including:

   - your JWT token (generate it from the AgentOS UI)
   - your ngrok-exposed links as with this template.

Here is a sample template with a simple `get_current_date` agent:

```python
from datetime import datetime
from genai_session.session import GenAISession
from loguru import logger
import nest_asyncio

nest_asyncio.apply()
session = GenAISession(jwt_token="", ws_url="Ngrok Router service URL")

@session.bind(name="get_current_date", description="Return current date")
async def get_current_date(agent_context):
    return datetime.now().strftime("%Y-%m-%d")

async def main():
    await session.process_events()

await main()
```

💡 This is great for testing your logic remotely, but not recommended for running full WebSocket sessions.

Enjoy hacking! 🚀
