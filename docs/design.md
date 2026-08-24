# Cert Manager Workload Design

## Purpose

This repository demonstrates that the reusable GitOps architecture can deploy an OpenShift operator workload in addition to an application workload.

Cert Manager is used as the operator installation example.

## Responsibilities

This repository is responsible for:

- defining the Cert Manager operator installation
- rendering OLM resources through Helm
- parameterizing operator configuration
- validating the workload chart

This repository is not responsible for:

- cluster selection
- ApplicationSet generation
- reusable GitHub Actions logic
- deployment orchestration

## Operator Installation Model

The workload uses standard OpenShift OLM resources:

```text
Namespace
   |
   v
OperatorGroup
   |
   v
Subscription
   |
   v
OLM
   |
   v
Cert Manager Operator
```

## Configuration

Default values:

```yaml
operator:
  namespace: cert-manager-operator
  subscriptionName: openshift-cert-manager-operator
  packageName: openshift-cert-manager-operator
  channel: stable-v1
  source: redhat-operators
  sourceNamespace: openshift-marketplace
  installPlanApproval: Automatic
```

The package and channel were validated against the OpenShift catalog.

The default channel reported by the catalog is:

```text
stable-v1
```

## GitOps Architecture

The intended deployment flow is:

```text
Reusable GitHub Actions Workflow
        |
        v
Reusable ApplicationSet Template
        |
        v
Cluster selector
gox.li9.com/environment=sbx
        |
        v
gox-sbx
        |
        v
Cert Manager Workload Repository
        |
        v
Namespace + OperatorGroup + Subscription
        |
        v
Cert Manager Operator
```

The workload repository does not duplicate workflow or ApplicationSet logic.

## Cluster Selection

The selected GOX sandbox cluster is registered with Argo CD using the label:

```text
gox.li9.com/environment=sbx
```

The reusable ApplicationSet template uses this label to generate the Argo CD Application for the sandbox cluster.

## Validation

Local chart validation:

```bash
helm lint charts/cert-manager
```

Rendering:

```bash
helm template cert-manager charts/cert-manager
```

Both commands completed successfully.

The OpenShift catalog was also checked to confirm:

```text
openshift-cert-manager-operator
stable-v1
redhat-operators
```

## Design Principle

The workload repository defines what is installed.

The reusable ApplicationSet determines where it is installed.

The reusable GitHub Actions workflows automate validation, rendering, and deployment.

Keeping these concerns separate allows the same GitOps architecture to support different application and operator workloads.
