# OpenShift AI GitOps

This repository follows GitOps principles for managing OpenShift AI infrastructure and configurations.

## Structure

```
openshift-ai-gitops/
├── README.md
├── base/
│   └── README.md
├── components/
│   └── README.md
├── services/
│   └── README.md
└── dependencies/
    ├── kustomization.yaml
    ├── cluster-config/
    │   └── kustomization.yaml
    └── operators/
        ├── kustomization.yaml
        ├── README-labels.md
        ├── authorino-operator/
        │   └── kustomization.yaml
        ├── cluster-observability-operator/
        │   ├── kustomization.yaml
        │   ├── subscription.yaml
        │   └── operatorgroup.yaml
        ├── jobset/
        │   └── kustomization.yaml
        ├── kueue-operator/
        │   ├── kustomization.yaml
        │   ├── subscription.yaml
        │   └── operatorgroup.yaml
        ├── leader-worker-set/
        │   ├── kustomization.yaml
        │   ├── subscription.yaml
        │   └── operatorgroup.yaml
        ├── opentelemetry-product/
        │   ├── kustomization.yaml
        │   ├── subscription.yaml
        │   └── operatorgroup.yaml
        ├── rhcl-operator/
        │   ├── kustomization.yaml
        │   ├── subscription.yaml
        │   └── operatorgroup.yaml
        ├── serverless-operator/
        │   └── kustomization.yaml
        ├── servicemesh-operator/
        │   └── kustomization.yaml
        └── tempo-product/
            ├── kustomization.yaml
            ├── subscription.yaml
            └── operatorgroup.yaml
```

## Directory Descriptions

### `dependencies/`
Contains all infrastructure dependencies required for OpenShift AI.

#### `dependencies/operators/`
OpenShift operators managed via OLM (Operator Lifecycle Manager) with label-based categorization:

**Monitoring Operators** (`monitoring=true`):
- **cluster-observability-operator**: Cluster observability and monitoring
- **tempo-product**: Distributed tracing backend
- **opentelemetry-product**: OpenTelemetry observability

**Auth Operators** (`auth=true`):
- **rhcl-operator**: Red Hat Container Library authentication

**Serving Operators** (`serving=true`):
- **rhcl-operator**: Red Hat Container Library (also auth)
- **leader-worker-set**: Leader-worker set management (also distributed workload)

**Distributed Workload Operators** (`distributedworkload=true`):
- **kueue-operator**: Job queuing and resource management
- **leader-worker-set**: Leader-worker set management (also serving)

See `dependencies/operators/README-labels.md` for detailed usage instructions.

#### `dependencies/cluster-config/`
Cluster-level configurations such as:
- ConfigMaps
- Network policies
- Storage classes
- Custom resources

## Usage

This repository contains operator configurations that can be deployed using standard Kubernetes tools:

```bash
# Apply all operators
kubectl apply -k dependencies/operators/

# Apply operators by category using labels
kustomize build dependencies/operators/ | kubectl apply -f - -l monitoring=true
kustomize build dependencies/operators/ | kubectl apply -f - -l auth=true
kustomize build dependencies/operators/ | kubectl apply -f - -l serving=true
kustomize build dependencies/operators/ | kubectl apply -f - -l distributedworkload=true
```

See `dependencies/operators/README-labels.md` for detailed label-based deployment instructions.

## Adding New Operators

1. Create a new directory under `dependencies/operators/`:
```bash
mkdir -p dependencies/operators/my-operator
```

2. Add operator resources (Subscription, OperatorGroup, etc.):
```bash
cat > dependencies/operators/my-operator/kustomization.yaml <<EOF
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
  - subscription.yaml
  - operatorgroup.yaml
EOF
```

3. Update `dependencies/operators/kustomization.yaml`:
```yaml
resources:
  - leader-worker-set
  - kueue-operator
  - rhcl-operator
  - cluster-observability-operator
  - tempo-product
  - opentelemetry-product
```

4. Add appropriate labels to your operator's kustomization.yaml:
```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
metadata:
  labels:
    monitoring: "true"  # or auth, serving, distributedworkload
resources:
  - subscription.yaml
```