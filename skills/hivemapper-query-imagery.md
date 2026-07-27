---
name: Query street-level imagery for an area
description: >-
  Fetch recent Bee Maps (Hivemapper) dashcam imagery for a geographic area,
  authenticate correctly, and respect the 5 sq km area limit and [lon, lat]
  coordinate order.
api: openapi/hivemapper-beemaps-openapi.yml
operations: [queryLatestImagery, queryImageryByPolygon, getDevices]
---

# Query street-level imagery

Use the Bee Maps Developer API to retrieve geolocated dashcam frames for an area.

## Auth
Send an API key from the developer dashboard (https://beemaps.com/developers) either as:
- HTTP Basic: `Authorization: Basic <base64(username:api-key)>`, or
- the `apiKey` query parameter.

## Steps
1. Build a GeoJSON geometry (`Point` + `radius`, `LineString` + `buffer`, or `Polygon`) using **[longitude, latitude]** order. Keep the query area **≤ 5 sq km**.
2. (Optional) Call `getDevices` (unauthenticated) to confirm connectivity and get camera calibration for `hdc`/`hdcs`/`bee`.
3. For the freshest coverage, call `queryLatestImagery` (`POST /latest/poly`) with your geometry and an optional `min_week` (YYYY-mm-dd) cutoff. For a specific week, call `queryImageryByPolygon` (`POST /imagery/poly`) with `week`.
4. Set `catalog: true` to get metadata only (no signed image URLs) when you just need to enumerate coverage.
5. Read the returned frames: each has a signed `url`, `timestamp`, `position` (lon/lat/azimuth), IMU data, and `deviceType`. Signed URLs expire — download promptly or re-query.

## Rules
- Imagery signed-URL views are metered in USD on top of API credits — avoid re-fetching the same frames.
- A `400` means invalid geometry or an over-large area; `401` means the API key is missing/invalid.
