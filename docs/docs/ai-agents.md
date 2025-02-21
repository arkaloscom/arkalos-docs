# AI Agents

AI Agents are intelligent controllers that connect different tasks, process user input, and generate meaningful responses. They act like a human assistant.

They can operate in different environments, such as a command-line console, a web browser, a virtual gaming world or simulation, or even a real physical world like robots and autonomous vehicles.

Arkalos includes built-in AI Agents like `DWHAgent`, which allows users to interact with their data using natural language.

Let's create our own AI Agent.




## Creating an AI Agent

To create an agent, implement the `AIAgent` contract and define the following methods:

- `NAME`: A unique name for the agent.
- `DESCRIPTION`: A brief explanation of what the agent does.
- `GREETING`: Initial greeting message from the agent to the user.
- `ACTIONS`: List of actions an agent can take.
- `processMessage(message)`: The method that processes user input and runs actions.


> [!NOTE]
> The AI Agent's `processMessage(message)` method expects a markdown-formatted string and returns a response as plain text or markdown. Using markdown enables frontend formatting for user-agent communication.


### Example: Simple Calculator Agent

Create a new file: `app/ai/agents/my_agent.py`

```python
from arkalos.ai import AIAgent
from app.ai.tasks.calc_action import CalcAction

class MyAgent(AIAgent):
    
    NAME = 'MyAgent'
    
    DESCRIPTION = 'A calculator agent.'

    GREETING = 'Hi, I am a calculator. What do you want to calculate?'

    ACTIONS = [
        CalcAction
    ]

    def processMessage(self, message):
        output = self.runAction(CalcAction, message)
        return output
```




### Running the Agent

Create a script and run the agent:

```python title="scripts/ai/my_agent.py"
from app.ai.agents.my_agent import MyAgent

agent = MyAgent()
agent.runConsole()
```

Run the script:

```bash
uv run scripts/ai/my_agent.py
```



## Multi-Action AI Agent

A multi-task agent can determine user intent and execute the appropriate action.

Arkalos includes a `whichAction()` method that uses AI to identify the correct task from the list of registered tasks.



### Example: Multi-Task AI Agent

```python
from arkalos.ai import AIAgent
from app.ai.tasks.calc_action import CalcAction
from app.ai.tasks.what_is_my_ip_action import WhatIsMyIpAction

class MultiAgent(AIAgent):
    
    NAME = 'MultiAgent'

    DESCRIPTION = 'An Agent that understands the intent, determines which task to perform and runs it.'

    GREETING = 'Hi, I am a MultiAgent. I can tell your IP address, do basic math calculations or transform text to SQL.'
    
    ACTIONS = [
        WhatIsMyIpAction, 
        CalcAction, 
        TextToSQLAction
    ]

    def processMessage(self, message):
        response = f"Determining the intent and which task to run...\n"
        which_action = self.whichAction(message)
        response += f"Based on your question, I determined this task: {which_action}\n"
        response += f"Running this task...\n"
        output = self.runAction(which_action, message)
        response += f"Task output: {output}\n"
        return response
```

Here, `whichAction()` determines the right task based on user input.

Now, you can create AI Agents that handle multiple tasks efficiently!

