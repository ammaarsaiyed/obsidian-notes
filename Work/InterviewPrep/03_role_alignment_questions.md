## Role Alignment Questions

### 1. Walk Me Through Relevant Projects

Question:
Can you walk me through projects that are interesting and relevant to this role?

Answer structure:

> I would pick three because they show the full shape of this role. First, Hive agentic AI MVPs show direct relevance to Agentic AI, applied research and MVP development. Second, the Colleague Channels LLM deployment framework shows I can take GenAI into production with performance, monitoring and operational discipline. Third, EDP Consumer Data Products show I can engineer inside Lloyds' complex governed environment using GCP, CI/CD, Terraform and data access controls.

Then give 30 seconds on each:

- Hive: Google ADK, Gemini, MCP, Analysis Agent, context-window experiments, token tracking.
- Colleague Channels: prefix caching, async batching, 91% latency reduction, 86% concurrency improvement, 1k+ requests/day, DynaTrace.
- EDP: dbt, BigQuery, Composer, Terraform, Jenkins, Spinnaker, Harness, secure views and policy tagging.

Close:

> Together, those experiences map well to this role because I can ideate, experiment, build, operationalise and understand the governance context.

Follow-ups:

Q: What was the hardest engineering challenge?

> In the LLM deployment work, the hardest part was making a probabilistic AI workload behave acceptably in a production-serving context. It required thinking about throughput, concurrency, latency, monitoring and failure behaviour, rather than only prompt quality.

Q: What was the hardest AI challenge?

> In the agentic work, the hard part is reliability. Agents can appear impressive in a demo but fail inconsistently under edge cases, long context or tool-call ambiguity. That is why I focused on context experiments, session design and token tracking.

Q: What would you do differently?

> I would introduce a more formal evaluation harness earlier, including golden test cases, adversarial prompts, tool-call accuracy, cost thresholds and human review rubrics.

### 2. Designing an AI Solution

Question:
What do you look for when designing an AI or ML solution?

Answer:

> I start with the business decision and risk level. What decision or workflow are we improving, who uses the output, and what happens if it is wrong? Then I define functional requirements, such as inputs, outputs, tools, integrations and user journey, and non-functional requirements, such as latency, availability, cost, security, explainability and auditability.
>
> For the AI method, I ask whether we need a deterministic workflow, a traditional ML model, RAG, tool use, or an agentic approach. I try to choose the simplest method that meets the need. Then I define evaluation before scaling: business metrics, model quality metrics, safety metrics, operational metrics and human feedback.
>
> For production, I think about data access, CI/CD, monitoring, fallbacks, ownership, support model and governance. In a bank, I would also design for customer harm prevention, data protection, audit logs, human-in-the-loop controls and explainability.

Follow-ups:

Q: Functional vs non-functional requirements?

> Functional requirements define what the system does: retrieve customer history, call a transaction tool, produce a structured summary, route a complaint. Non-functional requirements define how well and safely it must do it: latency, reliability, security, auditability, cost, scalability, maintainability and explainability.

Q: Batch vs streaming?

> Batch is suitable when work can be processed on a schedule, such as daily model scoring or data product refresh. Streaming is suitable when low-latency response to events matters. Streaming brings more complexity around ordering, retries, backpressure and observability, so I would only use it when the business need justifies it.

Q: How do you scale if demand is high?

> I would look at architecture bottlenecks first: model serving capacity, data access, tool-call latency, batching, caching, queueing and rate limits. Then I would use techniques such as horizontal scaling, async processing, batch optimisation, caching, request prioritisation, and graceful degradation.

Q: How do you monitor drift?

> For ML, I monitor input distribution drift, prediction distribution drift, label/performance drift when labels arrive, and business outcome drift. For LLM systems, I also monitor prompt changes, retrieval quality, tool-call failure, output quality, safety flags, user feedback and cost/latency drift.

### 3. Applied Research And Experimentation

Question:
How do you evaluate AI methods and technologies?

Answer:

> I try to turn research into small, decision-focused experiments. First I define the hypothesis: for example, "using session events improves agent reliability compared with passing full session history." Then I define what evidence would change the decision: accuracy, tool-call correctness, latency, cost, explainability, or operational complexity. I build the smallest PoC that tests that hypothesis, document the trade-offs, and recommend whether to continue, pivot or stop.
>
> In Hive, this is how I approached context-window degradation and session design. I was not just reading papers; I was translating research into practical engineering choices for agentic workflows in a Lloyds context.

Follow-ups:

Q: How do you keep up to date?

> I follow research papers and engineering write-ups, but I try not to treat novelty as value by itself. I look for patterns that solve real constraints: reliability, evaluation, latency, cost, integration and governance. I learn by building small prototypes and sharing what is useful with the team.

Q: How do you stop experimentation becoming endless?

> I define success criteria and decision points upfront. Each experiment should answer a question. If it does not improve the target metric or reduce uncertainty enough, I would stop or park it. MVP teams need curiosity with discipline.

### 4. Operational MVPs

Question:
How do you take a PoC toward an operational MVP?

Answer:

> I separate a PoC from an MVP. A PoC proves whether an approach can work. An operational MVP proves whether it can work inside the target environment with enough reliability, security, monitoring and user value to be taken seriously.
>
> My checklist would include: clear user journey, defined input/output contracts, access controls, evaluation harness, deployment path, monitoring, incident ownership, logging, human review points, failure modes, cost model, architecture review and a handoff plan for the engineering team that will scale it.
>
> My experience in Hive helps with agentic MVPs, Colleague Channels helps with production AI operations, and EDP helps with the governed platform route.

Follow-ups:

Q: What makes an MVP "operational"?

> It can be run by real users or teams in a controlled environment, with enough supportability to learn safely. It does not need every feature, but it does need monitoring, controls, ownership and clear limits.

Q: How do you hand over to engineering teams?

> I would provide architecture, code, deployment assumptions, runbooks, eval results, known limitations, risk controls, monitoring dashboards, backlog items and decision records. I would involve the receiving team early so the MVP does not become a surprise package.

### 5. Risk In A Regulated Environment

Question:
How do you manage operational and AI risk?

Answer:

> I manage risk as part of design. For operational risk, I think about reliability, availability, monitoring, deployment safety, rollback, incident ownership and data access. For AI risk, I think about incorrect outputs, hallucination, bias, explainability, safety, privacy, prompt injection, overreliance and auditability.
>
> In a bank, the key is to control the workflow around the model. That means least-privilege access, human-in-the-loop where decisions affect customers, clear source attribution, structured outputs, guardrails, evaluation sets, logging, versioning, monitoring and clear communication of limitations.
>
> I would also involve risk, architecture, data governance and platform teams early, especially for customer-facing or colleague decision-support use cases.

Follow-ups:

Q: What are guardrails?

> Guardrails are controls around an AI system that reduce unsafe or incorrect behaviour. They can include input filtering, tool permissioning, retrieval constraints, structured output validation, policy checks, human approval, monitoring and escalation paths.

Q: What is explainability for an LLM or agent?

> For LLM systems, explainability is less about exposing model internals and more about making the workflow traceable: what context was used, which tools were called, what sources support the answer, what rules were applied, and where uncertainty remains.

Q: How do you prevent prompt injection?

> Scope tools carefully, separate system instructions from retrieved content, treat retrieved text as untrusted, validate tool calls, restrict permissions, avoid exposing secrets, use allow-listed actions, and test adversarial prompts.

Q: How do you handle hallucinations?

> I would reduce the chance through retrieval/tool grounding, structured outputs, source citation, answer constraints, and refusing unsupported claims. I would reduce harm through human review, confidence signalling, monitoring and feedback loops.

### 6. Enterprise Architecture

Question:
How would you partner with Enterprise Architecture?

Answer:

> I would engage architecture early around integration, data access, security, platform fit, reuse and ownership. For Advanced AI Engineering, the risk is that a clever MVP becomes difficult to scale because it was not aligned with enterprise patterns. I would want architecture input before key decisions become expensive to change.
>
> My EDP experience gave me exposure to platform standards, Terraform, secure views, policy tagging and governed data assets. I would use that mindset when designing AI MVPs: clear contracts, clear ownership, clear integration points and documented trade-offs.

Follow-ups:

Q: What would you bring to an architecture discussion?

> A concise view of the business goal, user journey, data flows, model/tool choices, risk assessment, integration points, non-functional requirements, monitoring approach, and known trade-offs.

Q: What if architecture slows down innovation?

> I would try to bring them in at the right altitude. Early architecture input should accelerate safe scaling, not block learning. For experiments, I would keep the blast radius small. For operational MVPs, I would use architecture review to avoid creating future rework.

