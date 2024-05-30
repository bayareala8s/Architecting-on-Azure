In Azure, different network design patterns can help you optimize and secure your network infrastructure based on various requirements and scenarios. Here are some common network design patterns:

### 1. **Hub-and-Spoke Network Topology**
- **Description**: This pattern involves a central hub virtual network (VNet) that acts as a central point of connectivity to many spoke VNets. The hub VNet typically contains shared resources such as network virtual appliances (NVAs), firewalls, and gateways.
- **Use Cases**: Suitable for organizations that need centralized control over network traffic and services.
- **Advantages**:
  - Simplifies management of shared services.
  - Centralizes security policies.
  - Reduces costs by sharing resources.
- **Components**:
  - Hub VNet: Contains shared services.
  - Spoke VNets: Contain resources that connect to the hub VNet.

### 2. **Virtual WAN**
- **Description**: Azure Virtual WAN is a networking service that provides optimized and automated branch-to-branch connectivity through Azure. It allows for the creation of a unified wide area network (WAN) that can be managed centrally.
- **Use Cases**: Ideal for large-scale branch connectivity and hybrid networks.
- **Advantages**:
  - Simplifies management with centralized control.
  - Provides high availability and scalability.
  - Optimizes network performance with Azure backbone.
- **Components**:
  - Virtual WAN hubs.
  - Site-to-site VPN.
  - Point-to-site VPN.
  - ExpressRoute.

### 3. **DMZ (Demilitarized Zone)**
- **Description**: A DMZ is a subnet that is exposed to the internet but segmented from the internal network. It is designed to add an additional layer of security to an organization's network.
- **Use Cases**: Commonly used for hosting internet-facing services such as web applications and mail servers.
- **Advantages**:
  - Enhances security by isolating external-facing services.
  - Protects internal networks from direct exposure to the internet.
- **Components**:
  - Application Gateway or Load Balancer.
  - Network Security Groups (NSGs).
  - Azure Firewall or third-party NVAs.

### 4. **Microservices Architecture with Azure Kubernetes Service (AKS)**
- **Description**: This pattern involves using Azure Kubernetes Service to deploy, scale, and manage containerized applications. It promotes a microservices approach where each service is independently deployable.
- **Use Cases**: Suitable for applications that require scalability, flexibility, and isolation of services.
- **Advantages**:
  - Supports high availability and scalability.
  - Provides efficient resource utilization.
  - Enhances application resilience and agility.
- **Components**:
  - AKS cluster.
  - Azure Container Registry (ACR).
  - Ingress controllers and Load Balancers.
  - Virtual Network for AKS.

### 5. **Hybrid Network**
- **Description**: This pattern integrates on-premises networks with Azure Virtual Networks. It enables seamless connectivity between on-premises data centers and Azure resources.
- **Use Cases**: Ideal for organizations looking to extend their on-premises infrastructure to the cloud.
- **Advantages**:
  - Extends on-premises infrastructure.
  - Provides a unified network experience.
  - Enables disaster recovery and business continuity.
- **Components**:
  - VPN Gateway or ExpressRoute.
  - Virtual Network.
  - Azure Active Directory for identity management.

### 6. **Service Chaining with Azure Application Gateway**
- **Description**: This pattern uses Azure Application Gateway to route traffic to various services based on defined rules. It allows chaining multiple services, enhancing security and manageability.
- **Use Cases**: Suitable for complex applications requiring traffic routing and load balancing.
- **Advantages**:
  - Centralized traffic management.
  - Enhanced security with Web Application Firewall (WAF).
  - Improved performance and scalability.
- **Components**:
  - Application Gateway.
  - Backend Pools.
  - Health Probes.
  - HTTP Settings.

### 7. **Network Segmentation with Subnets**
- **Description**: This pattern involves dividing a virtual network into smaller, isolated subnets to improve security and manageability.
- **Use Cases**: Suitable for organizations that need to isolate different parts of their network for security, compliance, or operational reasons.
- **Advantages**:
  - Enhances security by isolating network segments.
  - Simplifies network management.
  - Improves performance by reducing broadcast traffic.
- **Components**:
  - Virtual Network.
  - Subnets.
  - Network Security Groups (NSGs).
  - Route Tables.

### 8. **Bastion Host**
- **Description**: Azure Bastion is a PaaS service that provides secure and seamless RDP and SSH access to virtual machines directly through the Azure portal.
- **Use Cases**: Ideal for securely accessing VMs without exposing them to public IP addresses.
- **Advantages**:
  - Eliminates the need for public IP addresses.
  - Provides secure access to VMs.
  - Simplifies management and reduces attack surface.
- **Components**:
  - Bastion Host.
  - Virtual Network.
  - Network Security Groups (NSGs).

These patterns can be combined and tailored to meet specific business requirements and enhance the overall security, performance, and manageability of your Azure network infrastructure.


### Hub-and-Spoke Network Topology

**Scenario**: A retail company with multiple branches across the country wants to centralize its network management and enhance security. The company uses Azure for its cloud infrastructure.

**Step-by-Step Implementation**:

1. **Create the Hub VNet**:
    - In the Azure portal, create a virtual network named `Hub-VNet`.
    - Define the address space, e.g., `10.0.0.0/16`.
    - Create subnets for shared services, such as `GatewaySubnet`, `NVA-Subnet`, and `SharedServices-Subnet`.

2. **Deploy Shared Services in the Hub VNet**:
    - Deploy an Azure Firewall or NVA in the `NVA-Subnet` for traffic inspection and security.
    - Deploy a VPN Gateway or ExpressRoute Gateway in the `GatewaySubnet` to enable on-premises connectivity.
    - Deploy shared resources, such as a file server or domain controller, in the `SharedServices-Subnet`.

3. **Create the Spoke VNets**:
    - For each branch office, create a spoke VNet, e.g., `Spoke-VNet-East`, `Spoke-VNet-West`.
    - Define the address space, e.g., `10.1.0.0/16` for East and `10.2.0.0/16` for West.

4. **Connect Spoke VNets to the Hub VNet**:
    - In the `Hub-VNet`, create peering connections to each spoke VNet.
    - In each spoke VNet, create a peering connection back to the `Hub-VNet`.
    - Enable gateway transit on the hub peering to allow the spoke VNets to use the VPN Gateway.

5. **Configure Network Security Groups (NSGs)**:
    - Apply NSGs to subnets to control traffic flow. For example, allow only necessary traffic between spokes and the hub.

**Example**: The retail company's central IT department can now manage security and shared services centrally, ensuring that all branch offices adhere to the same security policies and can access shared resources efficiently.

### Virtual WAN

**Scenario**: A multinational corporation with offices worldwide needs to optimize and automate branch-to-branch connectivity.

**Step-by-Step Implementation**:

1. **Create a Virtual WAN**:
    - In the Azure portal, create a Virtual WAN named `Corporate-VWAN`.
    - Define the regions where you want to create hubs.

2. **Create Virtual WAN Hubs**:
    - For each region, create a hub. For example, create a hub in `East US` and another in `West Europe`.
    - Each hub will serve as a regional point of connectivity.

3. **Connect Branch Offices**:
    - Use site-to-site VPN or ExpressRoute to connect each branch office to the nearest Virtual WAN hub.
    - Configure point-to-site VPN for remote users to connect securely.

4. **Configure Routing Policies**:
    - Define routing policies to control traffic flow between branches, regions, and on-premises data centers.
    - Use Azure Firewall Manager to enforce security policies centrally.

**Example**: The corporation achieves seamless connectivity between its global offices, ensuring high availability and optimal performance. The IT team can manage the entire network centrally, reducing complexity and operational overhead.

### DMZ (Demilitarized Zone)

**Scenario**: A financial services company needs to host internet-facing applications securely while protecting its internal network.

**Step-by-Step Implementation**:

1. **Create the DMZ VNet**:
    - In the Azure portal, create a virtual network named `DMZ-VNet`.
    - Define the address space, e.g., `10.0.0.0/24`.
    - Create a subnet named `DMZ-Subnet`.

2. **Deploy an Application Gateway**:
    - Deploy an Azure Application Gateway in the `DMZ-Subnet`.
    - Configure the Application Gateway with a Web Application Firewall (WAF) to protect against common threats.

3. **Deploy Web Applications**:
    - Deploy web applications in another subnet, e.g., `App-Subnet`.
    - Ensure these applications are only accessible via the Application Gateway.

4. **Configure Network Security Groups (NSGs)**:
    - Apply NSGs to control traffic between the DMZ and the internal network.
    - Allow only necessary traffic from the DMZ to the internal network, e.g., database queries.

**Example**: The financial services company can securely expose its web applications to the internet while protecting sensitive internal resources, such as databases and backend services.

### Microservices Architecture with Azure Kubernetes Service (AKS)

**Scenario**: An e-commerce company needs to deploy a scalable and resilient microservices application.

**Step-by-Step Implementation**:

1. **Create an AKS Cluster**:
    - In the Azure portal, create an AKS cluster named `Ecommerce-AKS`.
    - Define the virtual network and subnet for the AKS cluster.

2. **Deploy Azure Container Registry (ACR)**:
    - Create an ACR to store container images.
    - Push your microservices container images to ACR.

3. **Deploy Microservices to AKS**:
    - Use Kubernetes manifests or Helm charts to deploy your microservices from ACR to AKS.
    - Each microservice can be deployed as a separate Kubernetes deployment.

4. **Configure Ingress Controller**:
    - Deploy an Ingress controller, such as NGINX, to manage external access to the microservices.
    - Define Ingress resources to route traffic to the appropriate microservices based on the URL path.

5. **Implement Service Mesh**:
    - Optionally, deploy a service mesh like Istio to manage microservices communication, traffic policies, and observability.

**Example**: The e-commerce company can efficiently scale its application based on demand, ensuring high availability and resilience. Each microservice can be updated independently, reducing downtime and improving deployment velocity.

### Hybrid Network

**Scenario**: A healthcare provider wants to extend its on-premises data center to Azure for disaster recovery and additional compute capacity.

**Step-by-Step Implementation**:

1. **Create the Azure VNet**:
    - In the Azure portal, create a virtual network named `Hybrid-VNet`.
    - Define the address space, e.g., `10.0.0.0/16`.

2. **Deploy a VPN Gateway**:
    - In the `Hybrid-VNet`, create a VPN Gateway.
    - Configure the VPN Gateway for site-to-site connectivity.

3. **Configure On-Premises VPN Device**:
    - On the on-premises VPN device, configure the VPN settings to establish a connection with the Azure VPN Gateway.
    - Define the shared key and IP address of the Azure VPN Gateway.

4. **Create a Connection**:
    - In the Azure portal, create a connection between the on-premises VPN device and the Azure VPN Gateway.
    - Verify the connection status to ensure it is established.

5. **Implement Hybrid Identity**:
    - Set up Azure Active Directory (Azure AD) Connect to synchronize on-premises identities with Azure AD.
    - Configure hybrid identity to allow seamless access to both on-premises and Azure resources.

**Example**: The healthcare provider can leverage Azure for additional compute capacity during peak times and ensure business continuity with disaster recovery in the cloud. The hybrid network provides seamless connectivity and a unified experience for users.

### Service Chaining with Azure Application Gateway

**Scenario**: A SaaS provider needs to route traffic to different backend services based on specific URL paths.

**Step-by-Step Implementation**:

1. **Create the Application Gateway**:
    - In the Azure portal, create an Application Gateway named `SaaS-AG`.
    - Define the virtual network and subnet for the Application Gateway.

2. **Configure Backend Pools**:
    - Create backend pools for each microservice or application.
    - Assign the appropriate VMs or app services to each backend pool.

3. **Define HTTP Settings**:
    - Create HTTP settings to define how traffic should be forwarded to the backend pools.
    - Specify the protocol, port, and other settings.

4. **Create Routing Rules**:
    - Define routing rules based on URL paths.
    - For example, route `/api/v1/users` to the `UserService` backend pool and `/api/v1/orders` to the `OrderService` backend pool.

5. **Enable Web Application Firewall (WAF)**:
    - Configure WAF on the Application Gateway to protect against common web vulnerabilities.

**Example**: The SaaS provider can efficiently manage traffic routing to different backend services, ensuring that each service can be scaled independently and protected by the WAF.

### Network Segmentation with Subnets

**Scenario**: A university wants to segment its network to isolate different departments and enhance security.

**Step-by-Step Implementation**:

1. **Create the University VNet**:
    - In the Azure portal, create a virtual network named `University-VNet`.
    - Define the address space, e.g., `10.0.0.0/16`.

2. **Create Subnets for Departments**:
    - Create subnets for each department, e.g., `Admin-Subnet`, `Research-Subnet`, `Student-Subnet`.
    - Assign appropriate address spaces, e.g., `10.0.1.0/24` for Admin, `10.0.2.0/24` for Research, `10.0.3.0/24` for Students.

3. **Deploy Resources in Each Subnet**:
    - Deploy resources such as VMs, databases, and applications in the appropriate subnets.

4. **Configure Network Security Groups (NSGs)**:
    - Apply NSGs to each subnet to control traffic flow.
    - For example, restrict access to the `Research-Subnet` from the `Student-Subnet` but allow access

 from the `Admin-Subnet`.

5. **Implement Route Tables**:
    - Define custom route tables if needed to control traffic routing between subnets.
    - Associate the route tables with the appropriate subnets.

**Example**: The university achieves better security and management by isolating network segments for different departments, ensuring that sensitive data in the Admin and Research subnets are protected.

### Bastion Host

**Scenario**: A software development company needs to provide secure remote access to its virtual machines in Azure without exposing them to the public internet.

**Step-by-Step Implementation**:

1. **Create the VNet and Subnet**:
    - In the Azure portal, create a virtual network named `DevOps-VNet`.
    - Define the address space, e.g., `10.0.0.0/16`.
    - Create a subnet named `BastionSubnet` with the address range `10.0.1.0/24`.

2. **Deploy the Bastion Host**:
    - In the `BastionSubnet`, deploy Azure Bastion.
    - Configure the Bastion service to enable RDP and SSH access to VMs.

3. **Deploy Virtual Machines**:
    - Deploy VMs in another subnet, e.g., `VM-Subnet`.
    - Ensure these VMs do not have public IP addresses.

4. **Access VMs Securely**:
    - Use the Azure portal to connect to the VMs via Bastion.
    - Bastion provides a secure RDP/SSH session directly through the browser.

**Example**: The software development company can provide secure access to its VMs for developers and admins without exposing the VMs to potential threats on the public internet.

These detailed examples demonstrate how Azure's network design patterns can be applied to real-world scenarios to enhance security, manageability, and performance of your network infrastructure.


### Hub-and-Spoke Network Topology

**Scenario**: A retail company with multiple branches across the country wants to centralize its network management and enhance security. The company uses Azure for its cloud infrastructure.

**Terraform Script**:

1. **Create the Hub VNet**:
    ```hcl
    provider "azurerm" {
      features {}
    }

    resource "azurerm_virtual_network" "hub_vnet" {
      name                = "Hub-VNet"
      address_space       = ["10.0.0.0/16"]
      location            = "East US"
      resource_group_name = "HubResourceGroup"
    }

    resource "azurerm_subnet" "gateway_subnet" {
      name                 = "GatewaySubnet"
      resource_group_name  = azurerm_virtual_network.hub_vnet.resource_group_name
      virtual_network_name = azurerm_virtual_network.hub_vnet.name
      address_prefixes     = ["10.0.0.0/24"]
    }

    resource "azurerm_subnet" "nva_subnet" {
      name                 = "NVA-Subnet"
      resource_group_name  = azurerm_virtual_network.hub_vnet.resource_group_name
      virtual_network_name = azurerm_virtual_network.hub_vnet.name
      address_prefixes     = ["10.0.1.0/24"]
    }

    resource "azurerm_subnet" "shared_services_subnet" {
      name                 = "SharedServices-Subnet"
      resource_group_name  = azurerm_virtual_network.hub_vnet.resource_group_name
      virtual_network_name = azurerm_virtual_network.hub_vnet.name
      address_prefixes     = ["10.0.2.0/24"]
    }
    ```

2. **Deploy Shared Services in the Hub VNet**:
    ```hcl
    resource "azurerm_firewall" "hub_firewall" {
      name                = "HubFirewall"
      location            = azurerm_virtual_network.hub_vnet.location
      resource_group_name = azurerm_virtual_network.hub_vnet.resource_group_name
      sku_name            = "AZFW_VNet"
      threat_intel_mode   = "Alert"

      ip_configuration {
        name                 = "configuration"
        subnet_id            = azurerm_subnet.nva_subnet.id
        public_ip_address_id = azurerm_public_ip.firewall_pip.id
      }
    }

    resource "azurerm_public_ip" "firewall_pip" {
      name                = "FirewallPIP"
      location            = azurerm_virtual_network.hub_vnet.location
      resource_group_name = azurerm_virtual_network.hub_vnet.resource_group_name
      allocation_method   = "Static"
      sku                 = "Standard"
    }

    resource "azurerm_virtual_network_gateway" "vpn_gateway" {
      name                = "VPNGateway"
      location            = azurerm_virtual_network.hub_vnet.location
      resource_group_name = azurerm_virtual_network.hub_vnet.resource_group_name

      type     = "Vpn"
      vpn_type = "RouteBased"

      sku {
        name = "VpnGw1"
      }

      ip_configuration {
        name                          = "vpngateway"
        public_ip_address_id          = azurerm_public_ip.vpn_gateway_pip.id
        private_ip_address_allocation = "Dynamic"
        subnet_id                     = azurerm_subnet.gateway_subnet.id
      }
    }

    resource "azurerm_public_ip" "vpn_gateway_pip" {
      name                = "VPNGatewayPIP"
      location            = azurerm_virtual_network.hub_vnet.location
      resource_group_name = azurerm_virtual_network.hub_vnet.resource_group_name
      allocation_method   = "Dynamic"
    }
    ```

3. **Create the Spoke VNets**:
    ```hcl
    resource "azurerm_virtual_network" "spoke_vnet_east" {
      name                = "Spoke-VNet-East"
      address_space       = ["10.1.0.0/16"]
      location            = "East US"
      resource_group_name = "SpokeResourceGroup"
    }

    resource "azurerm_virtual_network" "spoke_vnet_west" {
      name                = "Spoke-VNet-West"
      address_space       = ["10.2.0.0/16"]
      location            = "West US"
      resource_group_name = "SpokeResourceGroup"
    }
    ```

4. **Connect Spoke VNets to the Hub VNet**:
    ```hcl
    resource "azurerm_virtual_network_peering" "hub_to_spoke_east" {
      name                       = "HubToSpokeEast"
      resource_group_name        = azurerm_virtual_network.hub_vnet.resource_group_name
      virtual_network_name       = azurerm_virtual_network.hub_vnet.name
      remote_virtual_network_id  = azurerm_virtual_network.spoke_vnet_east.id
      allow_forwarded_traffic    = true
      allow_gateway_transit      = true
      use_remote_gateways        = false
    }

    resource "azurerm_virtual_network_peering" "spoke_east_to_hub" {
      name                       = "SpokeEastToHub"
      resource_group_name        = azurerm_virtual_network.spoke_vnet_east.resource_group_name
      virtual_network_name       = azurerm_virtual_network.spoke_vnet_east.name
      remote_virtual_network_id  = azurerm_virtual_network.hub_vnet.id
      allow_forwarded_traffic    = true
      use_remote_gateways        = true
      allow_gateway_transit      = false
    }

    resource "azurerm_virtual_network_peering" "hub_to_spoke_west" {
      name                       = "HubToSpokeWest"
      resource_group_name        = azurerm_virtual_network.hub_vnet.resource_group_name
      virtual_network_name       = azurerm_virtual_network.hub_vnet.name
      remote_virtual_network_id  = azurerm_virtual_network.spoke_vnet_west.id
      allow_forwarded_traffic    = true
      allow_gateway_transit      = true
      use_remote_gateways        = false
    }

    resource "azurerm_virtual_network_peering" "spoke_west_to_hub" {
      name                       = "SpokeWestToHub"
      resource_group_name        = azurerm_virtual_network.spoke_vnet_west.resource_group_name
      virtual_network_name       = azurerm_virtual_network.spoke_vnet_west.name
      remote_virtual_network_id  = azurerm_virtual_network.hub_vnet.id
      allow_forwarded_traffic    = true
      use_remote_gateways        = true
      allow_gateway_transit      = false
    }
    ```

5. **Configure Network Security Groups (NSGs)**:
    ```hcl
    resource "azurerm_network_security_group" "hub_nsg" {
      name                = "HubNSG"
      location            = azurerm_virtual_network.hub_vnet.location
      resource_group_name = azurerm_virtual_network.hub_vnet.resource_group_name

      security_rule {
        name                       = "AllowVNetInBound"
        priority                   = 100
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "*"
        source_port_range          = "*"
        destination_port_range     = "*"
        source_address_prefix      = "VirtualNetwork"
        destination_address_prefix = "VirtualNetwork"
      }

      security_rule {
        name                       = "AllowInternetOutBound"
        priority                   = 200
        direction                  = "Outbound"
        access                     = "Allow"
        protocol                   = "*"
        source_port_range          = "*"
        destination_port_range     = "*"
        source_address_prefix      = "VirtualNetwork"
        destination_address_prefix = "Internet"
      }
    }

    resource "azurerm_network_security_group" "spoke_nsg" {
      name                = "SpokeNSG"
      location            = azurerm_virtual_network.spoke_vnet_east.location
      resource_group_name = azurerm_virtual_network.spoke_vnet_east.resource_group_name

      security_rule {
        name                       = "AllowVNetInBound"
        priority                   = 100
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "*"
        source_port_range          = "*"
        destination_port_range     = "*"
        source_address_prefix      = "VirtualNetwork"
        destination_address_prefix = "VirtualNetwork"
      }

      security_rule {
        name                       = "AllowInternetOutBound"
        priority                   = 200
        direction                  = "Outbound"
        access                     = "Allow"
        protocol                   = "*"
        source_port_range          = "*"
        destination_port_range     = "*"
        source_address_prefix      = "VirtualNetwork"
        destination_address_prefix = "Internet"
      }
    }

    resource "azurerm_subnet_network_security_group_association" "hub_subnet_nsg" {
      subnet_id                 = azurerm_subnet.shared_services_subnet.id
      network_security_group_id = azurerm_network_security_group.hub_nsg.id
    }

    resource "azurerm_subnet_network_security_group_association" "spoke_subnet_nsg" {
      subnet_id                 = azurerm_virtual_network.spoke_vnet_east.subnet.id
      network_security_group_id = azurerm_network_security_group.spoke_nsg.id
    }
    ```

**Explanation**:
1. **Create the Hub

 VNet**: The script defines a virtual network for the hub, including subnets for the gateway, NVA, and shared services.
2. **Deploy Shared Services in the Hub VNet**: This part includes setting up an Azure Firewall and a VPN Gateway in the hub VNet.
3. **Create the Spoke VNets**: Two spoke VNets are created for different regions.
4. **Connect Spoke VNets to the Hub VNet**: This section sets up VNet peering between the hub and spoke VNets, enabling communication.
5. **Configure Network Security Groups (NSGs)**: NSGs are applied to control traffic between the hub and spoke VNets.

### Virtual WAN

**Scenario**: A multinational corporation with offices worldwide needs to optimize and automate branch-to-branch connectivity.

**Terraform Script**:

1. **Create a Virtual WAN**:
    ```hcl
    provider "azurerm" {
      features {}
    }

    resource "azurerm_virtual_wan" "corporate_vwan" {
      name                = "Corporate-VWAN"
      location            = "East US"
      resource_group_name = "CorporateResourceGroup"
    }
    ```

2. **Create Virtual WAN Hubs**:
    ```hcl
    resource "azurerm_virtual_hub" "east_us_hub" {
      name                = "EastUSHub"
      location            = "East US"
      resource_group_name = azurerm_virtual_wan.corporate_vwan.resource_group_name
      virtual_wan_id      = azurerm_virtual_wan.corporate_vwan.id
      address_prefix      = "10.0.0.0/24"
    }

    resource "azurerm_virtual_hub" "west_europe_hub" {
      name                = "WestEuropeHub"
      location            = "West Europe"
      resource_group_name = azurerm_virtual_wan.corporate_vwan.resource_group_name
      virtual_wan_id      = azurerm_virtual_wan.corporate_vwan.id
      address_prefix      = "10.1.0.0/24"
    }
    ```

3. **Connect Branch Offices**:
    ```hcl
    resource "azurerm_vpn_site" "branch_office" {
      name                = "BranchOffice"
      location            = "East US"
      resource_group_name = azurerm_virtual_wan.corporate_vwan.resource_group_name

      virtual_hub_id      = azurerm_virtual_hub.east_us_hub.id
      address_prefixes    = ["10.2.0.0/24"]

      ip_address          = "1.2.3.4"
    }

    resource "azurerm_vpn_gateway" "vpn_gateway" {
      name                = "BranchVPNGateway"
      location            = "East US"
      resource_group_name = azurerm_virtual_wan.corporate_vwan.resource_group_name

      virtual_hub_id      = azurerm_virtual_hub.east_us_hub.id
    }

    resource "azurerm_vpn_connection" "branch_connection" {
      name                = "BranchConnection"
      location            = "East US"
      resource_group_name = azurerm_virtual_wan.corporate_vwan.resource_group_name

      vpn_gateway_id      = azurerm_vpn_gateway.vpn_gateway.id
      remote_vpn_site_id  = azurerm_vpn_site.branch_office.id
    }
    ```

**Explanation**:
1. **Create a Virtual WAN**: The script defines a Virtual WAN for the corporation.
2. **Create Virtual WAN Hubs**: Virtual WAN hubs are created in different regions (East US and West Europe).
3. **Connect Branch Offices**: This part sets up VPN sites and connections to connect branch offices to the Virtual WAN.

### DMZ (Demilitarized Zone)

**Scenario**: A financial services company needs to host internet-facing applications securely while protecting its internal network.

**Terraform Script**:

1. **Create the DMZ VNet**:
    ```hcl
    provider "azurerm" {
      features {}
    }

    resource "azurerm_virtual_network" "dmz_vnet" {
      name                = "DMZ-VNet"
      address_space       = ["10.0.0.0/24"]
      location            = "East US"
      resource_group_name = "DMZResourceGroup"
    }

    resource "azurerm_subnet" "dmz_subnet" {
      name                 = "DMZ-Subnet"
      resource_group_name  = azurerm_virtual_network.dmz_vnet.resource_group_name
      virtual_network_name = azurerm_virtual_network.dmz_vnet.name
      address_prefixes     = ["10.0.0.0/24"]
    }
    ```

2. **Deploy an Application Gateway**:
    ```hcl
    resource "azurerm_public_ip" "app_gateway_pip" {
      name                = "AppGatewayPIP"
      location            = azurerm_virtual_network.dmz_vnet.location
      resource_group_name = azurerm_virtual_network.dmz_vnet.resource_group_name
      allocation_method   = "Static"
      sku                 = "Standard"
    }

    resource "azurerm_application_gateway" "app_gateway" {
      name                = "AppGateway"
      location            = azurerm_virtual_network.dmz_vnet.location
      resource_group_name = azurerm_virtual_network.dmz_vnet.resource_group_name

      sku {
        name     = "Standard_v2"
        tier     = "Standard_v2"
        capacity = 2
      }

      gateway_ip_configuration {
        name      = "appGatewayIpConfig"
        subnet_id = azurerm_subnet.dmz_subnet.id
      }

      frontend_ip_configuration {
        name                 = "appGatewayFrontendIpConfig"
        public_ip_address_id = azurerm_public_ip.app_gateway_pip.id
      }

      frontend_port {
        name = "frontendPort"
        port = 80
      }

      backend_address_pool {
        name = "backendPool"
      }

      backend_http_settings {
        name                  = "httpSettings"
        cookie_based_affinity = "Disabled"
        port                  = 80
        protocol              = "Http"
        request_timeout       = 20
      }

      http_listener {
        name                           = "httpListener"
        frontend_ip_configuration_name = "appGatewayFrontendIpConfig"
        frontend_port_name             = "frontendPort"
        protocol                       = "Http"
      }

      request_routing_rule {
        name                       = "routingRule"
        rule_type                  = "Basic"
        http_listener_name         = "httpListener"
        backend_address_pool_name  = "backendPool"
        backend_http_settings_name = "httpSettings"
      }
    }
    ```

3. **Deploy Web Applications**:
    ```hcl
    resource "azurerm_virtual_network" "app_vnet" {
      name                = "App-VNet"
      address_space       = ["10.1.0.0/24"]
      location            = "East US"
      resource_group_name = "AppResourceGroup"
    }

    resource "azurerm_subnet" "app_subnet" {
      name                 = "App-Subnet"
      resource_group_name  = azurerm_virtual_network.app_vnet.resource_group_name
      virtual_network_name = azurerm_virtual_network.app_vnet.name
      address_prefixes     = ["10.1.0.0/24"]
    }

    resource "azurerm_network_interface" "app_nic" {
      name                = "AppNIC"
      location            = azurerm_virtual_network.app_vnet.location
      resource_group_name = azurerm_virtual_network.app_vnet.resource_group_name

      ip_configuration {
        name                          = "internal"
        subnet_id                     = azurerm_subnet.app_subnet.id
        private_ip_address_allocation = "Dynamic"
      }
    }

    resource "azurerm_virtual_machine" "web_app" {
      name                  = "WebAppVM"
      location              = azurerm_virtual_network.app_vnet.location
      resource_group_name   = azurerm_virtual_network.app_vnet.resource_group_name
      network_interface_ids = [azurerm_network_interface.app_nic.id]
      vm_size               = "Standard_DS1_v2"

      storage_os_disk {
        name              = "osdisk"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Standard_LRS"
      }

      os_profile {
        computer_name  = "webappvm"
        admin_username = "adminuser"
        admin_password = "Password123!"
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      source_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "18.04-LTS"
        version   = "latest"
      }
    }
    ```

4. **Configure Network Security Groups (NSGs)**:
    ```hcl
    resource "azurerm_network_security_group" "dmz_nsg" {
      name                = "DMZNSG"
      location            = azurerm_virtual_network.dmz_vnet.location
      resource_group_name = azurerm_virtual_network.dmz_vnet.resource_group_name

      security_rule {
        name                       = "AllowHTTP"
        priority                   = 100
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "80"
        source_address_prefix      = "*"
        destination_address_prefix

 = "10.0.0.0/24"
      }
    }

    resource "azurerm_network_security_group" "app_nsg" {
      name                = "AppNSG"
      location            = azurerm_virtual_network.app_vnet.location
      resource_group_name = azurerm_virtual_network.app_vnet.resource_group_name

      security_rule {
        name                       = "AllowAppGateway"
        priority                   = 100
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "80"
        source_address_prefix      = "10.0.0.0/24"
        destination_address_prefix = "10.1.0.0/24"
      }
    }

    resource "azurerm_subnet_network_security_group_association" "dmz_subnet_nsg" {
      subnet_id                 = azurerm_subnet.dmz_subnet.id
      network_security_group_id = azurerm_network_security_group.dmz_nsg.id
    }

    resource "azurerm_subnet_network_security_group_association" "app_subnet_nsg" {
      subnet_id                 = azurerm_subnet.app_subnet.id
      network_security_group_id = azurerm_network_security_group.app_nsg.id
    }
    ```

**Explanation**:
1. **Create the DMZ VNet**: The script defines a virtual network for the DMZ, including a subnet for the Application Gateway.
2. **Deploy an Application Gateway**: This part sets up an Application Gateway with a public IP and backend pool.
3. **Deploy Web Applications**: A separate VNet is created for web applications, along with a VM for hosting the web application.
4. **Configure Network Security Groups (NSGs)**: NSGs are applied to control traffic to and from the DMZ and application subnets.

### Microservices Architecture with Azure Kubernetes Service (AKS)

**Scenario**: An e-commerce company needs to deploy a scalable and resilient microservices application.

**Terraform Script**:

1. **Create an AKS Cluster**:
    ```hcl
    provider "azurerm" {
      features {}
    }

    resource "azurerm_resource_group" "aks_rg" {
      name     = "AKSResourceGroup"
      location = "East US"
    }

    resource "azurerm_kubernetes_cluster" "aks_cluster" {
      name                = "EcommerceAKS"
      location            = azurerm_resource_group.aks_rg.location
      resource_group_name = azurerm_resource_group.aks_rg.name
      dns_prefix          = "ecommerceaks"

      default_node_pool {
        name       = "default"
        node_count = 2
        vm_size    = "Standard_DS2_v2"
      }

      identity {
        type = "SystemAssigned"
      }

      network_profile {
        network_plugin    = "azure"
        service_cidr      = "10.0.0.0/16"
        dns_service_ip    = "10.0.0.10"
        docker_bridge_cidr = "172.17.0.1/16"
      }
    }
    ```

2. **Deploy Azure Container Registry (ACR)**:
    ```hcl
    resource "azurerm_container_registry" "acr" {
      name                = "EcommerceACR"
      location            = azurerm_resource_group.aks_rg.location
      resource_group_name = azurerm_resource_group.aks_rg.name
      sku                 = "Standard"
      admin_enabled       = true
    }
    ```

3. **Deploy Microservices to AKS**:
    ```hcl
    provider "kubernetes" {
      host                   = azurerm_kubernetes_cluster.aks_cluster.kube_config.0.host
      client_certificate     = base64decode(azurerm_kubernetes_cluster.aks_cluster.kube_config.0.client_certificate)
      client_key             = base64decode(azurerm_kubernetes_cluster.aks_cluster.kube_config.0.client_key)
      cluster_ca_certificate = base64decode(azurerm_kubernetes_cluster.aks_cluster.kube_config.0.cluster_ca_certificate)
    }

    resource "kubernetes_namespace" "microservices" {
      metadata {
        name = "microservices"
      }
    }

    resource "kubernetes_deployment" "user_service" {
      metadata {
        name      = "user-service"
        namespace = kubernetes_namespace.microservices.metadata[0].name
      }

      spec {
        replicas = 2

        selector {
          match_labels = {
            app = "user-service"
          }
        }

        template {
          metadata {
            labels = {
              app = "user-service"
            }
          }

          spec {
            container {
              name  = "user-service"
              image = "${azurerm_container_registry.acr.login_server}/user-service:latest"

              port {
                container_port = 80
              }
            }
          }
        }
      }
    }

    resource "kubernetes_deployment" "order_service" {
      metadata {
        name      = "order-service"
        namespace = kubernetes_namespace.microservices.metadata[0].name
      }

      spec {
        replicas = 2

        selector {
          match_labels = {
            app = "order-service"
          }
        }

        template {
          metadata {
            labels = {
              app = "order-service"
            }
          }

          spec {
            container {
              name  = "order-service"
              image = "${azurerm_container_registry.acr.login_server}/order-service:latest"

              port {
                container_port = 80
              }
            }
          }
        }
      }
    }
    ```

4. **Configure Ingress Controller**:
    ```hcl
    resource "kubernetes_service" "ingress_nginx" {
      metadata {
        name      = "ingress-nginx"
        namespace = "default"
        labels = {
          app = "ingress-nginx"
        }
      }

      spec {
        type = "LoadBalancer"

        selector = {
          app = "ingress-nginx"
        }

        port {
          port        = 80
          target_port = 80
        }
      }
    }

    resource "kubernetes_deployment" "ingress_nginx" {
      metadata {
        name      = "ingress-nginx"
        namespace = "default"
        labels = {
          app = "ingress-nginx"
        }
      }

      spec {
        replicas = 2

        selector {
          match_labels = {
            app = "ingress-nginx"
          }
        }

        template {
          metadata {
            labels = {
              app = "ingress-nginx"
            }
          }

          spec {
            container {
              name  = "nginx-ingress-controller"
              image = "quay.io/kubernetes-ingress-controller/nginx-ingress-controller:0.26.1"

              args = [
                "/nginx-ingress-controller",
                "--configmap=$(POD_NAMESPACE)/nginx-configuration",
                "--tcp-services-configmap=$(POD_NAMESPACE)/tcp-services",
                "--udp-services-configmap=$(POD_NAMESPACE)/udp-services",
                "--publish-service=$(POD_NAMESPACE)/ingress-nginx",
              ]

              env {
                name  = "POD_NAMESPACE"
                value_from {
                  field_ref {
                    field_path = "metadata.namespace"
                  }
                }
              }

              ports {
                container_port = 80
              }
            }
          }
        }
      }
    }
    ```

**Explanation**:
1. **Create an AKS Cluster**: The script sets up an AKS cluster with a default node pool.
2. **Deploy Azure Container Registry (ACR)**: ACR is created to store container images.
3. **Deploy Microservices to AKS**: Microservices (User Service and Order Service) are deployed to the AKS cluster.
4. **Configure Ingress Controller**: An NGINX ingress controller is configured to manage external access to the microservices.

### Hybrid Network

**Scenario**: A healthcare provider wants to extend its on-premises data center to Azure for disaster recovery and additional compute capacity.

**Terraform Script**:

1. **Create the Azure VNet**:
    ```hcl
    provider "azurerm" {
      features {}
    }

    resource "azurerm_virtual_network" "hybrid_vnet" {
      name                = "Hybrid-VNet"
      address_space       = ["10.0.0.0/16"]
      location            = "East US"
      resource_group_name = "HybridResourceGroup"
    }

    resource "azurerm_subnet" "subnet" {
      name                 = "default"
      resource_group_name  = azurerm_virtual_network.hybrid_vnet.resource_group_name
      virtual_network_name = azurerm_virtual_network.hybrid_vnet.name
      address_prefixes     = ["10.0.1.0/24"]
    }
    ```

2. **Deploy a VPN Gateway**:
    ```hcl
    resource "azurerm_public_ip" "vpn_gateway_pip" {
      name                = "VPNGatewayPIP"
      location            = azurerm_virtual_network.hybrid_vnet.location
      resource_group_name = azurerm_virtual_network.hybrid_vnet.resource_group_name
      allocation_method   = "Dynamic"
    }

    resource "azurerm_virtual_network_gateway" "vpn_gateway" {
      name                = "VPNGateway"
      location            = azurerm_virtual_network.hybrid_vnet.location
      resource_group_name = azurerm_virtual_network.hybrid_vnet.resource_group_name

      type     = "Vpn"
      vpn_type = "

RouteBased"

      sku {
        name = "VpnGw1"
      }

      ip_configuration {
        name                          = "vpngateway"
        public_ip_address_id          = azurerm_public_ip.vpn_gateway_pip.id
        private_ip_address_allocation = "Dynamic"
        subnet_id                     = azurerm_subnet.subnet.id
      }
    }
    ```

3. **Configure On-Premises VPN Device**:
    (This step involves configuring your on-premises VPN device manually to establish a connection with the Azure VPN Gateway using the settings provided in the Azure portal.)

4. **Create a Connection**:
    ```hcl
    resource "azurerm_local_network_gateway" "on_prem_gateway" {
      name                = "OnPremGateway"
      location            = azurerm_virtual_network.hybrid_vnet.location
      resource_group_name = azurerm_virtual_network.hybrid_vnet.resource_group_name

      gateway_address     = "OnPremisesVPNPublicIPAddress"
      address_space       = ["OnPremisesNetworkAddressSpace"]
    }

    resource "azurerm_virtual_network_gateway_connection" "vpn_connection" {
      name                           = "VPNConnection"
      location                       = azurerm_virtual_network.hybrid_vnet.location
      resource_group_name            = azurerm_virtual_network.hybrid_vnet.resource_group_name
      virtual_network_gateway_id     = azurerm_virtual_network_gateway.vpn_gateway.id
      local_network_gateway_id       = azurerm_local_network_gateway.on_prem_gateway.id
      connection_type                = "IPsec"
      shared_key                     = "YourSharedKey"

      enable_bgp                     = false
      ipsec_policy {
        sa_lifetime = 3600
        sa_data_size = 1024
        ipsec_encryption = "AES256"
        ipsec_integrity = "SHA256"
        ike_encryption = "AES256"
        ike_integrity = "SHA256"
        dh_group = "DHGroup14"
        pfs_group = "PFS2"
      }
    }
    ```

**Explanation**:
1. **Create the Azure VNet**: The script defines a virtual network and subnet for the hybrid network.
2. **Deploy a VPN Gateway**: This part sets up a VPN Gateway for site-to-site connectivity.
3. **Configure On-Premises VPN Device**: Configuration on the on-premises VPN device to establish a connection with Azure VPN Gateway.
4. **Create a Connection**: The script establishes a VPN connection between the on-premises network and the Azure VNet.

### Service Chaining with Azure Application Gateway

**Scenario**: A SaaS provider needs to route traffic to different backend services based on specific URL paths.

**Terraform Script**:

1. **Create the Application Gateway**:
    ```hcl
    provider "azurerm" {
      features {}
    }

    resource "azurerm_resource_group" "rg" {
      name     = "AppGatewayResourceGroup"
      location = "East US"
    }

    resource "azurerm_virtual_network" "vnet" {
      name                = "VNet"
      address_space       = ["10.0.0.0/16"]
      location            = azurerm_resource_group.rg.location
      resource_group_name = azurerm_resource_group.rg.name
    }

    resource "azurerm_subnet" "subnet" {
      name                 = "default"
      resource_group_name  = azurerm_virtual_network.vnet.resource_group_name
      virtual_network_name = azurerm_virtual_network.vnet.name
      address_prefixes     = ["10.0.1.0/24"]
    }

    resource "azurerm_public_ip" "app_gateway_pip" {
      name                = "AppGatewayPIP"
      location            = azurerm_resource_group.rg.location
      resource_group_name = azurerm_resource_group.rg.name
      allocation_method   = "Static"
      sku                 = "Standard"
    }

    resource "azurerm_application_gateway" "app_gateway" {
      name                = "AppGateway"
      location            = azurerm_resource_group.rg.location
      resource_group_name = azurerm_resource_group.rg.name

      sku {
        name     = "Standard_v2"
        tier     = "Standard_v2"
        capacity = 2
      }

      gateway_ip_configuration {
        name      = "appGatewayIpConfig"
        subnet_id = azurerm_subnet.subnet.id
      }

      frontend_ip_configuration {
        name                 = "appGatewayFrontendIpConfig"
        public_ip_address_id = azurerm_public_ip.app_gateway_pip.id
      }

      frontend_port {
        name = "httpPort"
        port = 80
      }

      backend_address_pool {
        name = "backendPool1"
      }

      backend_address_pool {
        name = "backendPool2"
      }

      backend_http_settings {
        name                  = "httpSettings1"
        cookie_based_affinity = "Disabled"
        port                  = 80
        protocol              = "Http"
        request_timeout       = 20
      }

      backend_http_settings {
        name                  = "httpSettings2"
        cookie_based_affinity = "Disabled"
        port                  = 80
        protocol              = "Http"
        request_timeout       = 20
      }

      http_listener {
        name                           = "httpListener"
        frontend_ip_configuration_name = "appGatewayFrontendIpConfig"
        frontend_port_name             = "httpPort"
        protocol                       = "Http"
      }

      request_routing_rule {
        name                       = "routingRule1"
        rule_type                  = "Basic"
        http_listener_name         = "httpListener"
        backend_address_pool_name  = "backendPool1"
        backend_http_settings_name = "httpSettings1"
      }

      request_routing_rule {
        name                       = "routingRule2"
        rule_type                  = "PathBasedRouting"
        http_listener_name         = "httpListener"
        url_path_map {
          default_backend_address_pool_name  = "backendPool1"
          default_backend_http_settings_name = "httpSettings1"

          path_rule {
            name                       = "pathRule"
            paths                      = ["/api/v1/orders"]
            backend_address_pool_name  = "backendPool2"
            backend_http_settings_name = "httpSettings2"
          }
        }
      }
    }
    ```

2. **Deploy Backend Services**:
    ```hcl
    resource "azurerm_network_interface" "nic1" {
      name                = "NIC1"
      location            = azurerm_resource_group.rg.location
      resource_group_name = azurerm_resource_group.rg.name

      ip_configuration {
        name                          = "internal"
        subnet_id                     = azurerm_subnet.subnet.id
        private_ip_address_allocation = "Dynamic"
      }
    }

    resource "azurerm_network_interface" "nic2" {
      name                = "NIC2"
      location            = azurerm_resource_group.rg.location
      resource_group_name = azurerm_resource_group.rg.name

      ip_configuration {
        name                          = "internal"
        subnet_id                     = azurerm_subnet.subnet.id
        private_ip_address_allocation = "Dynamic"
      }
    }

    resource "azurerm_virtual_machine" "backend1" {
      name                  = "BackendVM1"
      location              = azurerm_resource_group.rg.location
      resource_group_name   = azurerm_resource_group.rg.name
      network_interface_ids = [azurerm_network_interface.nic1.id]
      vm_size               = "Standard_DS1_v2"

      storage_os_disk {
        name              = "osdisk"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Standard_LRS"
      }

      os_profile {
        computer_name  = "backendvm1"
        admin_username = "adminuser"
        admin_password = "Password123!"
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      source_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "18.04-LTS"
        version   = "latest"
      }
    }

    resource "azurerm_virtual_machine" "backend2" {
      name                  = "BackendVM2"
      location              = azurerm_resource_group.rg.location
      resource_group_name   = azurerm_resource_group.rg.name
      network_interface_ids = [azurerm_network_interface.nic2.id]
      vm_size               = "Standard_DS1_v2"

      storage_os_disk {
        name              = "osdisk"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Standard_LRS"
      }

      os_profile {
        computer_name  = "backendvm2"
        admin_username = "adminuser"
        admin_password = "Password123!"
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      source_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "18.04-LTS"
        version   = "latest"
      }
    }
    ```

**Explanation**:
1. **Create the Application Gateway**: The script defines an Application Gateway with public IP and backend pools, routing rules for different paths.
2. **Deploy Backend Services**: Two VMs are deployed to serve as backend services for different routes in the Application Gateway.

### Network Segmentation with Subnets

**Scenario**: A university wants to segment its network to isolate different departments and enhance security.

**Terraform Script**:



1. **Create the University VNet**:
    ```hcl
    provider "azurerm" {
      features {}
    }

    resource "azurerm_virtual_network" "university_vnet" {
      name                = "University-VNet"
      address_space       = ["10.0.0.0/16"]
      location            = "East US"
      resource_group_name = "UniversityResourceGroup"
    }
    ```

2. **Create Subnets for Departments**:
    ```hcl
    resource "azurerm_subnet" "admin_subnet" {
      name                 = "Admin-Subnet"
      resource_group_name  = azurerm_virtual_network.university_vnet.resource_group_name
      virtual_network_name = azurerm_virtual_network.university_vnet.name
      address_prefixes     = ["10.0.1.0/24"]
    }

    resource "azurerm_subnet" "research_subnet" {
      name                 = "Research-Subnet"
      resource_group_name  = azurerm_virtual_network.university_vnet.resource_group_name
      virtual_network_name = azurerm_virtual_network.university_vnet.name
      address_prefixes     = ["10.0.2.0/24"]
    }

    resource "azurerm_subnet" "student_subnet" {
      name                 = "Student-Subnet"
      resource_group_name  = azurerm_virtual_network.university_vnet.resource_group_name
      virtual_network_name = azurerm_virtual_network.university_vnet.name
      address_prefixes     = ["10.0.3.0/24"]
    }
    ```

3. **Deploy Resources in Each Subnet**:
    ```hcl
    resource "azurerm_network_interface" "admin_nic" {
      name                = "AdminNIC"
      location            = azurerm_virtual_network.university_vnet.location
      resource_group_name = azurerm_virtual_network.university_vnet.resource_group_name

      ip_configuration {
        name                          = "internal"
        subnet_id                     = azurerm_subnet.admin_subnet.id
        private_ip_address_allocation = "Dynamic"
      }
    }

    resource "azurerm_virtual_machine" "admin_vm" {
      name                  = "AdminVM"
      location              = azurerm_virtual_network.university_vnet.location
      resource_group_name   = azurerm_virtual_network.university_vnet.resource_group_name
      network_interface_ids = [azurerm_network_interface.admin_nic.id]
      vm_size               = "Standard_DS1_v2"

      storage_os_disk {
        name              = "osdisk"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Standard_LRS"
      }

      os_profile {
        computer_name  = "adminvm"
        admin_username = "adminuser"
        admin_password = "Password123!"
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      source_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "18.04-LTS"
        version   = "latest"
      }
    }

    resource "azurerm_network_interface" "research_nic" {
      name                = "ResearchNIC"
      location            = azurerm_virtual_network.university_vnet.location
      resource_group_name = azurerm_virtual_network.university_vnet.resource_group_name

      ip_configuration {
        name                          = "internal"
        subnet_id                     = azurerm_subnet.research_subnet.id
        private_ip_address_allocation = "Dynamic"
      }
    }

    resource "azurerm_virtual_machine" "research_vm" {
      name                  = "ResearchVM"
      location              = azurerm_virtual_network.university_vnet.location
      resource_group_name   = azurerm_virtual_network.university_vnet.resource_group_name
      network_interface_ids = [azurerm_network_interface.research_nic.id]
      vm_size               = "Standard_DS1_v2"

      storage_os_disk {
        name              = "osdisk"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Standard_LRS"
      }

      os_profile {
        computer_name  = "researchvm"
        admin_username = "adminuser"
        admin_password = "Password123!"
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      source_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "18.04-LTS"
        version   = "latest"
      }
    }

    resource "azurerm_network_interface" "student_nic" {
      name                = "StudentNIC"
      location            = azurerm_virtual_network.university_vnet.location
      resource_group_name = azurerm_virtual_network.university_vnet.resource_group_name

      ip_configuration {
        name                          = "internal"
        subnet_id                     = azurerm_subnet.student_subnet.id
        private_ip_address_allocation = "Dynamic"
      }
    }

    resource "azurerm_virtual_machine" "student_vm" {
      name                  = "StudentVM"
      location              = azurerm_virtual_network.university_vnet.location
      resource_group_name   = azurerm_virtual_network.university_vnet.resource_group_name
      network_interface_ids = [azurerm_network_interface.student_nic.id]
      vm_size               = "Standard_DS1_v2"

      storage_os_disk {
        name              = "osdisk"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Standard_LRS"
      }

      os_profile {
        computer_name  = "studentvm"
        admin_username = "adminuser"
        admin_password = "Password123!"
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      source_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "18.04-LTS"
        version   = "latest"
      }
    }
    ```

4. **Configure Network Security Groups (NSGs)**:
    ```hcl
    resource "azurerm_network_security_group" "admin_nsg" {
      name                = "AdminNSG"
      location            = azurerm_virtual_network.university_vnet.location
      resource_group_name = azurerm_virtual_network.university_vnet.resource_group_name

      security_rule {
        name                       = "AllowVNetInBound"
        priority                   = 100
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "*"
        source_port_range          = "*"
        destination_port_range     = "*"
        source_address_prefix      = "VirtualNetwork"
        destination_address_prefix = "VirtualNetwork"
      }

      security_rule {
        name                       = "AllowInternetOutBound"
        priority                   = 200
        direction                  = "Outbound"
        access                     = "Allow"
        protocol                   = "*"
        source_port_range          = "*"
        destination_port_range     = "*"
        source_address_prefix      = "VirtualNetwork"
        destination_address_prefix = "Internet"
      }
    }

    resource "azurerm_network_security_group" "research_nsg" {
      name                = "ResearchNSG"
      location            = azurerm_virtual_network.university_vnet.location
      resource_group_name = azurerm_virtual_network.university_vnet.resource_group_name

      security_rule {
        name                       = "AllowVNetInBound"
        priority                   = 100
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "*"
        source_port_range          = "*"
        destination_port_range     = "*"
        source_address_prefix      = "VirtualNetwork"
        destination_address_prefix = "VirtualNetwork"
      }

      security_rule {
        name                       = "AllowInternetOutBound"
        priority                   = 200
        direction                  = "Outbound"
        access                     = "Allow"
        protocol                   = "*"
        source_port_range          = "*"
        destination_port_range     = "*"
        source_address_prefix      = "VirtualNetwork"
        destination_address_prefix = "Internet"
      }
    }

    resource "azurerm_network_security_group" "student_nsg" {
      name                = "StudentNSG"
      location            = azurerm_virtual_network.university_vnet.location
      resource_group_name = azurerm_virtual_network.university_vnet.resource_group_name

      security_rule {
        name                       = "AllowVNetInBound"
        priority                   = 100
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "*"
        source_port_range          = "*"
        destination_port_range     = "*"
        source_address_prefix      = "VirtualNetwork"
        destination_address_prefix = "VirtualNetwork"
      }

      security_rule {
        name                       = "AllowInternetOutBound"
        priority                   = 200
        direction                  = "Outbound"
        access                     = "Allow"
        protocol                   = "*"
        source_port_range          = "*"
        destination_port_range     = "*"
        source_address_prefix      = "VirtualNetwork"
        destination_address_prefix = "Internet"
      }
    }

    resource "azurerm_subnet_network_security_group_association" "admin_subnet_nsg" {
      subnet_id                 = azurerm_subnet.admin_subnet.id
      network_security_group_id = azurerm_network_security_group.admin_nsg.id
    }

    resource "azurerm_subnet_network_security_group_association" "research_subnet_nsg" {
      subnet_id                 = azurerm_subnet

.research_subnet.id
      network_security_group_id = azurerm_network_security_group.research_nsg.id
    }

    resource "azurerm_subnet_network_security_group_association" "student_subnet_nsg" {
      subnet_id                 = azurerm_subnet.student_subnet.id
      network_security_group_id = azurerm_network_security_group.student_nsg.id
    }
    ```

**Explanation**:
1. **Create the University VNet**: The script defines a virtual network for the university.
2. **Create Subnets for Departments**: Subnets are created for different departments: Admin, Research, and Students.
3. **Deploy Resources in Each Subnet**: VMs are deployed in each subnet to represent departmental resources.
4. **Configure Network Security Groups (NSGs)**: NSGs are applied to control traffic between subnets and the internet.

### Bastion Host

**Scenario**: A software development company needs to provide secure remote access to its virtual machines in Azure without exposing them to the public internet.

**Terraform Script**:

1. **Create the VNet and Subnet**:
    ```hcl
    provider "azurerm" {
      features {}
    }

    resource "azurerm_virtual_network" "devops_vnet" {
      name                = "DevOps-VNet"
      address_space       = ["10.0.0.0/16"]
      location            = "East US"
      resource_group_name = "DevOpsResourceGroup"
    }

    resource "azurerm_subnet" "bastion_subnet" {
      name                 = "AzureBastionSubnet"
      resource_group_name  = azurerm_virtual_network.devops_vnet.resource_group_name
      virtual_network_name = azurerm_virtual_network.devops_vnet.name
      address_prefixes     = ["10.0.1.0/24"]
    }
    ```

2. **Deploy the Bastion Host**:
    ```hcl
    resource "azurerm_public_ip" "bastion_pip" {
      name                = "BastionPIP"
      location            = azurerm_virtual_network.devops_vnet.location
      resource_group_name = azurerm_virtual_network.devops_vnet.resource_group_name
      allocation_method   = "Static"
      sku                 = "Standard"
    }

    resource "azurerm_bastion_host" "bastion" {
      name                = "BastionHost"
      location            = azurerm_virtual_network.devops_vnet.location
      resource_group_name = azurerm_virtual_network.devops_vnet.resource_group_name

      dns_name = "bastionhost"

      ip_configuration {
        name                 = "configuration"
        subnet_id            = azurerm_subnet.bastion_subnet.id
        public_ip_address_id = azurerm_public_ip.bastion_pip.id
      }
    }
    ```

3. **Deploy Virtual Machines**:
    ```hcl
    resource "azurerm_subnet" "vm_subnet" {
      name                 = "VM-Subnet"
      resource_group_name  = azurerm_virtual_network.devops_vnet.resource_group_name
      virtual_network_name = azurerm_virtual_network.devops_vnet.name
      address_prefixes     = ["10.0.2.0/24"]
    }

    resource "azurerm_network_interface" "vm_nic" {
      name                = "VMNIC"
      location            = azurerm_virtual_network.devops_vnet.location
      resource_group_name = azurerm_virtual_network.devops_vnet.resource_group_name

      ip_configuration {
        name                          = "internal"
        subnet_id                     = azurerm_subnet.vm_subnet.id
        private_ip_address_allocation = "Dynamic"
      }
    }

    resource "azurerm_virtual_machine" "dev_vm" {
      name                  = "DevVM"
      location              = azurerm_virtual_network.devops_vnet.location
      resource_group_name   = azurerm_virtual_network.devops_vnet.resource_group_name
      network_interface_ids = [azurerm_network_interface.vm_nic.id]
      vm_size               = "Standard_DS1_v2"

      storage_os_disk {
        name              = "osdisk"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Standard_LRS"
      }

      os_profile {
        computer_name  = "devvm"
        admin_username = "adminuser"
        admin_password = "Password123!"
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      source_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "18.04-LTS"
        version   = "latest"
      }
    }
    ```

**Explanation**:
1. **Create the VNet and Subnet**: The script defines a virtual network and subnet for the Bastion host.
2. **Deploy the Bastion Host**: This part sets up an Azure Bastion Host with a public IP.
3. **Deploy Virtual Machines**: A VM is deployed in a separate subnet, accessible through the Bastion Host.

These detailed Terraform scripts and explanations provide a comprehensive approach to implementing various network design patterns in Azure with real-world examples.
