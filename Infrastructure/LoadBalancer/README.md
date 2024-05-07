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

### Configure health probes for an Azure Load Balancer

To configure health probes for an Azure Load Balancer, you can use the Azure portal, Azure CLI, or Azure PowerShell. Here's how you can do it using Azure CLI:

1. First, create a health probe. Replace `myResourceGroup`, `myLoadBalancer`, and `myHealthProbe` with your actual resource group name, load balancer name, and health probe name:

```shell
az network lb probe create --resource-group myResourceGroup --lb-name myLoadBalancer --name myHealthProbe --protocol tcp --port 80 --interval 15 --threshold 4
```

This command creates a TCP health probe on port 80. The health probe will attempt a health check every 15 seconds, and if it fails 4 times in a row, the instance will be considered unhealthy.

2. Next, create a load balancer rule that uses the health probe. Replace `myLbRule`, `myFrontendIP`, `myBackendPool`, and `myHealthProbe` with your actual load balancer rule name, frontend IP configuration name, backend pool name, and health probe name:

```shell
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer --name myLbRule --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontendIP --backend-pool-name myBackendPool --probe-name myHealthProbe
```

This command creates a load balancer rule that routes incoming TCP traffic on frontend port 80 to backend port 80, using the specified health probe for health checks.

Remember to replace the placeholders with your actual values. Also, ensure that you have the Azure CLI installed and that you're logged in to your Azure account.

### Configure session affinity for an Azure Load Balancer

Session affinity, also known as "sticky sessions", can be configured on an Azure Load Balancer by setting the session persistence parameter on a load balancing rule.

Here's how you can do it using Azure CLI:

1. First, create a load balancer rule with session persistence. Replace `myResourceGroup`, `myLoadBalancer`, `myLbRule`, `myFrontendIP`, `myBackendPool`, and `myHealthProbe` with your actual resource group name, load balancer name, load balancer rule name, frontend IP configuration name, backend pool name, and health probe name:

```shell
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer --name myLbRule --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontendIP --backend-pool-name myBackendPool --probe-name myHealthProbe --load-distribution SourceIP
```

The `--load-distribution SourceIP` option sets the session persistence to be based on the source IP address.

2. If you want to set the session persistence to be based on the source IP and source port, you can use the `SourceIPProtocol` option:

```shell
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer --name myLbRule --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontendIP --backend-pool-name myBackendPool --probe-name myHealthProbe --load-distribution SourceIPProtocol
```

Remember to replace the placeholders with your actual values. Also, ensure that you have the Azure CLI installed and that you're logged in to your Azure account.


### Configure SSL/TLS termination for an Azure Load Balancer


