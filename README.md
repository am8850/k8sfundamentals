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
