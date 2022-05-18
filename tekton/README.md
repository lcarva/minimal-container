# Tekton Pipelines Example

This directory holds various Tekton Pipelines resources that are meant to illustrate how to build a
container image via Tekton Pipelines.

It is purposely arranged to use different features of Tekton Pipelines (even if it does not make
sense to do so).

## Usage

It is assumed that oc is logged in to a cluster that already has Tekton Pipelines in working
condition.

```bash
oc new-project minimal-container

# Install the required resources - requires kubeadmin for installing the ClusterTask
oc -n minimal-container apply -k ./tekton

# Run the pipeline. Change the parameters accordingly. The example below relies on the
# integrated registry that is part of OpenShift.
tkn -n minimal-container pipeline start simple-build \
  --param git-repo=https://github.com/lcarva/minimal-container \
  --param git-revision=main \
  --param output-image=image-registry.openshift-image-registry.svc:5000/minimal-container/min:latest \
  --workspace name=shared,pvc,claimName="tekton-build" \
  --showlog
```

That's it!
