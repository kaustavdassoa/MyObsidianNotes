# 📚 STUDY GUIDE: Developing Evaluation Metrics for Agentic Harnesses

## 1. Concise Overview of the Topic

In Harness Engineering, moving from qualitative observation to rigorous quantitative evaluation requires shifting away from binary "Pass/Fail" metrics. Because agents interact with dynamic environments, invoke tools, and operate over long horizons, a single outcome score can mask dangerous intermediate actions, "lucky passes," or severe resource inefficiencies. To comprehensively measure a harness's true performance, engineers must develop a multi-dimensional set of Key Performance Indicators (KPIs) that capture task success, execution trajectory quality, resource consumption, latency, and system safety.

---

## 2. Key Concepts & Definitions

- **Value-Aware Agent Optimization:** An evaluation paradigm that replaces raw success metrics with a utility function that jointly optimizes task value against cost, latency, risk, and reliability constraints.
- **Process-Centric vs. Outcome-Centric Metrics:** Outcome metrics (e.g., success rate) evaluate the final deliverable. Process-centric metrics (e.g., Progress Rate, Trace Coverage) evaluate the intermediate reasoning, tool usage, and state changes to ensure the agent did not achieve the goal through unsafe or illogical steps.
- **Pass@k (Capability):** The probability that an agent succeeds at least once when allowed $k$ attempts.
- **Pass^k (Consistency):** The probability that an agent passes _all_ $k$ runs of the same task. This is a critical KPI for enterprise deployments requiring deterministic reliability.
- **Cost-per-Accepted-Outcome (CAPO):** A unit economics KPI that shifts cost measurement from raw tokens consumed to the actual business value delivered by the agent harness.

---

## 3. Important Details, Facts, and Examples: A KPI Taxonomy

To quantify agent behavior, harnesses must track metrics across five core dimensions:

### A. Task Completion and Quality KPIs

- **Full vs. Partial Completion Scores:** Because long-horizon tasks often fail at the last step, binary metrics discard valuable signal. Harnesses like _TheAgentCompany_ use a Partial Completion Score that rewards fractional progress across milestones, while strongly incentivizing full completion with a 50% bonus upon passing all checkpoints.
- **Progress Rate:** Measures how effectively the agent advances toward its goal by comparing the agent's actual trajectory against an expected optimal path.
- **Output Quality (Subjective KPIs):** Metrics such as Response Relevance, Factual Correctness, Fluency, and Logical Coherence, often scored via LLM-as-a-judge.

### B. Efficiency and Resource Economics

- **Latency Metrics:** Measured as _Time To First Token (TTFT)_ for streaming applications, or _End-to-End Request Latency_ for asynchronous tasks.
- **Token Efficiency:** The ratio of output information tokens to total tokens consumed (input + output across all agent calls).
- **Cost per Instance:** Calculated directly as $(\text{Prompt token count} \times \text{Prompt token cost}) + (\text{Completion token count} \times \text{Completion token cost})$.

### C. Reliability and Predictability

- **Consistency ($C_{out}, C_{traj}, C_{res}$):** Measures variance across multiple runs of the same task. Includes Outcome Consistency (success variance), Trajectory Consistency (whether the agent uses the same tools in the same order), and Resource Consistency (variance in latency/costs).
- **Calibration ($P_{cal}$) & Discrimination ($P_{auroc}$):** Predictability KPIs. Calibration measures whether an agent's stated confidence aligns with its actual empirical success rate. Discrimination measures if the agent assigns higher confidence to correct outputs than to incorrect ones.

### D. Safety and Process Compliance

- **Constraint Violation Rate (CVR) & Catastrophic Event Rate (CER):** CVR tracks step-level hard-rule/policy violations (e.g., attempting to access forbidden files), while CER tracks episode-level severe harms.
- **Decision Coverage Rate (DCR):** A trace completeness metric indicating the fraction of actions that have complete, auditable logs.
- **Partial Response Rate (PRR):** The fraction of tool calls returning partial or incomplete data, used to detect silent upstream degradation.

---

## 4. Connections Between Ideas Across the Sources

- **Latency vs. Correctness Erosion:** Evaluation must correlate latency KPIs with accuracy KPIs. The _Production Agentic Evaluation Framework (PAEF)_ identifies a failure mode (FM-6) where systems under latency pressure silently skip feature enrichment or fall back to simpler paths. Without cross-correlating latency spikes with decision quality, this erosion remains invisible.
- **The Oracle Problem and Grading Costs:** The shift from binary verification to rich trajectory analysis requires robust "oracles" (graders). Because LLM-as-a-judge evaluators are computationally expensive, applying them at scale requires adaptive budget optimization and _Trace-First Offline Analysis_ (scoring stored production traces rather than generating new live runs) to dramatically reduce evaluation token costs.

---

## 5. Summary of Key Sources

1. **"Towards a Science of AI Agent Reliability":** Proposes 12 concrete metrics that operationalize agent reliability across four dimensions (Consistency, Robustness, Predictability, and Safety) rather than relying on raw task success.
2. **"Evaluation and Benchmarking of LLM Agents: A Survey":** Catalogs evaluation objectives (behavior, capabilities, reliability, safety) and introduces specific KPIs like pass^k, TTFT, and Execution Accuracy.
3. **"TheAgentCompany: Benchmarking LLM Agents on Consequential Real World Tasks":** Details the implementation of checkpoints to measure partial task completion alongside rigid monetary cost calculations (Cost per instance).
4. **"Evaluating Agentic AI in the Wild (PAEF)":** Identifies metrics for detecting production drift, including the Partial Response Rate for tool reliability, and normalized Shannon entropy to detect "Distribution Collapse" when an agent's outputs become overly narrow.
5. **"AgentAssay: Token-Efficient Regression Testing":** Introduces metrics for Agent Coverage (measuring decision-path and tool exploration) and uses behavioral fingerprinting to create token-efficient, quantitative regression testing.

---

## 6. Diagrams & Tables

### Table 1: Matrix of Core Agentic KPIs

|Evaluation Objective|Primary KPIs / Metrics|Measurement Focus|
|:--|:--|:--|
|**Task Completion**|Success Rate (SR), Partial Completion Score, Progress Rate|Goal achievement & milestone tracking|
|**Efficiency/Cost**|Cost per instance, CAPO, Token Efficiency|Monetary spend & computational resource ratio|
|**Latency**|TTFT, End-to-End Request Latency|Speed of processing & time to response|
|**Reliability**|Pass^k, Outcome Consistency ($C_{out}$), Calibration ($P_{cal}$)|Determinism, variance, and confidence accuracy|
|**Safety/Compliance**|CVR, CER, Decision Coverage Rate (DCR)|Policy adherence & auditability of execution traces|

_(Synthesized from)_

---

## 7. Practice Questions & Answers

**Q1: Why is Pass^k a better reliability KPI for enterprise applications than Pass@k?** _Answer:_ Pass@k only measures if the agent is capable of succeeding _at least once_ if given $k$ attempts. Pass^k measures consistency, calculating the probability that the agent succeeds on _all_ $k$ runs. For mission-critical applications where deterministic behavior is required, Pass^k is the superior indicator of reliability.

**Q2: What two sub-metrics make up the predictability KPI of an agent?** _Answer:_ Calibration (whether the agent's stated confidence matches its empirical success rate) and Discrimination/AUROC (whether the agent accurately assigns higher confidence scores to correct predictions than to incorrect ones).

**Q3: How do frameworks like _TheAgentCompany_ calculate a "Cost per instance" KPI?** _Answer:_ It calculates the monetary cost of querying the APIs by using the formula: $(\text{Prompt token count} \times \text{Prompt token cost}) + (\text{Completion token count} \times \text{Completion token cost})$.

**Q4: What is the "Partial Response Rate" (PRR) and what failure mode does it detect?** _Answer:_ PRR is the fraction of tool calls that return partial or incomplete data. Tracking it helps detect "Availability-Truth Decoupling" (or silent degradation), where stable final accuracy masks a degrading or failing upstream tool.

---

## 8. Key Takeaways and Exam-Style Points

- **Move Beyond Binary Testing:** Evaluating agents exclusively via "Pass/Fail" discards the rich execution trace and masks intermediate reasoning flaws. KPIs must assess the _trajectory_ as well as the outcome.
- **Value-Aware Optimization:** The ultimate goal of evaluation is not simply to maximize success rate, but to maximize success within constraints bounded by latency, cost, and safety risks.
- **The 4 Pillars of Reliability:** When developing KPIs beyond task completion, ensure coverage across _Consistency_, _Robustness_, _Predictability_, and _Safety_.
- **Process Evidence is Mandatory:** In high-risk environments, outcome metrics must be paired with process metrics like Constraint Violation Rate (CVR) and Decision Coverage Rate (DCR) to prove the agent isn't merely "getting lucky".

---

### ✅ Quick Revision Checklist

- [ ] I can differentiate between outcome-centric and process-centric evaluation metrics.
- [ ] I know the difference between Pass@k and Pass^k.
- [ ] I can define the components of the "Cost per instance" calculation.
- [ ] I understand how latency and token efficiency serve as critical KPIs.
- [ ] I can explain why predictability (Calibration and Discrimination) is required for human-in-the-loop escalation.
- [ ] I understand the concept of Value-Aware Agent Optimization.