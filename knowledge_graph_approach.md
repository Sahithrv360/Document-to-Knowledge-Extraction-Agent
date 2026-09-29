# Document-to-Knowledge Graph: Approach and Technology

## Purpose

This document consolidates the project approach discussed so far. The problem is to turn already-extracted business document text into structured entities, facts, and relationships; connect them to an existing business model where reliable; and make evidence and uncertainty inspectable.

The proposed implementation direction is an **incremental graph updater inspired by React DOM rendering**. React DOM is a user-interface renderer, not a graph database or document extraction library. The useful analogy is its update pattern: compare desired output with current state, calculate a small change set, and apply only the changes required. This project would implement that pattern for document-derived knowledge.

## Problem to solve

Business documents contain information in prose, tables, and forms. OCR can produce readable text, but downstream analytics need consistent fields and explicit links. The system must:

- identify business entities, facts, and relationships;
- map mentions to canonical records in the existing business model when there is sufficient evidence;
- retain the original text and location supporting each result;
- distinguish document-stated facts from normalization or interpretation;
- represent ambiguous, conflicting, or unresolved results without silently guessing;
- update stored knowledge when a document changes without rebuilding unrelated data.

The initial user and first document type remain to be validated. A likely first user is a business data analyst or document reviewer.

## React DOM analogy

| React DOM concept | Knowledge processing equivalent |
|---|---|
| Component tree / desired UI | Canonicalized extraction for one document |
| Previous rendered tree | Last accepted extraction snapshot for that document |
| Reconciliation / diff | Compare new and stored facts, entities, and evidence |
| DOM mutations | Database or graph mutations for changed records only |
| Stable keys | Stable identifiers for document, mention, fact, relationship, and evidence |

The analogy is about **incremental reconciliation**. It does not imply that React DOM can update a knowledge graph directly, or that its UI diff algorithm should be reused as a graph algorithm.

## Proposed processing flow

1. **Receive document text.** The project problem begins after OCR/text extraction. Add OCR as an upstream stage only if the prototype needs to start from scans or PDFs.
2. **Extract candidates.** Find entities, facts, and relationships with source spans (document, page/section, character offsets or other stable location).
3. **Normalize carefully.** Store normalized values alongside verbatim text; never discard the source wording.
4. **Resolve entities conservatively.** Use exact IDs and approved aliases first. Similarity search can suggest candidate records, but should not silently establish identity.
5. **Validate.** Check types, required fields, relationship direction/roles, and allowed vocabulary against the business model.
6. **Reconcile.** Compare the new extraction snapshot with the stored snapshot for the same document and produce a change set.
7. **Apply atomically.** Apply additions, revisions, and removals in a transaction. Record run/version metadata for audit and recovery.
8. **Review and query.** Expose evidence, match status, ambiguity, and changes for users and downstream systems.

## Incremental update design

### Stable identity

Each extracted item should have an identity that survives reprocessing when its meaning and source are unchanged. Candidate identity ingredients include:

- document ID and document version;
- item kind (entity mention, fact, relationship, evidence);
- canonical subject/object IDs where known;
- predicate or field name;
- normalized value;
- source location or source-span fingerprint.

Do not use a model-generated display label as the sole ID. Text edits, page shifts, or changed extraction models can move spans; where identity cannot be matched confidently, mark the old item as superseded and the new item as added rather than applying a potentially incorrect edit.

### Change set

For each document, compare the new extraction with the last stored snapshot and classify items as:

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
| API/service | Python with FastAPI | Receive text, coordinate extraction, validate, and return structured results. |
| Data contract | Pydantic / JSON Schema | Validate item shape and required types. |
| Extraction | Schema-constrained LLM plus deterministic rules | Interpret varied wording while retaining predictable checks. |
| Entity candidates | Exact lookup first; fuzzy/embedding search as candidate generation | Suggest possible canonical records without automatic identity assumptions. |
| Primary persistence | PostgreSQL | Documents, snapshots, facts, relationships, evidence, review and audit state. |
| Optional graph projection | Neo4j/property graph or RDF triple store | Add if graph traversal or standards-based knowledge exchange is needed. |
| OCR (if required) | Benchmark a document OCR/IDP service or local OCR on project samples | Upstream text extraction; the original problem begins after this step. |
| Review interface | React application | Show source text beside extracted values and support accept/edit/unresolved decisions. |

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

1. Select one document type and high-value workflow with intended users.
2. Define the business vocabulary, minimum schema, stable identifiers, and review rules.
3. Assemble a permitted sample set with clear, ambiguous, and conflicting cases; label expected output.
4. Build extraction from already-extracted text with evidence spans and unresolved status.
5. Store document-scoped snapshots and implement an idempotent add/update/remove/supersede change set.
6. Add a review screen that makes each extracted result traceable to source evidence.
7. Evaluate field, relationship, match, provenance, and incremental-update quality.
8. Add OCR, advanced entity resolution, graph projection, or graph database only when sample results and user queries demonstrate the need.

## References

- Zhang and Soh, [“Extract, Define, Canonicalize: An LLM-based Framework for Knowledge Graph Construction,” EMNLP 2024](https://aclanthology.org/2024.emnlp-main.548/).
- [Document Parsing Unveiled: Techniques, Challenges, and Prospects for Structured Information Extraction (2024)](https://arxiv.org/abs/2410.21169).
- [Deep Learning based Visually Rich Document Content Understanding: A Survey (2024)](https://arxiv.org/abs/2408.01287).
- W3C, [PROV-O: The PROV Ontology](https://www.w3.org/TR/prov-o/).
- W3C, [Shapes Constraint Language (SHACL)](https://www.w3.org/TR/shacl/).
- Neo4j, [Graph database concepts](https://neo4j.com/docs/getting-started/appendix/graphdb-concepts/).
- PostgreSQL, [JSON types](https://www.postgresql.org/docs/current/datatype-json.html).
- AWS, [AnalyzeExpense API](https://docs.aws.amazon.com/textract/latest/APIReference/API_AnalyzeExpense.html).
