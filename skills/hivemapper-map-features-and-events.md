---
name: Pull road features and AI driving events
description: >-
  Query ML-detected road features and search AI driving-event videos from Bee
  Maps (Hivemapper), respecting the 31-day event window and credit accounting.
api: openapi/hivemapper-beemaps-openapi.yml
operations: [queryMapData, queryMapFeaturesByPolygon, searchAIEvents, getAIEvent]
---

# Road features and AI events

## Auth
API key via HTTP Basic (`base64(username:api-key)`) or the `apiKey` query parameter.

## Map features
1. Call `queryMapData` (`POST /map-data`) with `type: ["mapFeatures"]` (and/or `["imagery"]`), a GeoJSON `geometry`, and an optional `startDate` (YYYY-MM-DD, defaults to one week ago).
2. Or call `queryMapFeaturesByPolygon` (`POST /mapFeatures/poly`) with a polygon to get detected signs, hydrants, traffic lights, lane lines, and restrictions with `confidence` scores and `position`.
3. Read `totalCreditsUsed` / `totalCreditsRemaining` from `queryMapData` responses to track credit burn.

## AI driving events
1. Call `searchAIEvents` (`POST /aievents/search`) with `startDate`/`endDate` (**within 31 days**), a `polygon` of `[lon, lat]` points (min 4), and optional `types` (e.g. `HARSH_BRAKING`, `STOP_SIGN_VIOLATION`, `TAILGATING`). Page with `limit` (max 500) + `offset`.
2. For a single event, call `getAIEvent` (`GET /aievents/{id}`) with `includeGnssData=true` / `includeImuData=true` to expand synchronized sensor arrays and get the `videoUrl`.

## Rules
- Keep coordinates in **[lon, lat]** order and the area **≤ 5 sq km**.
- Event date ranges wider than 31 days return `400`.
