# Staten Island Ferry sample

The inline demo uses valid AIS AIVDM sentences derived from NOAA's public AIS archive for 2024:

https://coast.noaa.gov/htdata/CMSP/AISDataHandler/2024/

The records are embedded directly in `kql/demo/staten-island-ferry-inline-demo.kql` so the demo can run without any ingestion setup.

## Usage

- Use the embedded data for quick KQL demonstrations.
- Use the production script when you need persistent tables and continuous ingestion.
- Do not use this sample data for navigation or safety decisions.
- Treat the sample as historical public data prepared for analytics demonstration, not live operational vessel data.

## Adding more samples

If you add separate sample files later, keep them small enough for GitHub, document their source, and confirm they are approved for public redistribution.
