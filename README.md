# Kubernetes Namespace Template

A production-ready template for onboarding a new service into a Kubernetes cluster. Replace every occurrence of `project-n` with your service name, then apply the manifests in order.

## Files

| File | Kind | Purpose |
|---|---|---|
| `namespace.yaml` | Namespace | Creates the namespace with Pod Security Admission labels enforcing the `restricted` profile |
| `serviceaccount.yaml` | ServiceAccount | Dedicated identity for the workload; auto-mount disabled |
| `resourcequota.yaml` | ResourceQuota | Caps total CPU/memory requests and limits across all pods in the namespace |
| `limitrange.yaml` | LimitRange | Injects default CPU/memory requests and limits for containers that don't set their own |
| `networkpolicy.yaml` | NetworkPolicy (×3) | Default-deny all traffic; allow ingress from the Traefik Gateway controller; allow egress to kube-dns |
| `deployment.yaml` | Deployment | Rolling-update deployment with security context, probes, topology spread, and emptyDir volumes for nginx writable dirs |
| `service.yaml` | Service | ClusterIP service; only routes to ready pods |
| `hpa.yaml` | HorizontalPodAutoscaler | Scales 1–10 replicas on CPU and memory (50% target utilization) |
| `vpa.yaml` | VerticalPodAutoscaler | Runs in `Off` mode — recommendations only, no automatic resizing |
| `pdb.yaml` | PodDisruptionBudget | Keeps at least 1 pod available during voluntary disruptions |
| `gateway.yaml` | Gateway | Traefik Gateway API listeners on port 80 (HTTP) and 443 (HTTPS/TLS termination) |
| `httproute.yaml` | HTTPRoute (×2) | HTTP → HTTPS redirect (301) and HTTPS → backend routing |

## Usage

1. **Clone** this repo and `cd` into it.
2. **Replace placeholders** — swap `project-n` (service name) and `hpa` (namespace) throughout all files:
   ```sh
   find . -name '*.yaml' | xargs sed -i '' 's/project-n/your-service/g'
   ```
3. **Provision the TLS secret** referenced in `gateway.yaml` (`project-n-tls`) via cert-manager or manually before applying the Gateway.
4. **Apply in order:**
   ```sh
   kubectl apply -f namespace.yaml
   kubectl apply -f serviceaccount.yaml
   kubectl apply -f resourcequota.yaml
   kubectl apply -f limitrange.yaml
   kubectl apply -f networkpolicy.yaml
   kubectl apply -f deployment.yaml
   kubectl apply -f service.yaml
   kubectl apply -f hpa.yaml
   kubectl apply -f vpa.yaml
   kubectl apply -f pdb.yaml
   kubectl apply -f gateway.yaml
   kubectl apply -f httproute.yaml
   ```

## Requirements

- Kubernetes 1.25+ (for Pod Security Admission)
- [Traefik](https://doc.traefik.io/traefik/providers/kubernetes-gateway/) installed in the `traefik` namespace with the Gateway API provider enabled
- VPA CRDs installed if you want VPA recommendations (`vpa.yaml`)
- Metrics Server installed for HPA to function (`hpa.yaml`)
