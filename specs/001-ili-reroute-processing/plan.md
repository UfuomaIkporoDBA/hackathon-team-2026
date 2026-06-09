# Implementation Plan: ILI Reroute Processing Prototype

**Branch**: `[001-ili-reroute-processing]` | **Date**: 2026-06-09 | **Spec**: [spec.md](./spec.md)

**Input**: Feature specification from `/specs/001-ili-reroute-processing/spec.md`

## Summary

Build a 3-day proof-of-concept pipeline that lets a user upload a GeoPackage or CSV through a simple web UI, send the file to FME for reroute processing, view the returned CSV-driven reroute result on an interactive map, and publish by copying the final CSV into `/published`. The prototype uses Python and FastAPI for orchestration, FME Workbench for geospatial processing, and CSV files as the system of record. There is no relational database, ORM, SQL layer, or migration work.

## Technical Context

**Language/Version**: Python 3.11+

**Primary Dependencies**: FastAPI, Uvicorn, Jinja2 or lightweight server-rendered templates, a map library such as Leaflet for display, standard library CSV/file handling, and FME Workbench integration scripts or command invocation

**Storage**: File-based storage only; raw uploads, processed CSV output, and published CSV copies on disk

**Testing**: pytest for backend logic, request-level tests for upload and publish endpoints, and lightweight integration checks for file lifecycle and FME handoff behavior

**Target Platform**: Desktop browser against a local or internal FastAPI service on Windows for the prototype demo

**Project Type**: Web application / API-backed UI

**Performance Goals**: Accept a typical proof-of-concept upload quickly, show processing status promptly, and render map results during a live demo without blocking the UI for long-running work

**Constraints**: No database, no ORM, no SQL, no migrations, no production-grade deployment automation; keep the implementation simple enough to complete in 3 days

**Scale/Scope**: Single workflow, single-user demo behavior, one reroute job at a time or a small queue of jobs, focused on the upload-process-review-publish loop

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

- CSV is the system of record after FME processing completes.
- Publishing is a file operation that copies the approved CSV into `/published`.
- The prototype avoids relational database persistence, SQL access, ORM layers, and migrations.
- FastAPI orchestrates upload, processing handoff, result retrieval, map display, and publish actions.
- FME owns reroute transformation, geometry updates, and coordinate recalculation.
- The UI must support review before publish and make the published state visible.

## Project Structure

### Documentation (this feature)

```text
specs/001-ili-reroute-processing/
├── plan.md           # This file
├── spec.md           # Feature specification
├── research.md       # Optional support notes for implementation decisions
├── quickstart.md     # Developer/demo run instructions
├── data-model.md     # File and job state model for CSV-based workflow
└── tasks.md          # Generated after plan approval
```

### Source Code (repository root)

```text
demo-app/
├── app/
│   ├── api/
│   ├── services/
│   ├── models/
│   └── main.py
├── static/
│   ├── css/
│   └── js/
├── templates/
├── uploads/
├── processed/
├── published/
└── tests/
```

**Structure Decision**: Use a single FastAPI web application under `demo-app/` with server-rendered templates for the UI, a small service layer for file processing and FME handoff, and disk-based folders for uploads, processed CSV output, and published CSV copies. This matches the prototype scope and avoids introducing separate frontend/backend repos or a database layer.

## Phases and Tasks

### Phase 0: Confirm workflow shape and file conventions

- Define the upload, processed, and published directory conventions.
- Confirm the CSV fields and job metadata required for the FME handoff.
- Confirm the minimum map fields needed to draw the updated centerline and feature points.
- Agree on the publish file naming convention so the latest approved CSV is easy to identify.

### Phase 1: Build the FastAPI file pipeline

- Implement file upload endpoints for GeoPackage and CSV inputs.
- Validate file type, basic structure, and required job metadata before processing.
- Persist accepted uploads into an `uploads/` area and create a job record in memory or as a simple file-based manifest.
- Add job status tracking for queued, processing, processed, review-ready, published, and failed states.
- Implement the FME submission step that passes the uploaded file and job metadata to the workspace.
- Capture the returned processed CSV into a `processed/` area and associate it with the originating job.

### Phase 2: Build the review UI and map visualization

- Create a simple results page that lists job status and links to the latest processed output.
- Render the processed centerline and feature locations on an interactive map.
- Auto-fit the map to the processed route extent when results load.
- Distinguish the centerline, feature markers, and selected feature details visually.
- Show the current publish state and keep the Publish action disabled until review is complete.

### Phase 3: Implement CSV publishing

- Implement Publish as a copy or write operation into `/published`.
- Ensure the final CSV becomes the system of record after publish.
- Preserve the processed CSV even after publish so review history is not lost.
- Prevent publish if the job is incomplete, failed, or not explicitly approved.
- Surface publish success and publish failure clearly in the UI.

### Phase 4: Demo hardening and verification

- Add focused tests for upload validation, FME handoff plumbing, result persistence, and publish file copy behavior.
- Verify the demo flow end to end using one sample GeoPackage and one sample CSV.
- Confirm the UI can show map results and publish state without requiring developer intervention.
- Trim any nonessential behavior that does not help the live demonstration.

## Delivery Tasks by Day

### Day 1

- Scaffold the FastAPI app structure and template pages.
- Implement upload validation and job tracking.
- Wire the FME handoff path and processed CSV capture.

### Day 2

- Implement the interactive map results page.
- Load processed centerline and feature data onto the map.
- Add review state, selection behavior, and publish enablement.

### Day 3

- Implement publish-to-`/published` file copying.
- Add end-to-end verification scripts or tests.
- Perform a full demo rehearsal with a representative input file.

## Success Criteria Mapping

- A user can upload a supported file and receive a validation or acceptance response.
- A user can send the file to FME and receive a processed CSV output.
- A user can view the rerouted centerline and updated features on a map.
- A user can publish the reviewed result by saving the final CSV into `/published`.
- The prototype remains CSV-first and avoids any SQL database or ORM usage.

## Complexity Tracking

No constitution violations require extra justification. The plan intentionally avoids relational storage, ORM layers, and migrations to preserve the 3-day delivery target.
