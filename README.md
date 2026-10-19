# Day 16: Kubernetes Deployments & Rollouts (Kubeadm on AWS EC2)

## Objective
Practice safe application releases, revision history tracking, failure isolation via cluster events, and rolling back unhealthy deployments on a self-managed Kubernetes cluster built with Kubeadm on AWS EC2.

## Architecture & Key Concepts
- **Rolling Update:** Gradually replaces old application Pods with new ones derived from an updated Pod template, ensuring smooth transitions and controlled availability.
- **ReplicaSet Revisions:** Deployments manage separate ReplicaSets for each template version, maintaining an auditable revision history.
- **maxSurge:** Specifies the maximum number of extra Pods allowed above the desired replica count during an update process (default 25%).
- **maxUnavailable:** Specifies the maximum number of desired Pods allowed to be unavailable during an update process (default 25%).
- **Change-Cause Annotation (`kubernetes.io/change-cause`):** Attaches a descriptive note to the release to make revision histories transparent.
- **Rollback (`kubectl rollout undo`):** Reverts the deployment back to a previously saved stable revision in case of bad deployments or runtime failures.

## Cluster Setup & Baseline Verification
- Verified active cluster context and running workloads using `kubectl get deployments`, `kubectl get pods`, and `kubectl get rs`.
- Inspected active rolling update parameters (`maxSurge: 25%`, `maxUnavailable: 25%`) via `kubectl describe deployment`.

## Controlled Release & Revision History
- Applied release change-cause annotation (`Day 16: update Nginx for rollout practice`).
- Updated the deployment image to `nginx:1.28` using `kubectl set image`.
- Verified successful rollout completion and tracked revision history using `kubectl rollout history`.

## Failure Induction & Diagnostics
- Recorded the known-good image and active revision.
- Deliberately triggered a failed rollout using an invalid image tag (`nginx:1.99-broke`).
- Inspected pod state (`ImagePullBackOff`) using `kubectl describe pod` and tracked scheduling failure events.
- Observed that existing healthy pods continued running and serving traffic uninterrupted throughout the failure.

## Rollback Recovery
- Executed `kubectl rollout undo deployment/nginx-deployment` to revert the deployment back to the stable revision.
- Verified restoration of healthy running pods and clean rollout status.

## Bonus Challenge (Custom Rolling Strategy)
- Configured a custom rolling strategy manifest (`nginx-strategy-test.yaml`) with `maxSurge: 1` and `maxUnavailable: 0`.
- Verified that all original pods remained active during updates until replacement pods were fully running, guaranteeing 100% availability.

## Repository Structure
```text
day16-rollouts/
├── README.md
├── nginx-strategy-test.yaml
└── evidence/
    ├── rollout-success.png
    ├── rollout-history.png
    ├── failed-rollout-events.png
    └── rollback-verified.png
```

## Cleanup

kubectl delete deployment nginx-deployment 

kubectl delete deployment nginx-strategy-test


