# AI Agents

AI Agents are intelligent controllers that connect different tasks, process user input, and generate meaningful responses. They act like a human assistant.

They can operate in different environments, such as a command-line console, a web browser, a virtual gaming world or simulation, or even a real physical world like robots and autonomous vehicles.

Arkalos includes built-in AI Agents like `DWHAgent`, which allows users to interact with their data using natural language.

Let's create our own AI Agent.




## Creating an AI Agent

To create an agent, implement the `AIAgent` contract and define the following methods:

- `NAME`: A unique name for the agent.
- `DESCRIPTION`: A brief explanation of what the agent does.
- `__init__()`: The initialization method where tasks and environment are defined.
- `action(user_input)`: The method that processes user input and runs tasks.



### Example: Simple Calculator Agent

Create a new file: `app/ai/agents/my_agent.py`

```python
from arkalos.ai import AIAgent, ConsoleEnv
from app.ai.tasks.calc_task import CalcTask

class MyAgent(AIAgent):
    @property
    def NAME(self):
        return "MyAgent"
    
    @property
    def DESCRIPTION(self):
        return "A calculator agent."
    
    def __init__(self):
        self._env = ConsoleEnv(
            self.NAME, 
            "Hi, I am a calculator. What do you want to calculate?", 
            self.action
        )
        self._tasks = {
            "calc": CalcTask(),
        }

    def action(self, user_input):
        output = self.runTask("calc", user_input)
        self.message(output)
```




### Running the Agent

Create a script and run the agent:

```python title="scripts/ai/my_agent.py"
from app.ai.agents.my_agent import MyAgent

agent = MyAgent()
agent.run()
```

Run the script:

```bash
uv run scripts/ai/my_agent.py
```




## Defining the Environment and Tasks

In the `__init__()` method, you need to specify:

- **Environment**: Defines where and how the agent operates. `ConsoleEnv` provides a basic terminal chat interface.
- **Tasks**: The set of actions the agent can perform.

For `ConsoleEnv` provide:

- The agent's name.
- A greeting message displayed at startup.
- The `action()` method, which runs each time the user inputs a message.




## Multi-Task AI Agent

A multi-task agent can determine user intent and execute the appropriate task.

Arkalos includes a `whichTask()` method that uses AI to identify the correct task from the list of registered tasks.



### Example: Multi-Task AI Agent

```python
from arkalos.ai import AIAgent, ConsoleEnv
from app.ai.tasks.calc_task import CalcTask
from app.ai.tasks.what_is_my_ip_task import WhatIsMyIpTask

class MultiAgent(AIAgent):
    @property
    def NAME(self):
        return "MultiAgent"
    
    @property
    def DESCRIPTION(self):
        return "An agent that understands user intent and selects the right task."
    
    def __init__(self):
        self._env = ConsoleEnv(
            self.NAME, 
            "Hi, I can tell you your IP address or do math calculations.",
            self.action
        )
        self._tasks = {
            "what_is_my_ip": WhatIsMyIpTask(),
            "calc": CalcTask(),
        }

    def action(self, user_input):
        self.message("Determining the appropriate task...")
        selected_task = self.whichTask(user_input)
        self.message(f"Identified task: {selected_task}")
        output = self.runTask(selected_task, user_input)
        self.message(f"Task output: {output}")
```

Here, `whichTask()` determines the right task based on user input, and `self.message()` sends output to the console.

Now, you can create AI Agents that handle multiple tasks efficiently!

