## System Design Practice

### Design An Agentic Complaints Analysis MVP

Prompt:
Design an agentic AI MVP for complaints analysis in Lloyds.

Answer:

> I would start by defining the colleague workflow. The MVP should support, not replace, complaint handlers. It should gather relevant context, analyse it against known policy or complaint categories, highlight suspicious or relevant transaction history, and produce a structured summary with sources and uncertainty.

Functional design:

- User selects or opens a complaint case.
- System retrieves authorised customer and transaction context.
- Agent uses scoped tools through MCP or equivalent interfaces.
- RAG retrieves relevant policy/process guidance.
- Agent produces structured output: facts found, sources, possible issues, recommended next checks, confidence/uncertainty, and escalation flags.
- Colleague reviews and decides.

Non-functional design:

- Low enough latency for colleague workflow.
- Strict access controls and audit logs.
- Explainable source references.
- Human-in-the-loop for decisions.
- Monitoring for tool failures, output quality, latency, cost and safety.
- Clear data retention and logging policy.

Evaluation:

- Historical complaint cases with known outcomes.
- Expert review by complaints SMEs.
- Tool-call accuracy.
- Source-grounding checks.
- Hallucination and unsupported-claim rate.
- Time saved and colleague satisfaction.
- Safety and policy compliance.

Risks and controls:

- Risk: hallucinated explanation. Control: source citations and unsupported-claim checks.
- Risk: excessive access to customer data. Control: least privilege and audit.
- Risk: colleague overreliance. Control: decision-support UX and human approval.
- Risk: prompt injection from notes/documents. Control: treat retrieved content as untrusted and restrict tools.
- Risk: inconsistent outputs. Control: structured schema, regression evals and monitoring.

Handoff:

- Architecture decision records.
- Code repository and deployment guide.
- Evaluation report.
- Monitoring dashboard.
- Known limitations.
- Risk/control mapping.
- Runbook and ownership proposal.

### Design A RAG Assistant For Internal AI Guidance

Answer:

> I would build a permission-aware RAG system over approved internal guidance, patterns and architecture documents. The key is not just vector search; it is document governance, freshness, access control, citation accuracy and feedback loops.

Core components:

- Document ingestion with ownership and freshness metadata.
- Chunking strategy based on document type.
- Embeddings and retrieval, potentially with reranking.
- Access filtering before retrieval or before response.
- LLM synthesis with citations.
- Feedback capture and eval set.
- Monitoring for failed searches, unsupported answers and stale content.

Trade-off:

> If guidance is highly structured and stable, search plus summarisation may be safer than a fully agentic approach. If the assistant needs to complete tasks across tools, an agent may be justified.

### Design A Model Monitoring Framework

Answer:

> I would monitor four layers: data, model, system and business outcome. Data metrics include schema, missingness, distributions and drift. Model metrics include prediction quality, calibration, F1/precision/recall or task-specific metrics. System metrics include latency, throughput, errors and cost. Business metrics include adoption, time saved, complaints outcomes or operational KPIs.
>
> For GenAI, I would add output quality, groundedness, citation correctness, safety flags, prompt/template version, retrieval quality and tool-call success.

