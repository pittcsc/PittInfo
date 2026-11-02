### PittInfo Technical Design Document

**Author(s):** ArchZak \
**Date:** November 2, 2026 \
**Status:** Draft\
**Project:** PittInfo

PittInfo gives Pitt students a simple, point-and-click way to check campus availability, including dining, exercise facilities, study spaces, computing labs, and shuttles.

## Problem

Students need a convenient way to check campus availability when deciding where to go or which resource to use. PittInfo brings these checks together in one place.

### Goals

- Make campus availability easy to check through a point-and-click interface.
- Include dining, exercise facilities, study spaces, computing labs, and shuttles in the first release.
- For study spaces, show open buildings, opening hours, and available reservable rooms.
- Let students save favorite locations in their browser.
- Provide the agreed availability features across all five launch areas.

### Non-Goals

- Recreate the guides and reference content provided by PittCS Wiki.
- Expand beyond campus availability in the initial scope.
- User accounts or cross-device synchronization of favorites.
- A database or persistent backend storage.
- Provider-data caching, stale-data fallback, or background cache refresh.
- Booking rooms directly through PittInfo; send students to the provider to reserve.

## Proposed Solution

### High-Level Approach

Build a React and TypeScript frontend with a Python and FastAPI backend using PittAPI for supported campus data. Save favorites in the browser without requiring accounts.

Fetch provider data on demand through the backend, without a provider-data cache. Extend PittAPI to supply the building hours and reservable-room availability needed for study spaces. Provider-specific discovery and parsing belong in PittAPI. Refresh behavior and failure presentation remain to be decided.

| Area | First-release behavior |
| --- | --- |
| Dining | Open/closed status, hours, and menus |
| Exercise facilities | Gym occupancy, capacity, and closure status |
| Study spaces | Building hours and reservable-room availability, with links to the provider for booking |
| Computing labs | Open/closed status and available computer counts |
| Shuttles | Routes, stops, arrival estimates, and vehicle positions on a map |

### Architecture

React/TypeScript frontend → FastAPI backend → PittAPI → campus data providers.

The browser stores favorite locations locally. The backend does not store or cache provider results for reuse across requests. Deployment details remain to be decided.

## Blockers

- Study-space data coverage: PittAPI currently exposes Hillman reservation records and a reservation total, but does not expose building opening hours or a complete inventory of reservable rooms and available time slots. Identify suitable sources and determine the required PittAPI additions before claiming study-space availability.

## Testing Plan

## Alternative Approaches

### Database and Cached Provider Data

Deferred from the first release to keep the initial implementation small. A future iteration may introduce provider-data caching if request volume, response times, or provider reliability justify it. Persistent storage may be evaluated if a concrete storage requirement emerges. Neither is required for browser-local favorites.
