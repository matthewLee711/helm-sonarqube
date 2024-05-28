# Files

## Postgres
- PVC
    - This is temporary, and for setting up an internal DB in Kubernetes until flex postgres is setup
- ConfigMap
    - For configuring the postgres DB

## Sonarqube


## Setup guide
> helm repo add bitnami https://raw.githubusercontent.com/bitnami/charts/archive-full-index/bitnami
> helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
> helm dependency build
> kubectl create namespace sonarqube
> helm install -n sonarqube -f ./values.yaml sonarqube .
> helm upgrade -n sonarqube -f ./values.yaml sonarqube .
    - helm upgrade -n test -f ./values.yaml sonarqube .
> helm uninstall sonarqube -n sonarqube


## General run through
1. Create cluster with terraform
    > terraform plan
    > terraform apply
    > az aks get-credentials --resource-group aks_rg_dev --name aks_cluster_dev
2. Create namespaces: sonarqube
    - Move this to terraform later
    > kubectl create namespace sonarqube
3. Create a certificate (OPTIONAL)
    > kubectl create secret tls my-fancy-certs -key ssl.key -cert ssl.crt -n sonarqube
    - Not necessary, but need to set in ingress: nginx.ingress.kubernetes.io/ssl-redirect: "false"
4. Apply the postgres helm chart (TEMPORARY)
    1. Install the repo dependency first
        - Navigate to the postgres folder
        > helm repo add bitnami https://raw.githubusercontent.com/bitnami/charts/archive-full-index/bitnami
        > helm dependency build
    2. Install helm chart with release name "postgres" in sonarqube namespace
        > helm install -n sonarqube -f ./values.yaml postgres .
        > helm upgrade -n sonarqube -f ./values.yaml postgres .
5. Apply the sonarqube helm chart
    - Navigate to the sonarqube folder
    > helm dependency build
    > helm install -n sonarqube -f ./values.yaml sonarqube .
        - helm install [RELEASE_NAME] [CHART_NAME] [FLAGS]
    > helm upgrade -n sonarqube -f ./values.yaml sonarqube .

## Debug commands
> helm list -n sonarqube
- Gets a list of releases under the sonarqube namespace
> kubectl get logs
> kubectl describe pod
> kubectl get pods POD_NAME_HERE -o jsonpath='{.spec.containers[*].name}'
    > kubectl get pods -n sonarqube sonarqube-d47ff6947-phdp7 -o jsonpath='{.spec.containers[*].name}'
> kubectl port-forward sonarqube-b5b779cb9-p465l 8080:9000 -n sonarqube

kubectl logs ingress-nginx-controller-6858749594-m69lp -n ingress-nginx
Service "sonarqube/sonar-svc" does not have any active Endpoint.
