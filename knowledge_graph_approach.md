# Document-to-Knowledge Graph: Approach and Technology

## Purpose

This document describes a multimodal records system for turning documents, images, audio, and video into structured entities, facts, and relationships; connecting them to an existing business model where reliable; and making evidence and uncertainty inspectable. It should be easy to set up for a new organization and ingest new or changed source data dynamically after setup.

The proposed implementation direction is an **incremental graph updater inspired by React DOM rendering**. React DOM is a user-interface renderer, not a graph database or document extraction library. The useful analogy is its update pattern: compare desired output with current state, calculate a small change set, and apply only the changes required. This project would implement that pattern for document-derived knowledge.

## Problem to solve

Business records contain prose, tables, forms, images, recordings, video, and paper-origin scans. OCR or transcription can make some content searchable, but downstream workflows need consistent fields and explicit links. The system must:

- identify business entities, facts, and relationships;
- map mentions to canonical records in the existing business model when there is sufficient evidence;
- retain the original text and location supporting each result;
- distinguish document-stated facts from normalization or interpretation;
- represent ambiguous, conflicting, or unresolved results without silently guessing;
- update stored knowledge when a document changes without rebuilding unrelated data.
- provide guided initial setup for the organization's approved sources and data model.
- discover or receive new/changed records after setup and expose sync status and errors.
- preserve modality-appropriate evidence: text spans/pages, image regions, audio timestamps, video timecodes/frame references, or physical archive identifiers.

The initial user and first document type remain to be validated. A likely first user is a business data analyst or document reviewer.

## Multimodal input and evidence

Treat every input as a source asset with a stable identity, version, metadata, permissions, and processing status. Modality-specific processing can converge on a common extraction format:

- **Documents and PDFs:** Parse native text and layout; use OCR for scanned pages; retain page/section references, table structure, and bounding boxes where available.
- **Standalone images:** Use OCR and visual analysis to detect text, labels, objects, or context relevant to configured fields; retain image regions or bounding boxes.
- **Audio:** Transcribe speech and preserve timestamps; speaker diarization may help distinguish speakers, but a speaker label does not establish a verified person's identity.
- **Video:** Extract metadata, sample relevant key frames, process audio/transcripts, and preserve timecodes. Process frames at a rate appropriate to the use case rather than assuming every frame needs analysis.
- **Paper records:** Scan or photograph through an approved capture workflow and retain physical archive references such as box, folder, shelf, or file number.

Normalize outputs into a shared envelope such as `source_id`, `source_version`, `modality`, `item_type`, `value`, `verbatim_evidence`, `location`, `processing_run`, and `review_status`. `location` may represent a page/section, image region, audio interval, video time range/frame, or physical archive reference. OCR, transcription, and visual interpretation can contain errors, so preserve the evidence and its quality/status.

## First-time setup and dynamic ingestion

Provide guided onboarding so a new organization can connect approved sources without a bespoke installation effort for every source:

1. Create an organization workspace and configure administrators and user roles.
2. Connect approved sources through configurable adapters such as upload, watched folder, shared drive, document system, database, or API, using least-privilege access.
3. Select collections/folders, allowed modalities, document types, retention rules, and the initial business vocabulary/schema.
4. Preview discovered records and access scope before importing.
5. Run a resumable initial backfill with progress, duplicate checks, and visible failures.
6. Enable ongoing ingestion through the source's supported mechanism: event/webhook, change-data capture, watched folder, scheduled polling, or manual upload as fallback.
7. Show last sync, queued/processing/failed items, retry controls, and audit history.

Keep source connectors, modality processors, and extraction schemas as separate components. Then new sources, formats, or document types can be added through configuration/adapters without reinstalling the whole product. Version schemas and define migration or reprocessing behavior. Agree on data freshness with the startup; dynamic can mean near-real-time events or scheduled sync depending on source capabilities and cost.

New items should enter a queue, be checked for duplicate identity/content, and be processed idempotently. Start extraction and reconciliation only after source authorization and source version are established.

## React DOM analogy

| React DOM concept | Knowledge processing equivalent |
|---|---|
| Component tree / desired UI | Canonicalized extraction for one source asset (document, image, audio, or video) |
| Previous rendered tree | Last accepted extraction snapshot for that source asset/version |
| Reconciliation / diff | Compare new and stored facts, entities, and evidence |
| DOM mutations | Database or graph mutations for changed records only |
| Stable keys | Stable identifiers for source asset, mention, fact, relationship, and evidence |

The analogy is about **incremental reconciliation**. It does not imply that React DOM can update a knowledge graph directly, or that its UI diff algorithm should be reused as a graph algorithm.

## Proposed processing flow

1. **Connect sources.** Complete guided onboarding, permission checks, source selection, and initial backfill.
2. **Receive new or changed assets.** Use the configured event, watch, polling, or upload mechanism; record source version and content hash.
3. **Process by modality.** Parse document text/layout, OCR images/scans, transcribe audio, and analyze configured video frames/audio.
4. **Extract candidates.** Find entities, facts, and relationships with modality-appropriate evidence locations.
5. **Normalize carefully.** Store normalized values alongside verbatim text/transcript or visual evidence; never discard the source.
6. **Resolve entities conservatively.** Use exact IDs and approved aliases first. Similarity search can suggest candidate records, but should not silently establish identity.
7. **Validate.** Check types, required fields, relationship direction/roles, and allowed vocabulary against the business model.
8. **Reconcile.** Compare the new extraction snapshot with the stored snapshot for the same source asset/version and produce a change set.
9. **Apply atomically.** Apply additions, revisions, and removals in a transaction. Record run/version metadata for audit and recovery.
10. **Review and query.** Expose evidence, match status, ambiguity, ingestion status, and changes for users and downstream systems.

## Incremental update design

### Stable identity

Each extracted item should have an identity that survives reprocessing when its meaning and source are unchanged. Candidate identity ingredients include:

- source asset ID and source version;
- item kind (entity mention, fact, relationship, evidence);
- canonical subject/object IDs where known;
- predicate or field name;
- normalized value;
- source location or source-span fingerprint.

Do not use a model-generated display label as the sole ID. Text edits, page shifts, or changed extraction models can move spans; where identity cannot be matched confidently, mark the old item as superseded and the new item as added rather than applying a potentially incorrect edit.

### Change set

For each source asset, compare the new extraction with the last stored snapshot and classify items as:

- **Add:** New supported fact, mention, relationship, or evidence.
- **Update:** Existing logical item whose value, status, normalization, or evidence changed.
- **Remove / supersede:** Previously supported item absent from the new document version.
- **Unchanged:** Same logical item and support; perform no graph mutation.
- **Needs review:** Match or change is ambiguous, conflicting, or high impact.

Treat removal carefully. A document revision removes that document's support for a fact; it should not delete a shared canonical entity or a fact still supported by another document. Model source support separately from the canonical entity/fact wherever multiple documents can assert the same information.

### Transaction and audit behavior

- Store document versions and extraction runs.
- Apply a document's change set transactionally so partial updates do not leave inconsistent links.
- Preserve previous values or an audit log for review and rollback.
- Make repeated processing of the same version idempotent: applying the same result twice should not duplicate nodes or edges.
- Separate extraction status from review/approval status.

## Data representation options

### Relational database

Use tables for documents, versions, entities, facts, relationships, evidence, and review events. JSON columns can hold flexible model output while core entities and relationships stay queryable and constrained.

**Advantages:** mature transactions, constraints, audit-friendly records, familiar SQL, and a simple prototype path.  
**Tradeoffs:** multi-hop relationship exploration may require complex joins and careful query design.

**Candidate:** PostgreSQL.

### Property graph

Represent entities as nodes and named relationships as directed edges, with properties such as status, timestamps, or source IDs. Graph queries are a natural fit for multi-hop connections among organizations, contracts, invoices, and services.

**Advantages:** relationships are first-class and connected-data traversals are direct.  
**Tradeoffs:** introduces a separate graph technology and operational model; provenance, constraints, and change ownership still need deliberate modeling.

**Candidate:** Neo4j or another property-graph platform.

### RDF knowledge graph

Represent statements as triples using shared identifiers and vocabularies. RDF is useful when standards-based interchange, formal ontologies, or SPARQL querying are important. Use an existing business vocabulary when available rather than inventing a competing ontology.

**Useful standards:**

- **PROV-O** for representing provenance and derivation.
- **SHACL** for validating RDF data graphs against declared constraints.
- **SKOS** when managing controlled terms and vocabulary mappings.

### Recommended starting point: hybrid-ready relational model

Start with PostgreSQL tables for operational processing, evidence, audit, and review. Model relationship records explicitly and preserve stable canonical IDs. This supports reliable incremental updates without requiring graph infrastructure at the start. Keep the schema and identifiers suitable for later projection into a property graph or RDF store if multi-hop queries or interoperability justify it.

## Extraction methods

Use the simplest combination that meets measured needs for the chosen document type:

1. **Rules and templates:** Good for stable layouts and deterministic fields such as invoice numbers or known labels; less robust to format variation.
2. **Traditional NLP:** Named-entity recognition and relation extraction models can be trained or adapted for a stable domain with labeled examples.
3. **Layout-aware document models:** Combine text, visual structure, and page layout for forms and tables where spatial context matters.
4. **Schema-guided LLM extraction:** Flexible for varied language; require constrained output, application-side validation, and source evidence checks. Valid JSON alone does not guarantee factual correctness.
5. **Human review:** Essential for uncertain entity matches, conflicts, and consequential relationships. Capture reviewer corrections as labeled examples for improvement.

Research example: the 2024 EMNLP paper *Extract, Define, Canonicalize* studies LLM-based knowledge graph construction from text. Its extract/define/canonicalize framing is relevant to turning varied mentions into consistent graph types and identifiers, but production results still need evidence, validation, and review. See [EMNLP 2024 paper](https://aclanthology.org/2024.emnlp-main.548/).

Document-parsing research also covers a spectrum from modular parsing pipelines to end-to-end vision-language models. Layout-aware techniques are relevant when tables, page structure, or visual placement affect meaning. See [Document Parsing Unveiled (2024)](https://arxiv.org/abs/2410.21169) and [Deep Learning based Visually Rich Document Content Understanding (2024)](https://arxiv.org/abs/2408.01287).

## Entity resolution and mapping

Entity recognition answers “what organization is mentioned?” Entity resolution answers “which canonical organization record, if any, does this mention refer to?” Keep these as separate stages.

Suggested decision sequence:

1. Match stable identifiers (registration number, internal ID, or other authoritative key) when present.
2. Match exact approved names and aliases.
3. Generate candidate records with normalized string similarity or embeddings where useful.
4. Apply domain-specific checks and thresholds.
5. Auto-link only when policy and evidence justify it; otherwise retain candidates as suggestions and route for review.

Store the mention, chosen canonical ID (if any), match method, status, and evidence. Do not let a similarity score erase ambiguity.

## Evidence, uncertainty, and conflicts

For each extracted assertion, retain at least:

- source document and version;
- verbatim supporting text and source location;
- normalized value, if derived;
- extraction method/model and version;
- match status and confidence or review state;
- reviewer decision and timestamp, if reviewed.

Represent conflicting assertions as separate sourced claims until an authorized rule or reviewer resolves them. A missing year, unclear party role, or ambiguous organization should remain unknown or unresolved, rather than being inferred as fact. PROV-O offers a standard vocabulary for derivation and provenance relationships: [W3C PROV-O](https://www.w3.org/TR/prov-o/).

## Validation and quality evaluation

For relational data, enforce types, uniqueness, foreign keys, and domain rules in the database and service layer. For RDF graphs, SHACL can express constraints and produce validation reports: [W3C SHACL Recommendation](https://www.w3.org/TR/shacl/).

Evaluate on a representative sample labeled by domain users. Measure separately:

- field-level precision and recall for required facts;
- entity resolution accuracy and false-link rate;
- relationship type and direction accuracy;
- source evidence coverage and whether spans support the assertion;
- proportion of uncertain cases correctly sent to review;
- reviewer acceptance, correction, and rejection rates;
- time and effort compared with the current workflow;
- correctness and idempotence of incremental changes.

Include clear examples, ambiguous cases, conflicts, and document revisions. A single overall confidence score is not enough to establish correctness.

## Suggested prototype technology stack

| Layer | Candidate | Role |
|---|---|---|
| API/service | Python with FastAPI | Coordinate source ingestion, extraction, validation, and structured results. |
| Onboarding/connectors | Configurable source adapters plus OAuth/API credentials where applicable | Connect approved sources once, then discover or receive new and changed items dynamically. |
| Queue/orchestration | Start with a background task queue; consider Celery/RQ or managed queues | Decouple sync from long-running OCR, transcription, video processing, and retries. |
| Data contract | Pydantic / JSON Schema | Validate item shape and required types. |
| Extraction | Schema-constrained LLM plus deterministic rules | Interpret varied wording while retaining predictable checks. |
| Entity candidates | Exact lookup first; fuzzy/embedding search as candidate generation | Suggest possible canonical records without automatic identity assumptions. |
| Primary persistence | PostgreSQL | Documents, snapshots, facts, relationships, evidence, review and audit state. |
| Optional graph projection | Neo4j/property graph or RDF triple store | Add if graph traversal or standards-based knowledge exchange is needed. |
| Document/image processing | Native text/layout parsing, OCR, and visual analysis selected for sample quality | Process PDFs, scans, and standalone images; retain page and region references. |
| Audio/video processing | Speech recognition/transcription with timestamps; video key-frame sampling plus audio processing | Support recordings and video with time-based evidence; select providers after checking quality and data policy. |
| Review interface | React application | Show source passages, image regions, or media timecodes beside extracted values and support review decisions. |

For invoices and receipts, Amazon Textract's `AnalyzeExpense` API is one domain-specific option; it returns summary fields and line-item groups. It is an example to benchmark, not a requirement: [AWS AnalyzeExpense API](https://docs.aws.amazon.com/textract/latest/APIReference/API_AnalyzeExpense.html).

## Example conceptual records

This is illustrative rather than a final schema:

```text
Document(id, current_version, status)
DocumentVersion(id, document_id, content_hash, received_at)
ExtractionRun(id, document_version_id, method_version, created_at)
Entity(id, canonical_type, canonical_properties)
Mention(id, extraction_run_id, text, location, candidate_entity_id, match_status)
Claim(id, subject_id, predicate, value_or_object_id, status)
Evidence(id, claim_id, document_version_id, source_text, location, extraction_run_id)
ReviewEvent(id, target_id, reviewer, decision, timestamp, notes)
```

The source-backed `Claim` and `Evidence` distinction supports multiple documents making or supporting claims about the same canonical entity. The incremental reconciler compares extraction runs per document version and updates only affected mentions, claims, and evidence links.

## Main risks and mitigations

| Risk | Mitigation |
|---|---|
| A model invents or overstates a relationship | Require source support; validate relationship types; send uncertain cases to review. |
| Similar names are merged incorrectly | Resolve in stages; preserve mention identity; use conservative auto-link thresholds. |
| A document update deletes a fact another document still supports | Track evidence/support per document and remove only the changed source's support. |
| Extraction changes make incremental identity unstable | Version extraction runs; use stable IDs and conservative matching; supersede uncertain matches. |
| Graph infrastructure is introduced before it adds value | Begin with relational storage and measure query needs before adding a graph store. |
| OCR or layout errors are mistaken for reasoning errors | Retain text/layout evidence and evaluate upstream OCR quality separately. |

## Recommended implementation sequence

1. Select one user group, workflow, one or two modalities, and one high-value record type with the startup.
2. Define guided onboarding, source permissions, initial backfill, sync mechanism, freshness target, and support expectations.
3. Define the business vocabulary, minimum schema, stable identifiers, modality-specific evidence locations, and review rules.
4. Assemble permitted sample assets across the chosen modalities, including clear, ambiguous, and conflicting cases; label expected output.
5. Build modality-specific parsing/transcription followed by common extraction, validation, and evidence capture.
6. Store source-versioned snapshots and implement an idempotent add/update/remove/supersede change set.
7. Add a review/search screen with source evidence and ingestion/sync status.
8. Evaluate extraction, search, matching, provenance, dynamic ingestion, and incremental-update quality separately by modality.
9. Expand modalities, connectors, entity resolution, graph projection, or graph database as validated needs require.

## References

- Zhang and Soh, [“Extract, Define, Canonicalize: An LLM-based Framework for Knowledge Graph Construction,” EMNLP 2024](https://aclanthology.org/2024.emnlp-main.548/).
- [Document Parsing Unveiled: Techniques, Challenges, and Prospects for Structured Information Extraction (2024)](https://arxiv.org/abs/2410.21169).
- [Deep Learning based Visually Rich Document Content Understanding: A Survey (2024)](https://arxiv.org/abs/2408.01287).
- W3C, [PROV-O: The PROV Ontology](https://www.w3.org/TR/prov-o/).
- W3C, [Shapes Constraint Language (SHACL)](https://www.w3.org/TR/shacl/).
- Neo4j, [Graph database concepts](https://neo4j.com/docs/getting-started/appendix/graphdb-concepts/).
- PostgreSQL, [JSON types](https://www.postgresql.org/docs/current/datatype-json.html).
- AWS, [AnalyzeExpense API](https://docs.aws.amazon.com/textract/latest/APIReference/API_AnalyzeExpense.html).
