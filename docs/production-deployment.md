# Production deployment

Use this path when deploying AIS telemetry processing into a Microsoft Fabric Eventhouse.

## Prerequisites

- A Microsoft Fabric workspace.
- A Fabric Eventhouse and KQL database.
- Permission to create tables, functions, and update policies.
- An ingestion path for AIS/NMEA records, such as Eventstream, Event Hub, Kafka, file ingestion, or an AIS receiver feed.

## Deploy the KQL assets

1. Open your Fabric KQL database.
2. Review `kql/production/create-eventhouse-schema-and-policies.kql`.
3. Run the script as a database script.
4. Confirm the tables and functions were created.
5. Ingest raw AIS records into `AisRaw` with this shape:

```kusto
ReceivedTime: datetime,
RawSentence: string
```

If your ingestion source already provides event time, map it to `ReceivedTime`. If not, the decoder can still use ingestion-time semantics where appropriate.

## Validate the pipeline

After ingesting sample records, run:

```kusto
AisRaw
| count
```

```kusto
AisNormalized
| summarize Records=count() by Category, MessageType, MessageName
| order by Category asc, MessageType asc
```

```kusto
AisDecodedClassAPosition
| where IsValidPosition
| project ReceivedTime, MMSI, Latitude, Longitude, SpeedOverGroundKnots, CourseOverGroundDegrees, TrueHeadingDegrees
| take 100
```

## Run the production demo control script

After deploying the schema and update policies, use `kql/production/production-demo-control.kql` as a guided demo runbook.

Run selected blocks in this order:

1. **Reset demo tables**: clears bronze, silver, and gold tables so the demo starts from a known state.
2. **Ingest Staten Island Ferry sample track**: appends the NOAA-derived sample records into `AisRaw`.
3. **Ingest parser coverage records**: appends representative AIS/NMEA message types plus one intentionally malformed record for the dead-letter path.
4. **Validate routing and dead-letter behavior**: run the routing and dead-letter validation sub-blocks to confirm normalization, message classification, category routing, and malformed-record handling.
5. **Render the ferry sample track**: reads from `AisDecodedClassAPosition` and filters the map to the ferry sample MMSI and timestamp range so parser coverage records do not distort the visualization.

Blocks 2 and 3 can be run in the same demo environment. The map query is intentionally filtered to keep the vessel-track story clean while still letting you prove broader parser coverage.

## Operational notes

- `AisRaw` is the durable bronze landing table.
- `AisNormalized` is populated by an update policy from `AisRaw`.
- Category tables are populated by update policies from `AisNormalized`.
- `AisDecodedClassAPosition` is populated by an update policy from `AisPositionReports`.
- `AisDeadLetter` captures malformed or unsupported records from `AisRaw`.

## Production hardening checklist

- Add retention policies that match your operational and compliance requirements.
- Add ingestion mappings for each source system.
- Add monitoring for dead-letter volume and parse success rate.
- Add dashboard measures for message throughput, vessel count, stale positions, and invalid coordinates.
- Extend decoding for additional AIS message types required by your scenario.
- Add multipart assembly if your source includes multipart static-voyage messages.
- Validate public sample data and customer-specific data handling before publishing dashboards or screenshots.
