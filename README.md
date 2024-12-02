# week 11 inclass activity

from docker to CI/CD

We are going to be using python, Dockerfile, kubernetes services and deployments, helm, and finally CI/CD with ArgoCD.

## docker stuff

build the image

run the image

```bash
docker run -p 5001:5000 flask-app:latest
```

## k8s stuff

create a pull secret

```bash
kubectl create secret docker-registry acr-pull-secret \
    --docker-server=week9wiit7501.azurecr.io \
    --docker-username=week9wiit7501 \
    --docker-password=<DERP> \
    --docker-email=ewagner14@cscc.edu
```

Could even add the secret creation to your github CI and fetch from a Key Vault

```yaml
- name: Fetch ACR Password from Azure Key Vault
  uses: azure/secrets-store-csi-driver-provider-azure@v1
  with:
    keyvault-name: ${{ secrets.AZURE_KEYVAULT_NAME }}
    secret-name: acr-password
    tenant-id: ${{ secrets.AZURE_TENANT_ID }}

- name: Create Kubernetes Pull Secret
  run: |
    kubectl create secret docker-registry acr-pull-secret \
      --docker-server=${{ env.ACR_HOST }} \
      --docker-username=${{ secrets.DOCKER_USER }} \
      --docker-password=$ACR_PASSWORD \
      --docker-email=user@example.com
```

## helm stuff

delete the templates dir

add to the values file

replace all the services labels with {{ .Release.Name }}

## argocd stuff

```bash
helm repo add argo https://argoproj.github.io/argo-helm
helm repo update
kubectl create namespace argocd
helm install argocd argo/argo-cd --namespace argocd


```


youll get instructions to get the admin user password

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

in another terminal, kubectl port-forward to the argocd service

```bash
kubectl port-forward service/argocd-server -n argocd 8080:443
```

lets also create a new pull secret and new namespace for our app

```bash
kubectl create ns test
kubectl create secret docker-registry acr-pull-secret-argocd -n test \
    --docker-server=week9wiit7501.azurecr.io \
    --docker-username=week9wiit7501 \
    --docker-password=<DERP> \
    --docker-email=ewagner14@cscc.edu

```

Lets add to the helm chart and update the values pull secret

