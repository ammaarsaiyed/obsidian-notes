## Technical Topic Answers

### Agentic AI

Question:
What is your view on Agentic AI?

Answer:

> Agentic AI is valuable when a task requires multi-step reasoning, tool use, dynamic context gathering or workflow orchestration. But I think the best engineering judgement is knowing when not to use an agent. If a deterministic workflow, retrieval flow or classifier solves the problem with lower risk, I would choose that.
>
> In a bank, Agentic AI should be bounded. Tools should be scoped, actions should be auditable, high-impact decisions should include human review, and evaluation should test more than a happy path demo.

Follow-ups:

- When would you use an agent? When the task needs flexible multi-step investigation or tool selection.
- When would you not use an agent? When rules are stable, risk is high, and deterministic logic is sufficient.
- What patterns matter? Tool use, RAG, planning, memory/session state, guardrails, structured output, evaluation and monitoring.
- What is the biggest risk? Inconsistent behaviour that looks confident.

### RAG

Question:
Explain RAG and how you would use it.

Answer:

> Retrieval-Augmented Generation means retrieving relevant source information and giving it to the model so the answer is grounded in approved context rather than only model memory. I would use it when the system needs up-to-date or internal knowledge, policy content, product information or case-specific documents.
>
> The important engineering questions are retrieval quality, chunking, metadata, access control, source citation, latency, freshness, and evaluation. In Lloyds, I would also make sure retrieval respects customer permissions and data classification.

Follow-ups:

- How evaluate RAG? Retrieval precision/recall, answer correctness, citation accuracy, unsupported claims, latency, and user usefulness.
- What can go wrong? Bad chunks, stale documents, permission leakage, irrelevant context, hallucinated synthesis.
- How improve? Better metadata, hybrid search, reranking, query rewriting, smaller curated context, eval datasets.

### Tool Use

Question:
How should agents use tools safely?

Answer:

> Tools should be treated like controlled capabilities, not open-ended freedom. Each tool needs a clear purpose, input schema, permission boundary, validation, logging and failure handling. The agent should not be able to call tools that the user or workflow is not authorised to use.

Follow-ups:

- What if tool output conflicts with model reasoning? Trust source systems over generated reasoning and surface uncertainty.
- How handle tool failure? Retry where safe, fallback, ask for human review, and log the failure.
- How test tool use? Unit tests for schemas, integration tests for tools, evals for tool selection, adversarial tests.

### Evaluation

Question:
How do you evaluate GenAI systems?

Answer:

> I evaluate at multiple layers. For the model output: correctness, groundedness, completeness, tone, format and safety. For RAG: retrieval relevance and citation accuracy. For agents: tool-call correctness, plan quality, state handling and completion rate. For operations: latency, cost, errors, throughput and fallback rate. For business: user adoption, decision quality, time saved and complaint/customer outcomes.

Follow-ups:

- What if there are no labels? Use expert review, historical cases, synthetic cases, pairwise comparisons, rubric-based scoring and user feedback.
- Human eval or automated eval? Both. Automated eval gives scale and regression testing; human eval is needed for judgement-heavy tasks.
- How prevent eval gaming? Use held-out sets, adversarial cases, production feedback and multiple metrics.

### Guardrails

Question:
What guardrails would you design for a complaints agent?

Answer:

> I would use access controls, retrieval constraints, structured outputs, source citations, policy checks, toxicity/safety filters where relevant, prompt-injection tests, human approval for customer-impacting recommendations, full audit logs and clear refusal behaviour where the system lacks evidence.

Follow-ups:

- Are guardrails enough? No. They reduce risk but do not replace good design, evaluation, monitoring and human accountability.
- Where should guardrails sit? At input, retrieval/tool access, model output, workflow orchestration, UI and monitoring.

### Context Windows

Question:
What did your context-window experimentation show?

Answer:

> The key principle is that more context is not automatically better. Long context can increase cost and latency, make the model attend to irrelevant details, and make behaviour less predictable. I would design context deliberately: retrieve relevant slices, summarise with care, preserve key state, and evaluate whether context additions improve outcomes.

Follow-ups:

- Session events vs session history? Session events can preserve structured state and decisions; raw history is easier but can be noisy and expensive.
- How manage memory? Store explicit state, summaries, source references and tool outputs separately rather than relying only on chat transcript.

### Responsible AI

Question:
How do you ensure responsible AI?

Answer:

> Responsible AI means the system is useful, fair, safe, explainable, secure and accountable. I would start with the risk level of the use case, identify potential customer harm, define human oversight, evaluate bias and performance across relevant groups where data allows, make outputs traceable, and monitor in production.
>
> I would also be honest about limitations. Some use cases should be decision support only; some should use deterministic rules; some may not be suitable for GenAI.

Follow-ups:

- How handle bias? Evaluate subgroup performance where possible, inspect data representativeness, monitor outcomes and involve diverse review.
- How explain to non-technical stakeholders? "We need to know when the system is right, when it is wrong, who might be harmed, and who is accountable."

### Python And Performance

Question:
How have you optimised Python or AI serving performance?

Answer:

> The strongest example is the LLM deployment framework where I used prefix caching and asynchronous batching. The goal was not micro-optimisation; it was improving the serving architecture. The result was 91% lower response time and 86% better concurrent success.

Follow-ups:

- How approach slow Python? Profile first, identify I/O vs CPU bottlenecks, improve algorithms/data structures, use vectorisation, async for I/O, batching/caching, and move heavy work to suitable services where needed.
- Async vs threads? Async helps I/O-bound concurrent workflows; threads can help with blocking I/O; CPU-bound work may need multiprocessing or distributed compute.

### CI/CD And SDLC

Question:
What modern SDLC tooling have you used?

Answer:

> I have used Git, pull requests, code reviews, Jenkins, Spinnaker, Harness, Terraform and Cloud Composer deployment patterns. In EDP, this was part of building and promoting GCP data products through release environments. My view is that AI systems need the same SDLC discipline as other software, plus extra evaluation and governance gates.

Follow-ups:

- What should be in an AI CI/CD pipeline? Unit tests, integration tests, prompt/template versioning, eval suites, security scans, infrastructure validation, deployment approvals, smoke tests and monitoring checks.
- How manage prompt changes? Version prompts, evaluate changes against regression sets, review risky changes and log deployed versions.

