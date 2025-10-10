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
    │   ├── kustomization.yaml
    │   └── default-kueue.yaml
    └── operators/
        ├── kustomization.yaml
        ├── README-labels.md
        ├── cert-manager/
        │   ├── kustomization.yaml
        │   ├── namespace.yaml
        │   ├── subscription.yaml
        │   └── operatorgroup.yaml
        ├── cluster-observability-operator/
        │   ├── kustomization.yaml
        │   ├── namespace.yaml
        │   ├── subscription.yaml
        │   └── operatorgroup.yaml
        ├── jobset/
        │   └── kustomization.yaml
        ├── kueue-operator/
        │   ├── kustomization.yaml
        │   ├── namespace.yaml
        │   ├── subscription.yaml
        │   └── operatorgroup.yaml
        ├── leader-worker-set/
        │   ├── kustomization.yaml
        │   ├── namespace.yaml
        │   ├── subscription.yaml
        │   └── operatorgroup.yaml
        ├── opentelemetry-product/
        │   ├── kustomization.yaml
        │   ├── namespace.yaml
        │   ├── subscription.yaml
        │   └── operatorgroup.yaml
        └── tempo-product/
            ├── kustomization.yaml
            ├── namespace.yaml
            ├── subscription.yaml
            └── operatorgroup.yaml
```

## Directory Descriptions

### `dependencies/`
Contains all infrastructure dependencies required for OpenShift AI.

#### `dependencies/cluster-config/`
Cluster-level configurations:
- **default-kueue.yaml**: Default Kueue configuration for cluster-wide job queuing

#### `dependencies/operators/`
OpenShift operators managed via OLM (Operator Lifecycle Manager) with label-based categorization:

**Monitoring Operators** (`monitoring=true`):
- **cluster-observability-operator**: Cluster observability and monitoring
- **tempo-product**: Distributed tracing backend
- **opentelemetry-product**: OpenTelemetry observability

**Serving Operators** (`serving=true`):
- **rhcl-operator**: Red Hat Container Library (also auth)
- **leader-worker-set**: Leader-worker set management (also distributed workload)
- **cert-manager**: Certificate management (also distributed workload)

**Distributed Workload Operators** (`distributedworkload=true`):
- **kueue-operator**: Job queuing and resource management
- **leader-worker-set**: Leader-worker set management (also serving)
- **cert-manager**: Certificate management (also serving)

See `dependencies/operators/README-labels.md` for detailed usage instructions.

## Usage

This repository contains operator configurations that can be deployed using standard Kubernetes tools:

```bash
# Apply all operators
kubectl apply -k dependencies/operators/

# Apply cluster configurations
kubectl apply -k dependencies/cluster-config/

# Apply operators by category using labels
kustomize build dependencies/operators/ | kubectl apply -f - -l monitoring=true
kustomize build dependencies/operators/ | kubectl apply -f - -l serving=true
kustomize build dependencies/operators/ | kubectl apply -f - -l distributedworkload=true
```

See `dependencies/operators/README-labels.md` for detailed label-based deployment instructions.
