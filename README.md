# ARGOCD

This is a repository to build a k8s based, argocd-orchestrated software thingamabob.

## Installation

0. Have a k8s cluster with traefik as ingress controller
1. Install argocd: `helm install --repo https://argoproj.github.io/argo-helm argocd argo-cd --namespace=argocd --create-namespace` 
2. Follow the instruction from the chart (wait until the pods are up):
	1. start a port forwarding: `kubectl port-forward service/argocd-server -n argocd 8080:443`
	2. get the initial password: `kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d`
3. Login with the argo CLI: `argocd login localhost:8080`
	- optional: change password: `argocd account update-password`
4. Register the application package: `argocd app create apps --dest-namespace argocd --dest-server https://kubernetes.default.svc --repo https://github.com/chtime/argocd-apps.git --path apps`
5. Sync the root application: `argocd app sync apps`
6. Sync argocd to configure ingress: `argocd app sync argocd`
7. Login again under the new ingress: `argocd login argocd.k3s.internal`
8. Initialize the other applications


### WIP


```
# syncing traefik CDR first, so that the other resources can be interpreted
argocd app sync apps --local apps --resource argoproj.io:Application:traefik

# currently, for some reason metallb is not initialized properly
helm repo add metallb https://metallb.github.io/metallb
helm install metallb metallb/metallb -f metallb/values.yaml
```
