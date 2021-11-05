# certs-package

This package provides certificate management functionality using cert-manager and Let's Encrypt and/or a custom CA.

See [Cert manager's Route53 Docs](https://docs.cert-manager.io/en/release-0.11/tasks/issuers/setup-acme/dns01/route53.html) for how to configure AWS IAM

## Components

* certs

## Configuration

The following configuration values can be set to customize the certs installation.

### Global

| Value | Required/Optional | Default |Description |
|-------|-------------------|---------|-------------|
| `certmanager.namespace` | Optional | cert-manager | The namespace in which to deploy certs. |
| `ingress.namespace` | Optional | projectcontour | |
| `ingress.ingress_class` | Optional | contour | |
| `tlsdelegation.targetns` | Optional | * | |
| `domain` | Required | <EMPTY> | |
| `wildcard_domain` | Required | <EMPTY> | |
| `certs_use` | Required | <EMPTY> | Values: aws, local_ca, provided |
| `aws.access_key_id` | Required if certs_use=aws | <EMPTY> | |
| `aws.secret_access_key` | Required if certs_use=aws | <EMPTY> | |
| `aws.certs.region` | Optional if certs_use=aws | eu-west-1 | |
| `aws.certs.email` | Optional if certs_use=aws | user@none.com ||
| `local.root_ca.crt` | Required if certs_use=local_ca | <EMPTY> ||
| `local.root_ca.key` | Required if certs_use=local_ca | <EMPTY> ||
| `provided.wildcard_tls.crt` | Required if certs_use=provided | <EMPTY> ||
| `provided.wildcard_tls.key` | Required if certs_use=provided | <EMPTY> ||

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
2. Add [overlays](./src/bundle/config/overlays/) and [values](./src/bundle/config/values.yaml)
3. Test your bundle: `ytt --data-values-file src/example-values/minikube.yaml  -f target/bundle/config` providing a sample values file from [example-values](./src/examples-values/)
4. Build your bundle `./hack/build.sh`
5. Add it to the [failk8s-repo](https://github.com/failk8s-packages/failk8s-repo) and publish the new repo and test the package from there, or [test with local files](./target/test)

**NOTE**: `develop` versions will not be image locked and will have a version of 0.0.0+develop to comply with semver.