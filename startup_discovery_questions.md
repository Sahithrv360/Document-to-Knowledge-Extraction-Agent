# Questions for the Startup: Problem Discovery and Expectations

Use this questionnaire in a meeting with the startup that provided the problem statement. It is intended to clarify what they expect from the project before the team commits to a persona, features, architecture, or prototype scope.

> **Tip:** Start with the questions in **Priority questions** if meeting time is short. The remaining questions are prompts to use when relevant; they do not all need to be asked in one session.

## 1. Priority questions

1. **Who exactly is experiencing this problem?** Is the intended user one specific role, such as a records manager or contract administrator, or a group of roles across an organization?
2. **Which organization or industry should we design for first?** Is the intended solution cross-industry, or should the prototype focus on one sector and workflow?
3. **What do you expect us to deliver?** For example, research and design documents, a clickable prototype, a working web application, an extraction pipeline, a knowledge graph, or a demonstration using sample documents?
4. **What does a successful project look like to you?** Which outcomes or acceptance criteria will you use to judge it?
5. **Which documents and information are in scope first?** For example, client records, contracts, land/property agreements, invoices, or amendments.
6. **Where do those documents currently live?** Are they digital files, email attachments, business systems, scanned paper, or physical archives?
7. **What should the system do with a document?** Should it only extract and organize information, or also find related documents, flag mismatches, answer questions, and support review?
8. **What information should be extracted from each in-scope document type?** Do you have an existing schema, sample record, business vocabulary, or required fields?
9. **How should the system handle missing, conflicting, or uncertain information?** Should it flag the issue, suggest candidates, ask a user to review, or follow a defined rule?
10. **Can you provide representative sample documents and expected results?** Include ordinary, ambiguous, amended, duplicate-looking, and difficult cases, with permission to use them.
11. **What technologies or constraints are already required?** Include existing databases, cloud/platform preferences, integrations, security requirements, and any technology that must or must not be used.
12. **What is the expected project timeline, team size, and demo environment?**

## 2. Users and stakeholders

- Who is the primary user and who else interacts with the system?
- Who currently searches for, files, reviews, or updates client and contract records?
- Who owns the business meaning of fields such as client ID, contract status, property ID, renewal date, or obligation?
- Who approves a match between a document mention and an existing client, property, or business record?
- Who will use the extracted information: operations staff, managers, analysts, auditors, customers, or another system?
- Are there different user roles with different permissions or workflows?
- How often do these users perform the task, and roughly how many people are affected?
- Can we observe a user completing the current task and interview them about exceptions?

## 3. Problem and current workflow

- What specific problem led you to propose this project?
- Can you walk us through one recent example from receiving a document to finding and using its information?
- How are files named, categorized, indexed, and stored today?
- Which parts are digital, paper-based, scanned, or split across several systems?
- How do users search for a record when they know only partial information, such as an alternate name, address, parcel number, client ID, or date?
- What causes records to be missed, duplicated, misfiled, or linked to the wrong client or agreement?
- What happens when a relevant document cannot be found or a wrong version is used?
- How much time does the current process take, and where does the most rework occur?
- What existing tools or workarounds do users rely on?
- Which parts of the current process work well and should be preserved?

## 4. Scope and document types

- Which organization type or industry should the initial version serve?
- Which one or two document types should the first prototype support?
- What does “all relevant information” mean for each selected document type?
- Which documents are related to one another—for example, a contract, amendment, invoice, client form, property record, or correspondence?
- Do documents contain tables, handwriting, stamps, signatures, attachments, or poor-quality scans?
- Do you expect the team to implement OCR, or will extracted text be provided?
- Are documents in multiple languages or formats?
- Should the system index entire documents, selected fields, or both?
- What kinds of files and maximum document sizes or volumes should be supported?
- Which document types are explicitly out of scope for the first release?

## 5. Extraction requirements

- Which fields are mandatory, optional, or document-type-specific?
- Should the system extract parties and their roles, client identifiers, addresses, parcel/property details, contract numbers, dates, values, currency, terms, rights, obligations, conditions, renewals, termination clauses, signatures, and references?
- Are there existing schemas, forms, databases, controlled vocabularies, or sample reports we should reuse?
- Should original wording and normalized values both be stored?
- Should each extracted field include the source passage, page number, section, or coordinates?
- Should we capture confidence, review status, extraction method, model version, and processing time?
- What distinctions matter between facts explicitly stated in a document and values inferred or normalized by the system?
- Are there fields that must never be inferred or auto-filled?
- How should tables, multi-page clauses, cross-references, and attachments be handled?
- What does “complete extraction” mean, and how should omissions be measured?

## 6. Search, relationships, and knowledge representation

- What should users be able to search by: client name, alias, ID, address, parcel, contract number, date, document type, clause, or related party?
- Should search return documents only, structured fields, connected records, or all of these?
- Which relationships should the system represent—for example, client signed contract, contract covers property, amendment changes agreement, or invoice refers to contract?
- Should users browse connected entities and documents as a graph, or is a searchable record view sufficient?
- What questions should users be able to answer using the extracted information?
- Is there an existing business data model or canonical record system to map to?
- Do you expect a graph database, a relational database, a hybrid design, or do you want a recommendation based on the needs?
- Are there interoperability requirements such as RDF, an ontology, or an API schema?

## 7. Matching, duplicate detection, and document versions

- What authoritative identifiers exist for clients, companies, contracts, properties, and other assets?
- How are aliases, former names, misspellings, abbreviations, and duplicate records handled today?
- Should the system automatically link a document to a record, or only suggest possible matches for a person to confirm?
- What evidence and confidence threshold would be acceptable for automatic matching?
- How should possible duplicate clients or contracts be presented?
- How can the system identify the latest signed agreement, amendment, renewal, or superseded version?
- When two documents state different values, should both be retained, or should a defined source hierarchy determine which is current?
- What should happen when a document is corrected, replaced, or deleted?
- If multiple documents support the same fact, should the system preserve all supporting sources?

## 8. Review and correction workflow

- Which results require human review before being used?
- Who reviews uncertain matches, missing fields, conflicting values, and high-impact terms?
- What should reviewers be able to do: accept, edit, reject, merge, split, or mark unresolved?
- Should the reviewer see the document beside the extracted values?
- Should corrections be retained for audit or used to improve extraction and matching?
- How should review tasks be prioritized?
- Does the system need approval steps or sign-off before data is exported or used downstream?

## 9. Privacy, security, access, and governance

- Do the sample and production documents contain personal, financial, confidential, legal, or otherwise sensitive data?
- Which user roles may access each document and extracted field?
- What authentication, authorization, audit-log, and encryption requirements apply?
- Are there data residency, retention, deletion, or legal hold requirements?
- May documents be processed by external cloud or AI services? Are local or private deployments required?
- Are there restrictions on sending document content to third-party model providers?
- How should the system honor existing document permissions when indexing and searching?
- Who is responsible for approving data handling and security choices?

## 10. Integrations and technical environment

- Which file stores, document management systems, CRMs, contract systems, databases, or identity providers are currently used?
- Must the prototype integrate with them, or can it use a prepared sample set?
- What data should be imported, and where should results be written?
- Are APIs, SDKs, or integration documentation available?
- Do you have required programming languages, cloud providers, databases, model providers, or deployment environments?
- Is the intended system cloud-hosted, on-premises, local, or undecided?
- What document volume, throughput, availability, and response time should the system handle?
- Do you expect a web interface, API, command-line demonstration, or combination?
- Who will host, maintain, and support the system after the project ends?

## 11. Evaluation and success criteria

- What baseline should we compare against: current search time, manual data-entry effort, missed-document rate, or error rate?
- How will you measure whether users find the correct documents?
- Which fields and relationships are most important to evaluate?
- What rates of false matches, missed facts, or unresolved cases are acceptable for a prototype?
- Should quality be measured separately by document type and field rather than as one overall score?
- Can domain experts label a test set and review the outputs?
- What counts as a successful demonstration?
- What measurable result would justify further investment after the prototype?

## 12. Deliverables, schedule, and constraints

- What exact deliverables do you expect: research, requirements, persona, empathy map, architecture, schema, prototype, source code, deployment, report, or presentation?
- Is the goal a proof of concept, MVP, production-ready system, or technical recommendation?
- What must be completed by the final presentation or handover?
- What is the timeline and are there milestone dates?
- What resources are available: sample documents, subject-matter experts, developers, test environment, budget, or existing services?
- Are there limits on paid APIs, cloud services, or open-source dependencies?
- What should the prototype explicitly not do?
- How should the work be handed over, documented, and demonstrated?

## 13. Suggested closing questions

- What is the most important thing you want us to understand about this problem?
- If we could solve just one part of the problem first, which part would make the biggest difference?
- What assumption in our current understanding is most likely to be wrong?
- Who else should we speak with before finalizing the requirements?
- Can you share a safe, representative sample and walk us through what a correct result should look like?
- May we send you a short requirements summary after this meeting to confirm that we understood correctly?

## Meeting notes template

| Topic | Notes / decision |
|---|---|
| Primary user(s) |  |
| Initial organization/domain |  |
| First workflow |  |
| First document types |  |
| Current pain and impact |  |
| Required extracted information |  |
| Search and relationship needs |  |
| Matching/version rules |  |
| Review workflow |  |
| Sample documents available |  |
| Privacy/security constraints |  |
| Integrations/technology constraints |  |
| Success measures |  |
| Expected deliverables and timeline |  |
| Open questions / next actions |  |
