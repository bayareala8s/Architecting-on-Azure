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
