## Project Story 1: Hive Agentic AI MVPs

Use for:

- Agentic AI
- Applied research
- Requirements analysis
- MVP building
- Tool use and MCP
- Regulated AI risk
- Stakeholder collaboration
- Moving fast through ambiguity

Situation:

> In Hive, I worked on agentic AI solutions for complaints and customer-resolution workflows. One area was the Analysis Agent, which analyses transaction and customer-history context to help determine whether suspicious transactions or relevant history may affect a complaint-resolution decision.

Task:

> The task was to explore whether agentic workflows could support complex colleague decisioning. The challenge was that these workflows involve sensitive customer data, complex context, and the need for reliable reasoning rather than a simple single-prompt demo.

Action:

> I worked with Google ADK, Gemini and MCP servers to build MVP agent workflows. I helped shape ambiguous requirements into testable workflow steps, including what information the agent needed, what tools it should call, what outputs were useful for colleagues, and where human review should remain. I ran experiments into context-window degradation and compared session events with session-history approaches to understand reliability and performance. I also built a reusable token-tracking module so teams could see token usage by request or workflow step, improving cost visibility, debugging and evaluation.

Result:

> The result was practical learning and reusable patterns for building agentic MVPs inside a bank environment. The work helped move the conversation from "can an LLM answer this?" to "what workflow, tools, evaluation and controls would make this operationally credible?"

Reflection:

> What I would keep doing is making experiments measurable early. What I would improve is formalising evaluation even earlier: golden datasets, adversarial cases, tool-call accuracy, hallucination checks, latency, cost, and clear acceptance thresholds.

Likely follow-ups:

Q: What makes something "agentic" rather than just an LLM call?

> An agentic workflow has some ability to plan, select tools, use external context, maintain state, and iterate toward a goal. A simple LLM call transforms an input into an output. In a bank, I would be careful not to overuse agents where a deterministic workflow is safer. I would use an agent when the task genuinely needs flexible reasoning, tool selection, or multi-step investigation.

Q: What are the main risks of agentic AI in this use case?

> Incorrect reasoning, tool misuse, hallucinated facts, overconfidence, data leakage, inconsistent outputs, poor auditability, bias, prompt injection, and colleagues trusting the answer too much. Controls include scoped tools, least-privilege access, structured outputs, citations to source data, deterministic checks, human review, logging, eval suites, and clear confidence or uncertainty handling.

Q: How would you evaluate the Analysis Agent?

> I would split evaluation into layers: tool-call correctness, retrieval/context correctness, reasoning quality, output format validity, safety and policy compliance, latency, cost, and colleague usefulness. I would use labelled historical cases where possible, synthetic edge cases for rare scenarios, adversarial tests for prompt injection, and human review for judgement-heavy outputs.

Q: What did you learn from context-window degradation?

> More context does not automatically mean better performance. Long context can dilute attention, increase cost and latency, and make failures harder to debug. The design implication is to curate context, summarise carefully, use retrieval or tool calls where appropriate, and measure whether extra context improves the answer.

Q: Why token tracking?

> Token tracking gives visibility into cost, latency drivers, prompt bloat, and unexpected workflow behaviour. For agentic systems, it also helps compare designs: for example, whether a multi-step tool workflow is worth the extra cost compared with a simpler retrieval or deterministic approach.

## Project Story 2: Production LLM Deployment Framework

Use for:

- Production AI engineering
- Performance optimisation
- Operational support
- GenAI deployment
- Python/async design
- Technical initiative
- Bold innovation with practical impact

Situation:

> In Colleague Channels, before Gemini enablement was available, we had internal GPU infrastructure supporting GenAI workloads. The challenge was to make LLM serving fast and reliable enough for production use.

Task:

> I needed to research and implement a production deployment approach that improved latency and concurrent success while still being maintainable and observable.

Action:

> I researched LLM serving patterns and implemented a framework using prefix caching and asynchronous batching. Prefix caching helped avoid recomputing repeated prompt prefixes, and async batching improved throughput by grouping concurrent work more efficiently. I also supported production monitoring through DynaTrace, tracking model performance, accuracy and speed.

Result:

> The framework reduced response times by 91%, improved concurrent success by 86%, and supported more than 1,000 GenAI requests per day. It gave the team a practical bridge before wider managed-model enablement.

Reflection:

> The main lesson was that GenAI production success is not just model quality. It is serving architecture, concurrency, latency, cost, monitoring, fallback behaviour, and supportability.

Likely follow-ups:

Q: Explain prefix caching simply.

> If many requests share the same initial prompt or instruction block, prefix caching avoids repeatedly processing that shared part from scratch. It is like keeping the common setup ready so each request only pays for the new part.

Q: Explain asynchronous batching.

> Instead of processing every request completely separately, asynchronous batching lets the system collect compatible requests and process them together without blocking the whole application flow. It can improve throughput and concurrent success when demand increases.

Q: How did you know the optimisation worked?

> I would look at before-and-after metrics: p50/p95 latency, error rate, timeout rate, concurrent success, throughput, GPU utilisation, and user or downstream system impact. The key outcome was the measured 91% response-time reduction and 86% concurrency improvement.

Q: What would you monitor in production?

> Latency, throughput, error rates, timeout rates, token usage, cost, model output quality, safety flags, drift in input distributions, tool-call failures, fallback rates, user feedback, and incident patterns. For a bank, I would also care about audit logs and whether outputs can be traced back to source context.

Q: What could go wrong with batching?

> It can increase tail latency if batches wait too long, create fairness issues between request types, or make failures affect multiple requests. The design needs timeouts, batch-size limits, prioritisation, and good observability.

## Project Story 3: EDP Consumer Data Products

Use for:

- Complex engineering environments
- GCP
- Data products
- CI/CD
- Security and governance
- Architecture handoff
- Regulated delivery

Situation:

> In EDP/DTAC, I worked in the Transformation and Consumption team building Consumer Data Products end to end on GCP. The aim was to create trusted, reusable and governed data assets for consumption.

Task:

> The work required production-quality transformation, orchestration, deployment and access controls, not just analysis. It needed to fit into platform standards and be safe for consumption by other teams.

Action:

> I built dbt models, macros and scripts using SQL, Python and Jinja. I used BigQuery for transformation logic, Cloud Composer for DAG orchestration, and CI/CD tools including Jenkins, Spinnaker and Harness for release flows. I wrote Terraform to provision GCP resources and created secure views using policy tagging, authorisation and access controls.

Result:

> The result was governed data products that could be reused and consumed more confidently. It gave me practical experience with the kind of platform, controls and release discipline that an Advanced AI Engineering MVP will need when it moves beyond a prototype.

Reflection:

> The key lesson was that reusable products need clear contracts: data quality, ownership, access, documentation, lineage, and release processes. The same principle applies to AI MVPs.

Likely follow-ups:

Q: What is a data product?

> A data product is a reusable, trusted, governed data asset with clear ownership, quality expectations, access controls and consumption patterns. In Lloyds terms, data can move through concepts like origin, foundational and consumer products, with increasing readiness for reuse.

Q: How does this help in an AI engineering role?

> AI systems depend on reliable data and governed access. My EDP experience helps me think about data contracts, lineage, policy tagging, secure views, and the route from a prototype to something other teams can trust and consume.

Q: What GCP services have you used?

> BigQuery, Cloud Composer, and GCP resource provisioning through Terraform. I have also worked with policy tagging, authorisation, secure views, and CI/CD release tooling around GCP delivery.

Q: How would you ensure scalability and reliability?

> I would design for clear data contracts, orchestration retries, idempotent transformations, observability, separation of environments, infrastructure as code, automated validation, and well-defined ownership. For AI systems, I would add model/tool observability, eval gates and fallback paths.

## Project Story 4: Fine-Tuned Model With 96% F1

Use for:

- ML model deployment
- Model evaluation
- Troubleshooting performance
- Responsible AI
- Evidence of ML depth

Situation:

> In Colleague Channels, I fine-tuned an AI model for a production use case. The team needed strong model performance and a deployment-ready approach.

Task:

> My task was to improve model performance using statistical and ML methods, evaluate it properly, and support production deployment.

Action:

> I worked through data preparation, model tuning, evaluation and error analysis. I focused on the right metric for the problem, using F1 score because the balance between precision and recall mattered. I compared approaches, analysed misclassifications, and iterated until the model achieved the best team performance.

Result:

> The model achieved a team-best 96% F1 score and was deployed to production.

Reflection:

> The lesson was that the headline score is only part of the story. For a production model, I also care about error distribution, fairness, explainability, monitoring, support process, and how the model affects the business workflow.

Likely follow-ups:

Q: Why F1 rather than accuracy?

> F1 is useful when both false positives and false negatives matter, especially if the classes are imbalanced. Accuracy can hide poor minority-class performance.

Q: How did you troubleshoot model performance?

> I would start with data quality and labels, then class balance, leakage, feature distribution, threshold choice, misclassification analysis, and whether the evaluation set represents production. In production, I would also look at drift and feedback loops.

Q: How would you explain the model to stakeholders?

> I would explain what decision it supports, what data it uses, where it performs well or poorly, what the key risks are, and how humans remain in control. I would use confusion-matrix examples rather than only abstract metrics.

## Project Story 5: Lead Data Scientist

Use for:

- Leadership
- Stakeholder engagement
- Presenting to senior stakeholders
- Data-driven problem solving
- Mentoring/technical direction

Situation:

> As Lead Data Scientist, I led divisional data science initiatives using Python, R and advanced SQL.

Task:

> The work involved taking business problems from unclear questions through data collection, cleaning, modelling, analysis and recommendations for senior stakeholders.

Action:

> I used methods such as regression, clustering, GLMs, causal inference, causal discovery and EDA. I provided technical direction on modelling choices, interpretation and analytical best practice. I also presented findings to senior stakeholders and finance-wide audiences.

Result:

> The work helped the division make more evidence-based decisions and gave me experience explaining technical trade-offs to non-technical audiences.

Reflection:

> For this role, the transferable part is not just the modelling. It is structuring ambiguity, leading technical direction, and communicating evidence in a way that helps decisions.

Likely follow-ups:

Q: How do you persuade stakeholders to use an ML solution?

> I start by understanding the decision they need to make, not by selling a model. Then I show evidence: baseline performance, model performance, business impact, risks, limitations, and how the workflow would change. I try to make adoption feel controlled through pilots, human review and measurable success criteria.

Q: How do you choose between modelling approaches?

> I consider the decision problem, data quality, interpretability needs, risk level, latency, cost, maintainability, and evaluation evidence. In regulated environments, the best model is not always the most complex model.

## Project Story 6: SRE / PagerDuty Dashboard

Use for:

- Production operations
- Observability
- Reliability
- Operational improvement
- Software engineering beyond AI

Situation:

> As a Software Engineer, I worked across engineering teams including SRE, gaining practical experience in DevOps, observability and reliability.

Task:

> One operational challenge was alert resolution time. Teams needed better visibility into alert patterns and response performance.

Action:

> I led a data-analysis initiative using JavaScript and the PagerDuty API to create a dashboard. It made incident and alert information more visible and actionable for the team.

Result:

> The dashboard contributed to an 85% decrease in alert resolution time.

Reflection:

> That experience shaped how I think about AI systems. If an AI MVP becomes operational, it needs the same reliability thinking as any other production system: monitoring, alerting, ownership, failure modes and continuous improvement.

