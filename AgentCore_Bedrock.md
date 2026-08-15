# **Amazon Bedrock AgentCore**

> **Bedrock Agents = AWS gives you the agent.**
> **AgentCore = AWS gives you the production infrastructure/platform on which your agent can run.**

And there is an important 2026 update: **Bedrock Agents Classic is now in maintenance mode**, while AWS recommends AgentCore for new agent development. ([AWS Documentation][1])

<details>
**Maintenance mode** means AWS is **not actively adding major new features** to Bedrock Agents Classic.

In short:

* ✅ Existing applications can **continue running**
* ✅ AWS will provide **bug fixes/security/maintenance**
* ⚠️ Don't expect significant **new capabilities**
* ⚠️ AWS recommends **AgentCore for new agent applications**
* 🔄 Existing Classic agents may eventually need **migration/planning toward AgentCore**

**Interview one-liner:**

> **“Bedrock Agents Classic is in maintenance mode, meaning AWS continues to support existing workloads but is focusing new agent capabilities and development on Amazon Bedrock AgentCore.”**
</details>

---
<img width="1181" height="1331" alt="AgentCore" src="https://github.com/user-attachments/assets/dee0c9da-f3e3-440b-b345-49208dd970e9" />

---

# 1. First: What problem does AgentCore solve?

Suppose you build an AI agent like:

> **"Kubernetes Production Troubleshooting Agent"**

User asks:

> "Why is `payment-service` failing in EKS?"

Your agent needs to:

1. Understand the question
2. Query Kubernetes
3. Query CloudWatch
4. Maybe query Dynatrace
5. Read logs
6. Analyze metrics
7. Remember previous conversation
8. Potentially execute commands
9. Authenticate to AWS/Kubernetes/Dynatrace
10. Run securely
11. Handle many users simultaneously
12. Maintain sessions
13. Scale up/down
14. Monitor agent behavior
15. Trace LLM/tool calls
16. Prevent one user's data from leaking to another
17. Potentially run for minutes/hours
18. Connect to MCP tools
19. Deploy new agent versions safely

The **LLM is not the difficult part anymore**.

The difficult part is:

```text
                ┌───────────────────────────┐
                │        LLM / Agent        │
                │                           │
                │ "Reason → Tool → Observe │
                │  → Reason → Tool..."      │
                └─────────────┬─────────────┘
                              │
              ────────────────┼────────────────
                              │
                    Production Problems
                              │
        ┌─────────────┬───────┼───────┬─────────────┐
        │             │       │       │             │
     Scaling       Memory   Identity Security   Observability
        │             │       │       │             │
     Sessions      Tools     Auth    Isolation    Tracing
        │             │       │       │             │
     Runtime      MCP/API   OAuth    VPC/etc.     Metrics
```

**AgentCore is designed to handle this infrastructure layer.**

AWS describes it as a platform for building, deploying and operating agents securely at scale, independently of the agent framework and foundation model. ([AWS Documentation][2])

---

# 2. Where AgentCore fits

Think about the modern GenAI stack like this:

```text
┌──────────────────────────────────────────────────────────┐
│                    USER / APPLICATION                    │
│                                                          │
│   Chat UI / API / Slack / Teams / Web / CLI             │
└──────────────────────────┬───────────────────────────────┘
                           │
                           ▼
┌──────────────────────────────────────────────────────────┐
│                    AGENT APPLICATION                     │
│                                                          │
│  LangGraph / CrewAI / Strands / OpenAI Agents SDK       │
│  Custom Python agent                                     │
│                                                          │
│       Reason → Plan → Tool → Observe → Reason            │
└──────────────────────────┬───────────────────────────────┘
                           │
                           ▼
┌──────────────────────────────────────────────────────────┐
│                 AMAZON BEDROCK AGENTCORE                 │
│                                                          │
│  Runtime     Memory      Gateway       Identity          │
│  Browser     Code        Observability  Registry         │
│  Interpreter                                             │
└──────────────────────────┬───────────────────────────────┘
                           │
             ┌─────────────┼─────────────┐
             ▼             ▼             ▼
        LLM Models       Tools          Data
             │             │             │
      Claude / Nova    MCP / APIs     S3 / DB
      Gemini / OpenAI  Lambda         Knowledge
      etc.             Kubernetes    Bases
```

The important point:

**AgentCore isn't itself your business logic.**

Your agent still decides:

```text
What should I do?
Which tool should I call?
What does the result mean?
What's the next step?
```

AgentCore provides the **production-grade execution environment and surrounding capabilities**.

---

# 3. AgentCore vs Bedrock Agents Classic

This distinction is extremely important for interviews.

## Bedrock Agents Classic

The classic architecture was roughly:

```text
User
 │
 ▼
Bedrock Agent
 │
 ├── Foundation Model
 │
 ├── Instructions
 │
 ├── Action Groups
 │      │
 │      ├── Lambda
 │      └── APIs
 │
 └── Knowledge Base
```

AWS managed much of the orchestration.

You essentially configured:

```text
Model
+
Instructions
+
Tools / Action Groups
+
Knowledge Base
```

Then AWS ran the agent.

---

# 4. AgentCore is much more flexible

With AgentCore:

```text
                 Your Agent
                     │
       ┌─────────────┼──────────────┐
       │             │              │
   LangGraph       CrewAI        Strands
       │             │              │
       └─────────────┼──────────────┘
                     │
                     ▼
              AgentCore Runtime
                     │
        ┌────────────┼────────────┐
        ▼            ▼            ▼
     Memory       Gateway       Identity
        │            │            │
        ▼            ▼            ▼
    Context       Tools/API     Auth
```

You can use **LangGraph, CrewAI, LlamaIndex, Strands, OpenAI Agents SDK, custom agents**, etc., and use models from Bedrock or outside Bedrock such as OpenAI or Gemini. ([AWS Documentation][2])

---

# 5. The biggest conceptual difference

Think about it this way:

### Classic

```text
AWS owns more of the agent architecture

        AWS
 ┌─────────────────┐
 │ Agent           │
 │ Model           │
 │ Orchestration   │
 │ Action Groups   │
 │ Knowledge Base  │
 └─────────────────┘
        ▲
        │
     You configure
```

### AgentCore

```text
             YOU
              │
              ▼
       ┌──────────────┐
       │ Agent Logic  │
       │              │
       │ LangGraph    │
       │ CrewAI       │
       │ Strands      │
       │ Custom       │
       └──────┬───────┘
              │
              ▼
       ┌──────────────┐
       │ AgentCore    │
       │              │
       │ Runtime      │
       │ Memory       │
       │ Gateway      │
       │ Identity     │
       │ Observability│
       └──────────────┘
```

So:

> **Classic = managed agent product**

> **AgentCore = managed agent infrastructure/platform**

That is the mental model I'd use in your interview.

---

# 6. AgentCore building blocks

The current AgentCore platform is modular. You don't necessarily need every component. ([AWS Documentation][2])

Think:

```text
                    AGENTCORE
                        │
       ┌────────────────┼───────────────────┐
       │                │                   │
    Runtime           Memory             Gateway
       │                │                   │
   Execution         Context             Tools
   Scaling           History             APIs
   Isolation         Long-term           MCP
       │                │
       │                │
       ├────────────┬───┴───────┬──────────────┐
       │            │           │              │
    Identity     Browser   Code Interpreter Observability
```

Let's understand each.

---

# 7. AgentCore Runtime

This is probably the **most important component for you as a Platform Engineer**.

Runtime is essentially:

> **Managed serverless infrastructure for running your agent.**

AWS handles:

* execution
* scaling
* session management
* isolation
* infrastructure
* authentication integration
* observability plumbing

while your code handles the actual agent logic. ([AWS Documentation][3])

Think:

```text
                    AgentCore Runtime
                           │
              ┌────────────┴────────────┐
              │                          │
        Agent Container            Session
              │                          │
       ┌──────┴──────┐             User A
       │             │             User B
    Python         Tools           User C
```

Each session gets isolation.

AWS documents session isolation using isolated microVM environments; terminated sessions have their execution environment destroyed and memory sanitized. ([AWS Documentation][4])

---

# 8. Why is session isolation important?

Imagine:

```text
User A
"Tell me about my production incident"

             ↓

Agent Session A
```

At the same time:

```text
User B
"Tell me about my production incident"

             ↓

Agent Session B
```

You don't want:

```text
User A data
     ↓
     X
     ↓
User B
```

AgentCore Runtime provides session isolation to prevent cross-session contamination. ([AWS Documentation][4])

---

# 9. AgentCore Memory

Normal LLM:

```text
Conversation 1

User:
My application is payment-service.

LLM:
Okay.
```

Next day:

```text
User:
What was my application?

LLM:
I don't know.
```

AgentCore Memory provides mechanisms for:

### Short-term memory

```text
Current conversation
       ↓
Agent
```

### Long-term memory

```text
Previous interactions
       ↓
Memory
       ↓
Relevant memories retrieved
       ↓
Agent
```

AWS supports both short-term and long-term memory strategies. ([AWS Documentation][5])

---

# 10. AgentCore Gateway

This is especially important for your **AI Gateway / MCP learning**.

Suppose you have:

```text
AWS APIs
Lambda
REST APIs
Kubernetes
GitHub
Jira
ServiceNow
Databases
Internal APIs
```

Your agent shouldn't directly manage 50 different integrations.

Instead:

```text
                   Agent
                     │
                     ▼
            AgentCore Gateway
                     │
        ┌────────────┼────────────┐
        ▼            ▼            ▼
      API          Lambda        MCP
        │            │            │
        ▼            ▼            ▼
     Service       Function     Tool
```

AgentCore Gateway can expose existing APIs, Lambda functions and services as MCP-compatible tools, while providing authentication and policy controls. ([AWS Documentation][6])

This is where AgentCore starts looking very much like an **AI-native API gateway**.

---

# 11. AgentCore Identity

Suppose your agent needs to access:

```text
AWS
GitHub
Slack
Jira
ServiceNow
```

Don't do:

```python
github_token = "xxxxxxxx"
aws_secret = "xxxxxxxx"
```

inside the agent.

Instead:

```text
User
 │
 ▼
Identity Provider
 │
 ├── Cognito
 ├── Okta
 └── Microsoft Entra ID
        │
        ▼
 AgentCore Identity
        │
        ▼
 Agent
        │
        ▼
 External services
```

AgentCore supports inbound authentication and outbound authentication, including OAuth and API keys, with user-delegated and autonomous modes. ([AWS Documentation][4])

---

# 12. AgentCore Browser

Imagine:

> "Go to our vendor portal and check the latest invoice."

Traditional LLM:

```text
LLM
 X
Can't interact with website
```

AgentCore Browser:

```text
Agent
 │
 ▼
AgentCore Browser
 │
 ▼
Secure browser session
 │
 ├── Navigate
 ├── Login
 ├── Click
 ├── Fill forms
 └── Extract information
```

AWS provides a managed isolated browser environment with session isolation, observability, CloudTrail logging and replay capabilities. ([AWS Documentation][7])

---

# 13. AgentCore Code Interpreter

Suppose the agent receives:

```text
sales.csv
```

and user asks:

> "Find the top 10 customers and calculate monthly growth."

The agent can:

```text
User
 │
 ▼
Agent
 │
 ▼
Code Interpreter
 │
 ├── Python
 ├── Pandas
 ├── calculations
 └── analysis
 │
 ▼
Result
 │
 ▼
Agent
 │
 ▼
User
```

The important part is **sandboxed execution**, rather than letting arbitrary generated code execute directly on your application server.

---

# 14. AgentCore Observability

This is extremely important in production.

You want to know:

```text
User request
    ↓
Agent reasoning
    ↓
LLM call
    ↓
Tool call
    ↓
Tool response
    ↓
LLM call
    ↓
Final answer
```

And metrics such as:

```text
Latency
Token usage
LLM calls
Tool calls
Errors
Session duration
Agent behavior
```

AgentCore provides observability integration including CloudWatch and OpenTelemetry support. ([Amazon Web Services, Inc.][8])

For your Platform Engineering background, think:

> **AgentCore Observability ≈ distributed tracing/monitoring for agent execution.**

---

# 15. Now let's build the architecture

Let's use a practical example:

# Kubernetes AI Troubleshooting Agent

User:

> "Why is checkout-service crashing in EKS?"

Architecture:

```text
                         USER
                           │
                           ▼
                    Chat / API / CLI
                           │
                           ▼
                 ┌───────────────────┐
                 │ AgentCore Runtime │
                 │                   │
                 │  Agent           │
                 │  LangGraph       │
                 └─────────┬─────────┘
                           │
              ┌────────────┼─────────────┐
              │            │             │
              ▼            ▼             ▼
           Memory       Gateway       Identity
              │            │             │
              │            │             │
              │      ┌─────┼──────┐      │
              │      │     │      │      │
              │      ▼     ▼      ▼      ▼
              │    K8s  CloudWatch Dynatrace
              │    API    API        API
              │
              ▼
       Previous incidents
```

And:

```text
                   AgentCore
                       │
                       ▼
               Foundation Model
                       │
                 ┌─────┴─────┐
                 │           │
              Claude       Nova
                 │
                 ▼
              Reasoning
```

---

# 16. Step-by-step workflow

Now let's actually walk through a request.

## Step 1 — User sends request

```text
"Why is checkout-service crashing?"
```

↓

```text
Application
    ↓
AgentCore Runtime
```

---

# 17. Step 2 — Runtime creates/uses session

AgentCore Runtime establishes an isolated execution environment/session.

```text
runtimeSessionId
        │
        ▼
┌────────────────────┐
│ Agent Session      │
│                    │
│ User = Deepak      │
│ Request = #123     │
└────────────────────┘
```

The same session ID can be reused for subsequent interactions. ([AWS Documentation][4])

---

# 18. Step 3 — Agent receives request

Your LangGraph/custom agent receives:

```text
User:
Why is checkout-service crashing?
```

Agent sends request to model:

```text
System:
You are a Kubernetes troubleshooting agent.

Tools:
- get_pods
- get_logs
- get_events
- get_metrics
- restart_deployment
```

---

# 19. Step 4 — LLM reasons

The model determines:

```text
I need pod status first.
```

It chooses:

```text
get_pods(
    namespace="production",
    deployment="checkout-service"
)
```

---

# 20. Step 5 — Tool call goes through Gateway

Instead of your agent directly connecting to everything:

```text
Agent
 │
 ▼
AgentCore Gateway
 │
 ▼
Kubernetes API
```

Gateway handles the governed tool connection.

---

# 21. Step 6 — Kubernetes returns data

For example:

```text
checkout-service-7f8b9
STATUS: CrashLoopBackOff

RESTARTS: 17

LAST STATE:
OOMKilled
```

Agent receives:

```text
Tool Result:
Container terminated with OOMKilled.
```

---

# 22. Step 7 — Agent reasons again

Now the agent decides:

```text
OOMKilled means memory limit may be too low.

I should inspect:
1. Deployment memory limit
2. Previous logs
3. Current memory usage
```

It calls:

```text
get_deployment()
```

---

# 23. Step 8 — Another tool call

```text
Agent
  │
  ▼
Gateway
  │
  ▼
Kubernetes
```

Result:

```yaml
resources:
  limits:
    memory: 256Mi
  requests:
    memory: 128Mi
```

---

# 24. Step 9 — Agent may check historical memory

AgentCore Memory:

```text
Current incident
      │
      ▼
AgentCore Memory
      │
      ▼
Previous incident
```

Maybe it finds:

```text
Previous incident:
checkout-service exceeded 256Mi
after version 3.2 deployment.
```

Now the agent has more context.

---

# 25. Step 10 — Agent produces answer

```text
checkout-service is crashing because the container
is being OOMKilled.

Current memory limit:
256Mi

The service has restarted 17 times.

A similar incident occurred after version 3.2,
where memory consumption exceeded 256Mi.

Recommended action:
Increase memory limit to 512Mi and investigate
the memory increase introduced in version 3.2.
```

That's an **agentic workflow**.

Not:

```text
Prompt → Answer
```

but:

```text
Prompt
  ↓
Reason
  ↓
Tool
  ↓
Observe
  ↓
Reason
  ↓
Tool
  ↓
Observe
  ↓
Memory
  ↓
Reason
  ↓
Answer
```

---

# 26. Where AgentCore fits in that workflow

This distinction is critical.

### Your agent owns:

```text
Reasoning
Planning
Business logic
Tool selection
Workflow
```

### AgentCore owns/provides:

```text
Runtime
Session isolation
Scaling
Memory infrastructure
Identity
Tool connectivity
Browser
Code execution
Observability
```

That's why I would describe AgentCore as:

> **The "Kubernetes + IAM + API Gateway + observability" infrastructure layer for AI agents.**

Not literally those AWS services internally—but that's a useful mental model.

---

# 27. Classic Bedrock Agent example

Imagine the same troubleshooting agent using **Bedrock Agents Classic**.

You configure:

```text
Agent
 │
 ├── Instructions
 │
 ├── Claude
 │
 ├── Action Group
 │      │
 │      └── Lambda
 │             │
 │             └── get_pods()
 │
 └── Knowledge Base
```

The classic agent manages much of the agent orchestration.

You don't necessarily need to build your own LangGraph.

---

# 28. AgentCore version

With AgentCore:

```text
                 AgentCore Runtime
                         │
                         ▼
                 Your LangGraph
                         │
                ┌────────┼────────┐
                │        │        │
                ▼        ▼        ▼
             Memory   Gateway   Identity
                         │
                 ┌───────┼────────┐
                 ▼       ▼        ▼
                K8s   CloudWatch Dynatrace
```

You have much more architectural freedom.

---

# 29. Very important: AgentCore Runtime vs AgentCore Harness

There is another distinction you should know.

AWS now has **AgentCore Runtime** and **AgentCore Harness**.

### Runtime

You bring your own agent code:

```text
Your LangGraph
      │
      ▼
AgentCore Runtime
```

You own the orchestration loop.

AWS provides the infrastructure. ([AWS Documentation][3])

### Harness

AWS provides a managed agent loop.

You can essentially configure:

```text
Model
+
System Prompt
+
Tools
```

and AgentCore handles orchestration, tool execution, memory and response generation. ([AWS Documentation][2])

So:

```text
                    AgentCore
                        │
              ┌─────────┴─────────┐
              │                   │
           Runtime             Harness
              │                   │
       YOU build loop       AWS manages loop
              │                   │
         LangGraph            Config
         CrewAI               Model
         Strands              Tools
         Custom               Prompt
```

This is a **very important 2026 distinction**.

---

# 30. Runtime vs Harness — interview answer

If interviewer asks:

> "What's the difference between AgentCore Runtime and Harness?"

Say:

> **Runtime is a managed hosting environment where I bring my own agent implementation and orchestration loop. Harness is a higher-level managed agent execution layer where AgentCore manages the orchestration loop based on model, prompt and tools configuration.**

AWS explicitly distinguishes them this way. ([AWS Documentation][3])

---

# 31. Full AgentCore architecture

Now put everything together:

```text
                           USER
                            │
                            ▼
                    Web / API / CLI
                            │
                            ▼
                  ┌──────────────────┐
                  │ Identity / Auth  │
                  └────────┬─────────┘
                           │
                           ▼
              ┌──────────────────────────┐
              │      AGENTCORE            │
              │                           │
              │ ┌───────────────────────┐ │
              │ │ Runtime / Harness     │ │
              │ │                       │ │
              │ │ Agent                 │ │
              │ │ LangGraph / Strands   │ │
              │ │ / CrewAI / Custom     │ │
              │ └──────────┬────────────┘ │
              │            │              │
              │      ┌─────┼─────┐        │
              │      ▼     ▼     ▼        │
              │   Memory Gateway Identity │
              │                           │
              │   Browser                │
              │   Code Interpreter       │
              │   Observability          │
              └────────────┬──────────────┘
                           │
             ┌─────────────┼──────────────┐
             ▼             ▼              ▼
           Models         Tools           Data
             │             │              │
      ┌──────┼──────┐      │              │
      ▼      ▼      ▼      ▼              ▼
    Claude  Nova  Gemini  MCP/API       S3/DB
                    │      Lambda        KB
                    │      K8s           Logs
                    │      GitHub        Metrics
                    │
                    ▼
                 Response
```

---

# 32. AgentCore vs classic — comparison

| Area                       | Bedrock Agents Classic          | AgentCore                                     |
| -------------------------- | ------------------------------- | --------------------------------------------- |
| Main idea                  | Managed agent                   | Agent platform/infrastructure                 |
| Agent orchestration        | AWS managed                     | Your code **or** managed Harness              |
| Framework                  | More Bedrock-centric            | Framework agnostic                            |
| Model                      | Primarily Bedrock               | Bedrock + external models                     |
| LangGraph                  | Not the core model              | First-class deployment option                 |
| CrewAI                     | Not the core model              | Supported                                     |
| Strands                    | AWS-native                      | Supported                                     |
| Custom agent               | Limited compared with AgentCore | Strong                                        |
| Runtime                    | AWS-managed agent runtime       | Purpose-built Runtime                         |
| Memory                     | Knowledge/agent capabilities    | Dedicated Memory                              |
| Tools                      | Action Groups                   | MCP, Gateway, APIs, Browser, Code Interpreter |
| Identity                   | Bedrock/IAM integrations        | Dedicated AgentCore Identity                  |
| Browser                    | Not the central architecture    | Dedicated Browser                             |
| Code execution             | Not the central architecture    | Dedicated Code Interpreter                    |
| Observability              | AWS services                    | AgentCore Observability                       |
| Session isolation          | Managed                         | Explicit AgentCore Runtime isolation          |
| MCP                        | Not central                     | Core capability                               |
| A2A                        | Not central                     | Supported in Runtime                          |
| Infrastructure flexibility | Lower                           | Much higher                                   |
| New development            | Maintenance mode                | Recommended direction                         |

AWS currently documents Bedrock Agents Classic as being in **maintenance mode** and points developers toward AgentCore. ([AWS Documentation][1])

---

# 33. The "why did AWS create AgentCore?" story

This is the best way to remember the evolution.

### Generation 1

```text
LLM
 │
 ▼
Prompt
 │
 ▼
Answer
```

Problem:

> LLM can't take actions.

---

### Generation 2

```text
LLM
 │
 ▼
Agent
 │
 ├── Tool
 ├── Tool
 └── Knowledge
```

Bedrock Agents Classic helped here.

Problem:

> Production agent infrastructure becomes complicated.

---

### Generation 3

You build with:

```text
LangGraph
CrewAI
Strands
Custom Python
OpenAI Agents SDK
```

But now you have to solve:

```text
Deployment
Scaling
Isolation
Identity
Memory
MCP
Tool auth
Browser
Code execution
Observability
Long-running sessions
```

---

### Generation 4 — AgentCore

```text
                YOUR AGENT
                    │
                    ▼
             ┌──────────────┐
             │ AgentCore    │
             │              │
             │ Runtime      │
             │ Memory       │
             │ Gateway      │
             │ Identity     │
             │ Browser      │
             │ Code         │
             │ Observability│
             └──────────────┘
```

Now you can focus on:

```text
"What should my agent do?"
```

rather than:

```text
"How do I build the infrastructure to run this agent?"
```

That is the fundamental reason AgentCore exists.

---

# 34. A Platform Engineer's perspective

For **your Senior Platform Engineering / DevOps background**, I'd map it like this:

```text
Traditional Application

Developer
   │
   ▼
Application
   │
   ▼
Kubernetes / ECS
   │
   ├── Scaling
   ├── Networking
   ├── Identity
   ├── Secrets
   ├── Observability
   └── Security
```

Agentic application:

```text
AI Developer
     │
     ▼
AI Agent
     │
     ▼
AgentCore
     │
     ├── Runtime
     ├── Identity
     ├── Memory
     ├── Gateway
     ├── Observability
     ├── Browser
     ├── Code Interpreter
     └── Security
```

So AgentCore is effectively becoming a **platform layer for production agent workloads**.

---

# 35. One complete real-world example

Imagine your company has:

```text
GitLab
EKS
AWS
Dynatrace
Jira
ServiceNow
Slack
```

You build:

> **AI SRE Agent**

User asks in Slack:

> "Payment service has 5% error rate. Investigate."

Workflow:

```text
Slack
 │
 ▼
AI SRE Agent
 │
 ▼
AgentCore Runtime
 │
 ▼
LLM
 │
 ├── Check EKS
 │       │
 │       ▼
 │    Gateway
 │
 ├── Check Dynatrace
 │       │
 │       ▼
 │    Gateway
 │
 ├── Check CloudWatch
 │       │
 │       ▼
 │    Gateway
 │
 ├── Search Jira
 │       │
 │       ▼
 │    Gateway
 │
 └── Retrieve previous incidents
         │
         ▼
      Memory
```

Agent determines:

```text
Payment service deployed version 4.8
10 minutes ago.

Error rate increased immediately after deployment.

Dynatrace:
HTTP 500 +4.8%

EKS:
Pods healthy

Logs:
Database connection timeout

Previous incident:
Same error occurred in version 4.8-beta.
```

Agent responds:

```text
Root cause likely introduced by v4.8.

Evidence:
1. Error rate increased immediately after deployment.
2. Logs show DB connection timeout.
3. Same issue appeared in v4.8-beta.
4. Pods themselves are healthy.

Recommendation:
Rollback to v4.7.

I have NOT executed rollback because
this is a production-impacting action.
```

Then user:

> "Rollback."

Agent:

```text
User approval
     │
     ▼
Identity
     │
     ▼
Gateway
     │
     ▼
GitLab / Kubernetes
     │
     ▼
Rollback
```

That is where AgentCore becomes extremely powerful for **AIOps / LLMOps / Platform Engineering**.

---

# 36. The most important mental model

Remember these four layers:

```text
                 ┌─────────────────────┐
                 │       MODEL         │
                 │ Claude / Nova / etc │
                 └──────────┬──────────┘
                            │
                 "Reason about problem"
                            │
                            ▼
                 ┌─────────────────────┐
                 │        AGENT        │
                 │ LangGraph / CrewAI  │
                 │ Strands / Custom    │
                 └──────────┬──────────┘
                            │
                 "Decide & orchestrate"
                            │
                            ▼
                 ┌─────────────────────┐
                 │     AGENTCORE       │
                 │ Runtime / Memory    │
                 │ Gateway / Identity  │
                 │ Browser / Code      │
                 │ Observability       │
                 └──────────┬──────────┘
                            │
                 "Run securely at scale"
                            │
                            ▼
                 ┌─────────────────────┐
                 │       TOOLS         │
                 │ APIs / MCP / K8s    │
                 │ Lambda / DB / GitHub│
                 └─────────────────────┘
```

### In one sentence:

> **The model thinks, the agent orchestrates, AgentCore runs and governs the agent, and tools let the agent act.**

That's the cleanest way to explain AgentCore in a **Senior DevOps / Platform Engineering interview**. ([AWS Documentation][2])

And since you're learning **AI Gateway + MCP + Agentic AI + AIOps**, the next logical topic is the relationship:

**`Agent → AgentCore Runtime → AgentCore Gateway → MCP → Tools`**

because that connects AgentCore directly to the AI Gateway architecture you were studying.

[1]: https://docs.aws.amazon.com/bedrock/latest/userguide/agents-classic-maintenance-mode.html?utm_source=chatgpt.com "Amazon Bedrock Agents Classic maintenance mode - Amazon Bedrock"
[2]: https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/what-is-bedrock-agentcore.html?utm_source=chatgpt.com "Overview - Amazon Bedrock AgentCore"
[3]: https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/harness-vs-runtime.html?utm_source=chatgpt.com "AgentCore harness vs. Runtime - Amazon Bedrock AgentCore"
[4]: https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/runtime-how-it-works.html?utm_source=chatgpt.com "How it works - Amazon Bedrock AgentCore"
[5]: https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/how-it-works.html?utm_source=chatgpt.com "How it works - Amazon Bedrock AgentCore"
[6]: https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/gateway.html?utm_source=chatgpt.com "Amazon Bedrock AgentCore Gateway: A secure AI gateway for agents, tools, and models - Amazon Bedrock AgentCore"
[7]: https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/browser-tool.html?utm_source=chatgpt.com "Interact with web applications using Amazon Bedrock AgentCore Browser - Amazon Bedrock AgentCore"
[8]: https://aws.amazon.com/about-aws/whats-new/2025/07/amazon-bedrock-agentcore-preview/?utm_source=chatgpt.com "Amazon Bedrock AgentCore now available in preview - AWS"
