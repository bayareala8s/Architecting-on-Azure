### Connect to on-premise networks

To connect Azure Virtual Networks to on-premises networks, you can use one of the following methods:

1. **Azure VPN Gateway:** This creates a secure IPSec/IKE VPN tunnel between your on-premises network and Azure. This is typically used for smaller workloads and when you need to establish a secure connection quickly.

2. **Azure ExpressRoute:** This creates a private, dedicated, high-throughput network connection between Azure and your on-premises network. This is typically used for larger workloads, hybrid cloud applications, and when you need a higher level of control over your network.

Here's an example of how to create a VPN Gateway with Terraform:

```hcl
resource "azurerm_virtual_network_gateway" "example" {
  name                = "example-gateway"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name

  type     = "Vpn"
  vpn_type = "RouteBased"

  ip_configuration {
    name                          = "vnetGatewayConfig"
    public_ip_address_id          = azurerm_public_ip.example.id
    private_ip_address_allocation = "Dynamic"
    subnet_id                     = azurerm_subnet.example.id
  }

  vpn_client_configuration {
    address_space = ["10.2.0.0/24"]
    vpn_client_protocols = ["SSTP"]
  }
}
```

This script creates a VPN Gateway in a specific subnet. Replace `azurerm_resource_group.example.location`, `azurerm_resource_group.example.name`, `azurerm_public_ip.example.id`, and `azurerm_subnet.example.id` with your actual resource group location, name, public IP address ID, and subnet ID, respectively.

For ExpressRoute, you would need to work with an ExpressRoute partner to establish the connection, and then link the circuit to your virtual network.

Remember to configure your on-premises network to allow the necessary traffic to and from Azure.

### Configure a point-to-site VPN connection in Azure

A Point-to-Site (P2S) VPN connection allows a secure connection from an individual client computer to Azure Virtual Networks (VNet). Here's a step-by-step guide using Azure CLI:

1. **Create a Resource Group:**

```bash
az group create --name MyResourceGroup --location "East US"
```

2. **Create a Virtual Network:**

```bash
az network vnet create \
  --resource-group MyResourceGroup \
  --name MyVnet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name MySubnet \
  --subnet-prefix 10.0.0.0/24
```

3. **Create a Public IP address:**

```bash
az network public-ip create \
  --resource-group MyResourceGroup \
  --name MyGatewayPublicIP \
  --allocation-method Dynamic
```

4. **Create a VPN Gateway:**

```bash
az network vnet-gateway create \
  --resource-group MyResourceGroup \
  --name MyVpnGateway \
  --public-ip-address MyGatewayPublicIP \
  --vnet MyVnet \
  --gateway-type Vpn \
  --vpn-type RouteBased \
  --sku VpnGw1
```

5. **Create a Root Certificate:**

You need to create a self-signed root certificate. The root certificate is then uploaded to Azure. The public key is then considered 'trusted'.

```bash
openssl req -x509 -new -nodes -key root.key -sha256 -days 1024 -out root.pem
```

6. **Upload the Root Certificate to Azure:**

```bash
az network vnet-gateway root-cert create \
  --gateway-name MyVpnGateway \
  --resource-group MyResourceGroup \
  --name MyRootCertificate \
  --public-cert-data "MyRootCertificateData"
```

Replace `"MyRootCertificateData"` with the content of your root certificate.

7. **Generate a Client Certificate:**

Each client must have a certificate installed that is part of the chain of trust to the root certificate.

```bash
openssl req -new -key client1.key -out client1.csr
```

8. **Configure the VPN Client:**

You need to install the VPN client configuration package on the client computer. The VPN client configuration files contain the necessary information for the client to connect to the VNet.

Remember to replace `MyResourceGroup`, `MyVnet`, `MySubnet`, `MyGatewayPublicIP`, `MyVpnGateway`, and `MyRootCertificate` with names that are relevant to your setup.

Visual text diagram representing an Azure Point-to-Site VPN connecting to an on-premise network:

```
Azure Point-to-Site VPN Connecting to On-Premise Network

    Azure Virtual Network                                 On-Premise Network
          |                                                      |
+---------|---------+                                  +---------|---------+
|                     |                                  |                     |
|   Azure VPN Gateway|----------------------------------|On-Premise VPN Gateway|
|                     |                                  |                     |
|    Virtual Machines|                                  |       On-Premise    |
|                     |                                  |       Resources     |
+---------------------+                                  +---------------------+
        |                                                          |
   VPN Client                                                On-Premise
                                                             Client Devices
```

In this representation:
- "Azure Virtual Network" represents the virtual network in Azure.
- "Azure VPN Gateway" is the gateway component in Azure responsible for establishing the VPN connection to the on-premise network.
- "Virtual Machines" are the resources hosted within the Azure virtual network.
- "On-Premise Network" represents the network infrastructure at the on-premise location.
- "On-Premise VPN Gateway" is the gateway component at the on-premise location responsible for establishing the VPN connection to Azure.
- "On-Premise Resources" are the resources hosted within the on-premise network.
- "VPN Client" is the client device connecting to the Azure virtual network.

This diagram illustrates the connection between the Azure virtual network and the on-premise network via a Point-to-Site VPN, allowing communication between resources in both environments.
