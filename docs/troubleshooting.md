# Harness Deployment Troubleshooting

## 1. Artifact selection

The deployment service must have a primary artifact selected.

The service configuration should explicitly connect the deployment to the intended artifact source rather than leaving the artifact reference unresolved.

Relevant concepts:

- `serviceRef`
- `serviceInputs`
- `artifacts.primary`
- `primaryArtifactRef`

If the service UI shows the primary artifact as `<+input>`, verify that the pipeline supplies the corresponding service/artifact input.

## 2. Kubernetes image expression

A deployment previously failed because the Kubernetes workload received an unresolved Harness expression.

Problematic pattern:

```yaml
image: <+artifact.image>
```

Use the resolved primary artifact fields instead:

```yaml
image: <+artifacts.primary.imagePath>:<+artifacts.primary.tag>
```

or:

```yaml
image: <+artifacts.primary.image>
```

The exact expression should match the artifact object exposed by the Harness service configuration.

## 3. Nexus/Docker artifact configuration

The deployment work used a Nexus Docker registry as the artifact source.

Configure the following in Harness rather than committing them to Git:

- Docker/Nexus connector
- Registry URL
- Repository/artifact path
- Artifact tag/version

Example structure:

```text
Registry: https://<nexus-host>
Repository: <docker-repository>
Artifact path: <organization>/<image>
Tag: <version>
```

Never store the Nexus username/password or access token in this repository.

## 4. Nexus connector troubleshooting

If a Nexus trigger or repository lookup remains in a loading state, investigate:

1. Connector credentials and credential type.
2. Delegate connectivity to the Nexus endpoint.
3. TLS certificate trust.
4. DNS resolution from the delegate.
5. Firewall/proxy connectivity.
6. Nexus API permissions required to list repositories/tags.

Useful delegate-side symptoms include:

- `PKIX path building failed`
- `SSLHandshakeException`
- `UnknownHostException`
- connection timeout

## 5. Deployment validation

After deployment, validate the actual image received by Kubernetes.

```bash
kubectl get deployment <deployment> -n <namespace> -o yaml
kubectl get pods -n <namespace> -o wide
kubectl describe deployment <deployment> -n <namespace>
kubectl describe pod <pod> -n <namespace>
```

The container image should contain the real Nexus/Docker image reference and tag.

If Kubernetes shows the literal text `<+...>`, Harness expression resolution did not occur for that field.

## 6. Scope of this repository

This repository is intentionally a reconstruction rather than a copy of the corporate Harness project. It does not contain:

- corporate credentials
- access tokens
- internal certificates
- private Nexus configuration
- production cluster details
- corporate Harness project secrets

Those values must remain in the appropriate Harness secret/connector configuration.
