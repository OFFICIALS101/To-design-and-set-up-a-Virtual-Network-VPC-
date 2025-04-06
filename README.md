azure cli automation for beginner 
#!/bin/bash

# Variables
RESOURCE_GROUP="RG-VNet-Ireland"
LOCATION="northeurope"
VNET_NAME="VNet-Ireland"
PUBLIC_SUBNET="Subnet-Public"
PRIVATE_SUBNET="Subnet-Private"
PUBLIC_NSG="NSG-Public"
PRIVATE_NSG="NSG-Private"
NAT_GW="NAT-Gateway"
PUBLIC_IP="PublicIP-NAT"

PUBLIC_VM="PublicVM"
PRIVATE_VM="PrivateVM"
ADMIN_USER="azureuser"
PASSWORD="YourPassword123!"  # Replace or secure as needed

# 1. Create Resource Group
az group create --name $RESOURCE_GROUP --location $LOCATION

# 2. Create VNet and Subnets
az network vnet create --resource-group $RESOURCE_GROUP --name $VNET_NAME --address-prefix 10.1.0.0/16 --subnet-name $PUBLIC_SUBNET --subnet-prefix 10.1.1.0/24

az network vnet subnet create --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $PRIVATE_SUBNET --address-prefix 10.1.2.0/24

# 3. Create NSGs
az network nsg create --resource-group $RESOURCE_GROUP --name $PUBLIC_NSG --location $LOCATION
az network nsg create --resource-group $RESOURCE_GROUP --name $PRIVATE_NSG --location $LOCATION

# 4. NSG Rules
az network nsg rule create --resource-group $RESOURCE_GROUP --nsg-name $PUBLIC_NSG --name Allow-SSH --priority 100 --direction Inbound --access Allow --protocol Tcp --destination-port-range 22 --source-address-prefixes Internet --destination-address-prefixes '*'
az network nsg rule create --resource-group $RESOURCE_GROUP --nsg-name $PUBLIC_NSG --name Allow-HTTP --priority 110 --direction Inbound --access Allow --protocol Tcp --destination-port-range 80 --source-address-prefixes Internet --destination-address-prefixes '*'
az network nsg rule create --resource-group $RESOURCE_GROUP --nsg-name $PUBLIC_NSG --name Allow-HTTPS --priority 120 --direction Inbound --access Allow --protocol Tcp --destination-port-range 443 --source-address-prefixes Internet --destination-address-prefixes '*'

az network nsg rule create --resource-group $RESOURCE_GROUP --nsg-name $PRIVATE_NSG --name Allow-MySQL --priority 100 --direction Inbound --access Allow --protocol Tcp --destination-port-range 3306 --source-address-prefixes 10.1.1.0/24 --destination-address-prefixes '*'

# 5. Attach NSGs to Subnets
az network vnet subnet update --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $PUBLIC_SUBNET --network-security-group $PUBLIC_NSG
az network vnet subnet update --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $PRIVATE_SUBNET --network-security-group $PRIVATE_NSG

# 6. Create Public IP for NAT Gateway
az network public-ip create --resource-group $RESOURCE_GROUP --name $PUBLIC_IP --sku Standard --allocation-method Static --location $LOCATION

# 7. Create NAT Gateway
az network nat gateway create --resource-group $RESOURCE_GROUP --name $NAT_GW --public-ip-addresses $PUBLIC_IP --idle-timeout 10 --sku Standard --location $LOCATION

# 8. Attach NAT Gateway to Private Subnet
az network vnet subnet update --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $PRIVATE_SUBNET --nat-gateway $NAT_GW

# 9. Create Network Interfaces
az network nic create --resource-group $RESOURCE_GROUP --name "${PUBLIC_VM}-NIC" --vnet-name $VNET_NAME --subnet $PUBLIC_SUBNET --location $LOCATION --network-security-group $PUBLIC_NSG

az network nic create --resource-group $RESOURCE_GROUP --name "${PRIVATE_VM}-NIC" --vnet-name $VNET_NAME --subnet $PRIVATE_SUBNET --location $LOCATION --network-security-group $PRIVATE_NSG

# 10. Create Virtual Machines

# Public VM (Ubuntu)
az vm create --resource-group $RESOURCE_GROUP --name $PUBLIC_VM --location $LOCATION --nics "${PUBLIC_VM}-NIC" --image UbuntuLTS --admin-username $ADMIN_USER --admin-password $PASSWORD --public-ip-sku Standard --authentication-type password

# Private VM (Ubuntu)
az vm create --resource-group $RESOURCE_GROUP --name $PRIVATE_VM --location $LOCATION --nics "${PRIVATE_VM}-NIC" --image UbuntuLTS --admin-username $ADMIN_USER --admin-password $PASSWORD --authentication-type password --public-ip-address ""

echo "Setup complete! Public VM IP:"
az vm show --resource-group $RESOURCE_GROUP --name $PUBLIC_VM --show-details --query "publicIps" --output tsv

# Azure VNet Setup with Public and Private Subnets

This project automates the design and deployment of a secure Virtual Network (VNet) environment in **Microsoft Azure (North Europe - Ireland)** using **Azure CLI**. It includes both public and private subnets, routing, Network Security Groups (NSGs), a NAT Gateway for outbound access, and optional VM deployment.

---

## 🚀 Features

- 🔧 Creates a resource group and virtual network (VNet)
- 🌐 Configures public and private subnets with custom address spaces
- 🔐 Sets up NSGs for security rules (SSH, HTTP, HTTPS, MySQL)
- 🔁 Deploys NAT Gateway for secure internet access from the private subnet
- 💻 Optionally deploys Ubuntu VMs in each subnet
- 📍 Region: `North Europe (Ireland)`

---

## 📂 Folder Structure

```
.
├── setup-vnet.sh         # Azure CLI automation script
└── README.md             # Project documentation
```

---

## 🛠️ Prerequisites

- Azure CLI installed ([Install Guide](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli))
- Azure account with appropriate permissions
- Linux or WSL terminal recommended for running `.sh` script

---

## ⚙️ Usage

1. Clone the repository:
   ```bash
   git clone https://github.com/OFFICIALS101/azure-vnet-setup.git
   cd azure-vnet-setup
   ```

2. Make the script executable and run:
   ```bash
   chmod +x setup-vnet.sh
   ./setup-vnet.sh
   ```![public subnet](https://github.com/user-attachments/assets/42a9b6cf-0f32-40de-b52b-c94ef31000fb)

3. Wait for deployment to finish. The script will output the **Public VM's IP** address for SSH/web access.

---

## 🖥️ What Gets Deployed

- `VNet-Ireland` with CIDR `10.1.0.0/16`
- `Subnet-Public` and `Subnet-Private`
- NSG rules for:
  - Public access (SSH/HTTP/HTTPS)
  - Private access (from public subnet only)
- NAT Gateway for outbound internet access from private subnet
- Public and Private Ubuntu VMs (optional)

---

## 🔒 Security

- Public VM is secured with inbound NSG rules
- Private VM is only accessible from the public subnet (internal access only)
- NAT Gateway enables outbound-only internet traffic for the private subnet

---

## 📜 License

This project is licensed under the MIT License. See the `LICENSE` file for details.

---

## 🙌 Acknowledgments

Thanks to the Azure documentation and community for best practices on cloud network architecture.

---

## ✍️ Author

**Ayoade Josiah Ayomide**  
[ayoade705@gmail.com](mailto:ayoade705@gmail.com)  
[GitHub Profile](https://github.com/OFFICIALS101)
