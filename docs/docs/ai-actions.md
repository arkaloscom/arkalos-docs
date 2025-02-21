
# AI Actions

AI Actions or Tasks are actions that AI Agents can perform. Some tasks are simple, like getting basic information, while others use AI models or external tools to process data.

Arkalos provides built-in tasks like converting natural language to SQL, querying a data warehouse, and displaying data.

Tasks are essential building blocks — without them, an AI Agent can't do anything.




## Creating an Action

Let's create two actions:

1. **Basic Action:** Find the user's IP address.
2. **AI-Powered Action:** A natural language calculator.




### Basic Action: What is My IP

Create a new file: `app/ai/actions/what_is_my_ip_action.py`

```python title="app/ai/tasks/what_is_my_ip_action.py"
import socket
from arkalos.ai import AIAction

class WhatIsMyIpAction(AIAction):
    
    NAME = 'what_is_my_ip'
    DESCRIPTION = 'Get the user's IP address.'
    
    def run(self, message):
        hostname = socket.gethostname()
        return socket.gethostbyname(hostname)
```

Every AI Action in Arkalos must implement the `AIAction` contract and three key properties/methods:

- `NAME`: A unique name for the action.
- `DESCRIPTION`: A clear and descriptive explanation of what the action does. Other actions and AI Agents can use this information to determine if the task is capable of processing a certain input.
- `run(message)`: The main function that processes input and returns a result. We don't use user's input in this example.


### AI Action: Natural Language Calculator

Create a new file: `app/ai/actions/calc_action.py`

```python title="app/ai/tasks/calc_action.py"
from arkalos.ai import AIAction

class CalcTask(AIAction):
    
    NAME = 'calc'
    DESCRIPTION = 'Solve mathematical expressions from natural language.'
    
    def run(self, message):
        prompt = f"""
            ### Instructions:
            You are a calculator. Solve the following expression:

            ### Input:
            `{message}`

            ### Response:
            Return only the final numerical result.
        """
        return self.generateTextResponse(prompt)
```

This action uses an AI model to process user input, generate a math formula, and return a single calculated value.

The response time depends on your computer's CPU, RAM, GPU, and the AI model set in the `.env` file.




## Using Actions in an Agent

Now that we have actions, let’s add them to an AI Agent! [Learn how here →](/docs/ai-agents)


