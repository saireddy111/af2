Review and improve my existing Minimum Viable Provider Data presentation for GitHub issue #9780.

File: docs/presentations/minimum-viable-provider-data-session.html

Preserve the current HTML styling and existing content. Do not rebuild the deck.

1. ADD A PROVIDER LIFECYCLE EXAMPLE
Show the same provider across four import runs:
- Run 1: Active and complete → published, IsNotViable = 0.
- Run 2: Deactivated → excluded from published results; retain status metadata.
- Run 3: Reactivated → process again and re-evaluate viability.
- Run 4: Active but missing required name → retained internally, IsNotViable = 1.

Highlight that skipping an incoming deactivated row alone might leave an older published record visible. Show this as an open design decision.

2. CLARIFY THE DEACTIVATED NPI REGISTRY
Explain why a dedicated registry is proposed:
- Distinguish active, deactivated, and not-found NPI searches.
- Store status metadata without processing deactivated providers.
- Discuss whether an existing status mechanism could replace a new table.
- Define what should happen when an NPI is reactivated.
- Consider stale records and historical audit needs.

Clearly label the registry as a proposed design.

3. ADD REACTIVATION SCENARIOS
Show Active → Deactivated → Reactivated transitions.
Include expected outcomes for Silver, published data, and API searches.
Review date ordering and inconsistent date scenarios.
Do not assume both dates being present always guarantees currently active status without confirming NPPES rules.

4. REVIEW SILVER VIABILITY
- Required fields: NPI, entity type, and applicable name.
- Type 1: first and last name.
- Type 2: organization/facility name.
- Check whether missing-NPI records can actually be persisted in silver.Providers.
- If not, raise a rejected/staging-record approach for team discussion.
- Explain the risk of defaulting IsNotViable = 0 before evaluation.
- Address re-evaluation on subsequent runs.

5. EXPAND EXAMPLE RECORDS
Show source data, eligibility decision, viability decision, Silver outcome, and consumer/API response for:
- Valid provider
- Missing entity type
- Missing required name
- Currently deactivated NPI
- Reactivated NPI

Use anonymized actual sample rows where available; otherwise label examples illustrative.

6. ADD TEAM DISCUSSION QUESTIONS
- Skip deactivated providers or retain them with a status?
- How do we prevent an old published provider from remaining visible?
- Is a separate deactivated NPI registry necessary?
- How should reactivation update the registry?
- What happens when NPI is missing?
- Are historical backfills in scope?
- Should deactivation skips be informational rather than warnings?

7. ADD A DECISION RECORD
For every major design choice, show:
- Recommendation
- Reasoning
- Alternatives
- Risks/trade-offs
- Decision status
- Follow-up action

8. VERIFY CONSISTENCY
Preserve the sample statistics:
- 4,999 NPPES main records
- 480 currently deactivated
- 622 deactivation signal rows
- 142 reactivated
- 480 missing entity type

Do not assume that records missing entity type are the same records that are deactivated without checking row-level overlap.

Ensure the lifecycle scenario, registry behavior, decision matrix, and rollout plan agree.

Keep the presentation focused on WHAT and WHY, not code-level implementation.

Update the existing HTML file and summarize the changes.
