# certs-package

This package provides wildcard certificate management functionality using cert-manager and Let's Encrypt and/or a custom CA for Educates usage.

See [Cert manager's Route53 Docs](https://docs.cert-manager.io/en/release-0.11/tasks/issuers/setup-acme/dns01/route53.html) for how to configure AWS IAM

See: https://cert-manager.io/docs/configuration/selfsigned/#bootstrapping-ca-issuers

## Components

- certs

## Configuration

The following configuration values can be set to customize the certs installation.

### Global

| Value                       | Required/Optional                                        | Default        | Description                                                                                        |
| --------------------------- | -------------------------------------------------------- | -------------- | -------------------------------------------------------------------------------------------------- |
| `certmanager.namespace`     | Optional                                                 | cert-manager   | The namespace in which to deploy certs.                                                            |
| `ingress.namespace`         | Optional                                                 | projectcontour |                                                                                                    |
| `ingress.ingressClass`      | Optional                                                 | contour        |                                                                                                    |
| `tlsdelegation.targetns`    | Optional                                                 | \*             |                                                                                                    |
| `domains`                   | Required                                                 | <EMPTY>        | List of domains to generate a wildcard for. Wildcard `*` will be added to the provided domain name |
| `certProvider`              | Required                                                 | <EMPTY>        | Values: acme-aws, local, provided                                                                  |
| `aws.credentials.accessKey` | Required if certProvider=acme-aws and not using AWS IRSA | <EMPTY>        |                                                                                                    |
| `aws.credentials.secretKey` | Required if certProvider=acme-aws and not using AWS IRSA | <EMPTY>        |                                                                                                    |
| `aws.certs.region`          | Optional if certProvider=acme-aws                        | eu-west-1      |                                                                                                    |
| `aws.certs.email`           | Optional if certProvider=acme-aws                        | user@none.com  |                                                                                                    |
| `local.rootCA.crt`          | Required if certProvider=local                           | <EMPTY>        |                                                                                                    |
| `local.rootCA.key`          | Required if certProvider=local                           | <EMPTY>        |                                                                                                    |
| `provided.wildcartCert.crt` | Required if certProvider=provided                        | <EMPTY>        |                                                                                                    |
| `provided.wildcartCert.key` | Required if certProvider=provided                        | <EMPTY>        |                                                                                                    |

## Usage Example

This walkthrough guides you through using certs...

## Test

Test aws Route53/Let's encrypt certificate generation:

```
ytt -f src/example-values/aws.yaml -f src/bundle/config
```

Test providing a local CA to the cluster:

```
ytt -f src/example-values/kind.yaml -f src/bundle/config
```

Test providing a wildcard secret already generated to the cluster:

```
ytt -f src/example-values/minikube.yaml -f src/bundle/config
```

## Develop checklist

1. Update your [config.json](./config.json) with the package info
2. Add [overlays](./src/bundle/config/overlays/) and [values](./src/bundle/config/values.yaml)
3. Test your bundle: `ytt --data-values-file src/example-values/minikube.yaml  -f target/bundle/config` providing a sample values file from [example-values](./src/examples-values/)
4. Build your bundle `./hack/build.sh`
5. Add it to the [failk8s-repo](https://github.com/failk8s-packages/failk8s-repo) and publish the new repo and test the package from there, or [test with local files](./target/test)

**NOTE**: `develop` versions will not be image locked and will have a version of 0.0.0+develop to comply with semver.
