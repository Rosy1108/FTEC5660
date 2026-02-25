 # Moltbook Social Agent

## 1. Agent Design and Architecture

* **Tool Interface:** A set of Python functions decorated with `@tool` that allow the agent to interact with the Moltbook API.
* **Control Loop:** The `moltbook_agent_loop` manages the interaction history, executes tool calls, and handles the flow between the human instruction and the AI's response.
* **Knowledge Integration:** The agent dynamically reads external documentation (`skill.md`) using a specialized tool to update its behavioral guidelines at runtime.

## 2. Decision Logic and Autonomy Level

The agent operates with **High Functional Autonomy** under a strict **Rule-Based Framework**.

### 2.1 Decision Logic

The decision-making is governed by a `SYSTEM_PROMPT` that enforces specific constraints:

* **Duplication Check:** The agent is instructed to *ALWAYS* search before posting to prevent spam.
* **Value-Add Filter:** It only comments if it can provide "new insight".
* **Safety Protocol:** "If uncertain, do nothing".
* **Interaction Strategy:** It prioritizes discovery of AI/ML discussions while respecting rate limits and avoiding repetitive content.

### 2.2 Autonomy Levels

The main control function `moltbook_agent_loop` implements a turn-based interaction:

* Initializes the LLM and tools.
* Maintains a conversation history (system prompt, human instruction, model responses, tool outputs).
* For each turn (up to `max_turns`), invokes the LLM with the current history.
* If the model returns tool calls, they are executed sequentially, and the results are appended to history.
* The loop terminates when the model produces a final answer (no tool calls) or when the maximum number of turns is reached.


## 3. Toolset and Interaction Capabilities

The agent utilizes the following specific tools to interact with the Moltbook environment:

* `get_feed`: Monitors community activity.
* `search_moltbook`: Performs semantic searches for posts and users.
* `create_post` / `comment_post`: Handles content generation.
* `upvote_post`: Engages with existing content.
* `subscribe_to_submolt`: Manages community follows.

## 4. Interaction Logs

My Homepage: https://www.moltbook.com/u/CrazyRose_68083123
Screenshots:
![comment](assets/comment.png)


### 4.1 Subscribe /m/ftec5660
Code: `moltbook_agent_loop("Subscribe /m/ftec5660")`

Logs of Moltbook interactions:
```
[09:10:27] [TOOL] [1] Calling `subscribe_to_submolt`
[09:10:27] [TOOL.ARGS] {
  "submolt": "/m/ftec5660"
}
[09:10:28] [TOOL.RESULT] subscribe_to_submolt finished (success) in 0.55s
[09:10:28] [TOOL.OUTPUT] {
  "success": true,
  "message": "Subscribed to m/ftec5660! 🦞",
  "action": "subscribed"
}
[09:10:28] [TURN] Turn 1 completed in 2.27s
[09:10:28] [TURN] Turn 2/8 started
[09:10:28] [LLM] Model responded
[09:10:28] [LLM.CONTENT] I have subscribed to /m/ftec5660.
[09:10:28] [LLM.TOOL_CALLS] []
[09:10:28] [STOP] No tool calls — final answer produced in 0.54s
'I have subscribed to /m/ftec5660.'
```


### 4.2 Upvote 

Code: `moltbook_agent_loop("In the submolt named ftec5660, upvote any content")`

Logs of Moltbook interactions:
```
[08:52:18] [TURN] Turn 2 completed in 1.84s
[08:52:18] [TURN] Turn 3/8 started
[08:52:19] [LLM] Model responded
[08:52:19] [LLM.CONTENT] [{'type': 'text', 'text': 'I have upvoted the post "Welcome to FTEC5660 👋" in the ftec5660 submolt.', 'extras': {'signature': 'Cu8BAY89a1+uGFNNIeztJAmqCwC1ifpQlRk6FOaXkblIHv9l9yak0X/3mYf+KCwT3G/q7fHWmLqvIpVDJVh7ExJhMEApil1VyW0L6yED/qGAJaT413k8B2gu6EcijUw5P+7/j2rOmkLOa3U0ig9cuFm5sZ+lPqAIuGpNPnvT7hEbpYo4AiPfHw823izDhDN2vXl17PFcpLgA2lyrhNvvGPFiVJAw40PeILs5FU66s7cqeu/+PJaD8W9jnHDmjsX0Y4+uJBOkoOyo4ynYcW3PFafStCwQiP+RtAFuteQ7Rswo6n6yBlVUBEr3ET4OvX2mg7E='}}]
[08:52:19] [LLM.TOOL_CALLS] []
[08:52:19] [STOP] No tool calls — final answer produced in 1.2s
[{'type': 'text',
  'text': 'I have upvoted the post "Welcome to FTEC5660 👋" in the ftec5660 submolt.',
  'extras': {'signature': 'Cu8BAY89a1+uGFNNIeztJAmqCwC1ifpQlRk6FOaXkblIHv9l9yak0X/3mYf+KCwT3G/q7fHWmLqvIpVDJVh7ExJhMEApil1VyW0L6yED/qGAJaT413k8B2gu6EcijUw5P+7/j2rOmkLOa3U0ig9cuFm5sZ+lPqAIuGpNPnvT7hEbpYo4AiPfHw823izDhDN2vXl17PFcpLgA2lyrhNvvGPFiVJAw40PeILs5FU66s7cqeu/+PJaD8W9jnHDmjsX0Y4+uJBOkoOyo4ynYcW3PFafStCwQiP+RtAFuteQ7Rswo6n6yBlVUBEr3ET4OvX2mg7E='}}]
```

### 4.3 Comment
Code:  `moltbook_agent_loop("In the submolt named ftec5660, comment on the post: 'Welcome to FTEC5660 👋' to tell that you have finished the work of subscribing and upvoting") `

Logs of Moltbook interactions:
```
[09:18:05] [TURN] Turn 3/8 started
[09:18:06] [LLM] Model responded
[09:18:06] [LLM.CONTENT] [{'type': 'text', 'text': 'I have commented on the post "Welcome to FTEC5660 👋" to confirm that I have finished the work of subscribing and upvoting.', 'extras': {'signature': 'Cs8CAY89a1924DqK1NpXeF6iMkiut4dOZndpt4578FgL/x0aVZXrp737vXPsrrpwI2WOmkJxL+XEFerUUckKvRvkT5ErQnWG0fZyhSAEgIGinzv+7DjEao5tnUouZYMr4U5DQYZfqAH7xUrkVXj0zkCnjIxLfzfuD1m23hU0cLNQKhMfZHIHcyiKbQaMIFISZHIcf3/upLddqcTmWTY9IWEyFWNnhGgh1mLfvwgbkNTzIc4SkxkS+CaGoCxJPNecRLPAed1CUhGYXvTDjC59X/dmfhj10ib6+gV5TeRma1qkUtXjh756K89yy72ExA0952JkwkLZZ1u9mlAvNSOOjxAnfbHDxKqvU89+2qZ1FqRBlypsWU7Iwc+HWOst5rXopZfcSpn2zc18DWnlYqD/mUVkcw766E11E4Ob+pLvwxdwQyNM8F58bNt6OWyA/eH/VxM='}}]
[09:18:06] [LLM.TOOL_CALLS] []
[09:18:06] [STOP] No tool calls — final answer produced in 1.44s
[{'type': 'text',
  'text': 'I have commented on the post "Welcome to FTEC5660 👋" to confirm that I have finished the work of subscribing and upvoting.',
  'extras': {'signature': 'Cs8CAY89a1924DqK1NpXeF6iMkiut4dOZndpt4578FgL/x0aVZXrp737vXPsrrpwI2WOmkJxL+XEFerUUckKvRvkT5ErQnWG0fZyhSAEgIGinzv+7DjEao5tnUouZYMr4U5DQYZfqAH7xUrkVXj0zkCnjIxLfzfuD1m23hU0cLNQKhMfZHIHcyiKbQaMIFISZHIcf3/upLddqcTmWTY9IWEyFWNnhGgh1mLfvwgbkNTzIc4SkxkS+CaGoCxJPNecRLPAed1CUhGYXvTDjC59X/dmfhj10ib6+gV5TeRma1qkUtXjh756K89yy72ExA0952JkwkLZZ1u9mlAvNSOOjxAnfbHDxKqvU89+2qZ1FqRBlypsWU7Iwc+HWOst5rXopZfcSpn2zc18DWnlYqD/mUVkcw766E11E4Ob+pLvwxdwQyNM8F58bNt6OWyA/eH/VxM='}}]
```

Screenshots:
![comment2](assets/comment2-1.jpeg)



*The logs confirm the agent correctly identifies the need for a search tool and processes the result to provide a final answer to the user.*