# Empathy Map and Design Thinking

## Scope and evidence

This map is for the **hypothetical Client and Contract Records Manager** described in [persona.md](persona.md). The intended users may have different job titles across industries—for example, records coordinators, client/account managers, contract administrators, operations staff, property managers, or small-business owners.

The project addresses the difficulty of finding and using client and agreement information scattered across computer folders, business systems, scans, and paper records. These empathy statements are informed hypotheses, not direct user quotes or completed research. Validate them with people in the target workflow.

## Empathy map

| Says | Thinks |
|---|---|
| “I know we have the contract somewhere, but I can’t find it.” *(hypothesis)* | “The file may be under another name or in another folder.” |
| “Are these two records for the same client or two different clients?” *(hypothesis)* | “A wrong match could cause someone to use the wrong agreement.” |
| “Which version includes the latest change?” *(hypothesis)* | “An amendment may have changed a term in the original contract.” |
| “Show me the page where that detail came from.” *(hypothesis)* | “I need evidence before I rely on an extracted value.” |
| “I need all the important details in one place.” *(hypothesis)* | “I want complete enough records for this document type, without invented information.” |

| Does | Feels |
|---|---|
| Searches folder names, client names, IDs, addresses, dates, and paper indexes. | Frustrated when a relevant file is buried or inconsistently labeled. |
| Compares names, identifiers, terms, and dates across records. | Cautious about duplicates, conflicting values, and outdated versions. |
| Opens original documents to verify key details. | More confident when each detail links directly to the source. |
| Asks colleagues or domain owners when a record is unclear. | Concerned when a mismatch could create operational, financial, property, or contractual problems. |
| Follows up on missing, unsigned, amended, or expired agreements. | Relieved when status and next actions are visible. |

These behaviors and emotions are hypotheses to test through observation and interviews.

### Pains

- Client and contract records are spread across folders, systems, scans, and paper archives.
- A file may not be discoverable from the name or identifier the user knows.
- Similar names, alternate spellings, addresses, or parcel identifiers can cause incorrect matches.
- Amendments and renewals make it hard to tell which terms are current.
- Manual data entry and cross-checking take time and allow errors to persist.
- Uncited summaries and hidden uncertainty make extracted information hard to trust.

### Gains

- One searchable view of authorized, related documents and their key details.
- Faster retrieval using multiple identifiers and relationships.
- Comprehensive, structured information tailored to each document type.
- Clear source passages, document versions, review state, and history for each extracted fact.
- Early warnings for possible duplicates, mismatches, missing fields, and conflicting or outdated terms.
- Safer updates when records or documents change.

## Design thinking

### 1. Empathize

**Objective:** Understand how an organization currently stores, finds, verifies, updates, and uses client and contract records.

**Activities:**

- Interview records staff, client/account managers, contract administrators, operations users, and relevant domain owners.
- Observe a real search from the user's initial clue through locating the record, checking its version, and taking action.
- Map the sources involved: shared folders, email, line-of-business systems, paper indexes, scans, and records rooms.
- Ask users to demonstrate ordinary, duplicate-looking, amended, incomplete, and conflicting examples.
- Record time to retrieve, common search failures, rework, handoffs, sensitivity/access requirements, and consequences of an incorrect match.

**Questions to investigate:**

- Which user group and workflow should the first release serve?
- Which client, contract, asset, and document types are in scope first?
- What identifiers do users know when they search (client ID, company name, address, parcel number, contract number)?
- How do users know an agreement is active, amended, expired, or superseded?
- Which details must be extracted for each document type, and who owns that vocabulary?
- What is the acceptable behavior when identity or a term is ambiguous?
- Which records may each user role access, and what audit trail is required?

### 2. Define

**Working problem statement:**

> People who manage client and contract records need a reliable way to find and understand related information across scattered digital and paper-origin files, because inconsistent names, filing, identifiers, and document versions can hide relevant records or lead to serious mismatches.

**How might we…**

- help users find the right documents using any known client, contract, property, or project clue?
- bring relevant information together while preserving the source and version of each detail?
- extract comprehensive document-specific information without inventing missing facts?
- identify likely duplicate clients and conflicting or superseded contract terms for review?
- update the knowledge layer when a file changes without rebuilding unrelated records?
- enforce the organization's access and records-retention policies?

Refine this statement after selecting the first user group, organization type, and workflow.

### 3. Ideate

Possible directions to explore with users:

- A unified search across permitted files using names, aliases, IDs, dates, addresses, parcels, and contract numbers.
- A client or asset page that lists related documents, agreements, key facts, amendments, and source links.
- Document-type extraction templates with a shared core and configurable fields for land agreements, leases, service contracts, sales, and other records.
- A version timeline showing original agreements, amendments, renewals, expiry, and superseded versions.
- Evidence-first results showing the source text and page/section beside every extracted fact.
- Possible duplicate and entity-match suggestions with a human confirmation path.
- Mismatch checks for identifiers, parties, amounts, dates, signatures, and contract status.
- Incremental reconciliation so only changed evidence and relationships are updated.
- Role-based access and audit history for searching, reviewing, and modifying sensitive records.

These are ideas for testing, not a finalized product specification.

### 4. Prototype

Start with one organization and one retrieval/extraction workflow. A focused prototype could:

- ingest a permitted set of digital documents and, if available, paper-origin scans;
- extract text and layout, then capture document metadata and the fields relevant to the selected type;
- connect each extracted fact to a source passage and document version;
- associate documents with a client, contract, property, or asset only when confidence and evidence are sufficient;
- display duplicates, ambiguity, missing values, conflicts, and amendment status for review;
- search by common user clues and display related records;
- update only the changed document's evidence and links when a new version is processed.

For the first prototype, select a limited schema deliberately. Support broadening through configurable document types rather than claiming that one fixed list contains every possible fact for every business.

### 5. Test and learn

Test with users on representative records, including difficult cases. Measure:

- time and success rate for finding a relevant document;
- recall of relevant documents returned for a client/contract search;
- accuracy of extracted fields by document type;
- false client, property, or contract matches;
- ability to identify current versus superseded agreements;
- evidence coverage and whether cited passages support the displayed details;
- how often users accept, edit, reject, or leave a match unresolved;
- detection of missing fields and cross-document conflicts;
- correctness and idempotence of incremental updates;
- access-control and audit behavior in the tested workflow.

Review failures by type and consequence. Update the search fields, extraction schema, resolution rules, review experience, and document-type scope based on observed results.

## Initial design principles

1. **Search from real clues:** Support the names and identifiers users actually have.
2. **Keep evidence attached:** Every extracted value should lead back to its original record and location.
3. **Comprehensive but scoped:** Extract all required/relevant fields defined for each supported document type; preserve additional source content for later review where feasible.
4. **Uncertainty remains visible:** An ambiguous client or missing contract term can stay unresolved.
5. **Versions and amendments matter:** Preserve history and distinguish current terms from superseded ones.
6. **Do not merge on resemblance alone:** Similarity can suggest candidates, while confirmation rules protect identity.
7. **Respect permissions:** Results and sources must honor access rights and audit policies.
8. **Incremental updates are source-aware:** A changed document removes only its own support, not facts still supported by other records.
