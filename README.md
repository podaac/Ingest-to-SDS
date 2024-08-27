# swodlr-ingest-to-sds

A SWODLR microservice for ingesting granules from a CNM-R SNS topic to a SDS

## How it works

`ingest-to-sds` subscribes to a SNS topic which receives CNM-R messages from the
PO.DAAC Cumulus pipeline which processes the PIXC, PIXCVec, and Orbit granules
that SWODLR requires as input granules. `ingest-to-sds` then requests that the
SDS powering SWODLR perform an ingest on the products within those CNM-R
messages. `ingest-to-sds` will continue to track the ingest job as it proceeds
through the SDS in order to monitor for its completion. This data is tracked
within a DynamoDB table for later monitoring by operations/development teams.
Errors that result from ingestion will also be cataloged within that table
for quick mass troubleshooting/diagnostics.

`ingest-to-sds` is a purely internal SWODLR service and is not part of any user
request pipeline. `ingest-to-sds` also operates as a "best-effort" service.
Because `raster-create` can perform its own ingest process for required granules
via its Preflight process, `ingest-to-sds` is simply a service to help bring
down latencies for `raster-create` by preloading the granule database on the
SDS.

`ingest-to-sds` is ***NOT*** a critical SWODLR service and occasional errors
that occur during its operation should ***NOT*** be treated with high priority
**unless occuring with unusually high frequency**