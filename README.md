# SonarQube Deployment on Minikube

This project provides a setup for deploying SonarQube Community Edition on Minikube using Kubernetes and Docker Compose.

# Disclaimer

I do not guarantee that this deployment is production ready. Actually, the deployment manifest is intentionally missing resource limits.

This is a setup suitable for testing SonarQube platform and start familiarizing with static code analizers.

## Prerequisites

- [Minikube](https://minikube.sigs.k8s.io/docs/start/)
- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/install/)

## Project Structure

```
docker-compose.yaml
postgresql.yaml
sonarqube.yaml
split/
    deployment.yaml
    ingress.yaml
    namespace.yaml
    service.yaml
    volume-claims.yaml
    volumes.yaml


```

## Setup Instructions

### Step 1: Start Minikube

Start your Minikube cluster:

```sh
minikube start
```
### Step 2: Apply postgresql manifest
The provided `postgresql.yaml` creates a PostgreSQL database in a confined namespace. Once the database is up, connect to it and create a `sonar` database. 

### Step 3: Apply Kubernetes Manifests

Apply the single manifest `sonarqube.yaml` or use the manifests located in the `split` directory:

```sh
kubectl apply -f namespace.yaml
kubectl apply -f volumes.yaml
kubectl apply -f volume-claims.yaml
kubectl apply -f service.yaml
kubectl apply -f deployment.yaml
kubectl apply -f ingress.yaml

```
### Step 4: Access SonarQube

If you have installed `ingress-dns` as per [instructions](https://minikube.sigs.k8s.io/docs/handbook/addons/ingress-dns), SonarQube is available at `http://sonarqube.test`' .

In case you do not want to use `ingress-dns` minikube addon, you can still access SonarQube by querying minikube active services.
If you have installed minikube `ingress` addon (strongly advised) try to issue 
```shell
minikube service list
```
You should end up with something like : 

```
|----------------------|------------------------------------|---------------------|---------------------------|
|      NAMESPACE       |                NAME                |     TARGET PORT     |            URL            |
|----------------------|------------------------------------|---------------------|---------------------------|
| default              | hello-world-app                    | http/80             | http://192.168.49.2:32207 |
| default              | kubernetes                         | No node port        |                           |
| ingress-nginx        | ingress-nginx-controller           | http/80             | http://192.168.49.2:31680 |
|                      |                                    | https/443           | http://192.168.49.2:30795 |
| ingress-nginx        | ingress-nginx-controller-admission | No node port        |                           |
| kube-system          | hello-world-app                    | No node port        |                           |
| kube-system          | kube-dns                           | No node port        |                           |
| kube-system          | metrics-server                     | No node port        |                           |
| kubernetes-dashboard | kubernetes-dashboard               | No node port        |                           |
| postgresql-db        | postgresql-db                      |                5432 | http://192.168.49.2:30042 |
| sonarqube            | sonarqube-service                  | sonar-internal/9000 | http://192.168.49.2:30090 |
|----------------------|------------------------------------|---------------------|---------------------------|

```
Please note: the ip in the `URL` column may change.
If you find the service `sonarqube-service`, you only need to open the corresponding `URL` and you're set.

## Configuration Details

### Kubernetes Manifests


The Kubernetes manifests in the `split` directory are there for ease of inspection and define the necessary resources for deploying SonarQube:

- `namespace.yaml`: Defines the `sonarqube` namespace.
- `volumes.yaml`: Defines the PersistentVolumes for SonarQube data, extensions, and logs.
- `volume-claims.yaml`: Defines the PersistentVolumeClaims for the volumes.
- `service.yaml`: Defines the SonarQube service.
- `deployment.yaml`: Defines the SonarQube deployment, including an init container for setting volume permissions.
- `ingress.yaml`: Defines the Ingress resource for accessing SonarQube.


## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

## Trademarks
- PostgreSQL is a registered trademark of the PostgreSQL Community Association of Canada.
- SonarQube is a registered trademark of SonarSource SA.
- Docker and Docker Compose are trademarks or registered trademarks of Docker, Inc.
- Kubernetes and Minikube are trademarks or registered trademarks of The Linux Foundation.
- Kubectl is a trademark of The Linux Foundation.