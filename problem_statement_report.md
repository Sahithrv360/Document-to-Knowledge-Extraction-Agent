# Detailed Explanation of the Problem Statement
## Document-to-Knowledge Extraction Agent

**Source:** `problem stmt.png`  
**Project area:** Enterprise analytics and document intelligence  
**Team label shown in the image:** Paper Trail

---

## 1. What the statement says

Organizations keep important business information in documents such as contracts, invoices, reports, and other records. OCR and text-extraction tools can turn a scanned page or document into readable text. However, readable text by itself does not tell a computer which parts are important, how they relate to one another, or how they fit the organization's existing business data.

The proposed system is an AI assistant that takes already-extracted document text and:

1. Identifies meaningful entities and facts.
2. Detects relationships between those entities and facts.
3. Connects the extracted information to an existing business data model, when there is a reliable match.
4. Presents the resulting structured information so a person or another system can inspect or query it.

The intended outcome is structured knowledge derived from documents, with links to a known semantic model where possible. A central rule is to distinguish facts explicitly supported by the document from uncertain interpretations and to avoid making unsupported connections.

---

## 2. The problem in plain language

A document contains sentences written for people. A business system usually needs records with named fields and explicit links. For example, a contract might say:

> “Northwind Services will provide maintenance to Acme Retail for $12,000 per quarter beginning 1 July 2026.”

A person can understand that sentence, but a data system may need it represented as:

- **Supplier:** Northwind Services
- **Customer:** Acme Retail
- **Agreement type:** Maintenance
- **Amount:** $12,000
- **Frequency:** Quarterly
- **Start date:** 1 July 2026

It may also need references to the official supplier and customer records already present in the organization's systems. Turning prose into those fields and links is the core task. The example is illustrative; the image does not prescribe a particular schema, technology, or business domain.

---

## 3. Issues and challenges the project addresses

The image does not enumerate a formal “issues faced” list. The following challenges are implied by its problem statement.

### 3.1 Text is readable but not yet useful as structured data

OCR answers “what characters and words are on the page?” It generally does not answer “which words name a customer?”, “what is the payment term?”, or “which company is the supplier?” Text extraction is therefore an input step, not the complete solution.

### 3.2 The same fact can be expressed in many ways

A company may appear under a legal name, a shortened name, a trade name, or a spelling variation. Dates, currencies, units, and terms can also be written in different formats. The system must identify meaning while retaining the original evidence.

### 3.3 Facts are spread across documents

An invoice, contract, and quarterly report may each describe different parts of the same business relationship. The agent should make it possible to organize facts consistently and relate them to known records. Combining documents raises the risk of confusing similar entities or treating inconsistent statements as if they agree.

### 3.4 Relationships matter as much as entities

Finding “Acme Retail” is only part of the work. The system may need to establish whether Acme is the customer, supplier, buyer, subsidiary, or another party in a particular document. A wrong relationship can be more harmful than a missed name.

### 3.5 Existing business models may not match document wording

Organizations have existing data models, semantic models, or business vocabularies. A document may use a term that differs from the model's preferred label. The system needs to map terms carefully and should leave a match unresolved when the evidence is not strong enough.

### 3.6 Uncertainty and unsupported inference

Documents can be incomplete, ambiguous, or contradictory. The project principle explicitly says to distinguish extracted facts from uncertain interpretations and avoid unsupported connections. The system should preserve uncertainty rather than present a guess as a confirmed fact.

### 3.7 Inspection and trust

The resulting information needs to be exposed for inspection or querying. Users should be able to check what the system extracted and, ideally, trace a field or relationship back to its supporting text. The source image does not specify the exact interface, but inspection is part of the stated outcome.

---

## 4. What “entities,” “relationships,” and “business facts” mean

- **Entity:** A person, organization, product, location, account, contract, invoice, or other identifiable thing. Examples include a customer company, a supplier, and a particular invoice.
- **Fact:** A property or event stated in the document. Examples include an invoice total, a delivery date, a contract start date, or a payment term.
- **Relationship:** A meaningful connection between entities. Examples include “supplier provides service to customer,” “invoice belongs to contract,” or “report concerns a business unit.”
- **Business data model / semantic model:** The organization's agreed way to name and relate business concepts. It may define standard entity types, fields, and relationship types.
- **Structured knowledge:** The extracted entities, facts, and relationships represented in a consistent, machine-readable form rather than only as paragraphs of text.

These are general interpretations of the terms in the proposal, not a schema specified by the image.

---

## 5. What the system is expected to do

A sensible end-to-end interpretation of the proposed workflow is:

1. **Receive extracted text.** The project starts with text produced by OCR or another extraction process; the image does not require the agent to perform OCR itself.
2. **Identify candidate entities and facts.** Detect names, dates, values, terms, and other useful business information.
3. **Identify relationships.** Determine how the detected items connect and what role each party or item has.
4. **Normalize carefully.** Standardize formats where appropriate (for example, dates or currency) while preserving the original wording and source.
5. **Map to the existing model.** Link a candidate to a known business concept or record only when there is adequate evidence.
6. **Represent uncertainty.** Mark ambiguous values or possible matches as uncertain or unresolved rather than silently deciding.
7. **Expose results.** Make the structured output available for people to inspect or for supported queries.

This workflow expands the statement into implementation steps; it does not assume any particular AI model, database, API, user interface, or extraction format.

---

## 6. Where this could be used

The proposal is framed for enterprise analytics and document intelligence. Typical uses include:

- **Contracts:** Extract parties, effective dates, renewal terms, obligations, fees, and links to supplier or customer records.
- **Invoices:** Extract invoice number, vendor, customer/account, dates, line items, taxes, totals, and purchase-order references.
- **Reports:** Extract named business units, periods, metrics, events, and relationships between reported results and organizational records.
- **Other business documents:** Organize information that otherwise remains buried in prose or separate files.

The practical value is that analysts and business teams can search and query document-derived information in a consistent way, compare it across records, and use it in analytics or downstream workflows. The image does not specify deployment context, target users, or integrations, so those would need to be decided during project planning.

---

## 7. Example of a cautious result

Suppose a document says:

> “The agreement with Northwind begins on 1 July. Quarterly service charges are $12,000.”

A useful result could record the stated organization name, start date, and fee with their source passages. If the year is absent, the system should not invent one. If the organization could match multiple records, it should leave the record link unresolved or flag it for review. If the document never identifies the counterparty's role, the system should not label it “supplier” without supporting evidence.

This example illustrates the proposal's principle: extract what is supported, distinguish interpretation from fact, and avoid fabricated links.

---

## 8. Suggested output fields for review

The statement does not mandate a data format. To make the outcome inspectable, a project could consider recording fields such as:

- Document identifier and document type
- Extracted entity or fact
- Entity/fact category
- Relationship and its direction, if applicable
- Original evidence text and page/section location when available
- Normalized value, if one was derived
- Match to the existing business model or record
- Confidence or review status
- Notes explaining ambiguity or unresolved mapping

These fields are suggestions, not requirements in the problem statement. Keeping source evidence alongside normalized results helps reviewers verify the extraction.

---

## 9. What is in scope—and what remains unspecified

### Explicitly in scope

- Process text that has already been extracted from documents.
- Identify meaningful entities and relationships.
- Connect information to an existing business data model where possible.
- Convert extracted text into structured knowledge.
- Expose the result for inspection or querying.
- Separate supported facts from uncertain interpretations and avoid unsupported connections.

### Not specified by the image

- Which OCR or text-extraction engine to use.
- Which document formats, languages, or business domains to support first.
- The exact entity types, relationship types, or target schema.
- Which AI models, databases, or software frameworks to use.
- Whether processing is real time or batch.
- How confidence is calculated or when a person must review a result.
- Security, access control, retention, privacy, and compliance requirements.
- Quantitative success measures such as accuracy, speed, or cost targets.

These are important design decisions for the team; they should not be mistaken for requirements already present in the slide.

### Expanded product direction from project discussion

The project owner has since broadened the desired product beyond the slide's text-first starting point. The product direction now includes:

- ingesting and extracting information from documents, images, audio, and video;
- supporting organizations that manage client and contract records across digital folders, business systems, scans, and paper archives;
- offering a straightforward first-time setup to connect approved data sources;
- dynamically discovering or receiving new and changed data after setup, with visible sync and processing status.

These are later product requirements to clarify with the startup, not claims about what the original slide explicitly required. Confirm initial modalities, connector types, freshness targets, security rules, and extraction fields before implementation.

---

## 10. A practical definition of success

A working project should demonstrate that it can ingest agreed sample sources and modalities, produce structured entities, facts, and relationships; map supported items to the agreed business model; preserve modality-specific evidence; flag ambiguity; and let a user inspect or query the results. If dynamic ingestion is in scope, demonstrate a new or changed source item being detected, processed, and reflected without a full rebuild.

Useful evaluation questions include:

- Did it identify the important entities and facts?
- Are the relationships and entity roles correct?
- Are mapped records genuinely the same as the document references?
- Can a reviewer trace outputs back to source text?
- Does the system leave uncertain cases unresolved instead of presenting guesses as facts?
- Can users retrieve the resulting information in a useful way?

The team should choose measurable thresholds and a representative test set before claiming production readiness; the image itself gives no numerical targets.

---

## 11. Short summary

The original slide describes moving from **document text** to **reliable, connected business information**. The expanded product direction is multimodal: documents, images, audio, and video can be processed into entities, facts, and relationships, aligned with an existing business model when justified, and made available for review or queries. A product also needs easy source setup and dynamic intake of new or changed data. The key quality requirement remains trustworthiness: preserve source evidence and the difference between what a source states and what the system merely suspects.


---

# Research Extension: Users, Discovery Approach, and Technology Choices

## 12. First clarify who has the problem

The slide says **“a data team wants an AI assistant”**. That makes the data team the stated project sponsor or builder, but it does not prove that the data team is the only group experiencing the problem. The likely affected groups below are inferred from the document types and desired outcome in the slide. Validate them with interviews before fixing product scope.

| Group | How the problem shows up for them | What they would need from the system |
|---|---|---|
| **Data / analytics teams** | Business facts are trapped in prose or inconsistent extracted text, so they spend time cleaning, joining, and structuring it before analysis. | Consistent fields, reliable links to the company’s data model, usable exports or queries, and traceable evidence. |
| **Document operations / back-office teams** | Staff manually read, copy, classify, and route invoices, contracts, and reports. | Less repetitive entry, clear exception queues, and a quick way to correct extraction. |
| **Finance / accounts payable** | Invoice data and purchase-order or supplier references need to be checked and entered into finance workflows. | Accurate amounts, dates, currency, vendor identity, line items, and review before posting. |
| **Procurement / vendor management** | Supplier documents and agreements contain terms that need to be found and related to supplier records. | Supplier resolution, contract fields, obligations, renewal dates, and links to authoritative records. |
| **Legal / contract management** | Key terms and parties are present in lengthy agreements and amendments. | Searchable clauses and terms with page-level source evidence; uncertain interpretation should be flagged. |
| **Business analysts and decision-makers** | They need organization-wide answers but the relevant facts are spread across documents. | Queryable facts with definitions, provenance, and enough context to judge whether an answer is trustworthy. |
| **IT, data governance, security, and compliance teams** | A system processing business documents can introduce access, privacy, retention, integration, and audit concerns. | Clear permissions, data handling controls, logs, retention rules, and a reviewable system design. |
| **Records / knowledge-management teams** | Documents and metadata can be stored in separate repositories with uneven classification. | Consistent document metadata, search, classification, and links to source files. |

### Who should be interviewed first?

Start with people who do the work every week, not only project sponsors:

1. **One document processor** who manually handles the chosen document type.
2. **One data analyst or data engineer** who currently turns extracted text into usable tables or reports.
3. **One domain owner** (for example, accounts payable for invoices or contract management for contracts) who can judge whether fields and relationships are correct.
4. **One data-model / governance owner** who can explain the “existing business data model” named in the slide.
5. **One IT/security stakeholder** once the data flow and document sensitivity are understood.

For a small student project, 5–8 interviews across these roles plus a review of a small, permission-cleared sample of documents is a practical starting point. This number is a suggested research plan, not a figure from the problem statement.

---

## 13. Research approach: find the real pain before choosing models

### Step 1 — Pick one narrow document workflow

Do not begin with “all business documents.” Choose one document type and one workflow, such as extracting invoice facts for review or pulling key terms from a contract. Invoices are often a good first prototype because common fields and outcomes are comparatively concrete. The slide itself does not prioritize invoices.

Record:

- Who receives the document and where it comes from.
- What they do with it today, step by step.
- Which fields they enter or look up.
- Which systems they copy those fields into.
- What errors or exceptions happen, and what happens next.
- How many documents they handle and how long each one takes, if the team can provide credible estimates.

### Step 2 — Interview using recent examples

Ask participants to walk through a real, recent case (using data they are permitted to share). Useful questions:

- “Show me the last document you processed. What did you do first?”
- “Which information did you need to find, and where did it go?”
- “What do you have to look up elsewhere to decide what a name or reference means?”
- “Which cases require a second person or a manual check?”
- “What mistakes are expensive or hard to notice?”
- “What should the system do when a value is missing, contradictory, or ambiguous?”
- “How would you verify that an extracted result is correct?”
- “What would make you stop using an automated result?”
- “What should be searchable or reportable after processing?”

Avoid asking only “Would you use an AI tool?” People’s demonstrated workflow and actual exceptions are more useful than a general yes/no answer.

### Step 3 — Observe and map the current process

Make a simple “current state” flow:

**document arrives → text is extracted → person reviews it → facts are entered or matched → exceptions are resolved → downstream system/report is updated**

For each step, note the person or system responsible, the inputs and outputs, wait time, rework, and common failure. This reveals whether the largest problem is OCR quality, entity extraction, record matching, data entry, or an unclear business process. The proposed agent mainly addresses interpretation and structuring after text extraction; it may not solve upstream scanning or downstream approval problems by itself.

### Step 4 — Agree on the business meaning and target model

Ask the data-model owner and users to define a small vocabulary:

- Which entity types matter (for example, invoice, supplier, customer, contract)?
- Which properties are required?
- Which relationships are allowed (for example, invoice **issued by** supplier)?
- Which existing IDs or systems are authoritative?
- What counts as an exact match, a possible match, or no match?
- Which date, amount, and currency formats should be normalized?

Write down examples and counterexamples. Do not ask the AI model to invent the business model while extracting documents.

### Step 5 — Build a representative, approved sample

Collect a small sample that covers ordinary documents and known edge cases: scans, varied layouts, missing fields, multiple pages, amended documents, name variants, and contradictory values. Remove or mask sensitive information when possible and follow the organization’s access rules. Label the expected entities, relationships, evidence spans, and correct match decisions. Have a domain user review labels because model evaluation is only as useful as the reference answers.

### Step 6 — Define success measures before a demo

Choose measures that reflect users’ work, for example:

- Precision and recall for each critical entity/field.
- Correctness of relationships and party roles.
- Entity-linking accuracy (whether a document reference maps to the right canonical record).
- Percentage of results with usable source evidence.
- Rate of uncertain cases correctly sent to human review.
- Human correction time per document.
- End-to-end time or cost per document.
- User trust and adoption after a realistic trial.

Do not collapse everything into one “AI accuracy” score. Missing a low-risk optional field and assigning an invoice to the wrong supplier have different consequences. Set thresholds with process owners; the image supplies no target values.

### Step 7 — Prototype, review, and iterate

Run the sample through a simple pipeline, compare output with the reviewed labels, inspect failures with users, and refine the schema and prompts/rules. Test ordinary, difficult, and ambiguous cases separately. Keep a record of which errors came from text extraction, model interpretation, entity matching, or the target data model.

---

## 14. Recommended first version

A manageable first release would:

1. Accept **extracted text plus document ID and source/page references**.
2. Support **one document type** and a small agreed schema.
3. Extract entity/fact candidates and their supporting text.
4. Return strict structured data that can be validated by the application.
5. Match entities against a small, authoritative reference table.
6. Leave weak or ambiguous matches unresolved and send them to a review screen.
7. Store document, extraction, evidence, match decision, and reviewer corrections.
8. Provide a basic search/list view or API to inspect and query results.

For an initial prototype, the core deliverable is a trustworthy extraction and review workflow—not a fully autonomous agent that updates financial or legal systems without approval.

### Example result shape

This is one possible application schema, not something specified by the slide:

```json
{
  "document_id": "doc-001",
  "document_type": "invoice",
  "facts": [
    {
      "name": "invoice_date",
      "value": "2026-07-01",
      "source_text": "Invoice date: 1 July 2026",
      "page": 1,
      "status": "extracted"
    }
  ],
  "entities": [
    {
      "type": "supplier",
      "mention": "Northwind Services",
      "matched_record_id": null,
      "match_status": "needs_review"
    }
  ],
  "relationships": [],
  "review_required": true
}
```

The key properties are a stable schema, source evidence, explicit match status, and a way to represent unresolved results.

---

## 15. Technology choices

Treat this as a reference stack, not a requirement. Select cloud services only after confirming budget, document sensitivity, region, identity controls, and what platforms the organization already uses.

### Suggested prototype stack

| Layer | Practical choice | Why it fits |
|---|---|---|
| **Application language** | Python | Broad support for text processing, APIs, data validation, and document/AI SDKs. |
| **API / service** | FastAPI | A small HTTP service can accept extracted text, run the pipeline, and return validated results. |
| **Schema validation** | Pydantic plus JSON Schema | Validate required fields, types, enums, and allowed relationships before saving model output. |
| **Text extraction input** | Start with the extracted text already available, as the slide describes. For image/PDF OCR, compare Google Document AI, Azure AI Document Intelligence, and Amazon Textract on the team’s own sample. | The proposal starts after OCR/text extraction, but a working end-to-end demonstration may need an OCR service. Managed services provide OCR and/or document-specific extraction; capabilities vary by product and processor. |
| **Entity/fact extraction** | A capable language model with schema-constrained output, paired with deterministic validation and domain rules | LLMs can interpret varied prose; schema constraints help produce application-shaped output. Schema-valid JSON does not prove that a fact is true, so preserve evidence and validate it. |
| **Entity resolution** | Deterministic exact ID/name matching first; fuzzy string matching and embeddings only to generate candidates; require a score threshold and/or human review | Resolving “Acme Inc.” to the correct canonical organization is a separate task from extracting the mention. Similarity alone should not silently establish identity. |
| **Primary database** | PostgreSQL with relational tables and JSONB for flexible extraction payloads | Supports document metadata, entities, facts, relationships, review status, and audit history in one approachable store. |
| **Semantic model / vocabulary** | Use the organization’s existing model; represent its concepts and labels explicitly. Consider W3C SKOS for controlled vocabularies and W3C PROV concepts for provenance where interoperability matters. | The slide specifically asks to connect information to an existing business data model. Reuse it rather than building a competing ontology. |
| **Evidence / source files** | Existing document store or controlled object storage; save text spans, page numbers, and source IDs in the database | Lets a reviewer trace each result back to the document. Follow company retention and access rules. |
| **Review interface** | A small React web screen or server-rendered web page with a document/evidence panel and accept/edit/reject actions | Users need to inspect the resulting information; a review screen also captures corrections for evaluation. |
| **Background processing** | For a small prototype, a simple task queue or synchronous processing is enough. Add Celery/RQ plus Redis or a cloud queue when volume and retries require it. | Avoid distributed components until asynchronous volume, retries, or long-running OCR actually require them. |
| **Deployment / operations** | Containerized service, managed secrets, role-based access, logs, and versioned prompts/models/configuration | Makes runs traceable and gives the team a route to repeat, debug, and govern processing. |

Official product documentation describes Google Document AI processors for OCR and specialized extraction, including invoice fields; Amazon Textract’s expense APIs return invoice/receipt summary fields and line items; and Azure Document Intelligence supports layout and query-field extraction. These are alternatives to benchmark, not endorsements or a claim that one is best for this project. [Google Document AI processor overview](https://docs.cloud.google.com/document-ai/docs/overview), [Google processor list](https://docs.cloud.google.com/document-ai/docs/processors-list), [Amazon Textract AnalyzeExpense](https://docs.aws.amazon.com/textract/latest/APIReference/API_AnalyzeExpense.html), [Azure query fields](https://learn.microsoft.com/en-us/azure/ai-services/document-intelligence/concept/query-fields?view=doc-intel-4.0.0).

For model output, use a constrained schema feature where the chosen model provider supports it, then validate it in your own application. OpenAI’s current documentation distinguishes schema-adherent Structured Outputs from JSON mode, which guarantees valid JSON but does not by itself guarantee conformance to a supplied schema. This is an example of the capability to look for; the project can select another provider or a local model based on its requirements. [OpenAI Structured Outputs documentation](https://developers.openai.com/api/docs/guides/structured-outputs).

SKOS is a W3C vocabulary standard for representing concept schemes and controlled vocabularies; PROV-O is a W3C ontology for representing provenance. They can inform interoperability and evidence modeling, but a small prototype can store the same ideas in ordinary relational tables if that is more practical. [W3C SKOS reference](https://www.w3.org/TR/skos-reference/), [W3C PROV overview](https://www.w3.org/TR/prov-overview/).

### What I would avoid in the first prototype

- Training a custom foundation model before measuring the baseline.
- Adding a graph database just because the output contains relationships. Start with relational tables; add a graph store only if graph queries or scale make it useful.
- Embedding/vector search as the authoritative identity decision. It can find candidates, but the match needs validation.
- Autonomous write-back to ERP, finance, or contract systems before users have reviewed results and the integration has appropriate controls.
- Designing for every department and document type before one workflow has been validated.

### Cloud versus local / self-hosted

Choose based on the organization’s constraints, not fashion:

- **Managed cloud services:** Often the fastest route to a prototype and can provide specialized document processors, but require review of data residency, retention, security configuration, and cost.
- **Self-hosted OCR/model services:** May offer more control over where documents are processed, but the team takes on deployment, scaling, model maintenance, and quality evaluation.
- **Hybrid:** Keep source documents in approved storage, use a permitted processing service, and persist normalized results and evidence in the existing data platform.

The actual document sensitivity and company policy are unknown from the slide, so no specific provider should be chosen until those facts are established.

---

## 16. A concise project plan

### Phase A — Discover (about 1 week for a student prototype)

- Pick a document type and workflow.
- Interview processors, analysts, and the relevant domain owner.
- Map current steps and pain points.
- Agree on the first schema and canonical reference data.
- Obtain a permitted sample and define review labels.

### Phase B — Build a baseline

- Accept already extracted text.
- Define a strict extraction schema.
- Implement extraction, evidence capture, validation, and match status.
- Save output and show it in a simple review page.
- Measure field, relationship, match, and evidence quality against labels.

### Phase C — Improve and demonstrate

- Add OCR only if the prototype needs to start from scanned files.
- Compare candidate OCR/IDP providers using the same sample and measures.
- Add correction capture and error analysis.
- Demonstrate an ordinary case, an ambiguous case, and a failure/review case.
- Present measured results, limitations, and a clear next-step recommendation.

A credible demo should show that the system knows when it does **not** know: it should preserve document evidence and ask for review on uncertain entity links or interpretations.

---

## 17. Research conclusion

The underlying problem is shared across several roles, but they experience it differently. Operations teams feel the manual handling burden; analysts feel the cost of turning text into usable datasets; domain owners feel the risk of incorrect facts; and governance/IT teams need the process to be controlled and auditable. The data team is the stated builder, while the first user and first document type still need to be established through discovery.

The most defensible approach is to begin with one real workflow, learn its current process and error costs, define the target business vocabulary with its owners, and prototype a source-connected, multimodal extraction pipeline that keeps evidence and uncertainty visible. Make first-time setup guided and repeatable, then add dynamic ingestion through the connectors and freshness level the startup needs. Technology should support that workflow: modality-specific parsing/transcription, schema-guided extraction, conservative entity matching, a conventional database, incremental updates, and a human review path. Expand to additional modalities and connectors based on validated priorities.
