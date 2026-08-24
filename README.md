# Cert Manager Workload

This repository contains the Cert Manager operator workload used by the reusable GitOps reference architecture.

The repository is intentionally workload-specific. It defines the OpenShift resources required to install the Cert Manager operator through OLM and does not contain reusable workflow or ApplicationSet logic.

## Repository Purpose

This repository owns:

- the Cert Manager workload Helm chart
- Namespace definition
- OperatorGroup definition
- Subscription definition
- operator-specific configuration values
- implementation documentation

This repository does not contain:

- reusable GitHub Actions workflows
- reusable ApplicationSet templates
- cluster selection logic
- deployment orchestration logic

## Repository Structure

```text
charts/
└── cert-manager/
    ├── Chart.yaml
    ├── values.yaml
    └── templates/
        ├── namespace.yaml
        ├── operatorgroup.yaml
        └── subscription.yaml

docs/
└── design.md
```

## Operator Configuration

The default configuration uses:

```text
Package: openshift-cert-manager-operator
Channel: stable-v1
Catalog source: redhat-operators
Catalog namespace: openshift-marketplace
Install plan approval: Automatic
```

The package and default channel were validated against the OpenShift OperatorHub catalog.

## Rendered Resources

The Helm chart renders:

1. Namespace
2. OperatorGroup
3. Subscription

These resources allow OLM to install the Cert Manager operator on the selected OpenShift cluster.

## Validation

Validate the chart:

```bash
helm lint charts/cert-manager
```

Render the chart:

```bash
helm template cert-manager charts/cert-manager
```

The chart has been validated successfully with `helm lint` and `helm template`.

## GitOps Integration

This repository is intended to be consumed by the reusable ApplicationSet template repository.

The ApplicationSet layer is responsible for:

- selecting the target cluster
- selecting the workload repository and path
- selecting the target revision
- generating the Argo CD Application

This repository defines only the Cert Manager operator workload.

## Clean-Room Implementation

This repository is a new implementation created for the reusable GitOps reference architecture.

Existing Li9 implementations may be reviewed conceptually only. No existing workflow, ApplicationSet, Helm template, or repository structure is copied into this repository.
