# Student ID Print — Moodle local plugin

## Overview

**Student ID Print** (`local_studentid_print`) is a Moodle local plugin for generating and printing student ID cards as PDFs, straight from your site's user and custom‑profile data.

It includes a **visual drag‑and‑drop layout editor**, a **live preview that renders real student data**, optional **two‑sided cards**, a built‑in **themed card design** (so you don't even need a template PDF), **barcodes/QR codes**, **serial‑number management**, and a **QR verification page** that anyone can scan to confirm a card is genuine.

Fields are mapped **dynamically** to your Moodle user fields and custom profile fields — there is nothing hard‑coded to a particular institution, and the default design follows your site theme.

---

## Accessing the plugin

### From Site administration
- **The dashboard:** *Site administration → Users → Accounts → **Student IDs***.
- **Plugin settings:** *Site administration → Plugins → Local plugins → **Student ID Print***.
- **Layout editor:** open it from the **Card layout** section of the settings page (button), or directly at `/local/studentid_print/layout_editor.php`.

### Direct URLs
Replace `https://your-moodle-site.edu` with your site URL.

| Page | URL |
|------|-----|
| Dashboard (print student IDs) | `/local/studentid_print/index.php` |
| Printed‑IDs list | `/local/studentid_print/index.php?view=printed_ids` |
| Print one student's ID | `/local/studentid_print/print.php?id=USER_ID` |
| Visual layout editor | `/local/studentid_print/layout_editor.php` |
| QR verification page | `/local/studentid_print/info.php?id=USER_ID&token=…` |

### Who can access?
- **View dashboard / generate / serials:** capability `local/studentid_print:view`.
- **Print / generate via web service:** capability `local/studentid_print:print`.

Both default to **Manager**. Grant them to other roles via *Site administration → Users → Permissions → Define roles*.

---

## Features

### Dashboard (student list)
- Paginated, searchable list of students (15 per page).
- **Collapsible "More filters"** — a single search bar is always visible; the advanced filters (Reg. no., Programme, Faculty, Campus, Intake year, Status, Template) live behind a **More filters** toggle that shows a badge with the number of active filters and auto‑expands when filters are in use.
- **Status badges** per student: *Pending*, *Generated*, *Printed*, *Incomplete profile* — with inline validation notes (e.g. "ID number is missing").
- Stat cards: total students, generated, pending, printed.
- "Student" scope is defined by a configurable **email pattern** (default `@stud`).

### Visual layout editor
- **Drag‑and‑drop** every field onto the card; resize image boxes with a handle.
- **Properties panel** per field: X/Y position, width/height, font size, font family, **bold**, and **side** (front/back).
- Each field shows both its **label and its mapped placeholder** (e.g. `{fullname}`, `{programme}`) on the canvas and in the "Available fields" list.
- **Available fields** is a collapsible, **searchable dropdown** so the (potentially long) list of profile/user fields stays manageable.
- Text positioning is **WYSIWYG**: text is left‑aligned to where you drop it, so dragging *is* the alignment.
- Optional **"Show label with value"** per field → prints e.g. `FACULTY: Law`.

### Live preview
- A true‑to‑print preview that updates **instantly** as you move, resize, or restyle fields.
- Reflects **font size (in points), family, bold, alignment, width/height**, and the **template/themed background**.
- Renders **real student data** via a **"Preview as" student picker** (it defaults to a student who actually has data), falling back to representative sample values for any empty field — so the card never shows bare labels.
- Shows **both sides** when two‑sided cards are enabled, at the same size as the editing canvas.

### Built‑in themed card (no template required)
- When **no template PDF is uploaded**, the plugin generates a clean university‑style ID card that **follows your site theme colour**.
- Selectable styles: **Corporate ID badge** (header band with company name + address, footer band, accent rings — the default), **Classic** (top & bottom bands), **Side band**, **Minimal**.
- Configurable **accent colour** (defaults to the theme brand colour) and an optional **header address**.
- The same design is used in the editor, the live preview, and the printed PDF — so preview = print.

### Two‑sided cards
- Optional **front and back** sides (off by default).
- Assign each field to **Front** or **Back**; the editor gets **Front/Back tabs** and a preview of each side.
- The back uses its own uploaded back‑template PDF, or the themed default card.
- Printing outputs **two pages per student** (front then back), interleaved per student for duplex printers.

### Field data & mapping
- Fields are generated **dynamically** from:
  - **Standard user fields** — full name, ID number, email, department, institution, city, country, phone, …
  - **All custom profile fields** on your site (Programme, Faculty, Registration number, Gender, etc.).
  - Plugin fields — **serial number**, **photo**, **logo**, **signature**, **barcode**, **QR code**.
- Values resolve from the real user record / profile data (with the proper Moodle display value for menus, dates, checkboxes via the profile API). Missing values print **blank** (never "Not available").
- Photo comes from the Moodle **user profile picture**.

### ID card output (PDF)
- Card size and **orientation** (landscape/portrait) are configurable (default ≈ 95 × 64 mm).
- **Barcode** (Code‑128) and **QR code**, with a configurable QR source (verification page, profile link, or a chosen field).
- Built with **FPDI + TCPDF**; imports an uploaded template PDF or draws the themed card.

### QR verification page
- Each card's QR code opens a **public, token‑protected verification page** (`info.php`).
- Shows the student's **photo**, a **"verified genuine"** badge (with your site/institution name), the **printed date**, the **same fields that are on the card** (with real values), and the student's **course list**.
- Built with a Mustache template + Moodle Output API.

### Serial numbers
- Per‑student serial field with **Save** and **assign‑Next** controls, plus **"Assign next to whole page"**.
- Optional **auto‑assignment** with a configurable **prefix** and **next counter**.

### Bulk actions
- **Select all / select rows**, then **Bulk generate** or **Bulk print** (single combined PDF).
- Bulk generate skips students with validation issues and reports how many were generated/skipped.

---

## Settings reference

*Site administration → Plugins → Local plugins → Student ID Print*

| Group | Settings |
|------|----------|
| **Templates** | Default / Faculty / Temporary template PDFs |
| **Two‑sided cards** | Enable back side; back‑side template PDF |
| **Default card design** | Style (Corporate/Classic/Side band/Minimal); accent colour; header address |
| **Dashboard** | Student email pattern; profile‑field shortnames for Programme/Faculty/Campus/Intake/Reg. no. |
| **Card** | Orientation; page width/height; PDF author/title; default text cell width |
| **Serial numbers** | Prefix; next counter; auto‑assign on/off |
| **Assets** | Logo and signature images |
| **Layout** | Link to the visual layout editor |

---

## Capabilities & access

| Capability | Purpose | Default role |
|------------|---------|--------------|
| `local/studentid_print:view` | View the dashboard, manage serials, generate cards | Manager |
| `local/studentid_print:print` | Print / generate via the web service | Manager |

All access is checked with `require_login()` + `require_capability()` at the system context; AJAX and web‑service calls validate sesskey/token and capabilities.

---

## Web services (mobile / API)

External functions are provided in `classes/external/` and registered in `db/services.php` (all `ajax`‑enabled and usable via `core/ajax`):

- `local_studentid_print_get_student_id_pdf_url` — get a download URL for a student's ID PDF.
- `local_studentid_print_generate_student_id` — (re)generate and return the PDF URL.
- `local_studentid_print_save_serial`, `…_assign_next_serial`, `…_assign_next_page`, `…_generate_id`, `…_bulk_generate` — used by the dashboard.

---

## Requirements

- **Moodle 3.10+** (`requires 2020110900`).
- Installed under `local/studentid_print`.
- **No template PDF is required** — the themed default card is used when none is uploaded. Upload a template PDF if you want your own background.
- Custom profile fields are optional; create the ones you want on the card and map/enable them in the layout editor.

---

## Tech stack

- **FPDI 2.6.3** (MIT) for importing template PDFs — declared in `thirdpartylibs.xml`; PDF rendering via Moodle's bundled **TCPDF**.
- **Mustache templates** + Moodle **Output API** for pages.
- **AMD ES6 JavaScript modules** (`amd/src` → `amd/build`) loaded via `js_call_amd`.
- **Moodle File API** for templates, logos, signatures, and user pictures.
- Privacy provider, scheduled‑task and event definitions included.

---

## Summary

| Item | Description |
|------|-------------|
| **Plugin type** | Local plugin (`local_studentid_print`) |
| **Dashboard** | *Site admin → Users → Accounts → Student IDs* (or `/local/studentid_print/index.php`) |
| **Designer** | Drag‑and‑drop visual layout editor with live, real‑data preview |
| **Output** | One‑ or two‑sided PDF ID card with photo, barcode, QR verification code; uploaded template **or** themed default card |
| **Data** | Any standard user field or custom profile field, mapped dynamically |
| **Who can use** | `local/studentid_print:view` / `:print` (default: Manager) |
