---
title: Oktatóanyag – Helyszíni virtuális hálózat létrehozása az Azure-ban a Terraform használatával
description: Oktatóanyag, amely bemutatja, hogyan valósítható meg egy helyszíni virtuális hálózat az Azure-ban, amely helyi erőforrásokat is képes
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 361f9919fdd406a1fef6bbf2b7512dbc20266a54
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74159218"
---
# <a name="tutorial-create-on-premises-virtual-network-in-azure-using-terraform"></a>Oktatóanyag: Helyszíni virtuális hálózat létrehozása az Azure-ban a Terraform használatával

Ez az oktatóanyag bemutatja, hogyan valósítható meg egy helyszíni hálózat egy Azure virtuális hálózat (VNet) használatával. Az Azure virtuális hálózat ot lecserélheti a saját privát virtuális hálózatával. Ehhez rendelje hozzá a megfelelő IP-címeket az alhálózatokban.

A következő feladatokat ismertetik:

> [!div class="checklist"]
> * HCL (HashiCorp Language) használata helyszíni virtuális hálózat megvalósításához a hub-küllős topológiában
> * Hub hálózati berendezés erőforrásainak létrehozása a Terraform segítségével
> * Helyszíni virtuális gép létrehozása a Terraform segítségével
> * Helyszíni virtuális magánhálózati átjáró létrehozása a Terraform segítségével

## <a name="prerequisites"></a>Előfeltételek

1. [Hozzon létre egy hub ot és a küllős hibrid hálózati topológiát a Terraform segítségével az Azure-ban.](./terraform-hub-spoke-introduction.md)

## <a name="create-the-directory-structure"></a>A könyvtárstruktúra létrehozása

A helyszíni hálózat szimulálásához hozzon létre egy Azure-beli virtuális hálózatot. A bemutató virtuális hálózat egy tényleges helyszíni magánhálózat helyét veszi át. Ha ugyanezt szeretné tenni a meglévő helyszíni hálózattal, rendelje hozzá a megfelelő IP-címeket az alhálózatokban.

1. Keresse fel az [Azure Portalt](https://portal.azure.com).

1. Nyissa meg [az Azure Cloud Shell](/azure/cloud-shell/overview)t. Ha még nem választott ki környezetet, válassza a **Bash** környezetet.

    ![Cloud Shell-parancssor](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. Lépjen be a `clouddrive` könyvtárba.

    ```bash
    cd clouddrive
    ```

1. Lépjen be az új könyvtárba:

    ```bash
    cd hub-spoke
    ```

## <a name="declare-the-on-premises-vnet"></a>A helyszíni virtuális hálózat deklarálása

Hozza létre a helyszíni virtuális hálózatdeklaráló Terraform konfigurációs fájlt.

1. A Cloud Shellben nyisson `on-prem.tf`meg egy új nevű fájlt.

    ```bash
    code on-prem.tf
    ```

1. Másolja az alábbi kódot a szerkesztőbe:

    ```hcl
    locals {
      onprem-location       = "SouthCentralUS"
      onprem-resource-group = "onprem-vnet-rg"
      prefix-onprem         = "onprem"
    }

    resource "azurerm_resource_group" "onprem-vnet-rg" {
      name     = local.onprem-resource-group
      location = local.onprem-location
    }

    resource "azurerm_virtual_network" "onprem-vnet" {
      name                = "onprem-vnet"
      location            = azurerm_resource_group.onprem-vnet-rg.location
      resource_group_name = azurerm_resource_group.onprem-vnet-rg.name
      address_space       = ["192.168.0.0/16"]

      tags {
        environment = local.prefix-onprem
      }
    }

    resource "azurerm_subnet" "onprem-gateway-subnet" {
      name                 = "GatewaySubnet"
      resource_group_name  = azurerm_resource_group.onprem-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.onprem-vnet.name
      address_prefix       = "192.168.255.224/27"
    }

    resource "azurerm_subnet" "onprem-mgmt" {
      name                 = "mgmt"
      resource_group_name  = azurerm_resource_group.onprem-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.onprem-vnet.name
      address_prefix       = "192.168.1.128/25"
    }

    resource "azurerm_public_ip" "onprem-pip" {
        name                         = "${local.prefix-onprem}-pip"
        location            = azurerm_resource_group.onprem-vnet-rg.location
        resource_group_name = azurerm_resource_group.onprem-vnet-rg.name
        allocation_method   = "Dynamic"

        tags {
            environment = local.prefix-onprem
        }
    }

    resource "azurerm_network_interface" "onprem-nic" {
      name                 = "${local.prefix-onprem}-nic"
      location             = azurerm_resource_group.onprem-vnet-rg.location
      resource_group_name  = azurerm_resource_group.onprem-vnet-rg.name
      enable_ip_forwarding = true

      ip_configuration {
        name                          = local.prefix-onprem
        subnet_id                     = azurerm_subnet.onprem-mgmt.id
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = azurerm_public_ip.onprem-pip.id
      }
    }

    # Create Network Security Group and rule
    resource "azurerm_network_security_group" "onprem-nsg" {
        name                = "${local.prefix-onprem}-nsg"
        location            = azurerm_resource_group.onprem-vnet-rg.location
        resource_group_name = azurerm_resource_group.onprem-vnet-rg.name

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

        tags {
            environment = "onprem"
        }
    }

    resource "azurerm_subnet_network_security_group_association" "mgmt-nsg-association" {
      subnet_id                 = azurerm_subnet.onprem-mgmt.id
      network_security_group_id = azurerm_network_security_group.onprem-nsg.id
    }

    resource "azurerm_virtual_machine" "onprem-vm" {
      name                  = "${local.prefix-onprem}-vm"
      location              = azurerm_resource_group.onprem-vnet-rg.location
      resource_group_name   = azurerm_resource_group.onprem-vnet-rg.name
      network_interface_ids = [azurerm_network_interface.onprem-nic.id]
      vm_size               = var.vmsize

      storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04-LTS"
        version   = "latest"
      }

      storage_os_disk {
        name              = "myosdisk1"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Standard_LRS"
      }

      os_profile {
        computer_name  = "${local.prefix-onprem}-vm"
        admin_username = var.username
        admin_password = var.password
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags {
        environment = local.prefix-onprem
      }
    }

    resource "azurerm_public_ip" "onprem-vpn-gateway1-pip" {
      name                = "${local.prefix-onprem}-vpn-gateway1-pip"
      location            = azurerm_resource_group.onprem-vnet-rg.location
      resource_group_name = azurerm_resource_group.onprem-vnet-rg.name

      allocation_method = "Dynamic"
    }

    resource "azurerm_virtual_network_gateway" "onprem-vpn-gateway" {
      name                = "onprem-vpn-gateway1"
      location            = azurerm_resource_group.onprem-vnet-rg.location
      resource_group_name = azurerm_resource_group.onprem-vnet-rg.name

      type     = "Vpn"
      vpn_type = "RouteBased"

      active_active = false
      enable_bgp    = false
      sku           = "VpnGw1"

      ip_configuration {
        name                          = "vnetGatewayConfig"
        public_ip_address_id          = azurerm_public_ip.onprem-vpn-gateway1-pip.id
        private_ip_address_allocation = "Dynamic"
        subnet_id                     = azurerm_subnet.onprem-gateway-subnet.id
      }
      depends_on = ["azurerm_public_ip.onprem-vpn-gateway1-pip"]

    }
    ```

1. Mentse a fájlt, és zárja be a szerkesztőt.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Hub virtuális hálózat létrehozása a Terraform segítségével az Azure-ban](./terraform-hub-spoke-hub-network.md)