### Azure Load Balancer

An Azure Load Balancer is a Layer 4 (TCP, UDP) load balancer that distributes incoming network traffic across multiple servers. Here's a textual representation:

```
    [ Internet ]
        |
  [ Azure Load Balancer ]
   /      |      |      \
[VM1]  [VM2]  [VM3]  [VM4]
```

In this diagram:

- "Internet" represents incoming traffic from users or clients.
- "Azure Load Balancer" sits between the Internet and the virtual machines (VMs). It distributes incoming traffic across the VM instances.
- "VM1," "VM2," "VM3," and "VM4" are virtual machines that are part of a pool. The load balancer directs traffic to these VMs based on configured rules (such as round-robin or least connections).

Keep in mind that Azure Load Balancer can also be configured for inbound NAT rules, outbound connections, and can be used for both public and private IP addresses.


```
[Internet]
    |
    | (requests)
    |
[Frontend IP Configuration]
    |
    | (routes based on Load Balancing Rules and Inbound NAT Rules)
    |
[Load Balancer]
    |
    | (distributes traffic)
    |
[Backend Pool]
    |
    | (responses)
    |
[Internet]
```

1. **Internet**: This is where the client requests originate from.
2. **Frontend IP Configuration**: This is the public IP address that is accessible from the internet. All client requests come to this IP.
3. **Load Balancer**: This is the main component that routes incoming traffic based on the Load Balancing Rules and Inbound NAT Rules.
4. **Backend Pool**: This consists of the IP addresses of the virtual machines or instances where your application is running. The Load Balancer distributes incoming traffic among these backend instances.
5. **Internet**: The responses from the backend pool are sent back to the client over the internet.

Please note that this is a simplified diagram and does not include components like Health Probes, which are used to determine the health of the instances in the backend pool.
