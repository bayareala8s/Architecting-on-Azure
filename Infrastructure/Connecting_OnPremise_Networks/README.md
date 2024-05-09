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

Components and their relationships in a Point-to-Site VPN connection in Azure:

1. **Client Computer**: This is the starting point of the VPN connection. It can be any device that supports VPN connectivity.

2. **VPN Client**: This is the software installed on the client computer. It's responsible for initiating and maintaining the VPN connection.

3. **Azure VPN Gateway**: This is the endpoint for the VPN connection in Azure. It's part of the Virtual Network and provides the entry point for the VPN connection.

4. **Virtual Network (VNet)**: This is the network within Azure where your resources are located. The VPN Gateway is part of this network.

5. **Azure Resources**: These are the resources within the VNet that the client computer can access over the VPN connection.

The VPN connection is established from the VPN Client on the Client Computer to the Azure VPN Gateway. Once the connection is established, the Client Computer can access Azure Resources within the VNet as if it was directly connected to the network.


### Configure a site-to-site VPN connection in Azure

To configure a Site-to-Site (S2S) VPN connection in Azure, you can use Azure CLI or Azure Portal. Here's a step-by-step guide using Azure CLI:

1. **Install Azure CLI:** Make sure you have Azure CLI installed on your machine. If not, you can download it from the official Microsoft website.

2. **Login to Azure:** Open your terminal and login to your Azure account using the following command:

   ```bash
   az login
   ```

3. **Create a Resource Group:** Create a resource group where you will deploy your resources.

   ```bash
   az group create --name MyResourceGroup --location "East US"
   ```

4. **Create a Virtual Network (VNet):** Create a VNet in the resource group.

   ```bash
   az network vnet create --resource-group MyResourceGroup --name MyVNet --address-prefix 10.0.0.0/16
   ```

5. **Create a Subnet:** Create a subnet in the VNet.

   ```bash
   az network vnet subnet create --resource-group MyResourceGroup --vnet-name MyVNet --name GatewaySubnet --address-prefix 10.0.0.0/24
   ```

6. **Create a VPN Gateway:** Create a VPN gateway in the GatewaySubnet.

   ```bash
   az network vnet-gateway create --resource-group MyResourceGroup --name MyVPNGateway --vnet MyVNet --gateway-type Vpn --vpn-type RouteBased --sku VpnGw1
   ```

7. **Create a Local Network Gateway:** The local network gateway is a specific object that represents your on-premises location (the 'site' you want to connect with) for routing purposes.

   ```bash
   az network local-gateway create --resource-group MyResourceGroup --gateway-ip-address OnPremisesVPNDeviceIP --name MyLocalGateway --local-address-prefixes 192.168.0.0/24
   ```

8. **Create a VPN Connection:** The VPN connection is a connection between the VPN gateway and the local network gateway.

   ```bash
   az network vpn-connection create --resource-group MyResourceGroup --name MyVPNConnection --vnet-gateway1 MyVPNGateway --local-gateway2 MyLocalGateway --shared-key "MySharedKey"
   ```

Remember to replace the placeholders with your actual values. The VPN client configuration files contain the necessary information for the client to connect to the VNet.


Visual text diagram representing an Azure Site-to-Site VPN connecting to an on-premise network:

```
Azure Site-to-Site VPN Connecting to On-Premise Network

    Azure Virtual Network                                 On-Premise Network
          |                                                      |
+---------|---------+                                  +---------|---------+
|                     |                                  |                     |
|   Azure VPN Gateway|----------------------------------|On-Premise VPN Gateway|
|                     |                                  |                     |
|    Virtual Machines|                                  |       On-Premise    |
|                     |                                  |       Resources     |
+---------------------+                                  +---------------------+
```

In this representation:
- "Azure Virtual Network" represents the virtual network in Azure.
- "Azure VPN Gateway" is the gateway component in Azure responsible for establishing the VPN connection to the on-premise network.
- "Virtual Machines" are the resources hosted within the Azure virtual network.
- "On-Premise Network" represents the network infrastructure at the on-premise location.
- "On-Premise VPN Gateway" is the gateway component at the on-premise location responsible for establishing the VPN connection to Azure.
- "On-Premise Resources" are the resources hosted within the on-premise network.

This diagram illustrates the connection between the Azure virtual network and the on-premise network via a Site-to-Site VPN, allowing communication between resources in both environments.


To configure a Site-to-Site VPN connection in Azure using Terraform, you need to define the necessary Azure resources and their configurations in a Terraform configuration file. Below is an example configuration that sets up a basic Site-to-Site VPN connection between an Azure virtual network and an on-premise network using Terraform:

```hcl
# Define provider (Azure)
provider "azurerm" {
  features {}
}

# Variables
variable "azure_subscription_id" {}
variable "azure_location" {}
variable "resource_group_name" {}
variable "virtual_network_name" {}
variable "local_network_gateway_name" {}
variable "virtual_network_gateway_name" {}
variable "shared_key" {}
variable "local_network_address_space" {}

# Create a resource group
resource "azurerm_resource_group" "rg" {
  name     = var.resource_group_name
  location = var.azure_location
}

# Create virtual network
resource "azurerm_virtual_network" "vnet" {
  name                = var.virtual_network_name
  address_space       = ["10.0.0.0/16"]
  location            = var.azure_location
  resource_group_name = azurerm_resource_group.rg.name
}

# Create local network gateway
resource "azurerm_local_network_gateway" "local" {
  name                = var.local_network_gateway_name
  location            = var.azure_location
  resource_group_name = azurerm_resource_group.rg.name

  gateway_address = "on-premise-public-ip"
  address_space   = [var.local_network_address_space]
}

# Create virtual network gateway
resource "azurerm_virtual_network_gateway" "gateway" {
  name                = var.virtual_network_gateway_name
  location            = var.azure_location
  resource_group_name = azurerm_resource_group.rg.name
  type                = "Vpn"
  vpn_type            = "RouteBased"
  sku                 = "VpnGw1"
  
  ip_configuration {
    name                          = "gwconfig"
    public_ip_address_id          = azurerm_public_ip.gateway.id
    private_ip_address_allocation = "Dynamic"
    subnet_id                     = azurerm_subnet.subnet.id
  }
}

# Create public IP for virtual network gateway
resource "azurerm_public_ip" "gateway" {
  name                = "gateway-public-ip"
  location            = var.azure_location
  resource_group_name = azurerm_resource_group.rg.name
  allocation_method   = "Dynamic"
}

# Create connection
resource "azurerm_virtual_network_gateway_connection" "connection" {
  name                             = "vnet-to-onprem-connection"
  location                         = var.azure_location
  resource_group_name              = azurerm_resource_group.rg.name
  virtual_network_gateway_id       = azurerm_virtual_network_gateway.gateway.id
  local_network_gateway_id         = azurerm_local_network_gateway.local.id
  type                             = "IPsec"
  routing_weight                   = 10
  shared_key                       = var.shared_key
}

# Output
output "public_ip_address" {
  value = azurerm_public_ip.gateway.ip_address
}
```

In this configuration:

- You define the Azure provider.
- You specify variables for the Azure subscription ID, location, resource group name, virtual network name, local network gateway name, virtual network gateway name, shared key, and local network address space.
- You create an Azure resource group.
- You create an Azure virtual network.
- You create an Azure local network gateway representing the on-premise network.
- You create an Azure virtual network gateway.
- You create a public IP address for the virtual network gateway.
- You create a connection between the virtual network gateway and the local network gateway using the specified shared key.

Make sure to replace placeholders like `"on-premise-public-ip"` with the actual public IP address of your on-premise VPN device.

You can run `terraform init`, `terraform plan`, and `terraform apply` to provision the Azure resources defined in this configuration file.
