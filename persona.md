# User Persona: Client and Contract Records Manager

> **Persona type:** Cross-industry working hypothesis. This persona represents a common user in any organization that manages client information and agreements. It is not based on a completed user study; validate the role, workflow, and priorities with people from the intended pilot organization.

## Snapshot

**Name:** Priya Sharma (fictional)  
**Role:** Client and Contract Records Manager  
**Organization:** A document-heavy business or public organization  
**Work context:** Maintains records for clients, customers, partners, properties, vendors, and agreements across digital folders, email/shared drives, databases, and paper files.

## Context

Priya needs to find, understand, and maintain information spread across files and records. Documents may include client forms, contracts, land or property agreements, invoices, permits, service agreements, correspondence, and supporting attachments. Some are searchable digital files; others are scans or paper records in a physical records room.

Information about one client or agreement may be repeated across several documents with different names, spellings, identifiers, dates, or terms. A mismatch can make a relevant document difficult to find, cause a record to be linked to the wrong client or property, or lead staff to act on incomplete or outdated contract information.

The exact organization, job title, document types, and systems are not specified yet. Priya is a composite persona for discovery, not a confirmed individual or a claim that every user shares these needs.

## Goals

- Find all relevant client and contract records quickly, even when stored in different folders or formats.
- See a complete, organized view of the information and documents associated with a client, property, project, or agreement.
- Extract important details from contracts and supporting records into consistent, searchable fields.
- Identify duplicates, mismatched names, missing values, outdated versions, and conflicting terms.
- Understand which documents support each detail and whether it has been reviewed.
- Keep client records accurate and help colleagues avoid decisions based on the wrong document or incomplete information.

## Behaviors and needs

- Searches by several clues: client name, alternate name, account or case ID, address or parcel identifier, contract number, date, organization, or subject.
- Checks folders, shared drives, paper indexes, and business systems; asks colleagues when information is missing.
- Compares details across contracts, forms, amendments, correspondence, invoices, and other supporting files.
- Needs the original source wording and location beside normalized information.
- Needs clear handling of amendments, renewals, superseded contracts, duplicate records, and conflicting assertions.
- Needs access controls appropriate to sensitive client and contract records.

## Frustrations and risks

- Files may be stored in inconsistent folder structures, paper archives, or disconnected systems.
- Similar client names, aliases, addresses, parcels, and company names can lead to false matches.
- A relevant document may be missed because its filename, filing location, scan quality, or metadata is poor.
- Contract terms can change through amendments; an older version may be mistaken for the current one.
- Manual data entry is time-consuming and can introduce mismatches or omissions.
- A polished summary without clear citations to the source is difficult to trust or act on.
- Missing information must not be silently inferred, especially where legal, financial, property, or contractual decisions are involved.

## Needs from the proposed system

1. **Easy first-time setup:** Connect approved folders, repositories, or business systems through a guided setup; configure access, document types, and destinations without requiring a custom installation project for every source.
2. **Find and ingest records:** Work with permitted documents, images, audio, and video, including scans or paper-derived files; preserve links to originals and their locations.
3. **Extract comprehensively within a defined scope:** Capture relevant content from text, images, audio, and video. For example, extract metadata, people and organizations, client identifiers, property or asset details, agreement parties and roles, dates, amounts, terms, rights, obligations, conditions, renewals, amendments, references, and supporting-record links as applicable to the source and document type.
4. **Support different domains:** Use a shared core model for common concepts and configurable fields/vocabularies for document types such as land agreements, service contracts, leases, sales, or client onboarding records.
5. **Add new data dynamically:** Allow connected sources to sync new or changed items automatically or through scheduled/push ingestion, then process them without reinstalling the product.
6. **Connect related information carefully:** Link documents and mentions to a client, property, project, or canonical record only when reliable; offer possible matches for review when uncertain.
7. **Preserve evidence and versions:** Show the original value, source passage or media segment, page/timecode, document/media version, extraction run, and review state.
8. **Surface mismatch and conflict:** Flag duplicate-looking clients, inconsistent identifiers, conflicting contract terms, missing expected fields, and superseded versions.
9. **Support incremental updates:** When a file or extraction changes, update only related facts, links, and evidence while preserving unrelated records and other sources' support.
10. **Respect records access rules:** Search and display only records the user is authorized to access; retain audit history for important changes.

## Success signals

- Users find relevant records faster than with the current folder/paper search process.
- Important fields and links are supported by source evidence and can be checked quickly.
- The system surfaces likely mismatches and version conflicts without silently merging records.
- Reviewers can tell current, amended, superseded, unresolved, and conflicting information apart.
- Corrections and document revisions update the right records without damaging unrelated knowledge.
- Extraction quality and search success are measured on representative examples for each supported document type.

## Design implication

Treat the system as an evidence-backed records index and knowledge layer across documents, clients, contracts, and relevant assets. Make finding and verifying the right source record the core workflow. Keep a configurable domain schema so the product can support different businesses without pretending every field applies to every document.

## Assumptions to validate

- The first users are records coordinators, operations staff, account managers, contract administrators, or similar roles; exact titles vary by organization.
- Documents, images, audio, video, and paper-origin records are intended input categories; the first release may prioritize a subset, chosen with users.
- “Dynamic” ingestion needs a defined behavior (connector sync, watched folder, webhook, scheduled import, or manual upload) and acceptable freshness target.
- Client identity, document version, and source location are central to matching and retrieval.
- Users need a shared common model plus document-type-specific fields.
- The desired extraction coverage, retention rules, access policies, and impact thresholds must be defined with the organization and its domain owners.
