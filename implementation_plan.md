# 02-storage-layer.md

## Specification: Database & Analytics Engine
This PostgreSQL database handles the persistent state of the exam manifests, stores the telemetry as JSONB, and acts as a Data Warehouse for post-exam reporting.

## Implementation Tasks

- [ ] **Task 1: Bootstrap Schema & Ephemeral Session Tables**
  - **Action**: Initialize an ORM (Prisma/Drizzle). Create tables for `ExamManifests`, `LabMachines`, and `StudentSessions`. Ensure `StudentSessions` are decoupled from `LabMachines` to allow seat mobility.
  - **Verification**: Run migrations against a test DB. `npm run db:test`
  - **Done**: Relational schema compiles and runs cleanly on a fresh database.

- [ ] **Task 2: Versioned Delta Storage (Distributed Transactions)**
  - **Action**: Create a `FileVersions` table. Implement a transactional function that receives the patched file from the Broker, stores the new text, updates the timestamp, and commits atomically to prevent race conditions during rapid auto-saves.
  - **Verification**: Write a concurrent load test simulating 30 PCs saving simultaneously. `npm run test:concurrency`
  - **Done**: Zero data corruption occurs under high concurrency.

- [ ] **Task 3: Implement JSONB Telemetry Storage**
  - **Action**: Create a `TelemetryLog` table utilizing PostgreSQL JSONB columns to store arbitrary process logs and keystroke metadata dynamically. Add GIN indexes.
  - **Verification**: Write an integration test inserting 1,000 JSON payloads and asserting query retrieval speed. `npm run test:telemetry`
  - **Done**: Complex JSON structures can be inserted and queried rapidly.

- [ ] **Task 4: Build Data Warehouse Aggregation Queries**
  - **Action**: Write advanced SQL queries/views to aggregate telemetry data into a Data Mart structure. Generate a view that calculates the frequency of IDE crashes or network drops per lab machine MAC address.
  - **Verification**: Seed the database with mock historical data and run the aggregation queries. `npm run test:analytics`
  - **Done**: Aggregation functions successfully map raw telemetry into clean reporting metrics.