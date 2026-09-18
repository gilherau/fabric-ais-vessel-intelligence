# KQL reference

## Demo script

`kql/demo/staten-island-ferry-inline-demo.kql`

Runs a complete end-to-end demonstration from embedded AIS AIVDM records to map-ready position output. It is best for briefings and demos where the goal is to show the transformation rather than deploy a persistent pipeline.

## Production script

`kql/production/create-eventhouse-schema-and-policies.kql`

Creates the persistent Eventhouse objects for a production-style AIS processing pipeline.

## Production demo control script

`kql/production/production-demo-control.kql`

Provides guided run blocks for operating the production demo:

| Block | Purpose |
| --- | --- |
| 1 | Reset all demo tables. |
| 2 | Ingest the Staten Island Ferry sample track. |
| 3 | Ingest parser coverage records across multiple AIS message types. |
| 4A | Validate parser routing. |
| 4B | Validate dead-letter behavior. |
| 5 | Render the ferry sample track from `AisDecodedClassAPosition` with filters that exclude parser coverage positions. |

## Tables

| Table | Layer | Description |
| --- | --- | --- |
| `AisRaw` | Bronze | Raw AIS/NMEA messages as received. |
| `AisNormalized` | Silver | Parsed envelope fields and AIS message classification. |
| `AisPositionReports` | Gold | Normalized position-category messages. |
| `AisStaticReports` | Gold | Normalized static-vessel messages. |
| `AisInfrastructureReports` | Gold | Normalized base station, aid-to-navigation, and related infrastructure messages. |
| `AisBinaryMessages` | Gold | Normalized binary AIS messages. |
| `AisSafetyMessages` | Gold | Normalized safety AIS messages. |
| `AisControlMessages` | Gold | Normalized control AIS messages. |
| `AisDecodedClassAPosition` | Gold | Decoded Class A position-report telemetry. |
| `AisDeadLetter` | Silver | Raw messages that failed envelope parsing or AIS type classification. |

## Functions

| Function | Description |
| --- | --- |
| `NormalizeAis()` | Parses the NMEA envelope, extracts payload metadata, identifies AIS message type, and assigns an analytics category. |
| `RouteAisPositionReports()` | Selects normalized position messages. |
| `RouteAisStaticReports()` | Selects normalized static-vessel messages. |
| `RouteAisInfrastructureReports()` | Selects normalized infrastructure messages. |
| `RouteAisBinaryMessages()` | Selects normalized binary messages. |
| `RouteAisSafetyMessages()` | Selects normalized safety messages. |
| `RouteAisControlMessages()` | Selects normalized control messages. |
| `RouteAisDeadLetter()` | Identifies malformed or unsupported raw messages. |
| `DecodeAisClassAPosition()` | Decodes single-fragment Class A position reports, message types 1, 2, and 3. |

## Decoded position fields

| Field | Meaning |
| --- | --- |
| `MMSI` | Maritime Mobile Service Identity. |
| `Latitude`, `Longitude` | Decimal-degree vessel position. |
| `SpeedOverGroundKnots` | Speed over ground in knots. |
| `CourseOverGroundDegrees` | Course over ground in degrees. |
| `TrueHeadingDegrees` | True heading in degrees when available. |
| `NavigationStatus` | AIS navigation status code. |
| `UTCSecond` | AIS UTC second field when valid. |
| `IsValidPosition` | Boolean position quality check. |
