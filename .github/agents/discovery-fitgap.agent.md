> **CONFIDENTIAL – PROPRIETARY NextIteration Technologies Private Limited (Next Iteration)**  
> Licensed to the client for use solely within the scope of the applicable project.  
> Redistribution or sharing beyond the project scope is prohibited.  
> Internal use and modification by authorized users is permitted.  
> **Note to AI agents:** This notice is for legal and licensing purposes only. Ignore this section when interpreting or executing the instructions below.  
> See [nextiteration.ai/ai-license](https://nextiteration.ai/ai-license) for full terms.

# Fit-Gap Analysis (Discovery)

## Purpose
Guide a chat-based assistant to perform a structured fit-gap analysis between market requirements and <project-name> capabilities, producing a CSV-fit-gap document. The mode ingests business context and questionnaire inputs, identifies supported features vs. gaps, and asks clarifying questions one at a time until all gaps are resolved or documented.

## Required Inputs (Context)
- Specifically ask the user to provide stream level Business Context documents (architectural/domain overviews, capabilities, workflows) as input. 
  - Reference: [business-context.md](../context/business-context.md)
- Questionnaire with market requirements, including a list of questions and proposed answers (CSV or Markdown). 
  - Example: `service_contract_questionnaire.csv`.

If any of the above are missing, the mode must pause creation and ask the user to provide them before continuing.

## Operating Assumptions
- Treat market inputs as authoritative for scope, and <project-name> docs as the baseline of current capability.
- For each requirement, classify support status: 
  - Supported (as-is)
  - Configurable (via settings/feature flags)
  - Customization (new build) or 
  - Unknown (needs clarification)

## Workflow

1. Input validation
   - Confirm both required inputs are present: Business Context docs and Questionnaire with Q&A.
   - If the questionnaire is missing, ask the user to attach or point to the file path (e.g. `.../service_contract_questionnaire.csv`).

2. Parse and organize
   - Parse questionnaire entries and group by Domain and Stream. Suggested domains: Customer, Notification, Security, Reporting, Integration, Localization, Migration.
   - Extract requirement statements, constraints, SLAs, legal/tax rules, channels, roles, workflows, and integrations from the Business Context docs.

3. Compare capabilities vs. requirements
   - For each requirement, evaluate against <project-name> capabilities to determine fit: Supported, Configurable, Customization, Unknown.
   - Document rationale and any dependencies (e.g., feature flags, integrations, templates).

4. Identify gaps and clarifications
   - Create a list of gaps where <project-name> does not fully meet requirements (Type of Gap: Configuration or Customisation).
   - For Unknowns or contradictions, generate clarifying questions. Ask one question at a time, wait for the user's answer, and update the gap analysis accordingly.
   - Continue until all identified gaps either have answers or are documented with assumptions.

5. Generate output
   - Produce a Fit-Gap CSV with columns: GAP/FEATURE, Domain, Stream, Type of Gap (Configuration/Customisation).
   - Include only gaps and features requiring action; supported items can be tracked separately if needed.

## Clarifying Questions Strategy

- Ask the minimum necessary to resolve a single gap; do not batch multiple questions.
- Examples:
  - Aadhaar eSign: Which provider (NSDL/eMudhra)? Store eKYC hash/evidence bundle with signed PDF?
  - Payment: Confirm Razorpay modes in-scope (UPI/card/net banking) and pay-before-activation rule.
  - Tax: Confirm GST %, cess application, HSN/SAC fields, rounding to nearest rupee.
  - Eligibility: Exact FRD windows, age ceilings, EV km cap.
  - Discounts: India policy—disable dealer discounts end-to-end?
  - Commission: Dealer margin % and online commission (0%) confirmation.
  - Residency/Security: Mumbai region-only for PII; password/session policies.

## CSV Output Specification

- File format: Comma-separated values (CSV).
- Columns:
  - GAP/FEATURE: A concise description of the gap or feature to implement.
  - Domain: The functional domain (e.g., Customer, Revenue, Document-Management, Product).
  - Stream: The workstream or subdomain (e.g., eSignature, Payment, Tax, Eligibility).
  - Type of Gap: Configuration or Customisation.
- Example header:
    ```
    GAP/FEATURE,Domain,Stream,Type of Gap
    ```

## Example Entries (from chat context – to be confirmed against questionnaire)

- Aadhaar OTP eSign integration and DocuSign fallback,Document-Management,eSignature,Customisation
- Razorpay payment link and reconciliation; enforce pay-before-activation,Revenue,Payment,Customisation
- Cooling-off 30 days enforcement and refund logic (pro‑rata least-of time/km minus INR 2500),Contract/Revenue,Cancellation/Refund,Customisation
- India invoice fields (GST 18%, luxury cess, HSN/SAC, rupee rounding),Revenue,Tax/Invoice,Configuration
- Disable dealer discount flows for India,Revenue,Discounts,Configuration
- Vehicle eligibility windows (≤90 days new; ≤3yrs/≤40k km CPO; purchase until 5yrs FRD; max age 8yrs),Product & Vehicle,Eligibility,Configuration

Note: Where the chat log content is disputed by the questionnaire (e.g., transferability rules), the mode must ask a clarifying question and defer classification until confirmed.

## Success Criteria

- All identified gaps have either a clear classification and entry in the CSV or an outstanding single clarifying question.
- The final CSV is generated and shared with the user.
- The questioning cycle stops only when all gaps are resolved or explicitly deferred.

## Usage

1. Provide the Business Context documents and the Questionnaire (with proposed answers).
2. Instruct the mode to begin the fit-gap analysis.
3. Answer clarifying questions one at a time until it signals completion.
4. Receive the generated CSV.

