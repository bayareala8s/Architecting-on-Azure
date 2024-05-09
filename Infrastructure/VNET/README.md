### Azure Virtual Net Peering

Azure Virtual Network Peering enables you to seamlessly connect two or more Azure virtual networks. Once peered, the virtual networks appear as one, for connectivity purposes. The traffic between virtual machines in the peered virtual networks is routed through the Microsoft backbone infrastructure, much like traffic is routed between virtual machines in the same virtual network, through private IP addresses only.

Here is a Terraform script to create two virtual networks and establish peering between them:

```hcl
provider "azurerm" {
  features {}
}

resource "azurerm_resource_group" "example" {
  name     = "example-resources"
  location = "West US"
}

resource "azurerm_virtual_network" "network1" {
  name                = "example-network1"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  address_space       = ["10.0.0.0/16"]
}

resource "azurerm_virtual_network" "network2" {
  name                = "example-network2"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  address_space       = ["10.1.0.0/16"]
}

resource "azurerm_virtual_network_peering" "network1_to_network2" {
  name                         = "network1-to-network2"
  resource_group_name          = azurerm_resource_group.example.name
  virtual_network_name         = azurerm_virtual_network.network1.name
  remote_virtual_network_id    = azurerm_virtual_network.network2.id
}

resource "azurerm_virtual_network_peering" "network2_to_network1" {
  name                         = "network2-to-network1"
  resource_group_name          = azurerm_resource_group.example.name
  virtual_network_name         = azurerm_virtual_network.network2.name
  remote_virtual_network_id    = azurerm_virtual_network.network1.id
}
```

This script creates two virtual networks and establishes peering between them. The `azurerm_virtual_network_peering` resources represent the peering connections. Note that peering is not a symmetric relationship, so you need to create two peering connections: one from network1 to network2, and another from network2 to network1.


Here's a simple text diagram to illustrate Azure Virtual Network (VNet) peering:

```
+----------------+       Peering       +----------------+
|                | <---------------->  |                |
|    VNet 1      |                     |    VNet 2      |
|                |                     |                |
| +------------+ |                     | +------------+ |
| | VM or     | |                     | | VM or     | |
| | Service A | |                     | | Service B | |
| +------------+ |                     | +------------+ |
|                |                     |                |
+----------------+                     +----------------+
```

In this diagram:

- `VNet 1` and `VNet 2` are two virtual networks in Azure.
- `VM or Service A` and `VM or Service B` represent virtual machines or services that are deployed in the virtual networks.
- The `<---------------->` symbol represents peering between the two virtual networks.

Once the peering is established, `VM or Service A` can communicate with `VM or Service B` as if they were in the same network. The traffic between the two services is routed through the Microsoft backbone infrastructure, not over the internet.

This architecture allows for low-latency, high-bandwidth connections between resources in different VNets, which can be in the same or different regions. It's useful for scenarios where you need to isolate resources in different VNets for management or security purposes, but still need them to communicate with each other.


### Configure access control for resources within a peered Azure Virtual Network

Access control for resources within a peered Azure Virtual Network can be configured using Network Security Groups (NSGs) and Azure Firewall.

**Network Security Groups (NSGs):** NSGs contain a list of Access Control List (ACL) rules that allow or deny network traffic to your virtual machine instances in a Virtual Network. The NSGs can be associated with either subnets or individual virtual machine instances within that subnet.

Here's an example of how to create an NSG with Terraform:

```hcl
resource "azurerm_network_security_group" "example" {
  name                = "example-nsg"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name

  security_rule {
    name                       = "SSH"
    priority                   = 1001
    direction                  = "Inbound"
    access                     = "Allow"
    protocol                   = "Tcp"
    source_port_range          = "*"
    destination_port_range     = "22"
    source_address_prefix      = "*"
    destination_address_prefix = "*"
  }
}
```

This script creates an NSG that allows inbound SSH traffic.

**Azure Firewall:** Azure Firewall is a managed, cloud-based network security service that protects your Azure Virtual Network resources. It's a fully stateful firewall as a service with built-in high availability and unrestricted cloud scalability.

Here's an example of how to create an Azure Firewall with Terraform:

```hcl
resource "azurerm_firewall" "example" {
  name                = "example-firewall"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name

  ip_configuration {
    name                 = "configuration"
    subnet_id            = azurerm_subnet.example.id
    public_ip_address_id = azurerm_public_ip.example.id
  }
}
```

This script creates a firewall in a specific subnet.

Remember to replace `azurerm_resource_group.example.location`, `azurerm_resource_group.example.name`, `azurerm_subnet.example.id`, and `azurerm_public_ip.example.id` with your actual resource group location, name, subnet ID, and public IP address ID, respectively.

By combining NSGs and Azure Firewall, you can create a robust network security strategy for your peered Azure Virtual Networks.


### Configure user-defined routes within a peered Azure Virtual Network

User-defined routes (UDRs) in Azure allow you to override Azure's default system routes, or to add additional routes to a subnet's route table. UDRs can be used to direct traffic between subnets through a network virtual appliance (NVA) for inspection, to a virtual network gateway, or to any other desired endpoint.

Here's an example of how to create a route table with a user-defined route using Terraform:

```hcl
resource "azurerm_route_table" "example" {
  name                = "example-route-table"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name

  route {
    name                   = "example-route"
    address_prefix         = "0.0.0.0/0"
    next_hop_type          = "VirtualAppliance"
    next_hop_in_ip_address = "10.0.1.4"
  }
}

resource "azurerm_subnet_route_table_association" "example" {
  subnet_id      = azurerm_subnet.example.id
  route_table_id = azurerm_route_table.example.id
}
```

In this example:

- `azurerm_route_table` creates a route table.
- `route` block within `azurerm_route_table` defines a route that directs all traffic (`0.0.0.0/0`) to a virtual appliance at `10.0.1.4`.
- `azurerm_subnet_route_table_association` associates the route table with a subnet.

Please replace `azurerm_resource_group.example.location`, `azurerm_resource_group.example.name`, and `azurerm_subnet.example.id` with your actual resource group location, name, and subnet ID, respectively.

Remember that the effectiveness of UDRs depends on the network security group (NSG) rules applied to the subnets and VMs. NSG rules are processed before route tables. If an NSG rule denies traffic, the packet is dropped before the routing choice is made.



