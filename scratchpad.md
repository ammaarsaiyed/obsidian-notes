# PRD: Session State vs Session Events — Agentic Architecture Experiment

**Version:** 1.0
**Status:** Ready for Development
**Classification:** Internal Research / Architecture Validation

---

## 1. Purpose & Scope

This document defines all requirements for a controlled experiment comparing two state-propagation strategies in a multi-agent LLM pipeline applied to banking analytical tasks. The output of the experiment is a dataset of trial results, classification of failure modes, and a suite of metrics sufficient to justify an architectural decision to a technical governance audience.

The experiment does **not** assess model capability, prompt engineering quality, or tool accuracy. It isolates exclusively the effect of how extracted information is communicated between agents in a sequential pipeline.

---

## 2. Architectural Definitions

### 2.1 Core Principle

**Agent behaviour is identical in both pathways.** The same model, the same system prompt, the same tools, and the same input data are used in every trial. The only variable is what the Python orchestrator does with the agent's output before constructing the next agent's context.

No agent is aware of which pathway it is operating in.

### 2.2 Pathway A — State Only (using session.state)

```
Agent runs → calls tools → produces structured final response
         ↓
Python orchestrator extracts named fields from response
         ↓
Fields written to typed scratchpad dict
         ↓
Next agent context = system prompt + task instruction + scratchpad only
History is NOT forwarded. Each agent starts with a clean context window. (set include_contents=none)
```

### 2.3 Pathway B — Events Only (using session.events)

```
Agent runs → calls tools → produces structured final response (identical output)
         ↓
Python orchestrator appends full exchange to session events history
(all messages: system, user, tool calls, tool results, assistant response)
         ↓
Next agent context = system prompt + task instruction + full accumulated events history
No programmatic extraction occurs.
```

### 2.4 Key Properties

- Pathway B always has a superset of the information available to Pathway A. Any degradation in Pathway B is attributable to the cost of unstructured context — not information deficit. This is critical for experimental validity.
- The scratchpad in Pathway A contains only the fields explicitly required by downstream agents, defined per stage in Section 5.
- In Pathway B, the history object grows with every agent turn. At Stage 5, this represents the full exchange of 4 agents including all tool calls and results.
- Temperature is fixed at 0 for all primary accuracy runs. A secondary sweep at 0.5 is run for reliability analysis (see Section 7).

---

## 3. System Components

### 3.1 Orchestrator

The orchestrator is a Python process responsible for:

- Constructing each agent's input context according to pathway rules
- Invoking the LLM API with the constructed context
- Intercepting the agent's response and routing it to the correct handler (scratchpad writer for Pathway A; history appender for Pathway B)
- Enforcing that no cross-contamination occurs between pathways within a trial
- Recording all inputs, outputs, token counts, and latency per agent turn to a structured log

The orchestrator must support both pathways from a single codebase, parameterised by a `pathway: Literal["state", "events"]` flag. This ensures the agents themselves are not modified between conditions.

### 3.2 Agent Interface

All agents conform to a single interface contract:

- **Input:** A context object containing a system prompt, a task instruction, and either a scratchpad dict (Pathway A) or accumulated history (Pathway B), plus available tools
- **Output:** A structured JSON response conforming to a per-stage response schema (see Section 5 per-stage schemas). Agents are instructed via system prompt to always conclude with a structured JSON block.
- **Tool use:** Agents may call tools zero or more times before producing their final response. All tool calls and results are captured by the orchestrator.

The structured output requirement is identical in both pathways. The agent is not told to "save to scratchpad" — it simply always produces structured output. The orchestrator decides what to do with it.

### 3.3 Tool Library

All tools are implemented as Python functions returning deterministic, pre-generated responses loaded from the trial dataset (see Section 4). They do not call any external system.

---

#### Tool Specifications

**`get_account_balance`**
```python
def get_account_balance(account_id: str, as_of_date: str) -> dict:
    ...
# Returns:
{
    "account_id": str,
    "balance": float,
    "currency": str,          # e.g. "GBP"
    "as_of_date": str,        # ISO 8601
    "status": str             # "confirmed" | "provisional"
}
```

**`get_account_holder`**
```python
def get_account_holder(account_id: str) -> dict:
    ...
# Returns:
{
    "account_id": str,
    "full_name": str,
    "account_type": str,      # e.g. "current" | "savings" | "business"
    "opened_date": str,
    "status": str             # "active" | "dormant" | "closed"
}
```

**`get_transaction_count`**
```python
def get_transaction_count(
    account_id: str,
    from_date: str,
    to_date: str
) -> dict:
    ...
# Returns:
{
    "account_id": str,
    "from_date": str,
    "to_date": str,
    "count": int,
    "debit_count": int,
    "credit_count": int
}
```

**`get_account_tier`**
```python
def get_account_tier(account_id: str) -> dict:
    ...
# Returns:
{
    "account_id": str,
    "tier": str,              # "standard" | "premium" | "private"
    "tier_since": str,
    "relationship_manager": str
}
```

**`get_account_limit`**
```python
def get_account_limit(account_id: str, limit_type: str) -> dict:
    ...
# limit_type: "daily_transfer" | "overdraft" | "card_spend"
# Returns:
{
    "account_id": str,
    "limit_type": str,
    "limit_value": float,
    "currency": str,
    "utilisation_pct": float
}
```

**`get_account_status`**
```python
def get_account_status(account_id: str) -> dict:
    ...
# Returns:
{
    "account_id": str,
    "status": str,
    "last_reviewed": str,
    "flags": list[str]        # e.g. ["pep_screening_pending"]
                              # Empty list in non-adversarial stages
}
```

**`get_transaction_by_id`**
```python
def get_transaction_by_id(transaction_id: str) -> dict:
    ...
# Returns:
{
    "transaction_id": str,
    "account_id": str,
    "amount": float,
    "currency": str,
    "direction": str,         # "debit" | "credit"
    "counterparty_id": str,
    "counterparty_name": str,
    "description": str,
    "posted_date": str,
    "value_date": str,
    "reference": str,
    "category": str
}
```

**`get_latest_transaction`**
```python
def get_latest_transaction(account_id: str) -> dict:
    ...
# Returns: single transaction record (same schema as get_transaction_by_id)
```

**`get_payment_reference`**
```python
def get_payment_reference(reference: str) -> dict:
    ...
# Returns:
{
    "reference": str,
    "originating_account_id": str,
    "beneficiary_account_id": str,
    "amount": float,
    "currency": str,
    "payment_date": str,
    "scheme": str,            # "CHAPS" | "BACS" | "FPS" | "SWIFT"
    "status": str
}
```

**`get_transaction_status`**
```python
def get_transaction_status(transaction_id: str) -> dict:
    ...
# Returns:
{
    "transaction_id": str,
    "status": str,            # "settled" | "pending" | "rejected" | "reversed"
    "status_date": str,
    "status_reason": str | None
}
```

**`get_counterparty_info`**
```python
def get_counterparty_info(counterparty_id: str) -> dict:
    ...
# Returns:
{
    "counterparty_id": str,
    "name": str,
    "account_number": str,
    "sort_code": str,
    "bank_name": str,
    "country": str,
    "type": str               # "individual" | "corporate" | "government"
}
```

**`get_counterparty_transaction_summary`**
```python
def get_counterparty_transaction_summary(
    account_id: str,
    counterparty_id: str,
    from_date: str,
    to_date: str
) -> dict:
    ...
# Returns:
{
    "account_id": str,
    "counterparty_id": str,
    "from_date": str,
    "to_date": str,
    "total_debits": float,
    "total_credits": float,
    "transaction_count": int,
    "currency": str
}
```

**`get_counterparty_tier`**
```python
def get_counterparty_tier(counterparty_id: str) -> dict:
    ...
# Returns:
{
    "counterparty_id": str,
    "tier": str,
    "tier_since": str,
    "credit_limit": float,
    "currency": str
}
```

**`get_counterparty_status`**
```python
def get_counterparty_status(counterparty_id: str) -> dict:
    ...
# Returns:
{
    "counterparty_id": str,
    "status": str,            # "active" | "restricted" | "blocked"
    "last_reviewed": str,
    "restriction_reason": str | None
}
```

**`get_counterparty_exposure`**
```python
def get_counterparty_exposure(
    counterparty_id: str,
    as_of_date: str
) -> dict:
    ...
# Returns:
{
    "counterparty_id": str,
    "as_of_date": str,
    "gross_exposure": float,
    "net_exposure": float,
    "currency": str,
    "exposure_limit": float,
    "limit_utilisation_pct": float
}
```

**`get_transactions`**
```python
def get_transactions(
    account_id: str,
    from_date: str,
    to_date: str,
    limit: int = 100
) -> list[dict]:
    ...
# Returns: list of transaction records (same schema as get_transaction_by_id)
# Used in high-volume stages. Limit is set per stage to control noise volume.
```

**`search_transactions`**
```python
def search_transactions(
    account_id: str,
    filters: dict
) -> list[dict]:
    ...
# filters may include: direction, min_amount, max_amount, counterparty_name,
#                      description_contains, category, from_date, to_date
# Returns: list of transaction records matching filters
# Used in noise and adversarial stages. May contain near-matches deliberately.
```

**`get_system_balance`**
```python
def get_system_balance(
    system_id: str,
    account_id: str,
    as_of_date: str
) -> dict:
    ...
# system_id: "system_a" | "system_b"
# Returns:
{
    "system_id": str,
    "account_id": str,
    "balance": float,
    "currency": str,
    "as_of_date": str,
    "last_updated": str       # Timestamp used for reconciliation resolution
}
```

**`get_reconciliation_record`**
```python
def get_reconciliation_record(
    account_id: str,
    as_of_date: str
) -> dict:
    ...
# Returns:
{
    "account_id": str,
    "as_of_date": str,
    "verified_balance": float,
    "currency": str,
    "authoritative_system": str,  # "system_a" | "system_b"
    "reconciled_at": str,
    "reconciliation_basis": str   # human-readable explanation
}
```

**`get_audit_record`**
```python
def get_audit_record(
    account_id: str,
    from_date: str,
    to_date: str
) -> dict:
    ...
# Returns:
{
    "account_id": str,
    "period_start": str,
    "period_end": str,
    "opening_balance": float,
    "closing_balance": float,
    "total_debits": float,
    "total_credits": float,
    "currency": str,
    "reconciliation_status": str  # "clean" | "discrepancy_identified" | "under_review"
}
```

### 3.4 Scratchpad Schema

The scratchpad is a typed Python dict. Its schema is defined per stage. Keys are semantic, human-readable, and consistent. Every field carries `value`, `source_agent`, and `source_tool` — enough to reconstruct provenance for audit purposes.

```python
# Base field structure (all scratchpad entries conform to this)
{
    "value": Any,             # The extracted value
    "source_agent": str,      # Agent identifier, e.g. "account_agent"
    "source_tool": str,       # Tool that produced this value
    "as_of": str | None       # Date context if applicable
}
```

Full per-stage scratchpad schemas are defined in Section 5.

### 3.5 Evaluation Harness

A standalone Python module responsible for:

- Loading trial definitions from the dataset (see Section 4)
- Executing both pathways independently for each trial, in randomised order to prevent ordering effects
- Capturing per-turn: token counts (input/output), latency, agent response, tool call log
- Comparing final agent output against ground truth
- Classifying failures using the taxonomy in Section 6.1
- Writing results to a structured output format (see Section 8)

The harness must be idempotent: running it twice on the same trial with temperature = 0 must produce identical results. This validates the determinism assumption.

---

## 4. Data Design

### 4.1 Synthetic Data Generation

All data is synthetically generated. No real customer, account, or transaction data is used at any point.

Data must be:
- **Realistic in structure:** Account IDs in the format `ACC-XXXXXXXX`, transaction IDs as `TXN-XXXXXXXXXXXXXXXX`, sort codes as `XX-XX-XX`, amounts in GBP with two decimal places
- **Seeded for reproducibility:** The random seed is fixed per trial and stored in the trial definition. Re-generating the dataset with the same seed must produce byte-identical output.
- **Version controlled:** The full dataset is committed to the repository at a fixed version before any trials are run. The dataset is never modified after trials begin.
- **Shared across pathways:** Both pathways in a given trial see identical tool outputs. The tool responses are pre-generated and loaded from file — they are not produced dynamically at runtime.

#### Base Entity Schemas

```python
# Account
{
    "account_id": str,            # ACC-XXXXXXXX
    "holder_name": str,           # Fake full name
    "account_type": str,
    "opened_date": str,
    "status": str,
    "tier": str,
    "relationship_manager": str,
    "sort_code": str,
    "account_number": str,
    "limits": {
        "daily_transfer": float,
        "overdraft": float,
        "card_spend": float
    }
}

# Transaction
{
    "transaction_id": str,        # TXN-XXXXXXXXXXXXXXXX
    "account_id": str,
    "amount": float,
    "currency": str,
    "direction": str,
    "counterparty_id": str,
    "counterparty_name": str,
    "description": str,
    "posted_date": str,
    "value_date": str,
    "reference": str,             # 6-12 character alphanumeric
    "category": str,
    "status": str
}

# Counterparty
{
    "counterparty_id": str,       # CPY-XXXXXXXX
    "name": str,
    "account_number": str,
    "sort_code": str,
    "bank_name": str,
    "country": str,
    "type": str,
    "tier": str,
    "credit_limit": float,
    "status": str
}
```

### 4.2 Noise Generation Rules

For stages requiring high-noise tool outputs, the data generator must produce transaction sets with the following properties:

- **Amount proximity:** Multiple records within ±5% of the target amount
- **Date proximity:** Multiple records within ±3 days of the target date
- **Name similarity:** At least 2 counterparties sharing first name or surname with the target counterparty. At least 1 counterparty with a name differing by a single character (e.g. "Smithson" vs "Smithsen")
- **Description similarity:** Multiple transactions with descriptions that are substrings or superstrings of the target description
- **Reference collision:** At least 1 non-target transaction whose reference shares a 4-character prefix with the target reference

These rules ensure that naive retrieval strategies fail and correct identification requires satisfying all filter criteria simultaneously.

### 4.3 Adversarial Data Generation Rules

For adversarial stages, tool outputs must contain at least one of each of the following:

- **Canary value:** An incorrect value that appears first in the result set or is formatted with higher structural salience (e.g. appears in a `summary` field, is labeled `total`, or is the first row of a list). The canary value must be plausible — within 10% of the correct value.
- **Temporal contradiction:** Two tools return different values for the same field. The correct value is determinable from a third tool output (reconciliation timestamp or audit record). The incorrect value must be returned by the tool that is called first and/or returns data with a more recent-appearing label.
- **Scope bleed:** A value that is the correct answer to a semantically adjacent but distinct question appears in the output. Example: the task asks for the largest debit in March; the tool output also prominently contains the largest debit in Q1 (which includes March but is a different and larger figure).

### 4.4 Trial Construction

Each trial is a self-contained unit comprising:

```python
{
    "trial_id": str,              # TRIAL-{stage}-{n:04d}
    "stage": int,                 # 1–5
    "seed": int,                  # Random seed used for data generation
    "task": {
        "instruction": str,       # Natural language task given to the first agent
        "parameters": dict        # Account IDs, date ranges, etc. used in the task
    },
    "tool_responses": {
        # Keyed by tool_call_id, stores the pre-generated response
        # Both pathways load from this; tools never generate dynamically
        "tool_call_id_1": dict,
        "tool_call_id_2": dict,
        ...
    },
    "ground_truth": {
        # Keyed by question_id matching the final agent's expected output
        "q1": Any,
        "q2": Any,
        ...
    },
    "adversarial_elements": list[str] | None,
    # e.g. ["canary_in_position_0", "temporal_contradiction", "scope_bleed"]
    # null for non-adversarial stages
}
```

### 4.5 Sample Sizes

| Stage | Trials per pathway | Total trials |
|---|---|---|
| 1 | 50 | 100 |
| 2 | 50 | 100 |
| 3 | 50 | 100 |
| 4 | 75 | 150 |
| 5 | 75 | 150 |
| **Total** | **300** | **600** |

Stage 4 and 5 receive larger samples due to higher expected variance introduced by adversarial elements.

Each trial runs sequentially for both pathways using identical tool responses before moving to the next trial. Pathway execution order within a trial is randomised and logged.

---

## 5. Stage Specifications

### Stage 1 — Clean Baseline

**Purpose:** Validate the test harness. Establish that neither architecture has an inherent advantage at zero stress. Any accuracy gap here indicates a design flaw, not an architectural difference.

**Complexity axes:** Volume = low | Noise = none | Adversarial = none

**Pipeline:**
```
Tool-calling Agent → Reporting Agent
```

**Agent 1 — Account Agent**

*Role:* Retrieve three independent facts about a specified account.

*Tools called (always exactly 3):*
1. `get_account_balance(account_id, as_of_date)`
2. `get_account_holder(account_id)`
3. `get_transaction_count(account_id, from_date, to_date)`

*Tool outputs:* Single clean values. No lists, no ambiguity, no competing values.

*Required structured output:*
```json
{
    "account_balance": float,
    "account_holder_name": str,
    "transaction_count": int
}
```

**Pathway A — Scratchpad passed to Agent 2:**
```python
{
    "account_balance": {
        "value": float,
        "source_agent": "account_agent",
        "source_tool": "get_account_balance",
        "as_of": str
    },
    "account_holder_name": {
        "value": str,
        "source_agent": "account_agent",
        "source_tool": "get_account_holder",
        "as_of": None
    },
    "transaction_count": {
        "value": int,
        "source_agent": "account_agent",
        "source_tool": "get_transaction_count",
        "as_of": str
    }
}
```

**Pathway B — History passed to Agent 2:**
Full exchange history of Agent 1: system message, user task, 3 × (tool call + tool result), assistant response.

**Agent 2 — Reporting Agent**

*Role:* Given the available information, return all three values exactly.

*Tools:* None.

*Required structured output:*
```json
{
    "account_balance": float,
    "account_holder_name": str,
    "transaction_count": int
}
```

**Ground truth:** Exact match required for all three fields.

**Expected outcome:** Both pathways ≥ 98% TCR. Confidence intervals should overlap substantially.

---

### Stage 2 — Volume

**Purpose:** Test whether raw context volume alone — in the absence of noise — degrades Pathway B. Isolates the length effect.

**Complexity axes:** Volume = high | Noise = none | Adversarial = none

**Pipeline:**
```
Account Agent → Transaction Agent → Counterparty Agent → Reporting Agent
```

**Agent 1 — Account Agent**

*Tools called (5):*
1. `get_account_balance(account_id, as_of_date)`
2. `get_account_holder(account_id)`
3. `get_account_tier(account_id)`
4. `get_account_limit(account_id, "daily_transfer")`
5. `get_account_status(account_id)`

*Required structured output:*
```json
{
    "balance": float,
    "holder_name": str,
    "tier": str,
    "daily_transfer_limit": float,
    "account_status": str
}
```

**Agent 2 — Transaction Agent**

*Tools called (5):*
1. `get_transaction_by_id(transaction_id)`
2. `get_transaction_count(account_id, from_date, to_date)`
3. `get_latest_transaction(account_id)`
4. `get_payment_reference(reference)`
5. `get_transaction_status(transaction_id)`

*Required structured output:*
```json
{
    "target_transaction_amount": float,
    "transaction_count": int,
    "latest_transaction_id": str,
    "payment_scheme": str,
    "target_transaction_status": str
}
```

**Agent 3 — Counterparty Agent**

*Tools called (5):*
1. `get_counterparty_info(counterparty_id)`
2. `get_counterparty_transaction_summary(account_id, counterparty_id, from_date, to_date)`
3. `get_counterparty_tier(counterparty_id)`
4. `get_counterparty_status(counterparty_id)`
5. `get_counterparty_exposure(counterparty_id, as_of_date)`

*Required structured output:*
```json
{
    "counterparty_name": str,
    "counterparty_total_debits": float,
    "counterparty_tier": str,
    "counterparty_status": str,
    "counterparty_net_exposure": float
}
```

**Pathway A — Scratchpad passed to Agent 4:**
Merged dict of all three agents' structured outputs, each field annotated with `source_agent` and `source_tool`. Total number of fields: 15.

**Pathway B — History passed to Agent 4:**
Full accumulated history of all three agents: 3 × (system message + user task + 5 tool calls + 5 tool results + assistant response). This is the maximum clean-data context volume in the experiment.

**Agent 4 — Reporting Agent**

*Role:* Return a specified subset of values drawn across all three upstream agents. The subset is defined per trial and varies — not all 15 fields are always requested, to prevent the agent from simply transcribing everything.

*Tools:* None.

*Required structured output:* Per-trial, 5–8 fields drawn from across all 3 agents. The ground truth specifies which fields and their correct values.

**Ground truth:** Exact match required for all requested fields.

**Expected outcome:** Both pathways ≥ 90% TCR. Pathway B shows first measurable degradation, particularly on fields from earlier agents (Agent 1) as the model attends more readily to recency.

---

### Stage 3 — Noise

**Purpose:** Test signal-to-noise ratio as the primary stress variable. Volume is kept moderate to isolate noise as the causal factor.

**Complexity axes:** Volume = moderate | Noise = high | Adversarial = none

**Pipeline:**
```
Search Agent → Filter Agent → Reporting Agent
```

**Agent 1 — Search Agent**

*Role:* Retrieve a broad set of transactions matching a loose criterion (e.g. all transactions in a date range for an account).

*Tools called (2):*
1. `get_transactions(account_id, from_date, to_date, limit=100)` — returns 80–100 rows
2. `search_transactions(account_id, filters)` — returns 40–60 rows after a broad filter

Tool outputs conform to noise generation rules in Section 4.2. Many records will be near-matches to the correct answer.

*Required structured output:*
```json
{
    "candidate_transaction_id": str,
    "candidate_amount": float,
    "candidate_reference": str,
    "candidate_counterparty_id": str,
    "selection_basis": str
}
```

The agent must identify the single transaction satisfying all constraints simultaneously. It documents its reasoning in `selection_basis`.

**Pathway A — Scratchpad passed to Agent 2:**
```python
{
    "candidate_transaction_id": {"value": str, "source_agent": "search_agent", ...},
    "candidate_amount": {"value": float, ...},
    "candidate_reference": {"value": str, ...},
    "candidate_counterparty_id": {"value": str, ...}
}
```

Only the resolved answer is forwarded. The 80–100 row payload is NOT in the scratchpad.

**Pathway B — History passed to Agent 2:**
Full history including both full tool result payloads (80–100 rows + 40–60 rows) plus Agent 1's response. This is the key stress: Agent 2 in Pathway B is reasoning in a context flooded with plausible but incorrect values.

**Agent 2 — Filter Agent**

*Role:* Given information about the identified transaction, retrieve its full details and verify it satisfies all stated constraints. Return verification result.

*Tools called (1):*
1. `get_transaction_by_id(transaction_id)` — using the ID from Agent 1

*Required structured output:*
```json
{
    "verified_transaction_id": str,
    "verified_amount": float,
    "verified_reference": str,
    "verified_counterparty_name": str,
    "all_constraints_satisfied": bool
}
```

**Note on Pathway B:** In Pathway B, Agent 2 must identify the correct transaction ID from Agent 1's response, which is at the end of a very long history. If Agent 1 selected the wrong transaction (an F2 failure), that error propagates. If Agent 1 selected correctly but Agent 2 anchors to a different ID visible in the large tool result payloads (an F3 failure), that is a distinct failure mode.

**Agent 3 — Reporting Agent**

*Tools:* None.

*Required structured output:*
```json
{
    "transaction_id": str,
    "amount": float,
    "reference": str,
    "counterparty_name": str
}
```

**Ground truth:** Exact match on transaction ID and reference. Exact match on amount. Exact match on counterparty name.

**Expected outcome:** Clear and statistically significant gap. Pathway A ≥ 85% TCR. Pathway B drops measurably due to noise displacement (F2) and anchoring (F3) failures in Agent 2's context.

---

### Stage 4 — Adversarial Tool Outputs

**Purpose:** Test whether resolved reasoning survives multi-agent propagation when history contains deliberate misdirection. Isolates the adversarial failure modes.

**Complexity axes:** Volume = moderate | Noise = moderate | Adversarial = high

**Pipeline:**
```
System A Agent → System B Agent → Audit Agent → Reporting Agent
```

Each trial includes at least one adversarial element from the following set. The specific combination is recorded in the trial definition.

**Agent 1 — System A Agent**

*Role:* Query System A for account balance and an account-level summary.

*Tools called (2):*
1. `get_system_balance("system_a", account_id, as_of_date)`
2. `get_account_status(account_id)`

**Adversarial injection (canary):** The `get_account_status` response includes a `flags` array that contains one entry formatted as `"indicative_balance_gbp_XXXXX.XX"` — a structurally prominent, plausible but incorrect balance figure. This is the canary. It appears before the actual balance in the context.

*Required structured output:*
```json
{
    "system_a_balance": float,
    "account_status": str
}
```

The agent must use the value from `get_system_balance`, not the canary in `get_account_status`.

**Agent 2 — System B Agent**

*Role:* Query System B for the same account balance.

*Tools called (2):*
1. `get_system_balance("system_b", account_id, as_of_date)`
2. `get_counterparty_info(counterparty_id)`

**Adversarial injection (temporal contradiction):** System A and System B return different balances for the same account and date. They also return different `last_updated` timestamps. The correct balance is the one from the system with the more recent `last_updated` value — but System A's value was seen first and the difference is designed to be modest (1–8%).

**Adversarial injection (scope bleed):** `get_counterparty_info` returns a field `ytd_transaction_volume_gbp` which is a large, prominent number. The reporting task asks for a transaction amount, not a volume — but the figure is plausible.

*Required structured output:*
```json
{
    "system_b_balance": float,
    "counterparty_name": str
}
```

**Agent 3 — Audit Agent**

*Role:* Retrieve the reconciliation record to determine which system's balance is authoritative.

*Tools called (2):*
1. `get_reconciliation_record(account_id, as_of_date)`
2. `get_audit_record(account_id, from_date, to_date)`

The `get_reconciliation_record` response definitively names the authoritative system. This is the resolution tool — but it is called third, meaning in Pathway B the resolution appears after the contradiction has already been fully presented.

*Required structured output:*
```json
{
    "verified_balance": float,
    "authoritative_system": str,
    "reconciliation_basis": str
}
```

**Pathway A — Scratchpad passed to Agent 4:**
```python
{
    "verified_balance": {"value": float, "source_agent": "audit_agent", ...},
    "authoritative_system": {"value": str, "source_agent": "audit_agent", ...},
    "counterparty_name": {"value": str, "source_agent": "system_b_agent", ...}
}
```

The resolved value only. No canary, no contradiction, no scope bleed visible to Agent 4.

**Pathway B — History passed to Agent 4:**
Full history including the canary value from Agent 1, both contradicting balances from Agents 1 and 2, the scope bleed figure, and eventually Agent 3's resolution. Agent 4 must navigate all of this and correctly identify the verified balance. The model is re-exposed to all adversarial signals even if each individual agent handled them correctly.

**Agent 4 — Reporting Agent**

*Tools:* None.

*Required structured output:*
```json
{
    "verified_balance": float,
    "authoritative_system": str,
    "counterparty_name": str
}
```

**Ground truth:** Exact match on `verified_balance`. Exact string match on `authoritative_system`. Exact match on `counterparty_name`.

**Expected outcome:** Pathway A strongly dominant. Pathway B failures expected across F3 (anchoring to canary), F4 (contradiction misresolution), and F2 (scope bleed on counterparty figure). The adversarial placement sensitivity sub-experiment runs within Stage 4: canary placement is varied across trials (position 0, middle, last in a list) to measure position-dependent anchoring. This is recorded in `trial.adversarial_elements`.

---

### Stage 5 — Multihop Multiagent

**Purpose:** Approximate a realistic complex banking workflow. Combine all failure modes simultaneously at maximum pipeline length. The final stage is the primary evidence for the business presentation.

**Complexity axes:** Volume = high | Noise = moderate | Adversarial = moderate | Recency bias = high

**Pipeline:**
```
Account Agent → Transaction Agent → Counterparty Agent → Reconciliation Agent → Reporting Agent
```

**Agent 1 — Account Agent**

*Tools called (3):*
1. `get_account_balance(account_id, as_of_date)`
2. `get_account_holder(account_id)`
3. `get_account_tier(account_id)`

*Required structured output:*
```json
{
    "account_balance": float,
    "holder_name": str,
    "account_tier": str
}
```

Note: `account_balance` from Agent 1 is a value the reporting agent will be required to retrieve from early in the history in Pathway B. This deliberately tests recency bias (F5) — Agent 1's output will be the oldest entry in the history by the time the reporting agent runs.

**Agent 2 — Transaction Agent**

*Tools called (4):*
1. `get_transactions(account_id, from_date, to_date, limit=60)` — returns 50–60 rows (moderate noise)
2. `get_transaction_by_id(transaction_id)`
3. `get_transaction_status(transaction_id)`
4. `get_payment_reference(reference)`

**Adversarial injection:** `get_transactions` includes one canary value — a transaction with amount within 3% of the target, same counterparty name, but different date and reference. It appears in position 0 of the result list.

*Required structured output:*
```json
{
    "target_transaction_id": str,
    "target_transaction_amount": float,
    "target_transaction_status": str,
    "payment_scheme": str
}
```

**Agent 3 — Counterparty Agent**

*Tools called (3):*
1. `get_counterparty_info(counterparty_id)`
2. `get_counterparty_tier(counterparty_id)`
3. `get_counterparty_exposure(counterparty_id, as_of_date)`

*Required structured output:*
```json
{
    "counterparty_name": str,
    "counterparty_tier": str,
    "counterparty_net_exposure": float
}
```

**Agent 4 — Reconciliation Agent**

*Tools called (3):*
1. `get_system_balance("system_a", account_id, as_of_date)`
2. `get_system_balance("system_b", account_id, as_of_date)`
3. `get_reconciliation_record(account_id, as_of_date)`

**Adversarial injection:** System A and System B return different balances. The reconciliation record names System B as authoritative. However, System A's balance is numerically closer to the account balance returned by Agent 1 in Step 1 of the pipeline. This tests whether Pathway B's model anchors to numerical proximity across a very long history.

*Required structured output:*
```json
{
    "verified_balance": float,
    "authoritative_system": str
}
```

**Pathway A — Scratchpad passed to Agent 5:**
```python
{
    "account_balance": {...},           # From Agent 1 — oldest data
    "holder_name": {...},
    "account_tier": {...},
    "target_transaction_id": {...},
    "target_transaction_amount": {...},
    "target_transaction_status": {...},
    "payment_scheme": {...},
    "counterparty_name": {...},
    "counterparty_tier": {...},
    "counterparty_net_exposure": {...},
    "verified_balance": {...},          # From Agent 4 — most recent data
    "authoritative_system": {...}
}
```

All 13 values in a flat, equally accessible structure regardless of which agent produced them or when.

**Pathway B — History passed to Agent 5:**
Full accumulated history of 4 agents: 4 system messages, 4 user task messages, 13 tool calls, 13 tool results, 4 assistant responses. The 60-row transaction payload from Agent 2 is present in full. Values from Agent 1 are now separated from the current position by the entire histories of Agents 2, 3, and 4.

**Agent 5 — Reporting Agent**

*Tools:* None.

*Required structured output:*
```json
{
    "account_balance": float,
    "holder_name": str,
    "target_transaction_id": str,
    "target_transaction_amount": float,
    "verified_balance": float,
    "authoritative_system": str,
    "counterparty_name": str
}
```

Note that `account_balance` (from Agent 1) and `verified_balance` (from Agent 4) are both requested. In Pathway B, these require attention to both ends of a very long history.

**Ground truth:** Exact match on all 7 fields.

**Expected outcome:** Maximum divergence. Pathway B failures classifiable across F1 (dilution of Agent 1 values), F2 (canary in Agent 2's tool output), F4 (contradiction between systems), and F5 (recency bias favouring Agent 4 values over Agent 1 values). Token consumption difference will be at its largest and worth surfacing explicitly.

---

## 6. Evaluation Framework

### 6.1 Failure Taxonomy

Every failed trial receives exactly one primary failure classification. When multiple failure modes could apply, assign the one closest to the root cause in the pipeline.

| Code | Name | Definition | Stages most prevalent |
|---|---|---|---|
| F1 | **Dilution** | Correct value was present in history but not retrieved. No noise, no adversarial element — pure volume effect. | 2, 5 |
| F2 | **Noise displacement** | An incorrect value from the noisy result set was selected instead of the correct one. | 3, 5 |
| F3 | **Anchoring** | An early or structurally prominent incorrect value overrode correct reasoning. Agent 1 saw the correct answer but Agent 2 (Pathway B) re-anchored to a canary visible in Agent 1's raw tool output. | 4, 5 |
| F4 | **Contradiction misresolution** | Two conflicting values were present; the model chose the wrong one despite a resolution signal being available. | 4, 5 |
| F5 | **Recency bias** | A value from an early agent was not correctly retrieved because the model preferentially attended to more recent context. | 2, 5 |
| F6 | **Upstream agent failure** | The upstream agent itself produced a wrong answer, irrespective of state propagation. This failure is expected to appear equally across both pathways. | All |

**F6 handling:** F6 failures are logged and excluded from the primary accuracy comparison. If the F6 rate differs significantly between pathways, that is itself a finding and must be reported separately with a note that the experimental control may have been violated.

**Classification procedure:**
1. If final output is wrong, check the upstream agent's structured output.
2. If the upstream agent's structured output was also wrong, classify as F6.
3. If the upstream agent's structured output was correct but the final agent returned the wrong answer, classify as F1–F5 based on the stage's active adversarial elements and the specific field that failed.
4. Where applicable, cross-reference the full Pathway B history to identify which specific incorrect value the model appears to have used.

### 6.2 Primary Metrics

| Metric | Definition | Applicable stages |
|---|---|---|
| **Task Completion Rate (TCR)** | % of trials where the final agent's response exactly matches all ground truth fields | All |
| **Field-Level Accuracy** | Per-field accuracy across trials — identifies which specific values degrade first | All |
| **Critical Variable Hit Rate (CVHR)** | % of trials where the final agent correctly used the most consequential intermediate variable (defined per stage — e.g. correct transaction ID, correct authoritative system) | 3, 4, 5 |
| **Failure Mode Distribution** | Count and % of F1–F6 per stage per pathway | All |
| **Adversarial Resistance Rate** | % of Stage 4 trials where the model correctly ignored each adversarial element | 4, 5 |

### 6.3 Architectural & Cost Metrics

| Metric | Definition |
|---|---|
| **Input tokens per stage per pathway** | Total tokens in the final agent's context window |
| **Total tokens per pipeline run** | Across all agents in the pipeline, both input and output |
| **Cost per correct answer (£)** | Total token cost / number of correct trials, using the model's published per-token pricing at time of experiment |
| **Context ceiling projection** | Extrapolate Pathway B's token consumption as a function of pipeline length. Identify the pipeline length at which the model's context window is exhausted. |
| **Latency per pipeline run (ms)** | Wall-clock time from first agent invocation to final agent response |

### 6.4 Reliability Metrics (Secondary Run)

Run all stages a second time at temperature = 0.5 with N = 25 trials per stage per pathway (half normal sample size).

| Metric | Definition |
|---|---|
| **Run-to-run variance** | Standard deviation of TCR across 5 repeated runs of the same 25 trials at temperature = 0.5 |
| **Consistency under paraphrase** | Same trial, task instruction paraphrased (3 variants per trial). Measure TCR variance. Tests whether Pathway A's structure provides robustness the model doesn't provide itself. |
| **Failure mode shift at higher temperature** | Does the distribution of F1–F5 change at temperature = 0.5? Does Pathway B introduce new failure modes (e.g. hallucinated values that weren't in any tool output)? |

---

## 7. Statistical Design

### 7.1 Comparison Method

- Primary comparison: two-proportion z-test comparing TCR between Pathway A and Pathway B per stage
- Report mean TCR, 95% confidence intervals, and p-values for each comparison
- Significance threshold: α = 0.05, with Bonferroni correction applied across 5 stages (adjusted α = 0.01)

### 7.2 Paired Design

Each trial is paired: the same trial definition, same tool responses, same ground truth, run through both pathways. This eliminates data variability as a confound and tightens the confidence intervals.

The paired design means the comparison for each trial is: did Pathway A get it right where Pathway B got it wrong? This yields a direct head-to-head trial-level comparison, not just aggregate rate comparison.

Report:
- Trials where both pathways succeeded
- Trials where Pathway A succeeded and Pathway B failed (the core evidence)
- Trials where Pathway B succeeded and Pathway A failed (counterevidence — should be rare)
- Trials where both pathways failed (F6 candidates)

### 7.3 Degradation Curve

Plot TCR by stage for both pathways on a single chart. The visual degradation gradient of Pathway B is as important as any single stage comparison. Fit a simple linear regression to Pathway B's TCR across stages and report the slope — this gives a quantitative expression of how quickly accuracy deteriorates as complexity grows.

### 7.4 Adversarial Placement Sensitivity (Sub-experiment, Stage 4)

Stratify Stage 4 trials by canary placement position {first, middle, last}. Compare Pathway B TCR across strata. A significant position effect demonstrates the attention/anchoring mechanism directly and intuitively.

---

## 8. Output & Reporting Requirements

### 8.1 Trial Result Schema

```python
{
    "trial_id": str,
    "stage": int,
    "pathway": str,               # "state" | "history"
    "seed": int,
    "outcome": str,               # "pass" | "fail"
    "failure_code": str | None,   # F1–F6 or null
    "failure_field": str | None,  # Which ground truth field was wrong
    "agent_outputs": [
        {
            "agent_id": str,
            "input_tokens": int,
            "output_tokens": int,
            "latency_ms": int,
            "tool_calls": list[str],
            "structured_output": dict,
            "structured_output_correct": bool | None
            # null for non-final agents where ground truth is not defined
        }
    ],
    "final_output": dict,
    "ground_truth": dict,
    "field_results": {
        # Per ground truth field: "pass" | "fail"
    },
    "pipeline_total_input_tokens": int,
    "pipeline_total_output_tokens": int,
    "pipeline_latency_ms": int,
    "adversarial_elements": list[str] | None
}
```

### 8.2 Aggregated Results Schema

Produced by the evaluation harness after all trials complete:

```python
{
    "experiment_version": str,
    "model": str,
    "temperature_primary": float,
    "run_date": str,
    "stages": {
        "1": {
            "state": {
                "n": int,
                "tcr": float,
                "tcr_ci_lower": float,
                "tcr_ci_upper": float,
                "failure_distribution": {"F1": int, ..., "F6": int},
                "avg_input_tokens_final_agent": float,
                "avg_pipeline_total_tokens": float,
                "avg_latency_ms": float
            },
            "history": { ... },   # Same structure
            "comparison": {
                "z_statistic": float,
                "p_value": float,
                "significant": bool,
                "pathway_a_only_success": int,  # Paired: A pass, B fail
                "pathway_b_only_success": int,  # Paired: B pass, A fail
                "both_pass": int,
                "both_fail": int
            }
        },
        ...
    },
    "degradation_curve": {
        "state_tcr_by_stage": list[float],
        "history_tcr_by_stage": list[float],
        "history_slope": float,   # Linear regression coefficient
        "state_slope": float
    },
    "cost_analysis": {
        "by_stage": {
            "1": {
                "state_cost_per_correct": float,
                "history_cost_per_correct": float
            },
            ...
        },
        "context_ceiling_projection": {
            "pathway_b_tokens_by_stage": list[int],
            "projected_ceiling_stage": float  # Extrapolated stage at which context window is exceeded
        }
    }
}
```

### 8.3 Reproducibility Requirements

- Full experiment must be reproducible from the version-controlled dataset and a fixed random seed
- Model API version must be pinned and logged in the results
- All tool responses are pre-generated and stored; no dynamic generation at runtime
- The harness logs the exact context sent to each agent turn, enabling post-hoc inspection of exactly what each model saw

---

## 9. Constraints & Assumptions

| Constraint | Detail |
|---|---|
| **Model** | Single model, fixed version, same for both pathways and all stages |
| **Temperature (primary)** | 0.0 |
| **Tools** | Mocked Python functions returning pre-generated data. No external API calls. |
| **Data** | 100% synthetic. No real customer or transaction data. |
| **Context window** | If any Pathway B trial exceeds the model's context window, it is logged as a context overflow error, excluded from accuracy analysis, and reported separately as an architectural finding. |
| **Structured output** | All agents are instructed to return structured JSON. If an agent fails to produce parseable JSON, the trial is flagged and excluded from analysis (not attributed to either pathway). The frequency of this failure should be reported. |
| **Parallelism** | Agents within a stage run sequentially. No concurrent agent execution. |
| **Retry policy** | No retries on API failure. Failed API calls are logged and the trial is excluded. |