---
title: "Mutate termination Grace Periods Seconds"
category: Sample
version: 
subject: Pod
policyType: "mutate"
description: >
    Pods with large terminationGracePeriodSeconds (tGPS) might prevent cluster nodes from getting drained, ultimately making the whole cluster unstable. This policy mutates all incoming Pods to set their tGPS under 50s. If the user creates a pod without specifying tGPS, then the Kubernetes default of 30s is maintained.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//other/mutate-large-termination-gps.yaml" target="-blank">/other/mutate-large-termination-gps.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: mutate-termination-grace-period-seconds
  annotations:
    policies.kyverno.io/title: Mutate termination Grace Periods Seconds
    policies.kyverno.io/category: Sample
    policies.kyverno.io/severity: medium
    policies.kyverno.io/subject: Pod
    policies.kyverno.io/description: >-
      Pods with large terminationGracePeriodSeconds (tGPS) might prevent cluster nodes
      from getting drained, ultimately making the whole cluster unstable. This policy
      mutates all incoming Pods to set their tGPS under 50s. If the user creates a pod
      without specifying tGPS, then the Kubernetes default of 30s is maintained.
spec:
  background: false
  rules:
    - name: mutate-termination-grace-period-seconds
      match:
        resources:
          kinds:
            - Pod
      preconditions:
        all:
        - key: "{{request.object.spec.terminationGracePeriodSeconds}}"
          operator: GreaterThan
          value: 50   # maximum tGPS allowed by cluster admin
      mutate:
        patchStrategicMerge:
          spec:
            terminationGracePeriodSeconds: 50

```
