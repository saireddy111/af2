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

1. What happens if an already published provider becomes deactivated?
My recommendation: The provider should no longer appear in published provider results, but we should preserve its deactivation status and historical information.

Why?

Simply skipping a deactivated NPI during a new import isn’t sufficient. If the provider was published during an earlier run, its existing Gold record might remain visible.

Proposed behavior:

Detect the deactivation during the new NPPES import.
Record the NPI’s deactivation status.
Ensure that provider is excluded from published queries after the new run is successfully committed.
Preserve its history for auditing and potential reactivation.
If the NPI is reactivated later, reprocess the provider and evaluate its viability again.
What you can say in the meeting:

I think we should handle deactivation as a lifecycle change, not just an import filter. Even if we skip the incoming deactivated row, we need to ensure an existing published provider doesn’t remain visible. I’d recommend retaining the status separately and making sure our published results reflect the latest committed state.

2. Do we really need a separate deactivated NPI registry table?
My recommendation: We need a reliable way to track deactivation status, but I wouldn’t commit to a new table until we verify whether an existing data model can support it.

Why?

Without status information, these situations could look identical to consumers:

Situation

Desired API response

NPI exists and is active

Active

NPI exists but is deactivated

Deactivated

NPI is absent from our available data

NotFound

A separate registry could support this, but it introduces additional synchronization, maintenance, and storage responsibilities.

I would first evaluate whether the current source-status or provider-status model can store the necessary information. If it cannot, a lightweight registry becomes reasonable.

Also, NotFound should mean not found in our available dataset, not proof that an NPI doesn’t exist anywhere.

What you can say in the meeting:

I think distinguishing a deactivated NPI from an NPI that isn’t found would be useful for consumers. My recommendation is to maintain explicit deactivation status, but before introducing a separate registry table, I’d like to confirm whether our existing data model can support that. If not, we can consider a lightweight registry.

3. What happens when a deactivated NPI becomes reactivated?
My recommendation: Treat reactivation as a status transition and re-evaluate the provider’s minimum viable data before republishing it.

Why?

Reactivation alone doesn’t guarantee the provider has complete data.

Here’s the expected lifecycle:

Import run

Provider status

Silver viability

Published result

Run 1

Active, complete

IsNotViable = 0

Included

Run 2

Deactivated

No new provider row under proposed intake rule

Excluded

Run 3

Reactivated, complete

IsNotViable = 0

Included

Run 4

Active, missing name

IsNotViable = 1

Excluded

On reactivation, the system should update the current deactivation status, ensure the provider doesn’t remain incorrectly flagged as deactivated, and evaluate the latest source data.

The transition should become visible to consumers only when the corresponding import is successfully committed, to avoid inconsistent status responses.

What you can say in the meeting:

For reactivation, I wouldn’t automatically restore the provider to published results. I’d process the provider through the normal viability checks again. If the required fields are present, it becomes publishable. If not, it remains excluded. I’d also make sure the current deactivation status is updated so our API doesn’t return conflicting information.

Your final recommendation to the team
You can combine all three answers into this closing statement:

My overall recommendation is to keep deactivation and viability as two separate concerns.Deactivation determines whether a provider is currently eligible to be published, while viability determines whether the provider has enough required information to be usable.I think we should preserve deactivation status for traceability and meaningful API responses, but we should first check if our existing tables can support that before adding a new registry.For providers that were previously published, we need to handle deactivation as a lifecycle change so old records don’t remain visible.And if an NPI is reactivated, we should re-evaluate its viability before publishing it again.

One additional point to bring up: All three recommendations depend on defining which source is authoritative for the provider’s current status and how complete each import is. If NPPES data is partial, absence from a file cannot automatically mean deactivation.

That shows you’re considering the entire provider lifecycle, not just individual import records.
