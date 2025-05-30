# opentour-quarkus

This project uses Quarkus, the Supersonic Subatomic Java Framework.

If you want to learn more about Quarkus, please visit its website: https://quarkus.io/ .

## Running the application in dev mode

You can run your application in dev mode that enables live coding using:
```shell script
./mvnw compile quarkus:dev
```

> **_NOTE:_**  Quarkus now ships with a Dev UI, which is available in dev mode only at http://localhost:8080/q/dev/.

## Packaging and running the application

The application can be packaged using:
```shell script
./mvnw package
```
It produces the `quarkus-run.jar` file in the `target/quarkus-app/` directory.
Be aware that it’s not an _über-jar_ as the dependencies are copied into the `target/quarkus-app/lib/` directory.

The application is now runnable using `java -jar target/quarkus-app/quarkus-run.jar`.

If you want to build an _über-jar_, execute the following command:
```shell script
./mvnw package -Dquarkus.package.type=uber-jar
```

The application, packaged as an _über-jar_, is now runnable using `java -jar target/*-runner.jar`.

## Creating a native executable

You can create a native executable using: 
```shell script
./mvnw package -Dnative
```

Or, if you don't have GraalVM installed, you can run the native executable build in a container using: 
```shell script
./mvnw package -Dnative -Dquarkus.native.container-build=true
```

You can then execute your native executable with `./target/opentour-quarkus-1.0.0-SNAPSHOT-runner`

If you want to learn more about building native executables, please consult https://quarkus.io/guides/maven-tooling.

## Deploy with GitOps

### Development

```
project: default
source:
  repoURL: 'https://github.com/rbaumgar/opentour-quarkus'
  path: src/main/kubernetes/dev
  targetRevision: master
destination:
  server: 'https://kubernetes.default.svc'
  namespace: opentour-dev
```

```bash
cat <<EOF | oc create -f -
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: quarkus-dev
  namespace: openshift-gitops
spec:
  destination:
    namespace: opentour-dev
    server: 'https://kubernetes.default.svc'
  project: default
  source:
    path: src/main/kubernetes/dev
    repoURL: 'https://github.com/rbaumgar/opentour-quarkus'
    targetRevision: master
EOF
```

### Production

```bash
cat <<EOF | od apply -f -
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: quarkus-prod
  namespace: openshift-gitops
spec:
  destination:
    namespace: opentour-prod
    server: 'https://kubernetes.default.svc'
  project: default
  source:
    path: src/main/kubernetes/prod
    repoURL: 'https://github.com/rbaumgar/opentour-quarkus'
    targetRevision: master
EOF
```

```bash
$ oc create ns opentour-prod
namespace/opentour-prod created
$ oc label ns opentour-prod argocd.argoproj.io/managed-by=openshift-gitops
namespace/opentour-prod labeled
$ oc create ns opentour-dev
namespace/opentour-dev created
$ oc label ns opentour-dev argocd.argoproj.io/managed-by=openshift-gitops
namespace/opentour-dev labeled
```

## Related Guides

- OpenShift ([guide](https://quarkus.io/guides/deploying-to-openshift)): Generate OpenShift resources from annotations

## Provided Code

### RESTEasy JAX-RS

Easily start your RESTful Web Services

[Related guide section...](https://quarkus.io/guides/getting-started#the-jax-rs-resources)

### RESTEasy Reactive

Easily start your Reactive RESTful Web Services

[Related guide section...](https://quarkus.io/guides/getting-started-reactive#reactive-jax-rs-resources)
