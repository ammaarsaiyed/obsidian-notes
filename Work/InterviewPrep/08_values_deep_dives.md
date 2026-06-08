## Values Deep-Dive Scripts For Detailed Rubric Answers

Use these when the question is phrased as "how would you..." or when the interviewer asks for more detail. The culture rubric is looking for more than agreeing with the value. It wants specific behaviours, evidence, impact and self-awareness.

### How To Make Values Answers Rubric-Ready

For values answers, use this structure:

1. State the principle in plain language.
2. Give a concrete example or realistic lead-engineer scenario.
3. Explain what you personally did or would do, step by step.
4. Show how you balanced competing needs.
5. Explain the impact on customers, colleagues, delivery or risk.
6. Reflect on what you learned or how you would repeat it.

A good values answer should include verbs such as:

- noticed
- listened
- clarified
- supported
- challenged
- explained
- adapted
- followed up
- measured
- learned

Avoid sounding generic. For this role, always connect values back to AI delivery, customers, data, risk, colleagues or engineering standards.

### People-First: Prioritising People Over Process

Question:
LBG is a complex organisation. How would you prioritise people over process in this role?

Detailed answer:

> I would prioritise people over process by remembering that the process exists to protect customers, colleagues and the organisation, not to create friction for its own sake. In an Advanced AI Engineering role, that means I would think about three groups: the customers who may be affected by AI-supported decisions, the colleagues using the tools, and the engineers or platform teams who need to support what we build.
>
> First, I would start with the customer or colleague journey. Before designing an AI MVP, I would ask who is using it, what pressure they are under, what a good outcome looks like, and what harm could happen if the system is wrong. For a complaints agent, for example, the colleague may need support finding relevant facts quickly, but they still need clarity, source evidence and control over the final judgement.
>
> Second, I would make process understandable. In a bank, governance, architecture review and risk controls are necessary, but they can feel abstract. I would explain why each control matters: data access protects customers, evaluation protects decision quality, audit logs protect trust, and human review protects accountability.
>
> Third, I would remove avoidable friction for colleagues. That could mean creating reusable templates for evaluation, standard patterns for tool access, clear runbooks, or simple decision records so teams do not have to rediscover the same process on every MVP.
>
> Fourth, I would create space for people to raise concerns early. If a colleague says the workflow does not reflect how complaints are actually handled, or an engineer says a design will be hard to support, I would treat that as valuable evidence rather than resistance.
>
> The balance is that people-first does not mean bypassing controls. It means making sure controls serve people: customers are protected, colleagues are helped rather than burdened, and engineers inherit systems they can understand and support.

Likely follow-ups:

Q: Tell me about a time you achieved this.

> A strong example to use is your work translating ambiguous Hive agentic AI opportunities into testable workflows. Frame it as listening to business needs, understanding colleague workflow, building MVPs that support rather than replace decision-making, and adding reusable token tracking so other engineers could debug and manage cost more easily. Add a specific real moment if possible.

Q: How would you ensure others feel supported by you?

> I would set clear expectations, make myself available for difficult decisions, pair on complex work, give feedback early, and protect people from unclear priorities. Support also means telling the truth about risks rather than letting someone quietly carry uncertainty alone.

### People-First: Making It Safe To Bring Problems

Question:
In this role, how would you ensure people feel comfortable coming to you with a problem?

Detailed answer:

> I would make it safe through repeated behaviour. People judge whether they can bring you problems by how you react the first few times they do it. If the response is blame, impatience or minimising the issue, they will stop surfacing risks. If the response is calm, curious and constructive, they will raise things earlier.
>
> My first step would be to explicitly invite risks in normal delivery routines. In stand-ups, design reviews or check-ins, I would ask questions like: "What are we least confident about?", "What could fail in production?", "Where do we need another view?", and "What are we assuming?" That normalises uncertainty.
>
> Second, when someone brings a problem, I would separate the person from the issue. I would thank them for raising it, clarify facts, understand impact, and agree next steps. I would avoid jumping straight to fault-finding.
>
> Third, I would help them structure the problem. For example, if an agent is giving inconsistent answers, I would help break it down into prompt, context, retrieval, tool, model config, data and evaluation layers. That turns anxiety into a solvable engineering investigation.
>
> Fourth, I would follow through. If people raise issues and nothing happens, trust disappears. I would make actions visible, communicate decisions, and close the loop with the person who raised it.
>
> In AI engineering, this is especially important because systems can look impressive while hiding serious weaknesses. I want the team to feel proud of finding weaknesses early because that is what makes the final product safer.

Likely follow-ups:

Q: What if someone raises a problem late?

> I would still respond constructively. I would focus first on impact and mitigation, then afterwards discuss how we could have surfaced it earlier. The retrospective should improve the system, not humiliate the person.

Q: How would you know people trust you?

> I would see risks being raised earlier, more honest design discussions, fewer surprises near release, and people asking for help before they are stuck. I would also ask directly for feedback on whether my support style is working.

### Trusting Each Other: Admitting Mistakes

Question:
Tell me about a time you admitted a mistake. How would you help others recognise the importance of admitting mistakes?

Detailed answer:

> I would use an example where I moved quickly on a technical prototype and later realised that evaluation or observability needed to be stronger earlier. [ADJUST with the exact real example.] In applied AI, it is easy to focus on getting the demo working, but if you cannot measure quality, cost, latency or failure modes, the team cannot make a reliable decision.
>
> The mistake was not that I moved quickly. Moving quickly is often necessary in MVP work. The mistake was not making the evidence base visible early enough. Once I recognised that, I would raise it openly with the team, explain the risk, and propose a fix: add clearer metrics, logging, an evaluation set or review criteria.
>
> The important part is the tone. I would not present it as a disaster or hide it. I would say: "I think we have a gap here. The prototype works, but we need stronger evidence before we can recommend this as an MVP pattern." That shows ownership and keeps trust.
>
> To help others recognise the importance of admitting mistakes, I would model it consistently. I would discuss my own learnings in retros, thank people when they surface problems, and keep the focus on customer and system impact. In regulated AI work, unspoken mistakes can become customer harm, operational incidents or wasted delivery. Spoken mistakes become better controls and better engineering.

Step-by-step approach for mistake handling:

1. Acknowledge the issue early.
2. State the impact without exaggeration.
3. Take ownership of your part.
4. Propose immediate mitigation.
5. Identify root cause.
6. Add a process, test, monitor or pattern to prevent repeat.
7. Share the learning with the team.

Likely follow-ups:

Q: What if admitting the mistake affects confidence in you?

> I think hiding it would affect confidence more. Trust comes from being accurate, accountable and constructive. I would make sure I bring a mitigation plan, not just a confession.

Q: How do you avoid a blame culture?

> By asking what in the system allowed the mistake to happen, not just who touched it last. Blameless does not mean accountability-free. It means accountability is used to improve the system rather than create fear.

### Bold: Challenging The Status Quo Responsibly

Question:
Describe a time you had to weigh up the benefits of implementing something new with the cost and time of doing it.

Detailed answer:

> The strongest example is the production LLM deployment framework in Colleague Channels. The status quo was that GenAI serving on internal GPU infrastructure had performance and concurrency constraints. The opportunity was to use prefix caching and asynchronous batching to make the workload more production-ready before wider managed-model enablement.
>
> I did not treat the new approach as automatically better because it was newer. I looked at the problem first: response times, concurrent success, daily request volume and operational viability. Then I researched serving patterns and considered the cost: implementation time, added complexity, possible batching delays, monitoring needs and supportability.
>
> I made the case by connecting the technical change to measurable outcomes. Prefix caching could reduce repeated computation for shared prompt prefixes. Async batching could improve throughput under concurrent load. I implemented the approach in a controlled way and measured the impact: 91% reduction in response times, 86% improvement in concurrent success and support for over 1,000 GenAI requests per day.
>
> The broader lesson is how I would be bold in this role. I would challenge the status quo where there is a clear business or operational benefit, but I would make the risk visible, test the idea, measure outcomes and explain trade-offs in language stakeholders can understand.

How to explain the decision to others:

1. Start with the current pain point.
2. Explain the options, including doing nothing.
3. State the benefit and cost of the new approach.
4. Show how risk will be controlled.
5. Define success metrics.
6. Share measured results.
7. Be honest about what complexity remains.

Likely follow-ups:

Q: How would you avoid innovation theatre?

> I would require a clear hypothesis, success metric and decision point. A demo is useful only if it helps us decide whether to invest, pivot or stop. For this role, being bold means creating useful capability, not just impressive prototypes.

Q: What if stakeholders want the exciting AI option but you think it is wrong?

> I would explain the trade-off respectfully and offer an alternative. For example, "An agent is possible, but this workflow is stable and high-risk, so a deterministic workflow with a smaller LLM summarisation step may be safer and faster." Boldness includes challenging overuse of AI.

### Inclusive: Valuing Different Perspectives

Question:
Outline the steps you would take to create an environment where different and diverse perspectives are valued. How would you adapt your own behaviour?

Detailed answer:

> I would start from the belief that AI systems are better when different perspectives shape them. A model or agent can fail because of a technical issue, but it can also fail because the team misunderstood a colleague workflow, ignored an accessibility need, missed a customer harm, or designed around the assumptions of the loudest group in the room.
>
> Before meetings, I would share context and decision questions early so people who prefer to think before speaking can contribute. I would invite written input, especially for architecture and risk reviews. During meetings, I would make the decision criteria explicit and actively ask for missing perspectives: business process, engineering support, customer impact, risk, data quality, accessibility and operational edge cases.
>
> I would also pay attention to power dynamics. If a senior stakeholder or confident engineer dominates the discussion, I would slow the decision down and ask for other views. If someone raises a concern, I would explore it rather than treating it as negativity.
>
> After decisions, I would document the rationale so people can challenge or understand it later. Inclusion is not just who speaks in the meeting. It is whether their input can affect the outcome.
>
> I would adapt my own behaviour by listening before solving, checking whether I am using jargon unnecessarily, and being willing to change my mind. As someone who enjoys technical depth, I would need to make sure I do not accidentally make discussions inaccessible to non-specialists. I would explain AI concepts plainly and create room for people to say, "I do not understand this yet."

Step-by-step approach:

1. Share context before decisions.
2. Ask for written and verbal input.
3. Make decision criteria explicit.
4. Invite perspectives across engineering, business, risk, architecture and users.
5. Watch for dominant voices and missing voices.
6. Treat challenge as useful information.
7. Document the decision and rationale.
8. Review whether the decision worked in practice.

Likely follow-ups:

Q: How would you know the environment is inclusive?

> I would see broader participation, earlier surfacing of risks, fewer late objections, and people from different disciplines shaping decisions. I would also ask for feedback, because inclusion cannot be assessed only by the person leading the room.

Q: Tell me about adapting your communication style.

> For engineers, I might go into architecture, interfaces and failure modes. For business stakeholders, I would explain workflow impact, evidence, risk and operational value. For risk or governance partners, I would focus on controls, auditability and customer harm. The content is consistent, but the framing changes.

### Sustainable: Long-Term Impact And Reuse

Question:
How would you ensure you consistently consider the long-term impact of actions in decision making?

Detailed answer:

> I would make long-term impact part of the decision criteria rather than something considered after delivery. In engineering, short-term decisions often look cheaper because they hide future costs: support burden, duplicated work, unclear ownership, poor monitoring, governance gaps and rework when the MVP needs to scale.
>
> In EDP, I saw the value of building Consumer Data Products rather than one-off outputs. It required more discipline: dbt models, BigQuery transformations, Cloud Composer orchestration, Terraform, CI/CD, secure views, policy tagging and authorisation. That discipline creates assets that are reusable, trusted and easier to consume.
>
> I would apply the same thinking to Advanced AI Engineering. For every MVP, I would ask: what can be reused, who will own it, how will it be monitored, what controls are needed, what documentation will help the next team, and what technical debt are we intentionally accepting?
>
> Sustainability also includes cost and environmental awareness. For GenAI, token usage, model size, unnecessary long context and repeated calls all have cost and compute implications. My token-tracking work is relevant because measuring usage helps teams make better design choices.

Step-by-step approach:

1. Include long-term ownership in design discussions.
2. Identify reuse opportunities: components, templates, evals, tool contracts.
3. Make technical debt explicit and time-bound.
4. Consider cost, latency and compute usage.
5. Document decisions so future teams understand them.
6. Build monitoring so problems are visible after handoff.
7. Review whether the MVP should scale, stop or be redesigned.

Likely follow-ups:

Q: Tell me about prioritising long-term impact over short-term benefit.

> Use EDP data products. The short-term benefit would have been quick one-off transformations. The long-term choice was governed reusable products with orchestration, CI/CD and access control. Tie it directly to AI MVPs: do not create throwaway demos when the role is explicitly about MVPs that engineering teams can scale.

Q: How do you stop sustainable thinking becoming slow?

> I would right-size it. A research spike needs lightweight notes and clear learning. An operational MVP needs stronger controls. Sustainability does not mean maximum process; it means making future ownership and impact visible at the right level.

### Integrity: Speaking Up About Risk

Question:
Why is acting with integrity important for Lloyds? Tell me about a time you spoke out about something you did not believe was right.

Detailed answer:

> Integrity matters at Lloyds because customers trust the bank with money, data and decisions that can materially affect their lives. In AI engineering, integrity is especially important because a system can sound confident even when it is wrong. If we overstate capability, ignore weak evaluation or hide operational risk, the harm may fall on customers or colleagues rather than the project team.
>
> The type of example I would use is where a metric, demo or AI output looked promising, but I believed the evidence was not strong enough to support the decision being made. [ADJUST with exact real example.] I would speak up by being specific rather than dramatic: "This result is encouraging, but I do not think we have tested enough edge cases yet", or "The model score is strong overall, but we need to understand false negatives before using it in this workflow."
>
> I would bring evidence and a proposal. For example, add a validation set, involve SMEs, create adversarial cases, add monitoring, or keep the system as decision support rather than automation. That way, speaking up is constructive and tied to the bank's responsibility.
>
> If the concern was not accepted, I would escalate proportionately through the right channels, especially if there was customer, data or regulatory risk. Integrity means being collaborative, but it also means not quietly accepting something unsafe.

Likely follow-ups:

Q: How do you speak up without damaging relationships?

> I focus on the shared goal: customer safety, reliable delivery and trust. I avoid making it personal. I state the risk, evidence, impact and proposed next step. Most people respond well when challenge is precise and constructive.

Q: What if the deadline is tight?

> A tight deadline changes the mitigation, not the principle. We might reduce scope, keep a human review step, release to a smaller pilot group or add a manual control. I would not remove essential safeguards just to meet an arbitrary date.

### Trusting Others: Delegation With Support

Question:
Give an example of a time you trusted a person in your team to deliver aspects of a challenging project. How did you balance giving space and support?

Detailed answer:

> For this answer, use a real example where you delegated analysis, implementation, testing, dashboarding or stakeholder preparation. [ADJUST with exact example.] The key is to show that trust was active, not passive. You did not simply disappear and hope it worked. You gave ownership with context, guardrails and support.
>
> I would start by explaining the overall goal and why their part mattered. Then I would agree the outcome, constraints, quality bar, dependencies and check-in points. I would ask what support they wanted and what risks they saw. After that, I would give them space to decide the implementation rather than prescribing every detail.
>
> During delivery, I would check evidence rather than micromanage. For engineering work, that might mean reviewing a design note, looking at tests, checking a pull request, validating metrics or pairing on a tricky issue. If they hit a blocker, I would help remove it, but I would try not to take the work back unless the delivery risk required it.
>
> After delivery, I would recognise their contribution and reflect on what worked. That builds capability, confidence and trust for the next project.

Step-by-step approach:

1. Explain the purpose and context.
2. Agree the outcome and quality bar.
3. Clarify constraints, risks and dependencies.
4. Ask what support they need.
5. Set checkpoints.
6. Give autonomy over the method.
7. Review evidence and unblock.
8. Recognise contribution and capture learning.

Likely follow-ups:

Q: What if they are going in the wrong direction?

> I would intervene early but constructively. I would ask them to explain their reasoning, compare it to the agreed criteria, and help them adjust. Trust does not mean leaving someone unsupported until failure is inevitable.

Q: How do you delegate in high-risk AI work?

> I delegate bounded pieces with clear review points: evaluation design, tool contract, monitoring dashboard, prompt regression tests or data-quality checks. For high-impact decisions, I keep appropriate senior review and governance involvement.

### Bold And Trust Combined: Moving Fast Safely

Question:
How would you move fast in an advanced AI team while maintaining trust and safety?

Detailed answer:

> I would use controlled speed. The way to move fast safely is to reduce the blast radius and increase the quality of feedback. I would frame each experiment around a specific hypothesis, build the smallest useful version, and define the evidence needed to continue.
>
> For example, if we are testing an agentic workflow for complaints analysis, I would not start by giving it broad autonomy. I would begin with a controlled offline evaluation or colleague-facing prototype using approved data access, scoped tools and human review. I would measure tool-call accuracy, groundedness, unsupported claims, latency and usefulness before expanding scope.
>
> I would also make risks visible early. That means involving architecture, risk and platform partners at the right points, not waiting until the MVP is technically finished. It means logging, evaluation and rollback are considered part of speed, because they stop us losing time later to rework or incidents.
>
> My experience fits this because I have had to move quickly in Hive on agentic MVPs, but also bring production thinking from Colleague Channels and governed platform thinking from EDP.

Step-by-step approach:

1. Define a hypothesis.
2. Keep the first build narrow.
3. Use approved data and scoped tools.
4. Add evaluation and logging from the start.
5. Review with risk/architecture early enough to shape decisions.
6. Pilot with human oversight.
7. Expand only when evidence supports it.
8. Document what should scale, stop or change.

