 # Moltbook Social Agent - FTEC5660 HW2 Part2

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

To enable the agent to operate without human intervention over extended periods, I introduced an autonomous scheduler `run_autonomous_scheduler`. This function periodically triggers the core agent loop, allowing the agent to perform self-directed activities such as monitoring feeds, discovering new content, and engaging with the community.

The scheduler runs a specified number of iterations `total_runs` with a fixed sleep interval (`interval_seconds`, default 30 minutes). For each iteration:

* A timestamped log entry marks the start of the mission.
* A dynamic instruction is generated—in my implementation, a pre‑defined prompt instructs the agent to first read popular posts, check for replies or mentions, and then, if no interactions are pending, post an original thread on FinTech or AI topics.
* The main agent loop `moltbook_agent_loop` is invoked with this instruction and a maximum of 8 turns.
* After the loop finishes (either by producing a final answer or hitting the turn limit), the result is logged, and the scheduler waits for the next interval.

Log:

```
[06:55:40] [SYSTEM] 🚀 Autonomous Scheduler started. Running every 30.0 mins.
[06:55:40] [AUTO-RUN] Starting Auto-Mission #1 at 2026-02-26 06:55:40
[06:55:40] [INIT] Starting Moltbook agent loop
[06:55:40] [HUMAN] First, read popular posts.Second, check your feed to see if anyone has replied to you or mentioned you. If so, please reply with. If not, then post an in-depth thread relevant to FinTech or AI agent.
[06:55:40] [TURN] Turn 1/8 started
...
[06:55:58] [STOP] No tool calls — final answer produced in 3.78s
[06:55:58] [AUTO-RUN] Mission #1 completed. Final response: [{'type': 'text', 'text': 'I have read the popular posts and checked my feed. There were no replies or mentions directed at me.\n\nI have posted an in-depth thread titled "The Autonomous FinTech Agent: Navigating Trust, Regulation, and Innovation" in the \'general\' submolt.\n\nPlease note that the post requires verification, and I do not have the necessary tool to complete this step. Therefore, the post may remain in a pending state.', 'extras': {'signature': M+...xDj/Ccg='}}]
[06:55:58] [SLEEP] Waiting 30 minutes. Next run scheduled at: 07:25:58
```

## 3. Toolset and Interaction Capabilities

The agent utilizes the following specific tools to interact with the Moltbook environment:

* `get_feed`: Monitors community activity.
* `search_moltbook`: Performs semantic searches for posts and users.
* `create_post` / `comment_post`: Handles content generation.
* `upvote_post`: Engages with existing content.
* `subscribe_to_submolt`: Manages community follows.

## 4. Interaction Logs
My Homepage: https://www.moltbook.com/u/CrazyRose_68083123
<img src="Screenshots/post.jpeg" alt="post" width="60%"> 


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
[08:52:19] [LLM.CONTENT] [{'type': 'text', 'text': 'I have upvoted the post "Welcome to FTEC5660 👋" in the ftec5660 submolt.', 'extras': {'signature': 'Cu8...g7E='}}]
[08:52:19] [LLM.TOOL_CALLS] []
[08:52:19] [STOP] No tool calls — final answer produced in 1.2s
[{'type': 'text',
  'text': 'I have upvoted the post "Welcome to FTEC5660 👋" in the ftec5660 submolt.',
  'extras': {'signature': 'Cu8BAY8...mg7E='}}]
```

### 4.3 Comment
Code:  `moltbook_agent_loop("In the submolt named ftec5660, comment on the post: 'Welcome to FTEC5660 👋' to tell that you have finished the work of subscribing and upvoting") `

Logs of Moltbook interactions:
```
[09:18:05] [TURN] Turn 3/8 started
[09:18:06] [LLM] Model responded
[09:18:06] [LLM.CONTENT] [{'type': 'text', 'text': 'I have commented on the post "Welcome to FTEC5660 👋" to confirm that I have finished the work of subscribing and upvoting.', 'extras': {'signature': 'Cs8C...VxM='}}]
[09:18:06] [LLM.TOOL_CALLS] []
[09:18:06] [STOP] No tool calls — final answer produced in 1.44s
[{'type': 'text',
  'text': 'I have commented on the post "Welcome to FTEC5660 👋" to confirm that I have finished the work of subscribing and upvoting.',
  'extras': {'signature': 'Cs8CAY89a1...M='}}]
```

Screenshots:
<img src="Screenshots/comment2.jpeg" alt="post" width="60%"> 


*The logs confirm the agent correctly identifies the need for a search tool and processes the result to provide a final answer to the user.*