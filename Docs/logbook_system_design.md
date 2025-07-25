# Automated E-Logbook System for Sailing Voyages

---

## Table of Contents

1. [Introduction](#introduction)
2. [System Overview](#system-overview)
3. [Key Features & Rationale](#key-features--rationale)
4. [System Architecture](#system-architecture)
5. [Data Model & Database Schema](#data-model--database-schema)
6. [User Experience & Workflow](#user-experience--workflow)
7. [Web UI Wireframes](#web-ui-wireframes)
8. [Reliability & Legal Integrity](#reliability--legal-integrity)
9. [Extensibility & Customization](#extensibility--customization)
10. [Implementation Strategy](#implementation-strategy)
11. [Appendix: Glossary](#appendix-glossary)

---

## Introduction

The Automated E-Logbook System is a robust, flexible, and legally sound digital logbook solution designed specifically for sailing voyages. It integrates seamlessly with Signal K to automatically record key navigation and environmental data, while allowing skippers to tailor the logging to their needs—whether cruising, racing, chartering, or solo sailing.

---

## System Overview

- **Hardware:** Industrial or consumer mini-PC (SSD storage, reliable DC-DC power supply, 12V/5A, Ubuntu/Linux OS)
- **Core Data Source:** [Signal K](https://signalk.org/) server, aggregating navigation, AIS, GPS, Seatalk, and sensor data via USB.
- **Database:** SQLite for robust, append-only, ACID-compliant logbook storage.
- **User Interface:** Web-based dashboard accessible from onboard devices (tablet, phone, laptop).
- **Output:** Logbook exportable as HTML (primary) and optionally as PDF.

---

## Key Features & Rationale

### 1. **Automated Logging**

- **Rationale:** Ensures regular, consistent data capture without manual effort.
- **How:** System polls Signal K at a configurable interval (default: every 30 minutes).
- **Captured Data:** Navigation, heading, speed, position, wind data, and more (customizable).

### 2. **Manual Log Entries**

- **Rationale:** Allows skipper to add narrative, status, or notes at any time for legal or operational reasons.
- **How:** Skipper uses Web UI to add entries—timestamped and immutable.

### 3. **Immutability & Legal Integrity**

- **Rationale:** Logbook must be a true, legally recognized record—no edits or backdating.
- **How:** All entries are append-only; once timestamped and saved, cannot be changed or deleted.

### 4. **Dynamic Field Selection & Profiles**

- **Rationale:** Too much or irrelevant data clutters the log; flexibility is key.
- **How:** Skipper can select, pause, or add Signal K fields on-the-fly, and save/load field profiles (e.g., "Cruising", "Racing").

### 5. **Voyage Management**

- **Rationale:** Clean separation of voyages, clarity for log review, export, and legal closure.
- **How:**
    - **Start:** Automatically (on boot) or manually via UI.
    - **Slow-Down Mode:** Skipper can reduce logging frequency (e.g., while at anchor or in port).
    - **End:** Explicitly via UI, or automatically detected after prolonged shutdown. On next boot, system prompts for voyage closure and annotation.

### 6. **Logbook Export**

- **Rationale:** Required for legal compliance, sharing, or printing.
- **How:** HTML export (tabular, styled, printable), with option for PDF generation via browser or server-side tool.

---

## System Architecture

```
+----------------------------------------------------------+
|                Automated E-Logbook System                |
+----------------------------------------------------------+
| [Mini-PC: Ubuntu]                                        |
|   |                                                      |
|   |-- Signal K Server (USB sensors: AIS, GPS, Wind, etc) |
|   |                                                      |
|   |-- [Backend Service]                                  |
|          |-- SQLite DB (logbook.db)                      |
|          |-- Log Scheduler (auto/manual entries)         |
|          |-- Signal K Field Selector/Profile Manager     |
|   |                                                      |
|   |-- [Web UI]                                           |
|          |-- Dashboard / Voyage Control                  |
|          |-- Log Entry Management                        |
|          |-- Field Selection & Profiles                  |
|          |-- Export/Print                                |
+----------------------------------------------------------+
```

---

## Data Model & Database Schema

### SQLite Tables

#### 1. Voyages

| Field         | Type     | Notes                                   |
|---------------|----------|-----------------------------------------|
| id            | INTEGER  | Primary key, auto-increment             |
| start_time    | TEXT     | UTC timestamp of voyage start           |
| end_time      | TEXT     | UTC timestamp of voyage end (nullable)  |
| vessel_name   | TEXT     | Pulled from Signal K                    |
| mmsi          | TEXT     | Pulled from Signal K                    |
| call_sign     | TEXT     | Pulled from Signal K                    |
| voyage_name   | TEXT     | User input (optional)                   |
| destination   | TEXT     | User input (optional)                   |

#### 2. Log Entries

| Field           | Type     | Notes                                 |
|-----------------|----------|---------------------------------------|
| id              | INTEGER  | Primary key, auto-increment           |
| voyage_id       | INTEGER  | Foreign key to voyages                |
| timestamp       | TEXT     | UTC, auto-generated                   |
| auto_data       | TEXT     | JSON snapshot of Signal K data        |
| propulsion      | TEXT     | Manual, enum                          |
| sea_state       | TEXT     | Manual, enum                          |
| visibility      | TEXT     | Manual, enum                          |
| autopilot_state | TEXT     | Manual, enum                          |
| narrative       | TEXT     | Manual, free text                     |
| entry_type      | TEXT     | 'auto' or 'manual'                    |

#### 3. Log Fields (Signal K Field Selection)

| Field         | Type     | Notes                                  |
|---------------|----------|----------------------------------------|
| id            | INTEGER  | Primary key                            |
| voyage_id     | INTEGER  | Foreign key to voyages                 |
| sk_path       | TEXT     | Signal K data path                     |
| display_name  | TEXT     | Friendly field name                    |
| profile       | TEXT     | Profile name (optional)                |
| status        | TEXT     | 'active', 'paused', 'removed'          |
| selected_at   | TEXT     | UTC timestamp of last change           |

---

## User Experience & Workflow

### 1. System Boot & Voyage Start

- On boot, system checks for active voyage.
    - If none, prompts: “Start new voyage?” (UI) or auto-starts with timestamp.
    - Pulls vessel info from Signal K.

### 2. Field Selection (Before or During Voyage)

- Skipper presented with live list of Signal K data fields (see [Wireframes](#web-ui-wireframes)).
- Can select/deselect/pause fields.
- Can load/save field profiles for different sailing modes.

### 3. Automated Logging

- At interval (e.g., every 30 minutes), system:
    - Collects selected Signal K fields.
    - Creates immutable, timestamped log entry in SQLite.

### 4. Manual Entries

- Skipper can add manual entry (status, narrative, etc.) at any time via UI.
- All entries timestamped, append-only.

### 5. Slow-Down Mode

- Skipper can activate “Slow-Down” (e.g., while in port).
    - Sets longer interval (e.g., 4 hours) for auto-logging.
    - Inserts marker entry in log.

### 6. Voyage End

- Explicitly via UI (“End Voyage”).
- Or, if powered off for >X hours, on next boot system prompts:
    - “Previous voyage ended at [last log time]. Annotate?”
    - Allows skipper to add final note before locking voyage.

### 7. Logbook Export

- Completed voyage is available for export as HTML (tabular, styled).
- Option for PDF export (browser print or server-side).

---

## Web UI Wireframes

### Voyage Dashboard

```
+---------------------------------------------------------+
| E-Logbook Dashboard                                    |
+---------------------------------------------------------+
| [Voyage Name: Portsmouth to Salcombe]                  |
| [Status: Underway]  [Start: 2025-07-24 11:00 UTC]      |
+---------------------------------------------------------+
| [End Voyage] [Export Logbook] [Activate Slow-Down]     |
+---------------------------------------------------------+
| Current Logged Fields: [Edit Fields]                   |
+---------------------------------------------------------+
| Latest Log Entry:                                      |
|  - Time: 2025-07-25 08:00 UTC                          |
|  - Position: 50.123N, 3.456W                           |
|  - ...                                                 |
+---------------------------------------------------------+
| [Add Manual Entry]                                     |
+---------------------------------------------------------+
```

### Field Selection

```
+-----------------------------------------------------------+
| Edit Logged Fields                                        |
+-----------------------------------------------------------+
| [Profile:  v  ] [Load Profile]   [Save as Profile]        |
+-----------------------------------------------------------+
| [x] navigation.headingMagnetic   (Autopilot) [active]     |
| [ ] performance.vmg              (VMG)    [paused]        |
| ...                                                       |
+-----------------------------------------------------------+
| [Select All] [Pause Selected] [Add Field] [Done]          |
+-----------------------------------------------------------+
```

### Manual Entry

```
+-----------------------------------------------------------+
| Manual Log Entry                                          |
+-----------------------------------------------------------+
| Time: [2025-07-25 08:07 UTC] (auto)                       |
| Propulsion: [Sail | Motor-sail | Engine | Drift] v        |
| Sea State:  [Slight | Smooth | Moderate | Rough | High] v |
| Visibility: [Good | Fair | Poor | Fog] v                  |
| Autopilot:  [Auto-heading | Wind | Standby] v             |
| Narrative:  [______________________________]              |
| [Add Entry]                                               |
+-----------------------------------------------------------+
```

### Voyage End / Resume

```
+-----------------------------------------------------------+
| Previous Voyage Detected                                  |
+-----------------------------------------------------------+
| Last entry: 2025-07-25 01:34 UTC                          |
| [Annotate Final Entry] [Start New Voyage]                 |
+-----------------------------------------------------------+
```

---

## Reliability & Legal Integrity

- **Immutability:** All entries append-only; no edits or deletions.
- **Timestamps:** All log events automatically timestamped in UTC.
- **Atomic Writes:** SQLite ensures no partial data in case of sudden power loss.
- **Power Resilience:** System designed for robust operation on 12V DC, with auto-restart on power return.
- **Periodic Backup (recommended):** Script to copy SQLite DB to USB/cloud at intervals.

---

## Extensibility & Customization

- **Profiles:** Save/load field configurations for different sailing modes.
- **Field Notes:** Optionally annotate why fields are added/removed.
- **Auto-Suggestions:** (Future) System can suggest fields based on context (e.g., VMG during upwind sailing).
- **API Hooks:** (Future) Export data to cloud or integrate with other marine software.

---

## Implementation Strategy

1. **Database Setup:**  
   - Initialize SQLite schema (`logbook.db`).
2. **Backend Service:**  
   - Script to poll Signal K and write automated entries.
   - REST API for manual entries, voyage control, field selection.
3. **Web UI:**  
   - Dashboard, field selection, manual entries, export.
4. **Export:**  
   - HTML (primary), PDF (optional).
5. **Testing:**  
   - Simulate power loss, field changes, and multi-voyage runs.
6. **Documentation:**  
   - Keep this and technical setup docs up-to-date.

---

## Appendix: Glossary

- **Signal K:** Open data format and server for marine instruments.
- **VMG:** Velocity Made Good, a sailing performance metric.
- **AIS:** Automatic Identification System (ship data).
- **Immutability:** Once written, data cannot be changed.
- **Profile:** Pre-set group of fields for specific sailing modes.

---

*For questions, feedback, or to contribute, please open an issue or pull request on this repository!*

