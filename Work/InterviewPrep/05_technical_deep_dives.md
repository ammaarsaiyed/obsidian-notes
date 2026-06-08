## Technical Deep-Dive Scripts For Grade E Rubric

Use these when the interviewer asks broad technical questions and the rubric expects detailed evidence. The aim is to show that you can operate as a lead engineer: you can diagnose the real problem, choose an approach, build it, manage risk, support production, influence others and reflect on trade-offs.

### How To Make Technical Answers Rubric-Ready

For most technical questions, use this pattern:

1. Define the business problem and user workflow.
2. State the technical challenge and why it was not trivial.
3. Explain your role and decisions clearly.
4. Describe the method step by step, including alternatives considered.
5. Include risk, governance, security and operational constraints.
6. Give evidence: metrics, tools, scale, outcomes, deployment, stakeholder impact.
7. Close with what you learned or would improve.

A strong lead-engineer answer should sound like:

> I did not just implement a component. I clarified the requirement, made trade-offs explicit, selected the appropriate pattern, built or guided the implementation, measured whether it worked, considered production and risk, and helped others reuse or understand the approach.

### Deep Dive: Engineering An AI Solution In A Complex Environment

Question:
Talk me through how you would engineer an AI solution in a complex regulated environment.

Rubric intent:
They want evidence of solution engineering, not just model knowledge. Cover requirements, architecture, SDLC, controls, evaluation, monitoring and handoff.

Detailed answer:

> I would start by separating three questions: should this use AI, what type of AI pattern is appropriate, and what would make it safe enough to operate in Lloyds. In a regulated environment, I would not begin with the model. I would begin with the workflow, the user, the decision being supported, the data involved and the harm if the system is wrong.
>
> My first step would be requirements discovery. I would work with business stakeholders and engineers to understand the current process, pain points, users, inputs, outputs, decision points, volumes, latency expectations and known edge cases. I would distinguish functional requirements, such as "retrieve relevant transaction history and produce a structured summary", from non-functional requirements, such as latency, availability, auditability, access control, explainability, cost and support model.
>
> The second step is choosing the simplest appropriate pattern. If the requirement is stable and rule-based, I would prefer deterministic logic. If it is prediction on structured data, a traditional ML model may be better. If the system needs grounded answers from internal documents, I would consider RAG. If it needs multi-step reasoning and controlled tool use, I would consider an agentic workflow. My Hive experience is directly relevant here because I have worked on agentic AI MVPs using Google ADK, Gemini and MCP servers, where tool use, state, context management and evaluation matter more than a single prompt.
>
> The third step is architecture. I would map the data flow, model or agent flow, tool interfaces, access boundaries, deployment path and integration points. For an agentic complaints workflow, that might mean a colleague-facing interface, an orchestration layer, scoped MCP tools for transaction and customer-history access, retrieval over approved policy documents, structured output validation, logging and a human-review step. My EDP placement helps here because I have built governed data products on GCP using BigQuery, dbt, Cloud Composer, Terraform and CI/CD tooling, so I am used to thinking about reusable assets, secure views, policy tagging, authorisation and release environments.
>
> The fourth step is evaluation. I would define evals before scaling. For a GenAI or agentic workflow, I would not rely only on subjective demos. I would test retrieval relevance, tool-call correctness, answer groundedness, unsupported claims, output schema validity, safety, latency, cost and colleague usefulness. If labelled data exists, I would use historical examples. If not, I would use SME review, synthetic edge cases, adversarial prompts, pairwise comparisons and rubric-based scoring.
>
> The fifth step is operational readiness. I would design for CI/CD, environment separation, versioning of prompts/tools/configuration, automated tests, monitoring, runbooks, rollback and ownership. My Colleague Channels work is useful evidence here: I worked on a production LLM deployment framework using prefix caching and async batching, reducing response times by 91%, improving concurrent success by 86%, supporting over 1,000 GenAI requests per day and monitoring through DynaTrace.
>
> The final step is handoff and scaling. An Advanced AI Engineering MVP should not be a clever artefact that only the original builder understands. I would produce architecture notes, decision records, evaluation evidence, risk/control mapping, deployment assumptions, known limitations, dashboards and a backlog for scaling. I would involve platform, risk, architecture and receiving engineering teams early so handoff is a gradual collaboration rather than a late surprise.

Short close:

> That is the mindset I would bring to this role: move quickly, but make the path from research to operational MVP visible, measurable and safe.

Likely follow-ups:

Q: What are the first things you would ask the stakeholder?

> I would ask what decision or workflow they want to improve, who uses the output, what data is available, what happens if the output is wrong, what success looks like, what constraints already exist and who owns the process today. Those answers determine whether we are building a demo, an MVP or a production candidate.

Q: What makes the environment complex?

> Lloyds has sensitive customer data, regulatory obligations, platform standards, existing business workflows, multiple engineering teams, architecture governance, risk controls and high expectations around reliability. The technical design has to respect all of that, otherwise the AI work will not scale beyond experimentation.

Q: What would make you stop an AI use case?

> I would stop or reframe it if the risk of customer harm is too high, the data access cannot be justified, the output cannot be evaluated, stakeholders want automation without accountability, or a simpler deterministic approach solves the problem with lower risk.

### Deep Dive: Troubleshooting Model Or AI System Performance

Question:
How do you troubleshoot model performance issues, especially when there are multiple possible causes?

Rubric intent:
They want structured diagnosis under pressure. Show you can separate model quality from system performance, data quality, deployment changes and business interpretation.

Detailed answer:

> I would start by defining what "performance" means in that situation. It could mean model quality, such as precision, recall or F1. It could mean system performance, such as latency, throughput or timeout rate. It could mean business performance, such as poor adoption or weaker complaint outcomes. A common mistake is treating all of these as one problem.
>
> My first step would be triage. I would check the symptom, when it started, the blast radius, whether it affects all users or specific segments, whether there were recent changes, and whether the issue is urgent enough to trigger rollback or mitigation. I would look at dashboards, logs, release history, model/prompt versions, data pipeline status and user feedback.
>
> Then I would isolate the layer. At the data layer, I would check schema changes, missingness, data freshness, class balance, label quality, input distribution drift and leakage. At the model layer, I would check prediction distribution, confusion matrix, thresholds, subgroup performance and error examples. At the GenAI layer, I would check prompt changes, retrieval quality, tool-call failure, context length, hallucination rate, output schema errors and safety refusals. At the system layer, I would check latency, concurrency, rate limits, queueing, dependency failures and resource utilisation.
>
> I have used this mindset in different contexts. In the fine-tuned model work, the important question was model quality, so I focused on F1, error analysis and improving the balance of precision and recall until we achieved a team-best 96% F1 score and deployed the model. In the LLM deployment framework, the issue was not just model quality but serving performance, so I researched prefix caching and async batching and measured improvements in response time and concurrent success. In EDP, troubleshooting is more about pipeline correctness, orchestration, access controls and data-quality issues.
>
> Once I identify the likely cause, I would apply the safest mitigation first. That might mean rollback, disabling a risky feature, adjusting a threshold, refreshing a data product, falling back to a simpler workflow, rate-limiting requests, or adding human review. Then I would do the deeper fix, add regression tests or monitoring so the issue is caught earlier next time, and communicate clearly to stakeholders.

Step-by-step checklist:

1. Define the metric that has degraded.
2. Confirm whether the issue is real or a measurement artefact.
3. Identify the time window and affected users/workflows.
4. Check recent changes: data, code, model, prompt, tools, infrastructure.
5. Split diagnosis into data, model, GenAI workflow, system and business layers.
6. Mitigate customer or colleague impact quickly.
7. Fix root cause and add regression coverage.
8. Document the learning and improve monitoring.

Likely follow-ups:

Q: What if you have multiple incidents at once?

> I would prioritise by customer impact, regulatory risk, operational severity and reversibility. I would make ownership clear, keep a visible incident log, communicate status regularly and avoid context-switching one person across every workstream. As a lead engineer, part of the job is creating order so the team can solve the right things first.

Q: How would you troubleshoot an agent that gives inconsistent answers?

> I would fix the evaluation harness first so inconsistency is measurable. Then I would examine prompt/version changes, context construction, retrieval results, tool outputs, temperature/configuration, session state, output schema failures and edge cases. I would reduce variability by tightening instructions, using structured outputs, grounding in tools or sources, reducing irrelevant context and adding deterministic checks where possible.

Q: How would you explain performance issues to stakeholders?

> I would separate impact from cause. For example: "The issue affects this workflow and creates this risk. We have mitigated it by doing X. Our current hypothesis is Y. We will confirm through these checks and report back by this time." That builds trust and avoids either overclaiming or hiding uncertainty.

### Deep Dive: Responsible AI And Governance

Question:
How do you ensure responsible AI practices and governance?

Rubric intent:
They want practical controls, not slogans. Show that you understand reliability, security, safety, explainability, fairness, privacy, human accountability and audit.

Detailed answer:

> I think responsible AI starts before implementation. The first thing I would do is classify the use case by risk: who is affected, what decision is being influenced, what data is used, whether customers could be harmed, whether the output is advisory or automated, and what regulation or internal governance applies.
>
> Then I would map harms and controls. For a complaints or customer-resolution agent, harms could include incorrect advice to a colleague, missed evidence, unfair treatment, leakage of sensitive customer data, prompt injection, hallucinated policy interpretation or overreliance on the output. For each harm, I would define a control: least-privilege access, human review, source citations, structured outputs, refusal rules, policy checks, logging, eval cases, monitoring or escalation.
>
> The next step is data governance. I would confirm data provenance, permissions, retention, data classification and whether the model or tool is allowed to process that data. My EDP experience is useful here because I have worked with secure views, policy tagging, authorisation and governed data products. For AI systems, those same controls need to apply to retrieval and tool access, not just tables.
>
> I would also build explainability into the workflow. For LLMs and agents, explainability is not about pretending we can inspect every model parameter. It is about traceability: what sources were retrieved, what tools were called, what customer facts were used, what policy was referenced, what output was generated and what uncertainty remains.
>
> Evaluation is the next governance control. I would use golden cases, edge cases, adversarial tests, SME review and operational metrics. I would test not just correctness but unsupported claims, unsafe outputs, subgroup performance where relevant, prompt injection, output schema validity and whether the human reviewer can understand the recommendation.
>
> Finally, I would treat governance as a lifecycle. Before release, there should be risk review, architecture input, security review and clear acceptance thresholds. After release, there should be monitoring, incident routes, drift checks, user feedback, periodic revalidation and a clear owner.

Practical control list:

- Use-case risk classification and customer harm assessment.
- Data access review and least-privilege permissions.
- Approved models, tools and deployment environments.
- Prompt, model, tool and retrieval versioning.
- Structured outputs and schema validation.
- Source grounding and citations.
- Human-in-the-loop for customer-impacting decisions.
- Evaluation set with normal, edge and adversarial cases.
- Monitoring for quality, safety, latency, cost and tool failures.
- Audit logs and traceability.
- Runbook, escalation path and rollback criteria.

Likely follow-ups:

Q: What is the difference between safety and explainability?

> Safety is about preventing or reducing harmful behaviour. Explainability is about making the system understandable and challengeable. They overlap, because a system that cites sources and exposes uncertainty is safer, but they are not identical.

Q: What would you log?

> I would log request metadata, model/prompt/tool versions, retrieved source references, tool calls, structured outputs, validation failures, latency, token usage, safety flags and user feedback. I would be careful not to log unnecessary sensitive content, and I would align logging with data retention and privacy rules.

Q: How do you avoid overreliance?

> Through UX and process design. The output should be framed as decision support, not final judgement. It should show evidence, uncertainty and next checks. For high-impact decisions, the colleague must remain accountable and the workflow should require review or approval.

### Deep Dive: Choosing Between RAG, Fine-Tuning, Tool Use And Agents

Question:
How do you decide which GenAI pattern to use?

Rubric intent:
They want evidence that you understand established patterns and can evaluate tools and approaches.

Detailed answer:

> I would choose the pattern based on the job the system needs to do. If the main issue is that the model needs internal or current knowledge, I would usually start with RAG because it grounds the answer in approved sources and can be updated without retraining. If the issue is output style, classification consistency or domain adaptation, fine-tuning might be useful, but I would need enough high-quality examples and a clear evaluation case. If the model needs to interact with live systems or structured data, tool use is more appropriate. If the task requires multi-step reasoning, dynamic tool selection and state management, then I would consider an agentic workflow.
>
> In my experience, these patterns are often combined. In Hive, agentic workflows can use tools through MCP servers and can also use retrieved context. In Colleague Channels, performance patterns like prefix caching and async batching mattered because the chosen AI approach had to be served reliably. In the fine-tuned model example, traditional model evaluation with F1 mattered because the task required measurable classification performance.
>
> I would be cautious about jumping straight to the most complex pattern. A lead engineer should be able to say, "This does not need an agent" if a smaller pattern is safer and easier to scale.

Decision guide:

| Need | Preferred pattern | Watch-outs |
|---|---|---|
| Answer from internal documents | RAG | Retrieval quality, permissions, stale documents, citation accuracy |
| Consistent classification or style | Fine-tuning or traditional ML | Data quality, labels, drift, governance |
| Use live structured systems | Tool use | Permissions, schemas, failure handling, audit |
| Multi-step investigation | Agentic workflow | Evaluation difficulty, state, tool misuse, inconsistency |
| Stable rule-based process | Deterministic workflow | Maintainability of rules, exception handling |

Likely follow-ups:

Q: When would you fine-tune instead of using RAG?

> I would fine-tune when the desired behaviour is a repeatable pattern learned from examples, such as classification, extraction style or domain-specific phrasing. I would use RAG when the model needs factual knowledge from changing documents or internal sources. Fine-tuning does not reliably make a model know all internal facts, and RAG does not automatically teach a model a new behaviour.

Q: When is an agent worth the complexity?

> When the task genuinely requires flexible sequencing, tool choice, intermediate reasoning and state. For example, analysing a complaint case may require checking transactions, retrieving policy, comparing timelines and deciding what evidence is missing. If the sequence is always the same, a deterministic workflow with LLM steps may be better.

### Deep Dive: Building And Supporting Operational MVPs

Question:
How would you build an operational MVP that engineering teams can scale?

Rubric intent:
They want evidence that you understand the gap between a PoC and something a platform team can own.

Detailed answer:

> I would start by defining what the MVP is proving. A PoC proves technical feasibility. An operational MVP should prove value, risk controls and a route to scale. For this role, I would expect an MVP to be usable in a controlled environment, observable, evaluated and understandable by the team that may inherit it.
>
> I would build it in slices. The first slice would be the thinnest end-to-end workflow: input, retrieval or tools, AI step, structured output and human review. The second slice would add evaluation and logging. The third would harden integration, security, monitoring and deployment. That keeps learning fast while avoiding a throwaway demo.
>
> I would keep engineering standards visible from the start: source control, code review, tests, environment configuration, secrets handling, infrastructure assumptions, versioned prompts, versioned tool contracts and a repeatable deployment path. The MVP does not need enterprise scale on day one, but it does need to avoid decisions that make enterprise scale impossible.
>
> I would also involve the future owning team early. I would ask what languages, platforms, observability tools, deployment route and support patterns they use. The handoff package should include architecture diagrams, ADRs, evaluation results, monitoring dashboards, runbook, risk controls, known limitations and backlog. That makes the MVP a bridge to delivery, not a separate island.

Operational MVP checklist:

- Clear user group and workflow.
- Measurable success criteria.
- Minimal end-to-end implementation.
- Security and data access controls.
- Evaluation harness and acceptance thresholds.
- Versioned prompts, model config and tools.
- Tests for business logic, integrations and output schemas.
- Monitoring dashboard and audit logging.
- Failure modes and fallback behaviour.
- Cost and latency profile.
- Runbook and support model.
- Handoff documentation and backlog.

Likely follow-ups:

Q: What would you compromise on in an MVP?

> I would compromise on breadth of features, volume, polish and automation. I would not compromise on customer safety, data access, auditability, basic evaluation, or clarity that the output is decision support where appropriate.

Q: How do you avoid over-engineering?

> I would define the learning goal for each build slice. If a feature does not reduce uncertainty, prove value, reduce risk or support handoff, I would challenge whether it belongs in the MVP.

### Deep Dive: Lead Engineer Technical Leadership

Question:
How do you operate as a lead engineer in an applied AI team?

Rubric intent:
They want evidence of leadership, mentoring, stakeholder direction and technical standards.

Detailed answer:

> I would see my role as creating technical clarity and raising the quality of decisions across the team. In applied AI, there is often ambiguity: the technology is changing, stakeholders may have broad ideas, and demos can be misleading. A lead engineer needs to turn that ambiguity into a small number of well-framed options, experiments and delivery decisions.
>
> Practically, I would do that through design reviews, clear written decision records, reusable templates and pairing. For example, I would want the team to have shared patterns for agent evaluation, prompt and tool versioning, human-in-the-loop design, token/cost tracking, logging and risk assessment. My reusable token-tracking work in Hive is an example of the sort of engineering contribution I like: it makes future work easier and more observable for others.
>
> I would also mentor through questions rather than just answers. If someone proposes an agentic workflow, I would ask: what task needs agency, what tools are in scope, how will we evaluate tool selection, what happens when a tool fails, what is the human review point, and what is the rollback plan? That helps engineers build the habit of thinking beyond the happy path.
>
> With stakeholders, I would translate uncertainty into choices. For example, "We can build a quick demo in two weeks, but to make this an operational MVP we also need evaluation, data access controls and monitoring. Here are the trade-offs." That keeps delivery moving while making risk visible.

Likely follow-ups:

Q: How do you raise standards without slowing people down?

> I would provide lightweight patterns, examples and checklists so quality is easier to achieve. I would keep reviews focused on the risks that matter: correctness, security, reliability, maintainability and customer impact. The goal is not bureaucracy; it is making good engineering the default path.

Q: How would you support someone less experienced with GenAI?

> I would pair on a small real task, explain the mental model, give them a safe area to build, and review their work against a clear rubric. I would also help them understand failure modes, because GenAI can look good before it is good.

