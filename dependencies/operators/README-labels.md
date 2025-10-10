# Applying Operators by Labels

You can apply operators selectively using labels with the following commands:

## Apply only monitoring operators:
```bash
kustomize build . | oc apply -f - -l monitoring=true
```

## Apply only auth operators:
```bash
kustomize build . | oc apply -f - -l auth=true
```

## Apply only serving operators:
```bash
kustomize build . | oc apply -f - -l serving=true
```

## Apply only distributed workload operators:
```bash
kustomize build . | oc apply -f - -l distributedworkload=true
```

## Apply only Openshift AI v2 operators:
```bash
kustomize build . | oc apply -f - -l v2=true
```

## Apply only Openshift AI v3 operators:
```bash
kustomize build . | oc apply -f - -l v3=true
```

## Apply all operators:
```bash
kustomize build . | oc apply -f -
```

## Label Categories

### Monitoring Operators (monitoring=true):
- cluster-observability-operator
- tempo-product  
- opentelemetry-product

### Auth Operators (auth=true):
- rhcl-operator
- authorino-operator

### Serving Operators (serving=true):
- rhcl-operator
- leader-worker-set

### Distributed Workload Operators (distributedworkload=true):
- kueue-operator
- leader-worker-set

## How it works

Each operator's kustomization.yaml now includes metadata labels:
```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
metadata:
  labels:
    monitoring: "true"
resources:
  - subscription.yaml
```

When you run `kustomize build`, these labels are applied to all generated resources, allowing you to filter them with `-l monitoring=true` or `-l serving=true`.
