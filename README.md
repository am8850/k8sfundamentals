# k8s Fundamentals

## Networking

### Pods

- Can have one ore more containers
- Share the same network stack - behaves like a VM
- Containers in the same Pod can communicate with each other over localhost
- Every Pod gets an IP address

#### Communication withing the same Node
- Pods are attached to a Virtual Host bridge 
  - The bridge operates at layer 2 
  - Routes network packages to connected data segments by resolving IP addressed by their corresponding MAC address
  - This allows any cointainer on the node to connect to any of the container in other container in the same node

#### Communication outside to other Nodes


- K8s does not provide an inter node networking implementation
- Has a flat networking model
- Requires athird party network plugins to provde by the following rules:
  - Third party plugin must abide by Container Networking Interface (CNI)
    - CNI describes what a plugin must provide, but does not prescribe how to achieve it
    - Typically with layer 3 routing or overlay networks
  - All containers can communicate with all other container without Network Address Translation (NAT)
  - All nodes can communicate with all containers (and vice-versa) without NAT
  - The IP address that a container sees itself as, is the same as the IP address that others see it as

> Main rule: Pod on one Node can communicate with Pods on other nodes without having to deal with any network configuration

### Services

  - Ability to deploy a replicated set of identical components to improve resiliancy and to be able to scale up/down on demand
  - Multiple replicas of identical Pods running on different nodes of managed by a deployment object
  - Each Pod will have its own IP for commuication
  - A Service is an abstraction that associates the Pods to the entity (the service)
  - The service gets its own virtual IP address and Port
  - Clients can then communicate to the Service IP which acts as a proxy to communicate to the individual Pods
  - Services is can define a Service type of: ClusterIP, LoadBalancer, NodePort
  - Service will connect to nodes assoicated by their Labels.
  - When a Service is create it also creates an EndPoints object which contain the IPs, Pods and Ports of the associated Pods 
  - Services can be found using environment variables, but it is better to rely on an in-cluster DNS for looking up services
  - Kube-proxy is the cluster component that enables traffic routing. It load balances traffic between pods using iptables or IP Virtual Service (IPVS)
  - Kube-proxy is a controller and it watches for state change and re-defines the proxy configuration accordingly
  - By default, services are insolated from clients external to the cluster
  
> Result: Traffic comming from a client to the cluster exposed by the service will be routed to one of the pods associuated with the service

#### Service Types

- ClusterIP: Service exposed to internal cluster only
- LoadBalancer: Service exposed externally with either a public IP or private IP (in Azure with annotations)
- NodePort: export the service on a specific port

#### Service Limitations

- Manual configuration when using NodePort type
- Potential latency due to network hops introduce by kube-proxy
- LoadBalance services can become expensive
- Service API cannot handle advance ingress patterns (host based and path based routing for example)

### Ingress API (Layer 7 Routing)

- An API object that manages routing of external HTTP/S traffic to services running in a cluster
- Kubernetes the ingress API, but still needs a third party controller to achieve it
  - Part of the functionaluty is to act as a reverse proxy
  - Infra providers can create ingress controllers optimisied for their environments
  
#### Host/path based routing

- A single ingress controller having one IP will route traffic to a service by extracting the host header and path information which is associated to a backend service served by a replica set.
- It is possible to combine rules that allow virtual hosts and paths.
  
#### Characteristics of Ingress API

- Defines traffic routes between external clients and services
- Allows encrypted communication using Transport Layer Security (TLS)
- Load balances client traffic across a service's endpoints (pods)
- Introduced in v1.1 and will likely keep evolving

#### Sample Ingress Controllers

Nginx
Traefik
Contour


