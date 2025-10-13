# Agentic Meta-Agent Self-Validation & Checklist Review Cycle

## Mission

You are to **self-audit and validate** the meta-agent blueprint or agentic output you have just produced, ensuring it meets all requirements for completeness, standards compliance, and methodological rigor as specified in the Agent OS Master Blueprint.

---

## Step 1: Master Deliverables Checklist Extraction

1. **Extract or reconstruct the “Master Deliverables Checklist”** from the blueprint/prompt you followed.
2. **For each item in the checklist**, note:
   - Section/location in your output where it appears (with explicit heading or code block reference)
   - Whether it is fully present, partially present, or missing
   - Any cross-references (inline examples, appendix entries, diagrams, etc.)

---

## Step 2: Section-by-Section Self-Validation

For each required section or artifact:

- Confirm all required content, sub-sections, and canonical examples are present and standards-compliant.
- Check that:
  - All tables, diagrams, process maps, and templates strictly match the referenced standards
  - All checklists are atomic, actionable, and mapped to process steps
  - Error-handling, escalation, and ambiguity protocols are included and correctly documented
  - All cross-references, appendix entries, and canonical examples are complete and properly linked
- Flag any omissions, ambiguities, or deviations from the blueprint

---

## Step 3: Output a Self-Validation Report

- For each checklist item, output a table with columns:
  - **Checklist Item**
  - **Section/Location**
  - **Present?** (Yes/Partial/No)
  - **Notes/Required Corrections**

Example Table:

| Checklist Item                       | Section/Location         | Present?   | Notes/Required Corrections                |
|--------------------------------------|-------------------------|------------|-------------------------------------------|
| Source Manifest                      | `## 1. Source Manifest` | Yes        | Complete                                  |
| Methodology Analysis & Simulation    | `## 2. Methodology...`  | Partial    | Missing scenario-based examples           |
| Standards Mapping & Simulation       | `## 3. Standards...`    | No         | Section missing; not mapped               |
| ...                                  | ...                     | ...        | ...                                       |

---

## Step 4: Actionable Remediation Plan

- For every “Partial” or “No” entry, list:
  - What specific content is missing or non-compliant
  - Which section or artifact should be updated or added
  - If more context is needed, flag it for user/maintainer clarification

---

## Step 5: Iterative Self-Review Cycle

- If any “Partial” or “No” items remain:
  - Revise your output to address deficiencies
  - Repeat Steps 1–4 until all items are “Yes” (fully present and compliant)
  - Document each revision cycle in a short log at the end of your output

---

## Final Output

- Append the completed **Self-Validation Report** and **Remediation Log** to your output, so that any user or auditor can trace the validation process and confirm completeness.

---

> **Instruction:**  
> Do not consider your meta-agent or agentic output complete or valid unless every checklist item is “Yes,” with all supporting content, examples, and cross-references fully present and compliant.  
> If uncertainty or ambiguity cannot be resolved, escalate and flag for human review.

---

**This self-validation protocol may be used as a stand-alone prompt or appended to the end of any agentic blueprint to bootstrap an AI-driven, checklist-based audit and review cycle.**
