# Harness CI/CD Work

This repository is a sanitized reconstruction of the Harness deployment work developed during troubleshooting.

## What is captured

- Harness Kubernetes deployment pipeline structure.
- Explicit Service and artifact selection.
- Nexus/Docker registry artifact configuration using placeholders.
- Correct Harness artifact expressions for Kubernetes manifests.
- Kubernetes deployment/service examples.
- Validation and troubleshooting notes for the deployment flow.
- Guidance for keeping credentials, internal URLs, and corporate identifiers out of source control.

## Important

The original corporate Harness project and pipeline configuration are not available as a source bundle. Therefore this repository contains the portions that can be reconstructed reliably from the work completed, with environment-specific values intentionally represented as placeholders.

No credentials, tokens, passwords, or internal certificates are stored here.

## Key artifact-expression fix

The deployment debugging established that the Kubernetes manifest must consume the resolved primary artifact rather than an unresolved generic artifact expression.

Preferred form:

`<+artifacts.primary.imagePath>:<+artifacts.primary.tag>`

or, where the Harness artifact object exposes the complete image reference:

`<+artifacts.primary.image>`

Avoid using an expression such as:

`<+artifact.image>`

when it is being passed literally to Kubernetes.

## Repository layout

```text
.
├── .gitignore
├── README.md
├── docs/
│   └── troubleshooting.md
├── harness/
│   └── pipeline.yaml
└── k8s/
    ├── deployment.yaml
    └── service.yaml
```

## Required environment-specific configuration

Before using the pipeline, replace the placeholders for:

- Harness organization/project identifiers
- Kubernetes connector
- Kubernetes namespace
- Nexus/Docker connector
- Nexus registry URL
- Artifact repository/path
- Deployment name
- Container port and service port

Do not commit the associated credentials.
