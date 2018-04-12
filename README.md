# Helm Subchart Environment specific values.


## Problem

You have a chart which has subcharts. Also you maintain env specific values in something like values_dev.yaml and values_qa.yaml. 

The subchart also has similar environment specific values but those are not respected by Helm


## Demo

This chart has a configmap and child chart also has a configmap. When we run follwing, the values from each chart's values.yaml are picked up (Expected):

```
$ helm install --dry-run --debug --name vbtest --namespace vbtest .
<truncated>
---
# Source: parent/charts/child/templates/configmap.yaml
apiVersion: v1
kind: ConfigMap
data:
  child_key: childValue
---
# Source: parent/templates/configmap.yaml
apiVersion: v1
kind: ConfigMap
data:
  parent_key: parentValue

```

But if we want to use the values_dev.yaml across all charts, that does not work:

```
$ helm install --dry-run --debug --name vbtest --namespace vbtest -f values_dev.yaml  .
---
# Source: parent/charts/child/templates/configmap.yaml
apiVersion: v1
kind: ConfigMap
data:
  child_key: childValue
---
# Source: parent/templates/configmap.yaml
apiVersion: v1
kind: ConfigMap
data:
  parent_key: parentDevValue
```


## Solution/Workaround

One way is to import the child chart's dev environment content into the parent chart's dev values file:

```
parentKey: parentDevValue
child:
  childKey: childDevParentValue
```

I don't find this very clean but this is closest I have got to.

## References

- https://github.com/kubernetes/helm/issues/2620 
- https://github.com/kubernetes/helm/issues/3220