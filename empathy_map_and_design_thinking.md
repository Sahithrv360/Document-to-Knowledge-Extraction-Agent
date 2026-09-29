# Empathy Map and Design Thinking

## Scope and evidence

This map is for the **hypothetical business data analyst persona** described in [persona.md](persona.md). It is derived from the project problem statement, which concerns converting already-extracted document text into structured entities, facts, and relationships, mapping them to an existing business model where possible, and keeping evidence and uncertainty visible.

These are informed hypotheses, not direct quotes from interviewed users. Validate them with analysts, document processors, and domain owners before treating them as findings.

## Empathy map

| Says | Thinks |
|---|---|
| “I need to know where this value came from.” *(hypothesis)* | “Can I defend this result if someone asks how we got it?” |
| “This company name might be a different record.” *(hypothesis)* | “A plausible match is not necessarily the correct match.” |
| “The document doesn’t say which year.” *(hypothesis)* | “The system should leave gaps open instead of filling them in.” |
| “I need these fields in a consistent format.” *(hypothesis)* | “I want to spend my time analyzing data, not repeatedly transcribing it.” |

| Does | Feels |
|---|---|
| Reads OCR text and locates names, dates, amounts, terms, and roles. | Frustrated by repetitive manual extraction. |
| Compares mentions against approved business records and vocabulary. | Cautious when names or relationships are ambiguous. |
| Checks extracted values against the source passage. | More confident when evidence is easy to inspect. |
| Flags unclear or conflicting cases for follow-up. | Concerned that a hidden mistake could contaminate downstream analysis. |

The behaviors and emotions are plausible consequences of the stated workflow and risks; they should be tested through observation and interviews.

### Pains

- Readable OCR output still needs human interpretation and structuring.
- Facts and relationships can be inconsistent across documents.
- Entity matching may be ambiguous, especially with similar names or alternate names.
- A system that hides its evidence or uncertainty makes review and correction difficult.

### Gains

- Consistent, searchable facts and relationships from document text.
- Faster review because the source passage is shown alongside each extracted result.
- Reliable links to canonical records, with clear unresolved status when evidence is weak.
- A traceable dataset that supports analysis and downstream queries.

## Design thinking

### 1. Empathize

**Objective:** Learn who handles the first document workflow, what they do today, and where errors or delays matter most.

**Activities:**

- Interview analysts, document processors, and the domain owners responsible for the business vocabulary.
- Observe one real workflow from receiving OCR text through review and use of the resulting data.
- Ask users to walk through ordinary, ambiguous, and conflicting examples.
- Record existing handoffs, tools, time spent, rework, and consequences of incorrect facts or links.

**Questions to investigate:**

- Which document type and business decision should the first prototype support?
- Which fields and relationships are essential, and which are optional?
- What evidence is enough to link a mention to a canonical record?
- Which fields require human approval because a mistake has higher impact?
- How do users currently record uncertainty and resolve conflicts?

### 2. Define

**Working problem statement:**

> Business data analysts need a way to turn document text into traceable, consistently structured facts and relationships, because manual interpretation is repetitive and unsupported matches can undermine trust in the resulting data.

This is a hypothesis to refine after discovery. The initial user, document type, and impact measures remain open in the project brief.

**How might we…**

- help analysts find and verify document facts with less repetitive handling?
- show enough source evidence to make extraction review quick and trustworthy?
- map document language to the business model while making uncertain matches explicit?
- help reviewers prioritize cases where ambiguity or conflict matters most?

### 3. Ideate

Possible directions to explore with users:

- An evidence-first review screen showing the source passage next to each extracted field.
- Clear states such as **extracted**, **normalized**, **needs review**, and **unresolved**.
- Candidate record suggestions with the evidence for a match and a human confirmation path.
- Validation rules for dates, currencies, required fields, and allowed relationship types.
- A comparison view for conflicting values across documents, preserving each source.
- Search and export for reviewed structured results.

These are design options, not confirmed requirements or a committed interface specification.

### 4. Prototype

Build a small, reviewable prototype for one selected document type and workflow. It should accept text that has already been extracted and return:

- entities, facts, and relationships in a defined schema;
- source passages and page/section locations where available;
- normalized values alongside original wording;
- match and review status, including unresolved values;
- an inspection path for a person to accept, correct, or flag a result.

Use a small set of permitted sample documents that includes clear, ambiguous, and conflicting examples. Keep the prototype's scope aligned to the chosen workflow; OCR can be added separately if end-to-end input is needed.

### 5. Test and learn

Evaluate with users against a labeled sample and the current workflow. Track:

- time to find, review, and correct the target information;
- accuracy of required fields and relationship roles;
- correctness of canonical record matches;
- whether each result has usable supporting evidence;
- how often users accept, edit, reject, or leave a result unresolved;
- whether users understand the difference between stated facts and interpretations.

Review errors by type and impact. Revise the schema, matching rules, review flow, or target scope based on observed problems. Do not treat a single overall confidence score as proof that a result is correct.

## Initial design principles

1. **Evidence stays attached:** Each extracted value should be traceable to its source passage.
2. **Uncertainty stays visible:** Ambiguous facts and matches can remain unresolved.
3. **Relationships need support:** A recognized name does not establish its role in a transaction.
4. **Normalize without erasing:** Preserve the document's wording alongside any standardized form.
5. **Review focuses on risk:** Make unclear or consequential results easy to find and resolve.
6. **Start with one workflow:** Validate a focused use case before broadening document types or automation.
