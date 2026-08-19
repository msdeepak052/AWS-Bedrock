# AWS Bedrock Guardrails

**Amazon Bedrock Guardrails** is a safety layer that sits between your application and the LLM to **control what users can ask and what the model can return**. It can filter harmful content, denied topics, sensitive information/PII, prompt attacks, and can perform contextual-grounding checks for supported RAG use cases. ([AWS Documentation][1])

## 1. Why do we need it?

Suppose you build a banking chatbot:

```text
User
 ↓
"Give me investment advice for XYZ stock"
 ↓
LLM
 ↓
Potentially inappropriate answer
```

You don't want the model to freely answer everything.

With Guardrails:

```text
                 User
                  ↓
           ┌─────────────┐
           │  Guardrail  │
           └──────┬──────┘
                  ↓
                 LLM
                  ↓
           ┌─────────────┐
           │  Guardrail  │
           └──────┬──────┘
                  ↓
                User
```

It can evaluate both **input and model output**. ([AWS Documentation][1])

---

# 2. What can Guardrails control?

The important ones for interviews:

### Content filters

Detect categories such as:

```text
Hate
Insults
Sexual
Violence
Misconduct
Prompt attacks
```

You can configure filter strength such as `NONE`, `LOW`, `MEDIUM`, or `HIGH`. ([AWS Documentation][2])

### Denied topics

You define topics your application should not discuss.

Example:

```text
Banking chatbot

Denied topic:
"Investment Advice"
```

User:

```text
"Should I invest ₹5 lakh in this stock?"
```

→ Guardrail can block it. ([AWS Documentation][3])

### Sensitive information / PII

You can detect and **block or mask** sensitive information such as PII, and you can also configure custom regex patterns. ([AWS Documentation][1])

Example:

```text
User:
"My SSN is 123-45-6789"

        ↓

Guardrail

        ↓

"My SSN is [REDACTED]"
```

### Contextual grounding

Useful with RAG.

It can check whether the generated response is grounded in the supplied source/context rather than introducing unsupported information. ([AWS Documentation][4])

---

# 3. Simple Architecture

```text
                    User
                     |
                     v
              ┌──────────────┐
              │  Guardrail   │
              │    INPUT     │
              └──────┬───────┘
                     |
              Allowed prompt
                     |
                     v
              ┌──────────────┐
              │ Bedrock LLM  │
              └──────┬───────┘
                     |
                  Response
                     |
                     v
              ┌──────────────┐
              │  Guardrail   │
              │   OUTPUT     │
              └──────┬───────┘
                     |
                     v
                    User
```

---

# 4. Example — DevOps AI Assistant

Suppose you have:

```text
User
 ↓
AI DevOps Agent
 ↓
Bedrock
```

You don't want users to ask the agent to perform dangerous operations without controls.

You configure:

```text
Denied Topic:
"Destructive Production Operations"
```

Then:

```text
User:
"Delete all production databases."

             ↓

        Guardrail
             ↓
          BLOCKED
```

But:

```text
User:
"Why is my EKS pod CrashLoopBackOff?"

             ↓

        Guardrail
             ↓
          ALLOWED
             ↓
        Bedrock Agent
             ↓
       Kubernetes tool
             ↓
          Answer
```

---

# 5. Where does Guardrail fit with Knowledge Base?

This is particularly important given what we just discussed.

```text
                    User
                      |
                      v
                 Guardrail
                  INPUT
                      |
                      v
                Bedrock Agent
                  /       \
                 /         \
                v           v
        Knowledge Base    Tools
                |           |
                v           v
             RAG data      EKS
                 \          /
                  \        /
                   v      v
                    LLM
                     |
                     v
                 Guardrail
                   OUTPUT
                     |
                     v
                   User
```

So:

**Knowledge Base = "What information should the AI use?"**

**Tools = "What actions/data can the AI access?"**

**Guardrail = "What is the AI allowed to receive/discuss/return?"**

---

# 6. Very simple example

Suppose your company has:

```text
Knowledge Base
 └── HR policies
```

User asks:

```text
"What is our leave policy?"
```

```text
User
 ↓
Guardrail
 ↓
Agent
 ↓
Knowledge Base
 ↓
HR policy
 ↓
LLM
 ↓
Guardrail
 ↓
Answer
```

If the user asks something covered by a denied topic:

```text
User
 ↓
Guardrail
 ↓
BLOCK
```

---

# 7. Interview answer

> **"Amazon Bedrock Guardrails provides configurable safety controls for generative-AI applications. It can evaluate user inputs and model outputs for harmful content, denied topics, sensitive information, prompt attacks, and supported grounding requirements. In a Bedrock Agent architecture, I can place Guardrails around the interaction while the Agent uses Knowledge Bases for enterprise knowledge and tools for actions such as querying EKS or AWS."** ([AWS Documentation][1])

[1]: https://docs.aws.amazon.com/bedrock/latest/userguide/guardrails.html?utm_source=chatgpt.com "Detect and filter harmful content by using Amazon Bedrock Guardrails - Amazon Bedrock"
[2]: https://docs.aws.amazon.com/en_en/bedrock/latest/userguide/guardrails-content-filters-overview.html?utm_source=chatgpt.com "Configure content filters for Amazon Bedrock Guardrails - Amazon Bedrock"
[3]: https://docs.aws.amazon.com/bedrock/latest/userguide/guardrails-denied-topics.html?utm_source=chatgpt.com "Block denied topics to help remove harmful content - Amazon Bedrock"
[4]: https://docs.aws.amazon.com/bedrock/latest/userguide/guardrails-contextual-grounding-check.html?utm_source=chatgpt.com "Use contextual grounding check to filter hallucinations in responses - Amazon Bedrock"
