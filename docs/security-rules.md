# Security Rules Reference

Complete reference for all 26 SquireX security rules. Each rule includes detection logic, real-world threat context, and remediation guidance.

---

## Category 1: Action Configuration

### AGENTFORCE-1.1 — Mandatory User Confirmation
- **Severity**: Critical
- **OWASP**: Excessive Agency
- **What it detects**: `GenAiFunction` nodes with `isConfirmationRequired=false` that perform state-modifying operations (DML, HTTP callouts)
- **Why it matters**: Without confirmation, the AI agent can autonomously create, update, or delete records based on misinterpreted user intent
- **Remediation**: Set `<isConfirmationRequired>true</isConfirmationRequired>` in the GenAiFunction XML

### AGENTFORCE-1.2 — Schema Synchronization Verification
- **Severity**: High
- **What it detects**: Mismatches between a GenAiFunction's declared parameters and its linked `schema.json` input/output definitions
- **Why it matters**: Schema drift causes the LLM planner to pass incorrect data types or miss required fields, leading to silent runtime failures
- **Remediation**: Regenerate the schema.json from the current function signature, or update the function to match the schema

### AGENTFORCE-1.3 — Target Context Privilege Analysis
- **Severity**: Critical
- **What it detects**: GenAiFunctions or ActionDefs that target Apex classes running `without sharing` or Flows running in `SystemModeWithoutSharing`
- **Why it matters**: The AI agent bypasses all record-level security (CRUD, FLS, sharing rules), accessing data the user shouldn't see
- **Remediation**: Change Apex classes to `with sharing` or `inherited sharing`. Change Flows to `DefaultMode` or `SystemModeWithSharing`

---

## Category 2: Agent Script Safety

### AGENTFORCE-2.1 — Validation Guard Clause Enforcement
- **Severity**: Medium
- **What it detects**: Action definitions that perform DML operations without preceding validation logic (guard clauses)
- **Why it matters**: Unguarded DML operations can create, modify, or delete records based on unvalidated AI-generated input
- **Remediation**: Add validation logic before DML operations in your action definitions

### AGENTFORCE-2.2 — Transition Integrity (Dead-Ends & Cycles)
- **Severity**: High
- **What it detects**: Topics with no outgoing transitions and no actions (dead-ends), transitions to non-existent topics, and transition cycles (A→B→C→A)
- **Why it matters**: Dead-end topics trap users. Cycles create infinite conversation loops (denial of service). Broken transitions cause runtime failures
- **Remediation**: Add transitions or actions to dead-end topics. Break cycles with guard conditions. Fix broken transition targets

### AGENTFORCE-2.3 — Prompt Injection Defense Heuristics
- **Severity**: High
- **OWASP**: LLM01 (Prompt Injection)
- **What it detects**: Dynamic user input variables (`{!$Input:}`, `{!Record.}`, `@variables.`) injected into instruction text without defensive boundary patterns (`<system>`, `<user_input>` tags)
- **Why it matters**: Without structural separation, user input can override system instructions and hijack agent behavior
- **Remediation**: Wrap injection points in XML boundary tags (`<system>...</system>`, `<user_input>...</user_input>`) and add explicit defensive instructions

---

## Category 3: Grounding Security

### AGENTFORCE-3.1 — Hardcoded Sensitive Indicators
- **Severity**: Critical
- **What it detects**: API keys, Bearer tokens, AWS credentials, SSNs, private keys, and internal URLs in prompt templates and plugin instructions
- **Why it matters**: Sensitive values in prompt templates are transmitted to external LLMs, creating data leakage
- **Remediation**: Move sensitive values to Named Credentials, Custom Metadata, or Protected Custom Settings

### AGENTFORCE-3.2 — Field-Level Security Masking Alignment
- **Severity**: High
- **What it detects**: Prompt templates that reference fields which may have FLS restrictions, without masking considerations
- **Why it matters**: FLS-restricted field values can be leaked through prompt template output to unauthorized users
- **Remediation**: Use `Security.stripInaccessible()` or explicit FLS checks before including field values in prompts

---

## Category 4: Structural Dependency

### AGENTFORCE-4.1 — Planner Orchestration Completeness
- **Severity**: High
- **What it detects**: GenAiPlannerBundle definitions that don't connect to any GenAiPlugins, leaving the planner with no actions to invoke
- **Why it matters**: An empty planner means the agent cannot perform any useful work
- **Remediation**: Link the planner to at least one GenAiPlugin via DEPENDS_ON edges

### AGENTFORCE-4.2 — State Deactivation Collision
- **Severity**: Medium
- **What it detects**: PromptTemplates or GenAiFunctions being deactivated (`accessLevel=Blocked`) while still referenced by active components
- **Why it matters**: Salesforce will block the deployment, or the agent will fail at runtime trying to invoke deactivated components
- **Remediation**: Remove all references to the component before deactivating it

### AGENTFORCE-4.3 — Synthetic Evaluation Completeness
- **Severity**: High
- **What it detects**: Agent configurations that lack AiEvaluationDefinition coverage
- **Why it matters**: Without evaluation definitions, there's no systematic way to measure agent quality or detect regressions
- **Remediation**: Create AiEvaluationDefinitions for each agent covering key user scenarios

---

## Category 5: Extended Graph Security

### AGENTFORCE-FLOW-01 — System Context Enforcement
- **Severity**: Critical
- **What it detects**: Flows invoked by agent actions that run in system context without sharing enforcement
- **Remediation**: Set Flow Run Mode to `DefaultMode` or `SystemModeWithSharing`

### AGENTFORCE-FLOW-02 — Silent State Modification
- **Severity**: High
- **What it detects**: Flows that create or update records without requiring user confirmation through the agent
- **Remediation**: Add confirmation steps in the agent action before invoking state-modifying Flows

### AGENTFORCE-FLOW-03 — Variable Injection in DML
- **Severity**: High
- **What it detects**: Dynamically bound AI `{!Variables}` within Flow DML filters and HTTP callout URLs
- **Why it matters**: Enables SOQL injection and SSRF attacks via AI-controlled input
- **Remediation**: Validate and sanitize variable values before use in DML filters or callout URLs

### AGENTFORCE-API-01 — External Callout Injection
- **Severity**: High
- **What it detects**: HTTP callout URLs constructed from dynamic AI variables without validation
- **Remediation**: Use allowlisted URLs or validate callout targets against a known-good list

### AGENTFORCE-PT-01 — Template Context Poisoning
- **Severity**: Critical
- **What it detects**: Unescaped AI outputs embedded directly inside PromptTemplate `<content>` XML blocks
- **Remediation**: Sanitize AI output before embedding in prompt templates

### AGENTFORCE-PT-02 — Experimental Template Activation Exposure
- **Severity**: High
- **What it detects**: PromptTemplateActivation records exposing experimental or draft templates to production
- **Remediation**: Only activate templates that have been reviewed and tested

---

## Category 6: Supply Chain Security

### AGENTFORCE-SC-01 — Malicious API Downgrade Injection
- **Severity**: Critical
- **What it detects**: `sfdx-project.json` or `package.xml` configurations targeting outdated API versions with known vulnerabilities
- **Remediation**: Upgrade to the latest stable API version

### AGENTFORCE-SC-02 — Silent Schema Desync Exploit
- **Severity**: High
- **What it detects**: Mismatches between Open API schemas and standard Salesforce metadata definitions that could allow data manipulation
- **Remediation**: Regenerate schemas from canonical metadata sources

### AGENTFORCE-SC-03 — Managed Package Origin
- **Severity**: Medium
- **What it detects**: Agent actions that invoke Apex classes or Flows from managed packages (namespace-prefixed components)
- **Why it matters**: Managed package code is opaque — you cannot audit, modify, or secure it. A compromised package inherits the agent's permissions
- **Remediation**: Wrap managed package calls in an org-owned Apex class with `with sharing`, explicit input validation, and output sanitization. Review the vendor's security posture

---

## Category 7: Agentic Architecture

### AGENTFORCE-7.1 — Topic Action Bloat (God-Topic Prevention)
- **Severity**: High
- **What it detects**: Topics with an excessive number of actions, creating a "god-topic" anti-pattern
- **Why it matters**: Overloaded topics confuse the LLM planner and increase the chance of incorrect action selection
- **Remediation**: Split large topics into focused, single-responsibility topics

### AGENTFORCE-7.2 — Inadequate Skill Semantics
- **Severity**: High
- **What it detects**: GenAiFunctions or plugins with missing or vague description text
- **Why it matters**: The LLM planner selects actions based on their descriptions. Vague descriptions lead to incorrect action selection
- **Remediation**: Write clear, specific descriptions explaining what the function does, what inputs it expects, and what it returns

### AGENTFORCE-8.1 — Context Traversal Exfiltration
- **Severity**: High
- **What it detects**: Cross-object relationship traversals in prompt templates or actions that could expose sensitive parent/child record data
- **Remediation**: Limit relationship traversal depth and apply FLS checks at each traversal level

---

## Category 8: Instruction Integrity

### AGENTFORCE-9.1 — Metadata Instruction Poisoning
- **Severity**: Critical
- **OWASP**: LLM01 (Prompt Injection)
- **What it detects**: Adversarial content patterns in metadata instruction fields — GenAiPlugin instructions, GenAiFunction descriptions, Agent Script system instruction overrides, PromptTemplate content
- **Pattern categories**: Jailbreak phrases, role override attempts, data exfiltration commands, system manipulation, encoding evasion
- **Why it matters**: These fields are read by the LLM planner but are often invisible to human reviewers in the Agent Builder UI. This is the Salesforce equivalent of MCP tool description poisoning
- **Remediation**: Review all instruction text. Remove adversarial patterns. Custom patterns can be added to `adversarial_patterns.json`

### AGENTFORCE-9.2 — Cross-Topic Instruction Boundary
- **Severity**: High
- **What it detects**: Topics whose instruction text references other topics by name (shared context contamination) or contains planner override patterns ("always route to this topic", "ignore other topics")
- **Why it matters**: Cross-topic instruction bleeding allows adversarial metadata to manipulate conversation routing without modifying the transition graph. Planner override patterns constitute privilege escalation within the agentic context
- **Remediation**: Keep topic instructions scoped to their own actions. Use transition definitions for routing, not instruction text

---

## Category 9: Operational Reliability

### AGENTFORCE-10.1 — Validation Rule Conflict
- **Severity**: Medium
- **What it detects**: Agent actions that perform DML on objects with validation rules
- **Why it matters**: Validation rules are invisible to the LLM planner and will cause cryptic `FIELD_CUSTOM_VALIDATION_EXCEPTION` errors the agent cannot handle gracefully
- **Remediation**: Add error handling in target Apex/Flows that catches DmlException and returns user-friendly error messages. Add pre-validation logic before DML operations

---

*For questions about specific rules, contact hello@squirex.dev*
