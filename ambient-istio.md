Last year in November, Istio’s Ambient Mode reached General Availability with 1.24. But do you know how it compares with the Sidecar mode and what’s better for you and how the architecture compare?


Understanding Istio SideCar vs Ambient Mode is essential to unlock the value.

In Istio's traditional sidecar architecture, each application pod runs alongside an Envoy proxy that handles all network traffic. This battle-tested approach has been around since Istio's first release in 2017.

→ The main challenge? Resource overhead. With one sidecar per pod, a cluster with 1,000 pods requires 1,000 sidecars consuming CPU and RAM. By default, Istio recommends allocating 0.1 CPU cores and 128MB of memory per sidecar.

→ Operational complexity is another pain point. Upgrading Istio requires restarting every application pod to update the sidecar version so your application would have been down while upgrading the mesh. 

Ambient mode introduces a split architecture that breaks the sidecar into two distinct components:

→ Ztunnel (Zero-Trust Tunnel): For L4 Operations that runs on each node, creating a secure mTLS overlay between different nodes via itself. 

→ Waypoint Proxy: Basically Envoy proxy deployed at the namespace level that enables advanced traffic management (l7) features when needed. Acts as a communication channel between Ztunnel when enabled with the help of Ambient Mode. 

So by breaking them you can choose your components and remove the entire overhead of sidecars. For example, just required mTLS? Use Ztunnel and not the entire stack, while using up to 73% less CPU than sidecar mode. The Benefits?

→ Less Resource Consumption and Cost Savings
→ Reduced disruption as upgrades can be selectively rolled out without affecting the entire application
→ Configure L7/L4 as needed according to requirements with a modular architecture.

Features like multi-cluster, multi-network, and VM support are still in development (track progress on GitHub issues like #54245), but my take is that Ambient Mode’s lightweight design and cost efficiency make it a game-changer for scaling clusters without breaking the bank.

```mermaid
%%{init: {'theme': 'neutral', 'themeVariables': { 'fontSize': '16px'}}}%%
graph TB
    subgraph "Sidecar Mode"
        subgraph "Node 1"
            subgraph "Pod A"
                A[App Container]
                AS[Envoy Sidecar]
                A --- AS
            end
            subgraph "Pod B"
                B[App Container]
                BS[Envoy Sidecar]
                B --- BS
            end
        end
        

        
        IC[Istiod Control Plane]
        IC --> AS & BS 
        
        %% mTLS connections between sidecars
        AS <--> BS
    end
    
    subgraph "Ambient Mode"
        subgraph "Node 1 AM"
            ZT1[ztunnel]
            subgraph "Pod A AM"
                A2[App Container]
            end
            subgraph "Pod B AM"
                B2[App Container]
            end
            ZT1 --- A2 & B2
        end
        
        subgraph "Node 2 AM"
            ZT2[ztunnel]
            subgraph "Pod C AM"
                C2[App Container]
            end
            subgraph "Pod D AM"
                D2[App Container]
            end
            ZT2 --- C2 & D2
        end
        
        subgraph "Namespace X"
            WP[Waypoint Proxy]
        end
        
        IC2[Istiod Control Plane]
        IC2 --> ZT1 & ZT2 & WP
        
        %% mTLS connections between ztunnels and Waypoint
        ZT1 <===> WP
        ZT2 <===> WP
    end

    classDef default fill:#f9f9f9,stroke:#333,stroke-width:1px;
    classDef sidecar fill:#d1e7dd,stroke:#333,stroke-width:1px;
    classDef ztunnel fill:#cfe2ff,stroke:#333,stroke-width:1px;
    classDef waypoint fill:#e2d9f3,stroke:#333,stroke-width:1px;
    classDef controlplane fill:#fff3cd,stroke:#333,stroke-width:1px;
    classDef mtls stroke:#ff6b6b,stroke-width:2px,stroke-dasharray: 5 5;
    
    class AS,BS,CS,DS sidecar;
    class ZT1,ZT2 ztunnel;
    class WP waypoint;
    class IC,IC2 controlplane;

```
