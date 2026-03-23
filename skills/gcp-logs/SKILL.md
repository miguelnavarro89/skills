---
name: manr:gcp-logs
description: Query and analyze GCP logs using gcloud CLI across resource types (GKE, Cloud Run, GCE, Cloud Functions, App Engine). Use when investigating errors, debugging services, analyzing incidents, or searching operational logs in Google Cloud.
---

# GCP Logs Query Skill

## Prerequisites

- `gcloud` CLI installed and authenticated
- Appropriate IAM permissions (`roles/logging.viewer` or equivalent)
- Target project set via `--project` flag or `gcloud config set project`

## Base Query Structure

```bash
gcloud logging read 'FILTER' \
  --project=PROJECT_ID \
  --limit=50 \
  --format=json \
  --freshness=1d
```

Always include `--limit` and `--freshness` (or timestamp bounds) to avoid unbounded queries.

## Resource Type Templates

### GKE / Kubernetes Containers

```bash
gcloud logging read '
  resource.type="k8s_container"
  AND resource.labels.project_id="PROJECT_ID"
  AND resource.labels.cluster_name="CLUSTER"
  AND resource.labels.namespace_name="NAMESPACE"
  AND resource.labels.container_name="CONTAINER"
' --project=PROJECT_ID --limit=50 --format=json --freshness=1d
```

Optional labels: `resource.labels.location`, `labels.k8s-pod/app`, or any custom pod labels via `labels.k8s-pod/LABEL_KEY`.

### Cloud Run

```bash
gcloud logging read '
  resource.type="cloud_run_revision"
  AND resource.labels.service_name="SERVICE"
  AND resource.labels.location="REGION"
' --project=PROJECT_ID --limit=50 --format=json --freshness=1d
```

### Compute Engine (GCE)

```bash
gcloud logging read '
  resource.type="gce_instance"
  AND resource.labels.instance_id="INSTANCE_ID"
' --project=PROJECT_ID --limit=50 --format=json --freshness=1d
```

### Cloud Functions

```bash
gcloud logging read '
  resource.type="cloud_function"
  AND resource.labels.function_name="FUNCTION"
  AND resource.labels.region="REGION"
' --project=PROJECT_ID --limit=50 --format=json --freshness=1d
```

### App Engine

```bash
gcloud logging read '
  resource.type="gae_app"
  AND resource.labels.module_id="SERVICE"
  AND resource.labels.version_id="VERSION"
' --project=PROJECT_ID --limit=50 --format=json --freshness=1d
```

### Cloud SQL

```bash
gcloud logging read '
  resource.type="cloudsql_database"
  AND resource.labels.database_id="PROJECT_ID:INSTANCE"
' --project=PROJECT_ID --limit=50 --format=json --freshness=1d
```

### Generic (Any Resource)

```bash
gcloud logging read '
  logName="projects/PROJECT_ID/logs/LOG_NAME"
' --project=PROJECT_ID --limit=50 --format=json --freshness=1d
```

## Progressive Query Strategy

Minimize context consumption by fetching progressively.

### Step 1: Narrow Fields First

Fetch only timestamps and messages to get an overview:

```bash
gcloud logging read 'FILTER' \
  --project=PROJECT_ID --limit=30 \
  --format='json(timestamp,textPayload,jsonPayload.message,severity)' \
  --freshness=1d
```

### Step 2: Full Context for Specific Entries

Once you spot interesting entries, narrow the filter and fetch full payloads:

```bash
gcloud logging read 'FILTER AND "SPECIFIC_TERM"' \
  --project=PROJECT_ID --limit=20 --format=json --freshness=1d
```

### Step 3: Extract Specific Fields

Post-process with `jq` for targeted extraction:

```bash
gcloud logging read 'FILTER' --format=json | jq '.[].jsonPayload.error'
```

## Common Filter Patterns

### By Severity

```
severity>="WARNING"
severity="ERROR"
severity>="ERROR"
```

### By Text Content

```
textPayload:"search term"
jsonPayload.message:"search term"
"exact phrase match"
```

### By HTTP Request

```
httpRequest.status>=500
httpRequest.requestMethod="POST"
httpRequest.requestUrl:"api/v1/users"
```

### By Trace / Request ID

```
trace="projects/PROJECT_ID/traces/TRACE_ID"
httpRequest.requestUrl:"REQUEST_ID"
jsonPayload.requestId="REQUEST_ID"
```

### Exclude Noisy Entries

```
-textPayload:"health check"
-jsonPayload.message:"routine_task"
NOT "expected_noise_pattern"
-severity="DEBUG"
```

### Combine Filters

Use `AND`, `OR`, and parentheses:

```
severity>="ERROR" AND (textPayload:"timeout" OR textPayload:"connection refused")
```

## Time Range Options

| Flag | Example | Description |
|------|---------|-------------|
| `--freshness` | `--freshness=1h` | Relative: last N hours/days/weeks |
| Timestamp filter | `timestamp>="2026-01-27T00:00:00Z"` | Absolute start |
| Range | `timestamp>="START" AND timestamp<="END"` | Absolute window |

Common freshness values: `15m`, `1h`, `6h`, `1d`, `7d`.

## Output Formats

| Format | Use Case |
|--------|----------|
| `--format=json` | Full structured data |
| `--format='json(FIELDS...)'` | Specific fields only (token-efficient) |
| `--format='value(FIELD)'` | Raw values, no structure |
| `--format='table(timestamp,severity,textPayload)'` | Human-readable table |

## Useful Post-Processing

### Count errors by type

```bash
gcloud logging read 'FILTER AND severity="ERROR"' --format=json \
  | jq -r '.[].textPayload // .[].jsonPayload.message' \
  | sort | uniq -c | sort -rn | head -20
```

### Timeline of errors

```bash
gcloud logging read 'FILTER AND severity>="ERROR"' \
  --format='csv(timestamp,severity,textPayload)' --freshness=1d
```

### Extract unique error messages

```bash
gcloud logging read 'FILTER AND severity="ERROR"' --format=json \
  | jq -r '[.[].jsonPayload.message // .[].textPayload] | unique[]'
```

## Tips

1. **Start specific** — filter by exact IDs, error patterns, or service names first
2. **Limit results** — always use `--limit` to avoid context overload
3. **Progressive detail** — overview first, then drill into specifics
4. **Exclude noise** — add `-textPayload:"known_noise"` filters liberally
5. **Time-bound everything** — use `--freshness` or timestamp filters
6. **Use jq** — post-process JSON locally rather than reading raw output
7. **Check resource types** — run `gcloud logging resource-descriptors list` if unsure which `resource.type` to use
