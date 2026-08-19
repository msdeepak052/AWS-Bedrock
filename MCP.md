# MCP — Model Context Protocol

For your **Platform/DevOps + AI interview**, keep MCP simple:

> **MCP is a standard protocol that allows an AI application/agent to connect to external tools and data sources in a standardized way.**

Instead of building custom integrations for every tool:

```text
AI Agent
   |
   +---- custom code → Kubernetes
   +---- custom code → GitHub
   +---- custom code → AWS
   +---- custom code → Jira
```

MCP gives you a standard interface:

```text
                    AI Agent
                       |
                    MCP Client
                       |
                    MCP Server
              ┌────────┼────────┐
              ↓        ↓        ↓
          Kubernetes  AWS     GitHub
             tools    tools    tools
```

---

# 1. What is MCP?

MCP = **Model Context Protocol**.

It standardizes how an AI application discovers and uses external:

* **Tools** → actions the AI can execute
* **Resources** → data/context the AI can read
* **Prompts** → reusable prompt templates

For your Kubernetes use case:

```text
User:
"Why is payment-api failing?"

        ↓

AI Agent
        ↓
MCP Client
        ↓
Kubernetes MCP Server
        ↓
kubectl / Kubernetes API
        ↓
Pods / Events / Logs / Deployments
```

The AI doesn't need to know the internal implementation of every Kubernetes API operation.

---

# 2. Why is MCP used?

Without MCP:

```text
AI Agent
   |
   +---- Kubernetes integration
   |
   +---- AWS integration
   |
   +---- GitHub integration
   |
   +---- Terraform integration
```

Every integration has its own interface.

With MCP:

```text
                    AI Agent
                       |
                   MCP Client
                       |
       +---------------+---------------+
       |               |               |
       v               v               v
   K8s MCP          AWS MCP        GitHub MCP
    Server           Server          Server
```

The AI interacts with standardized MCP interfaces.

### Main benefit

> **MCP separates the AI from the implementation of external tools/data.**

---

# 3. Architecture

The architecture you should remember:

```text
                  USER
                    |
                    v
             +-------------+
             | AI Agent    |
             | / Host App  |
             +------+------+
                    |
                MCP Client
                    |
              MCP Protocol
                    |
                    v
             +-------------+
             | MCP Server  |
             +------+------+
                    |
          +---------+---------+
          |         |         |
          v         v         v
       kubectl   AWS API   GitHub API
       /K8s API
```

### Important:

**MCP Server is not necessarily the actual backend system.**

For example:

```text
MCP Server
    |
    v
Kubernetes API
```

The MCP server exposes Kubernetes capabilities as MCP tools.

---

# 4. MCP Host, Client and Server

These three terms are important.

## MCP Host

The AI application itself.

Examples conceptually:

```text
AI assistant
AI coding agent
AI DevOps agent
```

Architecture:

```text
Host
 └── MCP Client
```

---

## MCP Client

The component inside the AI application that communicates with MCP servers.

```text
AI Host
   |
   +-- MCP Client
          |
          v
      MCP Server
```

---

## MCP Server

A program that exposes tools/resources to the AI.

Example:

```text
Kubernetes MCP Server
```

could expose:

```text
get_pods
get_logs
get_events
get_deployment
```

---

# 5. Tools

**Tools are actions the AI can execute.**

For a Kubernetes MCP server:

```text
get_pods
get_pod_logs
get_events
describe_deployment
restart_deployment
```

Flow:

```text
User:
"Show me failed pods"

        ↓

AI
        ↓
MCP Client
        ↓
MCP Server
        ↓
Kubernetes API
        ↓
Pod information
        ↓
AI
        ↓
Explanation
```

---

# 6. Resources

Resources are **data/context that an MCP server can expose**.

For example:

```text
Kubernetes cluster information
configuration
documentation
OpenAPI schema
```

Conceptually:

```text
AI
 ↓
MCP Resource
 ↓
Kubernetes information
```

Think:

```text
Tool     → "DO something"
Resource → "READ something"
```

---

# 7. Prompts

MCP can also expose reusable prompt templates.

For example:

```text
incident-investigation
```

could define a standardized investigation workflow:

```text
1. Check deployment
2. Check pods
3. Check events
4. Check logs
5. Check recent changes
```

Then the AI can use that prompt structure.

For interview purposes:

```text
Tools     → actions
Resources → context/data
Prompts   → reusable instructions
```

---

# 8. Complete DevOps example

Suppose your EKS cluster has:

```text
payment-api
```

User asks:

> "Why is payment-api crashing?"

Architecture:

```text
                  User
                   |
                   v
              AI Agent
                   |
              MCP Client
                   |
                   v
          Kubernetes MCP Server
                   |
       +-----------+-----------+
       |           |           |
       v           v           v
    Pods        Events       Logs
       |           |           |
       +-----------+-----------+
                   |
                   v
             Kubernetes API
```

The AI might execute:

```text
get_deployment(payment-api)
        ↓
get_pods(payment-api)
        ↓
get_events(payment-api)
        ↓
get_pod_logs(payment-api)
```

Then reason:

```text
Pod restarted
     ↓
Exit code 137
     ↓
OOMKilled
     ↓
Memory limit too low
```

And answer:

> "payment-api is being OOMKilled because its container exceeded the configured memory limit."

---

# 9. MCP configuration

Unlike Fluent Bit or OTel, MCP configuration depends heavily on the **MCP host/client** you're using.

A conceptual configuration might look like:

```json
{
  "mcpServers": {
    "kubernetes": {
      "command": "kubernetes-mcp-server",
      "args": [
        "--context",
        "my-eks-cluster"
      ]
    }
  }
}
```

Meaning:

```text
mcpServers
    |
    +-- kubernetes
           |
           +-- start this MCP server
           |
           +-- connect to this Kubernetes context
```

The exact configuration syntax depends on the MCP client/host.

---

# 10. Where does the Kubernetes API endpoint go?

This is similar to your previous OTel question.

MCP itself **doesn't define a Kubernetes endpoint**.

The **MCP server** is responsible for connecting to Kubernetes.

Architecture:

```text
AI
 |
 | MCP
 v
MCP Server
 |
 | Kubernetes client/API
 v
Kubernetes API Server
```

The server may use:

```text
KUBECONFIG
```

or:

```text
in-cluster ServiceAccount
```

or another authentication mechanism.

So:

```text
MCP
 ↓
standard AI ↔ tool communication

Kubernetes endpoint
 ↓
configured/handled by Kubernetes MCP Server
```

---

# 11. MCP on EKS

For a production DevOps agent, you could deploy:

```text
                    AI Agent
                       |
                   MCP Client
                       |
              +--------+--------+
              |                 |
              v                 v
       Kubernetes MCP       AWS MCP
           Server             Server
              |                 |
              v                 v
       Kubernetes API        AWS APIs
              |
          EKS Cluster
```

The MCP server could run:

```text
outside the cluster
```

or:

```text
inside the cluster
```

depending on your architecture/security requirements.

---

# 12. Security — very important

MCP can expose powerful tools.

For example:

```text
get_pods          ← read
get_logs          ← read

delete_pod        ← write
scale_deployment  ← write
apply_manifest    ← write
```

You should **not** give an AI unrestricted Kubernetes admin permissions.

Instead:

```text
AI Agent
   |
   v
MCP Server
   |
   v
Restricted ServiceAccount
   |
   v
Kubernetes RBAC
```

Example:

```text
AI
 ↓
MCP Server
 ↓
ServiceAccount
 ↓
RBAC
 ↓
Only allowed APIs
```

For production, separate:

```text
READ tools
```

from:

```text
WRITE tools
```

and require approval for destructive operations.

---

# 13. MCP vs API

This is a common interview question.

Normal API:

```text
Application
    |
    | REST API
    v
Kubernetes
```

MCP:

```text
AI application
    |
    | MCP
    v
MCP Server
    |
    | API/SDK/CLI
    v
Kubernetes
```

MCP doesn't replace the underlying API.

Instead:

> **MCP provides a standardized interface through which AI applications can discover and invoke tools/context exposed by an MCP server.**

---

# 14. MCP vs Agent

Don't confuse these.

### Agent

The **reasoning/orchestration layer**:

```text
Understand problem
    ↓
Plan
    ↓
Choose tool
    ↓
Execute
    ↓
Analyze result
    ↓
Next action
```

### MCP

The **standard interface to external tools/context**:

```text
Agent
  ↓
MCP
  ↓
Tool
```

So:

```text
Agent = reasoning/orchestration

MCP = standardized tool/context interface
```

---

# 15. MCP vs Function Calling

They're related but different.

Traditional function calling:

```text
LLM
 ↓
Function
 ↓
Application code
```

MCP:

```text
LLM/Agent
 ↓
MCP Client
 ↓
MCP Server
 ↓
Tool
```

The advantage is that tools can be exposed through a standardized protocol rather than each AI application implementing its own custom integration.

---

# 16. Simple end-to-end demo

Imagine we create a simple Kubernetes MCP server exposing:

```text
get_pods
get_logs
get_events
```

Architecture:

```text
User
 |
 | "Why is my pod failing?"
 v
AI Agent
 |
 v
MCP Client
 |
 | call get_pods
 v
K8s MCP Server
 |
 v
Kubernetes API
 |
 v
Pod status
 |
 v
AI
 |
 | call get_logs
 v
K8s MCP Server
 |
 v
Pod logs
 |
 v
AI
 |
 v
Final diagnosis
```

The important point is:

**The AI doesn't directly execute `kubectl`.**

Instead:

```text
AI
 ↓
MCP tool
 ↓
MCP server
 ↓
kubectl/Kubernetes API
```

The implementation behind the MCP tool could use either a Kubernetes SDK or `kubectl`; MCP doesn't require one specific implementation.

---

# 17. Your AIOps use case

This fits very well with the Kubernetes troubleshooting architecture you've been working on:

```text
                       User
                         |
                         v
                  AI / LLM Agent
                         |
                    MCP Client
                         |
          +--------------+--------------+
          |              |              |
          v              v              v
      K8s MCP         AWS MCP       Git MCP
          |              |              |
          v              v              v
     EKS/K8s APIs     AWS APIs       GitHub
          |
          v
       Cluster
```

Then the agent can reason:

```text
Pod CrashLoopBackOff
        ↓
MCP → get pod
        ↓
MCP → get events
        ↓
MCP → get logs
        ↓
MCP → get deployment
        ↓
LLM analyzes
        ↓
Root cause
```

---

# 18. What to remember for interview

```text
MCP
│
├── Host
│    └── AI application
│
├── Client
│    └── Communicates with MCP server
│
└── Server
     ├── Tools
     ├── Resources
     └── Prompts
```

And:

```text
AI Agent
   ↓
MCP Client
   ↓
MCP Server
   ↓
Tool / Resource
   ↓
Real system
```

### One-line interview answer

> **"MCP, or Model Context Protocol, is a standardized protocol that allows AI applications to discover and interact with external tools, resources and prompts. In a DevOps use case, an AI agent can use an MCP client to communicate with a Kubernetes MCP server, which exposes operations such as getting pods, logs and events and then interacts with the Kubernetes API using controlled credentials and RBAC."**
