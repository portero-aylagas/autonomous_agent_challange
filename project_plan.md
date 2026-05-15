# Autonomous Agent Project Plan

## 1. Use Case

### Project Overview

This project proposes a `QA Intake Agent` for an automotive software team working on an infotainment system. The agent focuses only on `touchscreen UI defects` reported during internal QA validation.

The agent ingests a plain-text bug report written by a QA tester and produces a structured triage brief with actionable testing guidance. Its purpose is to reduce the time spent turning messy, inconsistent defect notes into clear QA artifacts that are ready for formal filing or escalation.

### Problem Statement

Infotainment QA teams often receive bug reports that are incomplete, inconsistent, or missing critical reproduction context. A tester may describe a touchscreen issue in natural language, but omit details such as software version, vehicle state, exact UI path, language/region, user profile state, or whether the issue is intermittent.

This creates several problems:

- QA leads must spend time clarifying reports before they can assess severity.
- Reproduction steps are written inconsistently across testers.
- Regression coverage is ad hoc and depends heavily on individual experience.
- Engineering teams may receive tickets that are hard to reproduce or missing key environment details.

### Target Users

The primary users are:

- Internal QA testers validating infotainment builds
- QA leads reviewing and normalizing incoming defect reports
- Secondary users: developers and test managers who consume improved QA outputs downstream

### Current Manual Process

Today, a tester files a defect note manually after discovering an issue on the infotainment touchscreen. A QA lead or senior tester then:

- reviews the free-text report
- asks for missing details
- rewrites the defect into a more structured format
- drafts likely reproduction steps
- identifies related areas that should be regression-tested
- decides whether the report is ready for formal ticket creation

This process is repetitive, slow, and inconsistent across team members.

### Proposed Agent Behavior

Given one plain-text defect report, the agent should return:

- likely bug category
- likely severity
- missing information that should be requested from the tester
- proposed reproduction steps
- suggested manual regression scenarios
- recommended next QA action

### Example Input Reports

Example 1:

> After switching from the climate screen back to media, the touch buttons stop responding for a few seconds. I could still see the screen, but tapping pause or next did nothing until I went back to home and opened media again.

Example 2:

> The settings page layout breaks when changing language to German. Some labels overlap and the save button is partially cut off on the bottom right.

Example 3:

> When I open the profile menu after a guest login, the screen flashes and returns to the home page instead of opening the account options.

### Success Criteria

The project is successful if the MVP can help QA teams:

- reduce time spent normalizing raw defect reports
- improve completeness of bug reports before formal ticket filing
- generate more consistent reproduction steps
- suggest useful regression scenarios for related touchscreen UI paths
- identify missing context early enough to reduce back-and-forth

## 2. Technology Stack

### Selected Technologies

- `Core LLM`: OpenAI `GPT-5.5`
- `Application style`: Simple Python workflow
- `RAG`: None for MVP
- `Agent framework`: No LangGraph in MVP
- `Orchestration`: None for MVP
- `Integrations`: None for MVP

### Why This Stack Fits the MVP

The core job of the MVP is not complex automation across systems. It is structured interpretation of a defect report followed by consistent QA-oriented outputs. For that reason, the simplest useful design is an LLM-driven workflow with a constrained prompt and structured response format.

`GPT-5.5` is a reasonable primary model choice because the task relies on:

- interpreting ambiguous natural-language defect reports
- identifying missing context
- producing structured outputs from messy inputs
- generating plausible but reviewable reproduction and regression scenarios

A simple Python application is sufficient because the MVP does not require multi-agent coordination, long-lived state, or business-system orchestration.

### Why RAG Is Not Included in the MVP

The MVP can work from the text of the incoming report plus a fixed prompting rubric. It does not need document retrieval to demonstrate value.

RAG is intentionally deferred because it would add extra design and evaluation complexity around:

- indexing prior defects
- managing a bug taxonomy knowledge base
- ranking relevant examples
- preventing stale or misleading retrieved context

RAG becomes more useful in a later version if the team wants the agent to reference:

- historical infotainment UI defects
- known issue templates
- internal severity guidelines
- subsystem-specific QA checklists

### Why LangGraph Is Not Included in the MVP

LangGraph is best justified when the workflow needs explicit multi-step state transitions, branching logic, retries, or human-in-the-loop checkpoints across a larger process.

This MVP has a narrower goal:

1. read one report
2. classify it
3. identify missing information
4. draft repro steps
5. suggest regression scenarios
6. recommend next action

That can be handled with a simple linear workflow. Adding LangGraph now would increase implementation overhead without materially improving the planning story.

### Why n8n Is Not Included in the MVP

n8n is most useful when the solution must connect to business systems and automate cross-tool workflows. This MVP explicitly avoids:

- Jira or Azure DevOps ticket creation
- test-management integrations
- notifications or escalations
- scheduled autonomous processing

The lab can justify future n8n use in v2 if the agent later needs to ingest reports from an issue tracker and route them automatically.

### Alternatives Considered

- `Anthropic Claude`: strong alternative for structured analysis tasks, but not selected as the primary recommendation
- `LangGraph`: useful for future workflow expansion, but unnecessary for the first version
- `RAG + vector database`: useful when grounding against internal QA documents becomes essential
- `n8n`: useful once real business-system integrations become part of scope

### Trade-Offs

The chosen stack prioritizes simplicity and a fast MVP, but it also means:

- outputs are less grounded than a future RAG-enabled version
- there is no built-in workflow orchestration
- the first version depends heavily on prompt quality and review practices

This trade-off is acceptable because the assignment emphasizes scoping, not building a production platform.

## 3. MVP Scope

### MVP Goal

The MVP should convert one free-text infotainment touchscreen UI defect report into a structured QA-ready output.

### In Scope

- Plain-text defect report input
- Touchscreen UI defect domain only
- Internal QA team as the primary user
- Structured output containing:
  - likely category
  - likely severity
  - missing details to request
  - proposed reproduction steps
  - suggested manual regression scenarios
  - recommended next QA action
- On-demand use only
- Human review required before any report is treated as final

### Suggested Output Schema

- `report_summary`
- `likely_category`
- `likely_severity`
- `confidence_level`
- `missing_information`
- `proposed_reproduction_steps`
- `suggested_regression_scenarios`
- `recommended_next_action`
- `notes_for_reviewer`

### Candidate Bug Categories

- touch responsiveness issue
- navigation/transition issue
- layout or rendering defect
- state persistence defect
- profile/session flow issue
- localization-related UI issue
- intermittent UI behavior

### Out of Scope

- screenshots or image-based analysis
- logs, telemetry, ECU traces, or CAN data
- Bluetooth, voice assistant, navigation, or media subsystem bugs outside touchscreen UI scope
- automatic ticket creation
- automatic defect prioritization without human review
- code generation or engineering fixes
- CI or test automation pipeline integration
- autonomous approval or release gating

### Must-Have Features

- Analyze a single text report
- Identify missing context
- Draft reproduction steps
- Suggest manual regression scenarios
- Recommend whether QA should request more information, reproduce, or file the issue

### Should-Have Features for v2

- Issue-tracker integration
- historical defect retrieval
- subsystem-specific QA templates
- support for screenshot attachments
- severity calibration against team guidelines

### Nice-to-Have Features for v3+

- multilingual report support
- cluster similar issues automatically
- suggest probable owner/team
- compare current report against known fixed defects
- detect likely duplicate issues

### MVP Success Metrics

- reduction in average time spent triaging raw QA reports
- higher percentage of reports considered complete on first review
- higher consistency of reproduction-step quality across reviewers
- positive QA reviewer feedback on usefulness of suggested regression scenarios

## 4. Risk Assessment

### Technical Risks

#### Risk: Hallucinated Reproduction Steps

- `Probability`: Medium
- `Impact`: High
- `Mitigation`: require the agent to label uncertainty, keep steps tightly tied to the source report, and require human review before use

#### Risk: Incorrect Severity or Category

- `Probability`: Medium
- `Impact`: Medium
- `Mitigation`: use a constrained category list, include confidence scoring, and present outputs as recommendations rather than final decisions

#### Risk: Inconsistent Output Quality Across Similar Reports

- `Probability`: Medium
- `Impact`: Medium
- `Mitigation`: define a stable output schema, evaluate against curated sample reports, and refine prompts based on reviewer feedback

### Business and Process Risks

#### Risk: Low QA Trust in the Agent

- `Probability`: Medium
- `Impact`: High
- `Mitigation`: position the tool as QA decision support, not a replacement for reviewers; measure usefulness with pilot feedback

#### Risk: Scope Creep into Full Defect Management

- `Probability`: High
- `Impact`: Medium
- `Mitigation`: document a strict MVP boundary and defer integrations, automation, and non-touchscreen subsystems to later phases

#### Risk: Over-Reliance on Agent Recommendations

- `Probability`: Medium
- `Impact`: High
- `Mitigation`: require reviewer approval and make uncertainty explicit in outputs

### Data and Privacy Risks

#### Risk: Sensitive Internal Build Information Appears in Reports

- `Probability`: Medium
- `Impact`: High
- `Mitigation`: apply internal data-handling policies, limit stored inputs, and avoid unnecessary retention of raw reports

#### Risk: Poor Input Quality Reduces Usefulness

- `Probability`: High
- `Impact`: Medium
- `Mitigation`: explicitly return missing-information requests and define minimum report completeness expectations

## 5. Implementation Plan

This implementation plan follows the phase structure requested in the lab instructions while keeping the work scoped to a planning-first MVP.

### Phase 1: Setup and Data Preparation

Estimated time: `0.5-1 day`

Activities:

- define touchscreen UI bug categories
- define the structured output schema
- create a small set of representative defect-report examples
- define reviewer expectations for useful outputs

Deliverable:

- documented schema and evaluation examples

### Phase 2: Core Agent Development

Estimated time: `1-2 days`

Activities:

- design the prompt and system instructions
- implement linear analysis flow
- produce structured QA outputs from text input
- add clear uncertainty and reviewer notes fields

Deliverable:

- first working internal prototype logic

### Phase 3: Integration and Testing

Estimated time: `1 day`

Activities:

- test the structured output against sample QA intake reports
- validate whether the output format is usable for a future issue-tracker or test-management integration
- test the workflow against sample reports
- compare outputs for consistency and completeness
- review whether suggested regression scenarios are useful to QA
- identify recurring failure patterns

Deliverable:

- evaluation notes and prompt refinements

### Phase 4: Deployment and Monitoring

Estimated time: `0.5-1 day`

Activities:

- define the simplest deployment model for an internal QA workflow, such as a CLI or lightweight internal service
- define monitoring needs, including usage tracking, output-review sampling, and common failure logging
- define guardrails for human review before formal ticket creation
- document future integration points for issue trackers or test-management systems
- assess whether RAG is justified by real data needs in v2

Deliverable:

- deployment and monitoring recommendations plus a roadmap for post-MVP expansion

### Key Milestones

- milestone 1: scope and schema approved
- milestone 2: first structured-output workflow drafted
- milestone 3: sample evaluation completed
- milestone 4: v2 roadmap documented

### Dependencies

- access to representative sample defect reports
- QA reviewer input on output usefulness
- agreed bug taxonomy for touchscreen UI issues

### Resources Needed

#### Team Members and Roles

- one engineer or prototyper to design the workflow and prompts
- one QA reviewer or lead to validate output usefulness and domain fit

#### Tools and Services

- OpenAI API access for the primary LLM
- simple Python environment for the MVP workflow
- sample internal defect reports or synthetic equivalents for evaluation
- optional future issue tracker or test-management platform for later integration planning

### Budget Considerations

The MVP should have low infrastructure cost because it uses:

- one hosted LLM
- no vector database
- no orchestration platform
- no external integrations

The primary cost drivers would be API usage and reviewer time during evaluation.

## 6. Success Metrics

The MVP will be considered successful if it demonstrates that an agent can improve QA intake quality without increasing process risk.

Suggested measures:

- `Triage time`: decrease in average time to convert a raw report into a QA-ready draft
- `Completeness`: increase in the percentage of reports that include enough information for reviewer action
- `Consistency`: improved uniformity of reproduction steps and regression scenarios across reports
- `Reviewer usefulness`: positive feedback from QA reviewers on whether outputs save time
- `Escalation quality`: fewer reports sent to engineering without enough reproduction context

## Recommended Future Expansion

If the MVP proves useful, the next iteration should consider:

- adding a small RAG layer with severity guidelines and prior defect patterns
- integrating with Jira or a test-management platform
- expanding from touchscreen UI into Bluetooth/media or navigation workflows
- attaching screenshots and structured environment metadata

The recommended long-term direction is still conservative: keep a human QA reviewer in the loop and treat the agent as structured decision support, not autonomous defect authority.
