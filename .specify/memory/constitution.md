# ILI Re-Route Processing Demo Constitution

# ILI Re-Routing Prototype Constitution

## Project Structure Rules

The entire application MUST be implemented under the /demo-app directory.

All backend, frontend, and storage components MUST live inside this root.

No code or runtime artifacts may exist outside /demo-app.

---

## Core Principles

### I. FastAPI as the Orchestration Layer

FastAPI is responsible for:

* receiving uploaded files
* submitting files to FME
* receiving FME results
* displaying results in the web UI
* publishing approved results into the published CSV store

FastAPI MUST NOT perform centerline re-routing or geometry calculations.

---

### II. FME as the Processing Engine

FME is responsible for:

* centerline re-routing
* geometry updates
* coordinate recalculation
* generating the final output dataset

All spatial processing logic resides in FME.

---

### III. User Review Before Publish

Users must be able to review FME results before publishing.

The application must display:

* updated centerline geometry
* updated feature locations
* updated measures and coordinates

Publishing is a user action that copies the final CSV into the /published directory.

### IV. CSV Is the System of Record

After FME processing completes, the final CSV is the authoritative output for the prototype.

The prototype must:

* store the processed CSV as the main persisted artifact
* keep the original upload separate from the processed output
* write the approved final CSV into /published on publish
* avoid relational database persistence, SQL access, ORM layers, and migrations

---

## Workflow

### Upload

The user uploads either:

* GeoPackage (.gpkg)
* CSV

containing re-route information.

---

### Process

The application sends uploaded files to FME.

FME performs:

* centerline re-routing
* geometry updates
* feature relocation
* coordinate recalculation

FME returns a CSV containing:

* updated centerline information
* updated features
* updated measures
* updated latitude and longitude values

---

### Review

The web application displays:

* the updated centerline on a map
* updated features on a map
* attribute data from the FME output

The user can inspect the results before publishing.

---

### Publish

When the user clicks **Publish**:

* the final CSV is copied into the /published directory
* the published CSV becomes the source of truth for the updated route

---

## Deliverables

The prototype must demonstrate:

1. File upload from a web UI
2. Submission of files to FME
3. FME processing and output generation
4. Display of processed results on a map
5. User-triggered publishing to a /published CSV directory

---

## Governance

This project is a proof of concept intended to demonstrate the end-to-end workflow.

Priority is placed on:

* working functionality
* rapid development
* demonstrable business value
* CSV-first persistence

Production-grade concerns such as security, scalability, auditing, resiliency, SQL persistence, ORM design, and migration tooling are outside the scope of this prototype unless required for the demonstration.

---

**Version:** 0.1.0
**Type:** Proof of Concept / Hackathon Prototype
