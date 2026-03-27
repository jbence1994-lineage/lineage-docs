# Lineage — Genealogy AI & Data Platform

## Problem

I'm researching my ancestors, but genealogical data is spread across disparate sources and remains largely unstructured:
free-form notes recorded on paper, physical photos yet to be digitized, digitized photos scattered across cloud storage,
and oral knowledge shared by relatives.

**This presents significant challenges for managing existing and newly explored data.**

## Solution

A unified AI & Data platform to collect, structure and explore genealogical data, augmented by AI-powered capabilities.

## Features

### PII Data

All biographical data is stored encrypted at rest.

Managing (create, read, update, delete) core biographical data per person:

- `first_name`
- `middle_name`
- `last_name`
- `place_of_birth`
- `date_of_birth`
- `place_of_death`
- `date_of_death`

### Relationships

Stored as directional links — `parent_of` is stored explicitly; `child_of` is derived from it.

- `parent_of` / `child_of`
- `spouse_of`
- `sibling_of`

### Photos

- Uploading and removing photos per person
- Designating a primary (profile) photo per person

### Notes

- Free-form textual notes per person

### Search & Filtering

- Basic search by family name, birth year, or place of birth/death

### Family Tree Visualization

- Browsing and navigating the connected person graph

## AI Features

All AI capabilities run on a self-hosted, locally running LLM — no data is sent to external APIs or commercial AI
services.

- Biographical summary generation — generate a structured summary of a person based on their notes
- Natural language search — query the family tree in plain Hungarian
