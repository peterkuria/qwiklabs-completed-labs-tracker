# AK8S-15 Implementing Role-Based Access Control With Kubernetes Engine

## Task 1: Create namespaces for users to access cluster resources

### create environment variables for the GCP zone and cluster name that will be used to create the cluster for this lab.

```
export my_zone=us-central1-a
export my_cluster=standard-cluster-1

source <(kubectl completion bash)
```