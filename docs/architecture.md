# Architecture

This solution converts raw AIS/NMEA sentences into structured vessel-position intelligence in Microsoft Fabric.

## Design goals

- Keep the original AIS sentence for traceability.
- Decode the message inside KQL so no external parsing service is required.
- Support a lightweight demo path and a production Eventhouse path.
- Separate raw, normalized, routed, and decoded data into clear bronze, silver, and gold layers.
- Make the output easy to visualize in Power BI, Real-Time Dashboards, or KQL map rendering.

## Layers

| Layer | Table or query | Purpose |
| --- | --- | --- |
| Bronze | `AisRaw` | Stores raw NMEA sentences exactly as received. |
| Silver | `AisNormalized` | Parses NMEA envelope fields and classifies AIS message type/category. |
| Gold | `AisPositionReports`, `AisStaticReports`, `AisInfrastructureReports`, `AisBinaryMessages`, `AisSafetyMessages`, `AisControlMessages` | Routes normalized messages by analytics domain. |
| Gold | `AisDecodedClassAPosition` | Decodes Class A position reports into vessel position columns. |
| Silver | `AisDeadLetter` | Captures records that do not parse or classify cleanly. |

## Demo architecture

The demo script defines a small in-memory `datatable` named `MyTrack`, decodes the embedded AIS records directly in the query, and renders a map. This is ideal for executive briefings, technical walkthroughs, and customer demos where provisioning infrastructure would distract from the value story.

## Production architecture

The production script creates persistent tables and KQL functions, then wires update policies so new records automatically flow through the pipeline:

1. AIS messages land in `AisRaw`.
2. `NormalizeAis()` parses the NMEA envelope and classifies the AIS type.
3. Routing functions distribute valid records into category-specific gold tables.
4. `DecodeAisClassAPosition()` decodes position-report payloads into latitude, longitude, speed, course, heading, and validation fields.
5. `RouteAisDeadLetter()` records unsupported or malformed messages for operational review.

## Current decoder scope

The included decoder focuses on single-fragment AIS Class A position reports, message types 1, 2, and 3. The production schema also classifies all standard AIS message types 1 through 27, making it a clean foundation for adding static-voyage decoding, Class B decoding, multipart assembly, safety messages, and other maritime use cases.
