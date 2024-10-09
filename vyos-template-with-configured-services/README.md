# VyOS Deployment ARM Template

This Azure Resource Manager (ARM) template deploys a VyOS network appliance on an Azure Virtual Network with both public and private subnets. It includes configurations for network security groups (NSG), route tables, and static IP addresses for the VyOS instance.

## Features
- Deploys a VyOS virtual machine in a defined region.
- Creates a virtual network with two subnets: public and private.
- Configures Network Security Groups (NSGs) for controlling inbound traffic to the VyOS instance.
- Supports static public and private IP addresses for the VyOS VM.
- Configures the necessary routing table for network traffic through the VyOS instance.
- Allows for SSH, HTTP, OpenVPN, WireGuard, and IPsec communication via predefined NSG rules.

Additionally, this deployment configures key networking services including:

- **Remote Access VPN (WireGuard)**: Secure VPN for remote access to internal network resources using WireGuard.
- **Site-to-Site VPN**: Establish a secure connection between the Azure Virtual Network and on-premises networks.
- **BGP (Border Gateway Protocol)**: Dynamic routing between different networks, supporting failover and redundancy.
- **NAT (Network Address Translation)**: Handles source address translation for outgoing traffic.
- **DNS Forwarding**: VyOS is configured to forward DNS queries to external DNS servers.
- **Firewall**: Pre-configured firewall rules for SSH, WireGuard, OpenVPN, ICMP, and ESP traffic, with specific security settings for each protocol.

## Architecture Overview
This template creates the following resources in Azure:
- **Virtual Network**: Consists of a public and private subnet.
- **Network Security Groups**: For managing traffic to and from the VyOS instance.
- **Route Table**: Routes traffic through the VyOS instance.
- **Public IP**: Static IP address for accessing the VyOS instance.
- **VyOS Virtual Machine**: A network appliance running VyOS, configured via user-provided authentication.

## Parameters
- **adminUsername**: The username for the VyOS VM (default: `vyos_test`).
- **authenticationType**: Type of authentication (`password` or `sshPublicKey`). SSH is recommended for better security.
- **adminPasswordOrKey**: The password or SSH key for the VM.
- **azureInstanceBgpASnumber:** The BGP AS number for the VyOS instance in Azure (default: `65501`).
- **onPremInstanceBgpASnumber:** The BGP AS number for the on-premises VyOS instance (default: `65502`).
- **onPremInstancePublicIP:** The Public IP Address for the on-premises VyOS instance.
- **resourceTags**: Tags for all resources, useful for tracking and categorization.

## Variables
- **Virtual Network and Subnets**: 
  - Virtual network CIDR: `10.1.0.0/16`
  - Public Subnet: `10.1.11.0/24`
  - Private Subnet: `10.1.1.0/24`
- **VyOS VM**:
  - Static Private IP: `10.1.1.11`
  - Static Public IP: `10.1.11.11`
  - VM Size: `Standard_D2s_v3`

## Network Security Group Rules
- **SSH (Port 22)**: For management access.
- **WireGuard (Port 2224)**: For VPN connectivity.
- **OpenVPN (Port 1194)**: For OpenVPN access.
- **ESP Protocol**: For VPN encryption.
- **IKE (Port 500)**: For IPsec VPN negotiation.
- **IPsec (Port 1701)**: For IPsec VPN communication.
- **NAT Traversal (Port 4500)**: For IPsec NAT traversal.
- **ICMP**: For network diagnostics (ping).

## Outputs
- **adminUsername**: The username for the VyOS VM.
- **adminPasswordOrKey**: The password or SSH key for the VM.
- **VyOS-01-Public-IP**: The public IP address of the VyOS instance.

## Prerequisites
- An Azure account with sufficient permissions to create and manage Azure resources, including virtual machines, networks, public IP addresses, and network security groups.
- Ensure you have created a resource group where the VyOS deployment will take place
- You can install the Azure CLI using this [guide](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli).
- For Azure PowerShell, follow this [installation guide](https://learn.microsoft.com/en-us/powershell/azure/install-az-ps).

## Usage
1. Update the parameter file or the template directly with your specific values, especially for the `adminUsername` and `adminPasswordOrKey` parameters.
2. Deploy the template using the Azure CLI, Azure PowerShell, or through the Azure Portal.
   
   Example Azure CLI command:
   ```bash
   az deployment group create --resource-group <your-resource-group> --template-file vyos-deployment.json
  
## Notes
- The VyOS VM is deployed with a basic configuration. Additional VyOS configurations can be applied post-deployment.
- The template uses a standard VM size (`Standard_D2s_v3`), which can be adjusted based on your performance needs.

## SSH Access to VyOS
After successfully deploying the VyOS instance, follow these steps to access it via SSH.
From your deployment, the following outputs were generated:
- **Username**: `<USERNAME>`
- **Password or SSH Key**: `<PASSWORD>`
- **Public IP**: `<PUBLIC IP>`
Use the following command to access the VyOS instance via SSH:
```bash
ssh <USERNAME>@<PUBLIC IP>
```
If you are using an SSH key pair for authentication, use the following command, ensuring you provide the correct path to your private key:
```bash
ssh -i /path/to/private-key <USERNAME>@<PUBLIC IP>
```
