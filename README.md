
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
   ```

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
