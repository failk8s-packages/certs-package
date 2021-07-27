# certs-package

This package provides certificate management functionality using cert-manager and Let's Encrypt and/or a custom CA.

See [Cert manager's Route53 Docs](https://docs.cert-manager.io/en/release-0.11/tasks/issuers/setup-acme/dns01/route53.html) for how to configure AWS IAM

## Components

* certs

## Configuration

The following configuration values can be set to customize the certs installation.

### Global

| Value | Required/Optional | Description |
|-------|-------------------|-------------|
| `certmanager.namespace` | Optional | The namespace in which to deploy certs. |
| `ingress.namespace` | Required | |
| `ingress.ingress_class` | Optional | |
| `domain` | Required | |
| `wildcard_domain` | Required | |
| `certs_use` | Required | Values: aws, local_ca, provided |
| `aws.access_key_id` | Required if certs_use=aws | |
| `aws.secret_access_key` | Required if certs_use=aws | |
| `aws.certs.region` | Required if certs_use=aws | |
| `aws.certs.email` | Required if certs_use=aws | |
| `local.root_ca.crt` | Required if certs_use=local_ca | |
| `local.root_ca.key` | Required if certs_use=local_ca | |
| `provided.wildcard_tls.crt` | Required if certs_use=provided | |
| `provided.wildcard_tls.key` | Required if certs_use=provided | |

## Usage Example

This walkthrough guides you through using certs...

## Test

Test aws Route53/Let's encrypt certificate generation:
```
ytt -f src/bundle/config -v domain=example.com -v wildcard_domain=apps.example.com -v aws.access_key_id=xxxx -v aws.secret_access_key=yyyy -v certs_use=aws
```

Test providing a local CA to the cluster:
```
ytt -f src/bundle/config -v domain=example.com -v wildcard_domain=apps.example.com -v local.root_ca.crt=xxxx -v local.root_ca.key=yyyy -v certs_use=local_ca
```


Test providing a wildcard secret already generated to the cluster:
```
ytt -f src/bundle/config -v domain=example.com -v wildcard_domain=apps.example.com -v provided.wildcard_tls.crt=xxxx -v provided.wildcard_tls.key=yyyy -v certs_use=provided
```

## Develop checklist

1. Update your [config.json](./config.json) with the package info
2. Update your [vendir.yml](./src/bundle/vendir.yml) with your upstreams
3. `vendir sync` in `src/bundle` to fetch your new upstream files
4. Add [overlays](./src/bundle/config/overlays/) and [values](./src/bundle/config/values.yaml)
5. Update your [bundle.yml](./src/bundle/.imgpkg/bundle.yml) file
6. Test your bundle: `ytt -f bundle`
7. Lock images used: `kbld -f . --imgpkg-lock-output .imgpkg/images.yml`
8. Publish your bundle: `imgpkg push --bundle quay.io/failk8s/certs-package:develop --file .`. These steps can be done via [hack/build-package.sh](./hack/build-package.sh)
9. Package up your k8s manifests and test in k8s [hack/package-manifests.sh](./hack/package-manifests.sh). The files will be in `target` folder.
