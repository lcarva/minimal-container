# Tekton Pipelines

This directory holds various Tekton Pipelines resources that are meant to illustrate how to build a
container image via Tekton Pipelines.

It is purposely arranged to use different features of Tekton Pipelines (even if it does not make
sense to do so).

## Usage

It is assumed that oc is logged in to a cluster that already has Tekton Pipelines in working
condition.

```bash
oc new-project default

# Install the required resources - requires kubeadmin for installing the ClusterTask
oc -n default apply -k ./tekton

# Run the pipeline. Change the parameters accordingly. The example below relies on the
# integrated registry that is part of OpenShift.
tkn -n default pipeline start simple-build \
  --param git-repo=https://github.com/lcarva/minimal-container \
  --param git-revision=main \
  --param output-image=image-registry.openshift-image-registry.svc:5000/default/min:latest \
  --workspace name=shared,pvc,claimName="tekton-build" \
  --showlog
```

It is also possible to create a Tekton Pipeline via an Event Listener (require OpenShift 4):

```
# Set up an event listener.
oc -n default apply -f ./tekton/build-event-listener.yaml

# Grab the event listener URL.
EL_URL="$(oc get route build-event-listener -o json | jq '.spec.host' -r)"

# Then simply make a POST request with your favorite client, e.g. curl.
# See tekton/examples/event-listener-payload.json for a sample payload.
curl -X POST -H "Content-Type: application/json" \
    -d @tekton/examples/event-listener-payload.json \
    "http://${EL_URL}"
```

That's it!
