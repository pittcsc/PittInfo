### PittInfo Technical Design Document

**Author(s):** ArchZak \
**Date:** November 2, 2026 \
**Status:** Draft\
**Project:** PittInfo

PittInfo gives Pitt students a simple, point-and-click way to check campus availability, covering dining, exercise facilities, study spaces, and computing labs, with a redirect to official shuttle tracking.

## Problem

Students need a convenient way to check campus availability when deciding where to go or which resource to use. PittInfo brings these checks together in one place.

### Goals

- Make campus availability easy to check through a point-and-click interface.
- Include dining, exercise facilities, study spaces, computing labs, and shuttles in the first release.
- For study spaces, show open buildings, opening hours, and available reservable rooms.
- Provide a redirect to official shuttle tracking with minimal supporting information.

### Non-Goals

- Recreate the guides and reference content provided by PittCS Wiki.
- Expand beyond campus availability in the initial scope.
- Favorites, user accounts, or cross-device synchronization.
- Live shuttle tracking, arrival estimates, or a shuttle map.
- Frontend layout, visual design, and UX decisions during the current backend design phase.
- A database or persistent backend storage.
- Provider-data caching, stale-data fallback, or background cache refresh.
- Booking rooms directly through PittInfo; send students to the provider to reserve.

## Proposed Solution

### High-Level Approach

Prioritize the Python and FastAPI backend using PittAPI for supported campus data. A React and TypeScript frontend is planned; its layout and visual design are deferred.

Fetch provider data on demand through the backend, without a provider-data cache. Extend PittAPI to supply the building hours and reservable-room availability needed for study spaces. Provider-specific discovery and parsing belong in PittAPI. Fetch data on page entry and when the student requests a manual refresh; do not poll automatically.

If a provider or location fails, mark only the affected information unavailable and offer Retry. Preserve successful results elsewhere. Distinguish failures from valid outcomes such as closed facilities or no available rooms. The frontend may retain successful results for the current view; this does not introduce a reusable provider-data cache.

| Area | First-release behavior |
| --- | --- |
| Dining | Open/closed status, hours, and menus |
| Exercise facilities | Gym occupancy, capacity, and closure status |
| Study spaces | Building hours and reservable-room availability, with links to the provider for booking |
| Computing labs | Open/closed status and available computer counts |
| Shuttles | Redirect to official shuttle tracking with minimal supporting information; exact fields remain to be decided |

### Architecture

React/TypeScript frontend → FastAPI backend → PittAPI → campus data providers.

The backend does not use a database or store provider results for reuse across requests. Frontend layout and deployment details remain to be decided.

## Blockers

- Study-space data coverage: PittAPI currently exposes Hillman reservation records and a reservation total, but does not expose building opening hours or a complete inventory of reservable rooms and available time slots. Identify suitable sources and determine the required PittAPI additions before claiming study-space availability.

Verified starting points for provider discovery:

- [Pitt library hours](https://library.pitt.edu/hours).
- [Pitt group-study room instructions](https://library.pitt.edu/reserve-group-study) and [LibCal booking availability](https://pitt.libcal.com/spaces).
- [Official Pitt shuttle information](https://www.pc.pitt.edu/shuttle-services), which links to the tracking application and desktop tracker.

These pages establish where information is published; a usable structured hours or room-availability feed has not yet been verified.

## Testing Plan

- Backend tests cover the four availability areas using controlled provider responses, including timeouts and malformed data. Shuttle tests cover the redirect and agreed minimal information.
- Verify that a failed provider or location does not discard successful results for other information, and that failures are distinct from valid closed or empty states.
- When the frontend is implemented, test loading, results, empty results, failures, manual refresh, Retry, and preservation of successful results when other information fails.
- Browser tests cover the main browse/detail journeys and shuttle redirect when the frontend is implemented.
- Live smoke checks from the deployment environment verify provider access for launch features.
- Manual frontend checks cover mobile layouts and keyboard navigation when the frontend is implemented.
- Require the agreed behaviors to pass rather than setting an arbitrary coverage percentage.

## Alternative Approaches

### Database and Cached Provider Data

Deferred from the first release to keep the initial implementation small. A future iteration may introduce provider-data caching if request volume, response times, or provider reliability justify it. Persistent storage may be evaluated if a concrete storage requirement emerges. Neither is required for the agreed first-release behavior.

### Live Shuttle Tracking

Use the existing official shuttle tracking application rather than duplicating its map, vehicle tracking, and arrival estimates. PittInfo provides a redirect and minimal supporting information.
