# Implementation Tasks: ILI Reroute Processing Prototype

**Input**: `specs/001-ili-reroute-processing/spec.md` and `specs/001-ili-reroute-processing/plan.md`
**Goal**: Deliver a working end-to-end demo in 3 days using Python, FastAPI, FME Workbench, a simple web UI, and CSV-only storage
**Constraints**: No database, no ORM, no SQL, no migrations, no production hardening work

## Phase 0: Project scaffolding and file conventions

- [ ] T001 Create the FastAPI project skeleton with `app/`, `templates/`, `static/`, `uploads/`, `processed/`, and `published/` folders.
- [ ] T002 Add the base FastAPI application entry point and route registration for the upload, results, and publish views.
- [ ] T003 Define the file naming and folder conventions for uploads, processed CSV output, and published CSV copies.
- [ ] T004 Add a lightweight configuration module for runtime paths, allowed file types, and demo-friendly limits.

## Phase 1: Upload and job tracking backend

- [ ] T005 Implement the upload endpoint that accepts GeoPackage and CSV files from the web UI.
- [ ] T006 Add file validation for extension, required content, and basic size limits before processing begins.
- [ ] T007 Create a simple file-based job manifest or in-memory job tracker for status, input path, processed path, and publish status.
- [ ] T008 Store accepted uploads in the `uploads/` folder with a job-specific filename.
- [ ] T009 Add backend status values for queued, processing, processed, review-ready, publish-pending, published, and failed.

## Phase 2: FME integration

- [ ] T010 Define the FME handoff payload, including job identifier, source file path, and any required metadata.
- [ ] T011 Implement the service that invokes or triggers the FME workspace for a submitted job.
- [ ] T012 Capture the FME returned CSV into the `processed/` folder and link it to the originating job.
- [ ] T013 Add failure handling for missing output, invalid CSV output, or FME execution errors.
- [ ] T014 Mark the job as review-ready only when a valid processed CSV is available.

## Phase 3: Results review UI and map visualization

- [ ] T015 Create a results page that shows the job status, uploaded file name, and latest processed CSV link.
- [ ] T016 Parse the processed CSV into map-ready centerline and feature data for display.
- [ ] T017 Render the rerouted centerline and updated features on an interactive map.
- [ ] T018 Auto-fit the map to the processed route extent when the results page loads.
- [ ] T019 Add feature selection behavior so the user can inspect returned attributes or location details.
- [ ] T020 Display the publish state clearly and disable the Publish button until the job is review-ready.

## Phase 4: CSV publish workflow

- [ ] T021 Implement the Publish action as a copy/write operation into the `published/` folder.
- [ ] T022 Ensure the published CSV becomes the system of record after the copy completes.
- [ ] T023 Prevent publish when the job is incomplete, failed, or not explicitly approved.
- [ ] T024 Preserve the processed CSV in `processed/` after publish so the review artifact remains available.
- [ ] T025 Show publish success and publish failure feedback on the results page.

## Phase 5: Demo hardening and validation

- [ ] T026 Add backend tests for upload validation, job creation, and file storage behavior.
- [ ] T027 Add backend tests for FME handoff success and failure paths.
- [ ] T028 Add tests for processed CSV capture and publish-to-`/published` copy behavior.
- [ ] T029 Prepare at least one sample GeoPackage and one sample CSV for end-to-end demo validation.
- [ ] T030 Run a full demo rehearsal that covers upload, processing, map review, and publish.

## Suggested Build Order

1. Finish Phase 0 and Phase 1 first so uploads and job tracking work.
2. Complete Phase 2 next so the team can see the processed reroute on the map.
3. Implement Phase 3 after the review UI is stable.
4. Use Phase 5 to verify the live demo path and trim nonessential behavior.

## Notes

- Keep each task independently implementable and small enough for a single coding session.
- Avoid adding authentication, deployment automation, observability, or scalability work during the prototype window.
- Treat the processed CSV as the source of truth after FME completes.
