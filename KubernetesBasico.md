---
title: Kubernetes Basics
author: David Alberto Montaño Fetecua
---

# Agenda

- Contexto
- ¿Qué es Kubernetes?
- Arquitectura de Kubernetes
- Algunos Recursos del API
- Siguientes Pasos

# Contexto

## Objetivos

- Entender beneficios de usar Kubernetes
- Conocer su arquitectura
- Conocer recursos más relevantes
- Saber qué pasos seguir para empezar

## Procesos de DevOps

- Despliegues
- Rollbacks - Deshacer Despliegue
- Administración de Estado Distribuido
- Pruebas
- Escalabilidad
- Monitoreo
- Lógica de Autocuración
- Aprovisionamiento de Ambientes
- Seguridad
- Administración de la Configuración

## Escenarios Diferentes

![](images/container_evolution.svg)

## Muchas tecnologías

<img src="images/scenarios.svg" style="zoom:50%;" />

::: notes

- **Aplicaciones o Microservicios corriendo como Servicios del Sistema Operativo** (Windows Services, init.d, upstart, systemctl)
  - Máquinas Físicas
  - Máquinas Virtuales
- **Aplicaciones Containerizadas** (estándar OCI, docker, rkt, podman, buildah, containerd)

:::

# ¿Qué es Kubernetes?

## Kubernetes

<img src="images/originals/kubernetes.svg" style="zoom:50%;" />

- ***Plataforma de administración de aplicaciones y servicios "containerizados"***
  - *código abierto*
  - *configuración declarativa de estado y comportamiento*
  - *facilita instalación y monitoreo de aplicaciones distribuidas*

## Beneficios

- velocidad
- escalabilidad
- abstracción de infraestructura
- eficiencia

::: notes

Velocidad

Alto número de features + mantener una alta disponibilidad de los servicios

- Inmutabilidad
- Configuración Declarativa
- Sistemas de autocuración online

Escalabilidad (software y equipo)

- Desacoplamiento de Arquitecturas
  - desacoplando aplicaciones: load balancers for each
  - desacoplando equipos e infraestructura
  - desacoplando recursos de k8s: pods, namespaces, services, ingresses

- Separación de Intereses en cuanto a Consistencia y Escalabilidad
  - Desarrollador de Aplicación depende de los SLAs
  - El Ingeniero Fiabilidad del API de Orquestración de Contenedores se enfoca en entregar unos SLAs sobre el API sin preocuparse de las aplicaciones que corren encima de esta.
  - Los ingenieros de fiabilidad del Sistema Operativo se enfocan en los SLAs de los individuales sistemas operativos instalados en cada máquina.

- KaaS
  - Tectonic + Openshit
  - Rancher
  - Digital Ocean

Abstracción de Infraestructura

- Separar desarrolladores de las máquinas
- Portabilidad
  - los mismos manifiestos funcionan en cualquier cluster de K8s
  - intentar evitar servicios de la nube que lo amarren a esta

Eficiencia

- Facilita encontrar el número óptimo de nodos usado al mismo tiempo por varios equipos

:::

## Provee

- "Descubrimiento" de Servicios y Balanceo de Carga
- Interfaces de Almacenmiento
- Despliegues y Reversiones automáticas
- Asignación automatizada de trabajo a nodos
- Lógica de Auto Curación
- Administración de Configuración y Secretos

## ¿Qué cosas no hace K8s?

- No limita el tipo de aplicación
- No obtiene código fuente ni construye aplicciones
- No provee servicios de nivel de aplicación
- No obliga a usar aplicaciones de monitoreo, logging o alerta
- No obliga a usar un lenguaje específico
- No provee sistemas de configuración de máquinas, mantenimiento, administración o de auto curación.
- No es un sistema de orquestración pues no tiene centro de control

::: notes

- No limita el tipo de aplicación
- No obtiene código fuente ni construye aplicciones
- No provee servicios de nivel de aplicación
  - como buses, bases de datos, cachés
- No obliga a usar aplicaciones de monitoreo, logging o alerta
- No obliga a usar un lenguaje específico
- No provee sistemas de configuración de máquinas, mantenimiento, administración o de auto curación.
- No es un sistema de orquestración pues no tiene centro de control
  - poderozo
  - robusto
  - resiliente
  - extensible

:::

# Arquitectura de Kubernetes

## Componentes

![](images/components-of-kubernetes.png)

## Cliente de Kubernetes

### Versiones

```bash
$ kubectl version
```

- Mantenerse entre dos versiones menores de distancia
- No usar nuevas características en clusters viejos

### Estados de los componentes

```bash
$ kubectl get componentstatuses
```

- **controller manager**: controladores regulan el comportamiento del cluster
- **scheduler**: asocia diferentes pods en diferentes nodos del cluster
- **etcd**: almacenamiento de todos los objetos del API

### Nodos Worker

```bash
$ kubectl get nodes
```

- **nodos master**: donde los contenedores centrales del cluster se ejecutan
  - server API: servidor del API
  - scheduler: calendarizador
- **nodos worker**: donde los contenedores de aplicación se ejecutan

```bash
$ kubectl describe nodes node-1
```

- muestra información sobre
  - nodo
    - Os running
    - architecture
    - hostname
  - operación
    - Disk usage
    - Memory Usage
  - capacidad
    - Total Memory
    - Total CPU
    - GPU info
    - Pods
  - Pods que corren actualmente

## Componentes del Cluster

Los siguientes componentes corren en el namespace **kube-system**.

### Kubernetes Proxy

- Enruta el tráfico de red hacia servicios con balanceadores de carga en el cluster
- Presente en cada nodo del cluster
  - Usualmente corre como un **DaemonSet**

### Kubernetes DNS

- Provee nombramiento y descubrimiento de los servicios definidos en el cluster
- Dependiendo del tamaño del cluster se pueden encontrar uno o más corriendo
  - Usualmente corre como un **Deployment**
  - Usa un **Service** para balancear la carga entre las instancias del servidor de DNS
- La ip del **Service** se encuentra en el archivo `/etc/resolv.conf` de cada contenedor que se ejecuta en el cluster

### Kubernetes UI

- Corre una única replica de **Deployment** y usa un **Service** para estar disponible desde el cluster
- Puede ser accedido usando `$ kubectl proxy`
- No siempre se instala

# Algunos Recursos del API

## Recursos

### Namespaces

- Organiza objetos en el cluster (como carpetas)
  - si no se configura **kubectl** este interactúa por defecto con el namespace **defaut**

```bash
$ kubectl -n anothernamespace get pods
$ kubectl --all-namespaces get pods
```

### Contexts

- Cada contexto es un cluster de K8s diferente o un namespace de un cluster diferente

- La lista de contextos y su configuración se guarda en el archivo `$HOME/.kube/config`

- Se puede listar, crear, actualizar y borrarlos contextos de dos formas:

  - actualizando el archivo `$HOME/.kube/config` directamente
  - ejecutando kubectl

  ```bash
  $ kubectl config set-context my-context --namespace=my-namespace
  $ kubectl config use-context my-context
  ```

  - algunos comandos externos actualizan el archivo `$HOME/.kube/config`. ¡SEA CONSCIENTE DE ELLO!
    - helm
    - ranchercli
    - gcloud
    - doctl
    - minikube
    - az
    - eksctl

### Services

### Pods

### Ingress

### ReplicaSets

### Deployments

### DaemonSets

### Jobs

### ConfigMaps

### Secrets

[A Kubernetes story: Phippy goes to the zoo](https://www.youtube.com/watch?v=R9-SOzep73w)

# Siguientes Pasos

## Instalar Kubernetes

### Proveedor de Nube

#### GCP

```bash
$ gcloud config set compute/zone us-west1-a
$ gcloud container clusters create s4n-cluster
$ gcloud auth application-default login
```

#### Azure

```bash
$ az group create --name=s4n --location=westus
$ az aks create --resource-group=s4n --name=s4n-cluster
$ az aks get-credentials --resource-group=s4n --name=s4n-cluster
```

#### AWS

```bash
$ eksctl create cluster --name s4n ...
```

#### Digital Ocean

```bash
$ doctl kubernetes cluster list
$ doctl kubernetes cluster kubeconfig save s4n
$ kubectl get nodes
```

### Máquina local

#### minikube

Asegúrese de instalar un hypervisor como **virtualbox** o **kvm**.

```bash
$ minikube start
$ minikube stop
$ minikube delete
```

#### kind

Kubernetes in Docker

```bash
$ kind create cluster --wait 5min
$ export KUBECONFIG="$(kind get kubeconfig-path)"
$ kubectl cluster-info
$ kind delete cluster
```

### Máquinas on premise

#### k3s de Rancher

```bash
# On Server
$ curl -sfL https://get.k3s.io | sh -
$ sudo k3s server &
# Kubeconfig is written to /etc/rancher/k3s/k3s.yaml
$ sudo k3s kubectl get nodes

# On Nodes
$ curl -sfL https://get.k3s.io | K3S_URL=https://myserver:6443 K3S_TOKEN=XXX sh -
# On a different node run the below. NODE_TOKEN comes from 
# /var/lib/rancher/k3s/server/node-token on your server
$ sudo k3s agent --server https://myserver:6443 --token ${NODE_TOKEN}
```

#### Ansible

- [Kubernetes setup using ansible and vagrant](https://kubernetes.io/blog/2019/03/15/kubernetes-setup-using-ansible-and-vagrant/)
- [Ansible Galaxy](https://galaxy.ansible.com/geerlingguy/kubernetes)
- [Openshift](https://github.com/openshift/openshift-ansible)

## Interactuar con Objetos del API de Kubernetes

### Describir Objetos del API

```bash
$ kubectl get pods my-pod -o jsonpath --template={.status.podIP}
$ kubectl describe <resource-name> <obj-name>
```

### Crear, Actualizar y Destruir Objetos de K8s

```bash
$ kubectl apply -f obj.yaml
$ vi obj.yaml # update some resources
$ kubectl apply --dry-run -f obj.yaml # output the changes without making them
$ kubectl apply -f obj.yaml
$ kubectl edit <resource-name> <obj-name> # after saving it is automatically updated
$ kubectl delete -f obj.yaml
$ kubectl delete <resource-name> <obj-name>
```

### Etiquetando y anotando Objetos

```bash
$ kubectl label pods checkin project=airline
$ kubectl label pods checkin project=airline2 --overwrite
$ kubectl label pods checkin project- # delete the label
```

```bash
$ kubectl annotate pods checkin project=airline
$ kubectl annotate pods checkin project=airline2 --overwrite
$ kubectl annotate pods checkin project- # delete the annotation
```

### Comandos de Debugging

```bash
$ kubectl top nodes
$ kubectl logs <pod-name>
$ kubectl exec -ti exec <pod-name> -- bash
$ kubectl attach -it <pod-name>
$ kubectl cp <pod-name>:</path/to/remote/file> </path/to/local/file>
$ kubectl port-forward <pod-name> 8080:80
```

- Se puede hacer port forwarding también con servicios
  - pero las peticiones siempre se envían al mismo Pod aunque hayan varios detrás

### kubectl help

```bash
$ kubectl help
$ kubectl help | grep kubeconfig
$ kubectl help | less
```

## Role Based Access Control (RBAC)

- acceso granular al API de K8s para usuarios y **service accounts**

## K8s Storage Solutions

- Best practices
- StatefulSets
- Ceph

## Extending Kubernetes

- CRDs
- CNI
- plugins

## HELM

- Why?
- What?
- Usage

## Kubernetes as a Service

- gcp
- aws
- azure
- digital ocean
- tectonic + Open Shift
- rancher
- costs comparison

## Kubernetes Ingresses Controllers

- Nginx
- Kong
- Ambassador (API Gateway based on Envoy)
- Voyager (HaProxy)
- AWS ALB Ingress controller
- Contour (Envoy)
- Citrix
- F5
- Gloo
- HaProxy
- Istio (Envoy)
- Skipper
- Traefik

## CD Pipelines

- Jenkins X
- Spinnaker
- Tekton

## Managing Secrets and Configurations

- consul integration
- vault integration

## Cloud Native Groups

- CNCF

- CDF

# Bibliografía y Recursos

## Bibliografía

- *Kubernetes: Up and running*, 2nd edition, by Brendan Burns, Joe Beda and Kelsey Hightower (O'Reilly).

## Recursos

- [Kubernetes API Reference Documentation](https://kubernetes.io/docs/reference/)
- [The Illustrated Children's Guide to Kubernetes](https://www.youtube.com/watch?v=4ht22ReBjno)
- [A Kubernetes story: Phippy goes to the zoo](https://www.youtube.com/watch?v=R9-SOzep73w)
- [CNCF: A Kubernetes story](https://www.cncf.io/phippy-goes-to-the-zoo-book/)
- [Kubernetes.io](https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/)
