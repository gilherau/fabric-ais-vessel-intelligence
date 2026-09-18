# Demo walkthrough

Use this path when you want to show AIS decoding without provisioning ingestion infrastructure.

## Prerequisites

- Access to Microsoft Fabric.
- A KQL query window.
- No Eventhouse ingestion setup is required for this demo.

## Run the demo

1. Open `kql/demo/staten-island-ferry-inline-demo.kql`.
2. Paste the script into a KQL query window.
3. Run the query.
4. Review the projected fields: `MMSI`, `Latitude`, `Longitude`, `SpeedKnots`, `CourseDegrees`, `HeadingDegrees`, and `UTCSecond`.
5. Use the final `render scatterchart with (kind=map)` statement to show the decoded vessel track.

## What to explain while presenting

- The source data is still raw NMEA 0183 AIS wire format.
- KQL parses the NMEA envelope and extracts the AIS armored payload.
- The decoder expands each six-bit AIS character into a bit stream.
- Field-level bit ranges are reconstructed into vessel telemetry.
- Coordinates are converted from AIS units into decimal degrees.
- The final result is ready for maps, dashboards, and operational analytics.

## Demo positioning

The demo is intentionally self-contained. It is designed to prove that the decoding logic works and to make the business value visible quickly. Use the production path when you need continuous ingestion, persistent tables, update policies, and operational routing.

The embedded sample is derived from NOAA's public AIS archive for 2024: https://coast.noaa.gov/htdata/CMSP/AISDataHandler/2024/
