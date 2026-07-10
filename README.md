# aks_cluster

terraform-provision-aks-cluster# 🚀 Create AKS Cluster Using Terraform

This repository demonstrates how to create an Azure Kubernetes Service (AKS) cluster using Terraform, including architecture, workflow, and complete Terraform code.

===========================================================

📌 **Overview**

    AKS is Azure’s managed Kubernetes service.
    Terraform is used as Infrastructure as Code (IaC) to provision AKS resources in a repeatable and automated way.
    
================================================================

🔄 **Terraform Workflow**

terraform init    → Initialize Terraform
terraform plan    → Review execution plan
terraform apply   → Create AKS resources
terraform destroy → Delete all resources

================================================================

📂 **Project Structure**

aks-terraform/

│── provider.tf

│── main.tf

│── variables.tf

│── outputs.tf

│── README.md


=================================================================

✅ **Prerequisites**

    Azure Subscription
    Terraform >= 1.3
    Azure CLI
    kubectl

Login to Azure

az login
az account set --subscription "<SUBSCRIPTION_ID>"

==================================================================

⚙️ **Terraform Code**


📄 provider.tf

terraform {
  required_version = ">= 1.3"

  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 3.100"
    }
  }
}

provider "azurerm" {
  features {}
}

===================================================================

📄 variables.tf

variable "resource_group_name" {
  description = "AKS resource group name"
  default     = "aks-rg"
}

variable "location" {
  description = "Azure region"
  default     = "centralus"
}

variable "aks_name" {
  description = "AKS cluster name"
  default     = "prd-aks"
}

variable "node_count" {
  description = "Number of worker nodes"
  default     = 2
}

variable "vm_size" {
  description = "VM size for AKS nodes"
  default     = "Standard_D2ls_v7"
}

=================================================================

📄 main.tf

resource "azurerm_resource_group" "rg" {
  name     = var.resource_group_name
  location = var.location
}

resource "azurerm_kubernetes_cluster" "aks_name" {
  name                = "${var.resource_group_name}-aks"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  dns_prefix          = "prdaks"

  default_node_pool {
    name       = "system"
    node_count = 1
    vm_size    = "Standard_D2ls_v7"

    upgrade_settings {
     max_surge = "1"
    }
  }

  identity {
    type = "SystemAssigned"
  }

  role_based_access_control_enabled = true

  network_profile {
    network_plugin = "azure"
  }

  tags = {
    environment = "prd"
  }
}

📄 outputs.tf

output "aks_name" {
  description = "AKS cluster name"
  value       = azurerm_kubernetes_cluster.aks.name
}

==============================================================

🚀 **Deployment Steps**

terraform init
terraform plan
terraform apply

===============================================================

⏳ AKS creation takes approximately 5–10 minutes.
🔗 Connect to AKS

az aks get-credentials \
  --resource-group aks-rg \
  --name demo-aks

kubectl get nodes

================================================================

🧩 Azure Resources Created

    Azure Resource Group
    Virtual Network & Subnet
    AKS Cluster
    Managed Control Plane
    VM Scale Set (Worker Nodes)
    Load Balancer
    Managed Identity
    Network Security Groups

=================================================================

⭐ **Best Practices**

    Use remote backend (Azure Storage Account)
    Separate Terraform modules
    Use multiple node pools
    Enable autoscaling
    Enable RBAC and monitoring
    
=============================================================================================

🧹 Cleanup

    terraform destroy
    
