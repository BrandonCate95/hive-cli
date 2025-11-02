# hive-cli

How to kuber-your-netes made easy.

Hive is a tool that simplifies the annoying parts of creating kubernetes clusters.

---

## Features

### Simple Cluster Creation

1. Authenticate to cloud provider.
2. Create cluster
    ```sh
    hive create cluster --type test --name my-cluster --provider aws --region us-east1
    ```

### Unified Cluster Connections

Easy to authenticate and connect to clusters. Can save and share configuration files as well.

```sh
hive create connection --provder aws --region us-east1 --name gke-cluster
```

***Author's Note: This might be a worthwhile tool on its own.***

### True Multi Cloud

Yes, this time for sure 🤞. AWS, Azure and GCP supported.

What's that? There are other cloud providers? Fake news bro.

---

## Installation

### Windows (Chocolately)

```sh
choco install hive
```

### MacOS and Linux

```sh
brew install hive
```

---

## Cloud Provider

### AWS

reference docs folder based on needs

#### Authenticate

Provide cli command and iam roles needed as well as any env vars

### Azure

reference docs folder based on needs

#### Authenticate

Provide cli command and iam roles needed as well as any env vars

### GCP

reference docs folder based on needs

#### Authenticate

Provide cli command and iam roles needed as well as any env vars

---

## GitOps

For a more gitops distrubuted approach, yaml files can be used.

### Test Cluster

A simple opinionated cluster which should be used mainly for testing.

```yaml
# test_cluster.yaml
---
apiVersion: hive.io/v1alpha1
kind: TestCluster
metadata:
  name: gke-cluster
spec:
  provider: aws # (azure, gcp)
  version: 1.31 # or default latest
  region: us-east1 # provider specific
```

```sh
hive create cluster -f test_cluster.yaml
```

### Cluster

Basic configuration for custom cluster resource

```yaml
# cluster.yaml
apiVersion: hive.io/v1alpha1
kind: Cluster
metadata:
  name: gke-cluster
spec:
  provider: aws # (azure, gcp)
  version: 1.31
  region: us-east1 # provider specific
  vpc: "gke-vpc"
  # or
  # vpcRef: for externally created vpc
  control_plane:
    node_type: <machine_type> # (for provider)
    autoscale:
      min: 1
      max: 3
  cni: cilium # (flannel, calico)
  cluster_endpoint_public_access: true
```

```sh
hive create cluster -f cluster.yaml
```

### Cluster VPC

Simple VPC configuration resource.

***Not recommended for production use, yet. Create your own and reference in the Cluster `vpcRef` field***

```yaml
# cluster_vpc.yaml
apiVersion: hive.io/v1alpha1
kind: ClusterVPC
metadata:
  name: gke-vpc
spec:
  provider: aws # (azure, gcp)
  region: us-east1 # provider specific
  network: "netork-name"
  cidr: "network-range"
  subnets:
    - "subnet-1-range"
    - "subnet-2-range"
  firewall:
    - ruleName: "firewall-1"
      port: 22
      allow: false
```

```sh
hive create vpc -f cluster_vpc.yaml
```

### Cluster Connection

Config to connect to a cluster via cloud provider authentication.

```yaml
# cluster_connection.yaml
apiVersion: hive.io/v1alpha1
kind: ClusterConnection
metadata:
  name: gke-cluster
spec:
  provider: aws # (azure, gcp)
  region: us-east1
  clusterRef:
    name: gke-cluster
```

```sh
hive create connection -f cluster_connection.yaml
```

```sh
kubectl cluster-info
```

### Cluster Hooks

examples