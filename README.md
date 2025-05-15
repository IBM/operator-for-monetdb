# operator-for-monetdb
The goal of this project is to simplify the deployment and management of a MonetDb instance in a Kubernetes environment. It is based on the Operator SDK framework for operators


# CRD

```
apiVersion: monetdb.monetdb.ibm.com/v1
kind: MonetDb
metadata:
  labels:
    app.kubernetes.io/name: operator-for-monetdb
    app.kubernetes.io/managed-by: kustomize
  name: monetdb-sample
spec:
  database: 'myopdb'
  # optional
  # use custom image
  #image: '<REGISTRY>/monetdb:latest'

```


# How to build

## operator for development

```
registry=<YOUR REGISTRY URL>
echo $registry

namespace=<YOUR NAMESPACE>
echo $namespace


make undeploy
make docker-build docker-push IMG=${registry}/monetdb-operator:latest
make install
make deploy IMG=${registry}/monetdb-operator:latest


kubectl config set-context --current --namespace=${namespace}
kubectl apply -f config/samples/monetdb_v1_monetdb.yaml
```

## Catalog source

```
version=1.0.0
echo $version
registry=<YOUR REGISTRY URL>
echo $registry

#build the operator
make docker-build docker-push IMG=${registry}/monetdb-operator:${version}


#build the bundle
podman build -t ${registry}/monetdb-operator-bundle:${version} -f bundle.Dockerfile .
podman push ${registry}/monetdb-operator-bundle:${version}


#build the indexs
opm index add --bundles ${registry}/monetdb-operator-bundle:${version} --tag ${registry}/monetdb-operator-catalog:${version} --pull-tool podman
podman push ${registry}/monetdb-operator-catalog:${version}

```