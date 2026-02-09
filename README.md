Kubernetes YAMLs for NGINX (dev/test/prod)

Files added in this folder:

- kubernates/namespaces.yaml — creates dev, test, prod namespaces
- kubernates/configmap.yaml — nginx configuration and sample index.html (in dev/test/prod)
- kubernates/secret.yaml — sample credentials (base64 encoded) (in dev/test/prod)
- kubernates/pvc.yaml — example PersistentVolumeClaim for dev
- kubernates/nginx-deployment-dev.yaml — Deployment for dev (uses ConfigMap, Secret, PVC, emptyDir)
- kubernates/nginx-service-dev.yaml — ClusterIP Service for dev
- kubernates/ingress-dev.yaml — Ingress for the dev service (host: dev.example.com)
- kubernates/nginx-deployment-test.yaml — Deployment for test
- kubernates/nginx-service-test.yaml — Service for test
- kubernates/nginx-deployment-prod.yaml — Deployment for prod
- kubernates/nginx-service-prod.yaml — Service for prod
- kubernates/rbac.yaml — ServiceAccounts, Roles, RoleBindings and ClusterRoleBinding example

Quick apply (recommended to run namespace apply first):

kubectl apply -f kubernates/namespaces.yaml
kubectl apply -f kubernates/configmap.yaml
kubectl apply -f kubernates/secret.yaml
kubectl apply -f kubernates/pvc.yaml
kubectl apply -f kubernates/nginx-deployment-dev.yaml
kubectl apply -f kubernates/nginx-service-dev.yaml
kubectl apply -f kubernates/ingress-dev.yaml
kubectl apply -f kubernates/nginx-deployment-test.yaml
kubectl apply -f kubernates/nginx-service-test.yaml
kubectl apply -f kubernates/nginx-deployment-prod.yaml
kubectl apply -f kubernates/nginx-service-prod.yaml
kubectl apply -f kubernates/rbac.yaml

Notes:
- The configMap, secret, and pvc in this sample are created only in the dev namespace. Copy/adjust them into test/prod as needed.
- ingress-dev.yaml assumes an ingress controller with class nginx and a DNS entry for dev.example.com.
- Adjust resource names, storage class, replicas, and security policies according to your environment before using in production.

GitHub Actions (CI/CD)

This repo includes two example workflows in `.github/workflows/`:

- `terraform.yml` — runs `terraform fmt`, `init`, `validate`, `plan` on PRs and applies on push to `main`.
- `k8s-deploy.yml` — runs on push to `main` when files under `kubernates/` change and applies manifests using `kubectl`.


Required repository secrets (AWS / EKS example):

- `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`: credentials for an IAM user or role with permissions to manage infrastructure and EKS.
- `AWS_REGION`: AWS region where your resources and EKS cluster live (e.g., `us-east-1`).
- `EKS_CLUSTER_NAME`: name of the EKS cluster to target for Kubernetes deploys.

The `terraform.yml` workflow uses the AWS credentials for Terraform provider operations. The `k8s-deploy.yml` workflow uses the same credentials to run `aws eks update-kubeconfig` and then `kubectl apply`.

Security notes:

- Keep secrets scoped to the repository or environment and avoid exposing them in logs.
- Prefer using OIDC or least-privilege roles for CI in production rather than long-lived static credentials.
- Review and test the workflows in a non-production branch before allowing `apply` on `main`.

