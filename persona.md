# User Persona: Business Data Analyst

> **Persona type:** Evidence based working hypothesis, not a researched individual. The project brief identifies analysts as a user group but does not establish a specific first user. Validate this persona with interviews and workflow observation.

## Snapshot

**Name:** Maya Patel (fictional)  
**Role:** Business Data Analyst  
**Organization:** Mid-sized enterprise with contracts, invoices, and reports stored across shared repositories  
**Experience:** Comfortable with spreadsheets, SQL, dashboards, and business data; not expected to be an AI or ontology specialist.

## Context

Maya prepares reliable datasets and analysis from business documents. Source text may already have been produced by OCR, but useful facts—such as parties, dates, amounts, terms, and their relationships—remain buried in prose. She needs to turn those details into a consistent structure and connect them to approved records in the organization's business model.

The project brief does not prescribe a document type, company size, or current toolchain. The context above is a plausible starting scenario for discovery, not a confirmed deployment environment.

## Goals

- Find relevant facts in documents without repeatedly reading every page by hand.
- Produce structured, consistent information that can be searched, compared, and used in analysis.
- Link document mentions to the right existing business records when the evidence supports a match.
- Verify where a result came from and explain it to colleagues.
- Spend review time on ambiguous or high-impact cases rather than rechecking every straightforward field.

## Behaviors and needs

- Works with a mix of document layouts and wording; names, dates, amounts, and terms may be presented inconsistently.
- Needs source text or location beside each extracted value so she can check it quickly.
- Wants unresolved matches and uncertain interpretations clearly marked for review.
- Needs to distinguish what the document explicitly states from what the system inferred or normalized.
- Values a practical correction path and consistent output over a confident-looking answer without evidence.

## Frustrations and risks

- Manually copying information from long documents is repetitive and difficult to scale.
- OCR text can be readable while still lacking the entities, roles, facts, and links needed for analysis.
- Similar organization names and unclear party roles can lead to incorrect links or relationships.
- An unstated year, ambiguous amount, or conflicting source should not be silently resolved by guesswork.
- If evidence and uncertainty are hidden, she cannot confidently review or defend the resulting dataset.

## Motivations

- Deliver analysis on time using information that is complete enough to be useful and traceable.
- Reduce avoidable manual handling while retaining meaningful oversight.
- Build trust with business stakeholders by showing how a result was derived.

## Needs from the proposed system

1. Receive already-extracted document text (OCR may be a separate upstream step).
2. Identify entities, facts, and relationships in a structured format.
3. Preserve source evidence for each result, with page or section location when available.
4. Map to the existing business model only when there is a reliable match; otherwise leave it unresolved.
5. Represent confidence, ambiguity, and review status explicitly.
6. Let a person inspect and query results, and correct errors where the product supports review.

## Success signals

- Less time spent locating and re-entering facts than in the current workflow.
- Fewer unsupported or incorrect entity links and relationship labels.
- Reviewers can trace a result to its source passage and understand its status.
- The output can be queried or used downstream without losing provenance or uncertainty.
- Quality is measured on a representative sample, including ambiguous and conflicting cases.

## Design implication

Make the evidence, match status, and uncertainty visible at the point of review. Treat an unresolved value as a valid result that can be investigated, rather than forcing a potentially wrong answer.

## Assumptions to validate

- Analysts are the first daily users; document processors or domain owners may be the primary reviewers instead.
- The first workflow and document type are not yet selected.
- Users can access the source documents and the organization's canonical business records.
- Reviewers need evidence at field level, not only a document-level confidence score.
- Time saved and extraction quality are both important; acceptable error rates will depend on the field and its consequences.
