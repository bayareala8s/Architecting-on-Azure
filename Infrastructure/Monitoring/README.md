### Azure Network Watcher Service

Azure Network Watcher is a service that provides tools to monitor, diagnose, view metrics, and enable or disable logs for resources in an Azure virtual network. It provides network diagnostic and visualization tools to help you understand, diagnose, and gain insights to your network in Azure.

Here are some of the key features of Azure Network Watcher:

1. **Topology:** Allows you to view the network topology of your deployments.

2. **IP Flow Verify:** Checks if a packet is allowed or denied to or from a virtual machine. The information consists of direction, protocol, local IP, remote IP, local port, and remote port.

3. **Next Hop:** Determines the next hop for packets being routed in the Azure Network Fabric, enabling you to diagnose any misconfigured user-defined routes.

4. **VPN Diagnostics:** Helps you diagnose VPN Gateway connectivity issues.

5. **NSG Flow Logs:** Network security group (NSG) flow logs are a feature of Network Watcher that allows you to view information about ingress and egress IP traffic through an NSG.

6. **Connection Monitor:** Monitors the network connectivity to service and VM endpoints.

7. **Network Performance Monitor:** Detects network issues like traffic blackholing and packet loss.

8. **Packet Capture:** Allows you to create capture sessions to track traffic in and out of a virtual machine.

Remember, to use Network Watcher, you must have it enabled for the region where your resources are deployed.


### Use Azure Network Watcher to troubleshoot network connectivity issues in my Azure virtual network

Azure Network Watcher provides several tools to help troubleshoot network connectivity issues in your Azure virtual network. Here are some steps you can follow:

1. **Enable Network Watcher:** Network Watcher is not enabled by default. You need to enable it for your subscription and the region where your virtual network is located.

2. **Use IP Flow Verify:** If you suspect a problem with communication to or from a VM, you can use the IP Flow Verify capability. This tool will tell you whether a connection can be established between a VM and a given endpoint, based on the current network security group (NSG) rules.

3. **Use Next Hop:** If you're having issues with routing, you can use the Next Hop capability. This tool allows you to investigate the next hop type and IP address for a specified virtual machine.

4. **Use Connection Troubleshoot:** If you're having issues with a connection to a specific port on a VM, you can use the Connection Troubleshoot capability. This tool checks for connectivity from a VM to a specified port on a destination.

5. **Use Network Performance Monitor (NPM):** NPM can detect network issues like traffic blackholing and packet loss. It can monitor networks for performance degradation and outages.

6. **Use NSG Flow Logs:** NSG flow logs allow you to view information about ingress and egress IP traffic through an NSG. This can be useful for understanding the traffic flow through a network security group.

Remember, these tools are most effective when used together. The exact steps and tools you use will depend on the specific network issue you're trying to troubleshoot.


### Capture network traffic using Azure Network Watcher

To capture network traffic using Azure Network Watcher, you can use the Packet Capture tool. Here's how you can do it using Azure CLI:

1. **Install Azure CLI:** Make sure you have Azure CLI installed on your machine. If not, you can download it from the official Microsoft website.

2. **Login to Azure:** Open your terminal and login to your Azure account using the following command:

   ```bash
   az login
   ```

3. **Start a Packet Capture:** You can start a packet capture on a VM with the following command. Replace the placeholders with your actual values.

   ```bash
   az network watcher packet-capture create --resource-group MyResourceGroup --vm MyVM --name MyPacketCapture --file-path "mypacketcapture.cap"
   ```

   This command will start a packet capture named "MyPacketCapture" on the VM named "MyVM" in the resource group "MyResourceGroup". The captured packets will be stored in the file "mypacketcapture.cap".

4. **Stop a Packet Capture:** When you're done, you can stop the packet capture with the following command:

   ```bash
   az network watcher packet-capture stop --resource-group MyResourceGroup --name MyPacketCapture
   ```

5. **Download and Analyze the Capture File:** After stopping the packet capture, you can download the capture file and analyze it using a tool like Wireshark.

Remember, to use Network Watcher, you must have it enabled for the region where your resources are deployed.
