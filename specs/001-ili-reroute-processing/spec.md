# Feature Specification: ILI Reroute Processing Prototype

**Feature Branch**: `[001-ili-reroute-processing]`

**Created**: 2026-06-09

**Status**: Draft

**Input**: User description: "/speckit.specify Build an end-to-end ILI re-route processing prototype. Users upload a GeoPackage or CSV containing re-route information through a web UI. The application sends the file to an FME workspace for processing. FME updates the centerline geometry and feature locations and returns a CSV containing updated measures and coordinates. The web UI displays the updated centerline and features on an interactive map. Users can review the results and click Publish to write the final CSV into a /published directory. The solution should be implemented using Python, FastAPI, and a simple web UI suitable for a 3-day proof of concept."

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Upload and validate reroute data (Priority: P1)

As a user, I can upload a GeoPackage or CSV containing reroute information through the web UI and receive immediate validation feedback before processing starts.

**Why this priority**: Uploading valid input is the entry point for the entire workflow, so the prototype is only useful if users can reliably submit supported files.

**Independent Test**: Upload a supported file and verify the application accepts it, rejects unsupported files, and shows clear validation messages without starting processing.

**Acceptance Scenarios**:

1. **Given** a supported reroute file, **When** the user uploads it, **Then** the system accepts the file and creates a processing job.
2. **Given** an unsupported or malformed file, **When** the user uploads it, **Then** the system rejects the file and shows an actionable error message.

---

### User Story 2 - Process reroute data and review results (Priority: P2)

As a user, I can send the uploaded file to FME for processing and view the updated centerline and feature locations on an interactive map when processing completes.

**Why this priority**: The core value of the prototype is transforming the reroute data and presenting the result visually for review.

**Independent Test**: Upload a valid file, trigger processing, wait for completion, and verify the returned CSV data is reflected on the map with updated geometry and feature locations.

**Acceptance Scenarios**:

1. **Given** a validated upload, **When** processing is started, **Then** the application sends the file to the FME workspace and tracks the job status.
2. **Given** a completed processing job, **When** the user opens the results view, **Then** the interactive map shows the updated centerline and features.
3. **Given** a completed processing job, **When** the results view loads, **Then** the map is automatically centered on the processed route extent.
4. **Given** a feature on the map, **When** the user selects it, **Then** the UI shows the feature's returned attributes and updated position details.

---

### User Story 3 - Publish reviewed results (Priority: P3)

As a user, I can review the processed output and click Publish to copy the approved final CSV into a published location for later use.

**Why this priority**: Publishing is the final business action, but it depends on the earlier upload and processing steps being successful and only needs to persist the reviewed CSV output.

**Independent Test**: Complete a processing run, click Publish, and verify the final CSV is copied into the published directory only after explicit user approval.

**Acceptance Scenarios**:

1. **Given** a completed and reviewed processing result, **When** the user clicks Publish, **Then** the approved CSV is written into the published directory.
2. **Given** a job that has not completed successfully, **When** the user tries to publish, **Then** the system prevents publication and explains why.
3. **Given** a result that has not been explicitly approved by the user, **When** the user tries to publish, **Then** the system requires review confirmation before copying the CSV.
4. **Given** a successful publish action, **When** the file copy completes, **Then** the user sees a confirmation that the approved CSV was saved in the published directory.

---

### Edge Cases

- Uploading a file with the correct extension but invalid content is rejected before processing.
- Uploading a file larger than the supported prototype limit is blocked with a clear message.
- Processing fails in FME and the user is shown a status that allows the failure to be reviewed.
- The returned CSV is missing expected coordinate or measure fields and the job is marked as failed.
- The user attempts to publish before reviewing a completed result and the action is blocked.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: The system MUST provide a web UI for uploading reroute input files.
- **FR-002**: The system MUST accept GeoPackage and CSV files as valid input types for the reroute workflow.
- **FR-003**: The system MUST validate uploaded files before any processing begins.
- **FR-004**: The system MUST reject unsupported, malformed, or incomplete uploads with a clear user-facing message.
- **FR-005**: The system MUST create a processing job for each accepted upload.
- **FR-006**: The system MUST send validated uploads to an FME workspace for reroute processing.
- **FR-007**: The system MUST record the processing status from submission through completion or failure.
- **FR-008**: The system MUST ingest the CSV returned by FME and associate it with the original upload job.
- **FR-009**: The system MUST display the updated centerline and feature locations on an interactive map after processing completes.
- **FR-010**: The system MUST automatically fit the map to the processed route extent when results are loaded.
- **FR-011**: The system MUST visually distinguish the updated centerline from feature locations on the map.
- **FR-012**: The system MUST allow users to select a map feature and view its returned attributes or updated location details.
- **FR-013**: The system MUST allow users to review the processed results before publication.
- **FR-014**: The system MUST require an explicit Publish action before copying the final CSV to the published directory.
- **FR-015**: The system MUST prevent publication when processing has failed or when results have not been reviewed.
- **FR-016**: The system MUST preserve the original uploaded file separately from the final processed CSV.
- **FR-017**: The system MUST provide error information that helps users understand why upload, processing, or publishing failed.

### FME Integration Design

- The application must treat FME as the processing boundary for reroute transformation, geometry updates, and coordinate recalculation.
- Each accepted upload must be assigned a unique job identifier that is carried through the FME processing lifecycle and used to link the input, output, and review state.
- The application must pass the uploaded source file and required job metadata to the FME workspace in a way that allows the workspace to produce a matching result for that job.
- The FME workspace must return a CSV result that can be associated with the original job and used to drive the map review view.
- The application must mark the job as failed if FME does not return a usable result, returns incomplete data, or cannot complete the reroute transformation.
- The application must preserve the raw input file, the FME output CSV, and the publish decision separately so the workflow can be audited and reviewed later.
- The prototype should allow the user to re-run processing from the same uploaded input if the first FME execution fails, provided the source file remains available.

### CSV Publish Workflow

- The publish workflow begins only after a processing job reaches a completed review state.
- The user must be able to inspect the processed centerline and feature output before the publish action is enabled.
- Publish must be a deliberate action, triggered only after the user confirms the reviewed result is ready to become the published CSV artifact.
- When publish succeeds, the application must record that the reviewed result was copied into the published directory and make that outcome visible in the job status.
- When publish fails, the application must leave the reviewed result intact, preserve the failure reason, and allow the user to retry publication without re-running FME.
- The publish workflow must not overwrite the published artifact unless the current job is the latest approved result for that reroute submission.
- The user-facing status must distinguish between processing complete, review pending, publish pending, publish succeeded, and publish failed.

### Key Entities *(include if feature involves data)*

- **Upload Job**: Represents one user submission, including file type, status, timestamps, validation outcome, and links to source and result artifacts.
- **Reroute Input File**: The uploaded GeoPackage or CSV that contains reroute information and serves as the processing source.
- **Processing Result**: The CSV and derived map-ready data produced by FME, including updated measures, coordinates, and centerline geometry references.
- **Publish Action**: The user-approved step that copies the reviewed final CSV into the published directory.
- **Map Review State**: The visual presentation of the processed route, including centerline extent, feature locations, and any selected feature details shown to the user.
- **Published CSV**: The final processed CSV stored in the published directory and treated as the system of record for downstream use.
- **Publish Status**: The lifecycle state of a reviewed result after it is eligible for file publication, including pending, succeeded, and failed outcomes.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: A user can upload a supported file and receive a validation or acceptance response within 5 seconds for typical proof-of-concept file sizes.
- **SC-002**: At least 90% of successful test uploads complete the full processing flow through FME and return a visible result in the UI.
- **SC-003**: A user can identify whether a job succeeded, failed, or is still running without needing developer support.
- **SC-004**: A user can review the processed output on an interactive map and decide whether to publish it in a single session.
- **SC-005**: Users can clearly distinguish the updated centerline from feature locations on the map during review.
- **SC-006**: The map opens centered on the processed route without requiring manual navigation in the typical case.
- **SC-007**: Publication only occurs after an explicit user action, and no unreviewed result is copied into the published directory.
- **SC-008**: A user can tell whether a reviewed result is awaiting publish, has been published, or failed to publish without leaving the results page.

## Assumptions

- The prototype focuses on a single reroute workflow rather than multiple geospatial processing variants.
- FME Workbench is available in the target environment and can be invoked by the application during the proof of concept.
- The published CSV is the system of record after FME processing completes.
- The published directory is available and writable in the target environment.
- Mobile optimization is out of scope for the 3-day prototype unless it is needed for basic usability.
- Authentication and role management are minimal or deferred unless required by the deployment environment.
