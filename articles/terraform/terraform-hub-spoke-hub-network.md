---
title: Oktatóanyag – hub virtuális hálózat létrehozása az Azure-ban a Terraform használatával
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre olyan központi virtuális hálózatot az Azure-ban, amely közös kapcsolódási pontként működik más hálózatok között
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 25c4d6fa881f7ec6c96dd5ea7c935544374bc57d
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74077702"
---
# <a name="tutorial-create-a-hub-virtual-network-in-azure-by-using-terraform"></a>Oktatóanyag: hub virtuális hálózat létrehozása az Azure-ban a Terraform használatával

A hub virtuális hálózat központi kapcsolódási pontként működik a helyszíni hálózathoz. A virtuális hálózat a küllős virtuális hálózatokban üzemeltetett munkaterhelések által használt megosztott szolgáltatásokat üzemelteti. Bemutató célból egyetlen megosztott szolgáltatás sincs implementálva ebben az oktatóanyagban.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * A HashiCorp konfigurációs nyelv (HCL) használatával implementálhatja a hub virtuális hálózatot egy sugaras topológiában.
> * A Terraform használatával hozzon létre egy hub Jumpbox virtuális gépet.
> * A Terraform használatával hozzon létre egy hub virtuális magánhálózati átjárót.
> * Hub-és helyszíni átjáró-kapcsolatok létrehozásához használja a Terraform.

## <a name="prerequisites"></a>Előfeltételek

1. [Hozzon létre egy sugaras hibrid hálózati topológiát a Terraform az Azure-ban](./terraform-hub-spoke-introduction.md).
1. Helyszíni [virtuális hálózat létrehozása az Azure-beli Terraform](./terraform-hub-spoke-on-prem.md).

## <a name="create-the-directory-structure"></a>A könyvtárstruktúra létrehozása

A hub-hálózat a következő összetevőkből áll:

- Egy hub virtuális hálózat
- Hub virtuális hálózati átjáró
- Hub Gateway-kapcsolatok 

A következő Terraform-konfigurációs fájl határozza meg az erőforrásokat:

1. Keresse fel az [Azure Portalt](https://portal.azure.com).

1. Nyissa meg az [Azure Cloud Shellt](/azure/cloud-shell/overview). Ha még nem választott ki környezetet, válassza a **Bash** környezetet.

    ![Cloud Shell-parancssor](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. Lépjen be a `clouddrive` könyvtárba.

    ```bash
    cd clouddrive
    ```

1. Módosítsa a címtárakat az új könyvtárba.

    ```bash
    cd hub-spoke
    ```

## <a name="declare-the-hub-virtual-network"></a>A hub virtuális hálózat deklarálása

Hozza létre a hub virtuális hálózatot deklaráló Terraform-konfigurációs fájlt.

1. Hozzon létre egy `hub-vnet.tf` nevű fájlt a Cloud Shellben.

    ```bash
    code hub-vnet.tf
    ```

1. Másolja az alábbi kódot a szerkesztőbe:

    ```hcl
    locals {
      prefix-hub         = "hub"
      hub-location       = "CentralUS"
      hub-resource-group = "hub-vnet-rg"
      shared-key         = "4-v3ry-53cr37-1p53c-5h4r3d-k3y"
    }

    resource "azurerm_resource_group" "hub-vnet-rg" {
      name     = local.hub-resource-group
      location = local.hub-location
    }

    resource "azurerm_virtual_network" "hub-vnet" {
      name                = "${local.prefix-hub}-vnet"
      location            = azurerm_resource_group.hub-vnet-rg.location
      resource_group_name = azurerm_resource_group.hub-vnet-rg.name
      address_space       = ["10.0.0.0/16"]

      tags {
        environment = "hub-spoke"
      }
    }

    resource "azurerm_subnet" "hub-gateway-subnet" {
      name                 = "GatewaySubnet"
      resource_group_name  = azurerm_resource_group.hub-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.hub-vnet.name
      address_prefix       = "10.0.255.224/27"
    }

    resource "azurerm_subnet" "hub-mgmt" {
      name                 = "mgmt"
      resource_group_name  = azurerm_resource_group.hub-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.hub-vnet.name
      address_prefix       = "10.0.0.64/27"
    }

    resource "azurerm_subnet" "hub-dmz" {
      name                 = "dmz"
      resource_group_name  = azurerm_resource_group.hub-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.hub-vnet.name
      address_prefix       = "10.0.0.32/27"
    }

    resource "azurerm_network_interface" "hub-nic" {
      name                 = "${local.prefix-hub}-nic"
      location             = azurerm_resource_group.hub-vnet-rg.location
      resource_group_name  = azurerm_resource_group.hub-vnet-rg.name
      enable_ip_forwarding = true

      ip_configuration {
        name                          = local.prefix-hub
        subnet_id                     = azurerm_subnet.hub-mgmt.id
        private_ip_address_allocation = "Dynamic"
      }

      tags {
        environment = local.prefix-hub
      }
    }

    #Virtual Machine
    resource "azurerm_virtual_machine" "hub-vm" {
      name                  = "${local.prefix-hub}-vm"
      location              = azurerm_resource_group.hub-vnet-rg.location
      resource_group_name   = azurerm_resource_group.hub-vnet-rg.name
      network_interface_ids = [azurerm_network_interface.hub-nic.id]
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
        computer_name  = "${local.prefix-hub}-vm"
        admin_username = var.username
        admin_password = var.password
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags {
        environment = local.prefix-hub
      }
    }

    # Virtual Network Gateway
    resource "azurerm_public_ip" "hub-vpn-gateway1-pip" {
      name                = "hub-vpn-gateway1-pip"
      location            = azurerm_resource_group.hub-vnet-rg.location
      resource_group_name = azurerm_resource_group.hub-vnet-rg.name

      allocation_method = "Dynamic"
    }

    resource "azurerm_virtual_network_gateway" "hub-vnet-gateway" {
      name                = "hub-vpn-gateway1"
      location            = azurerm_resource_group.hub-vnet-rg.location
      resource_group_name = azurerm_resource_group.hub-vnet-rg.name

      type     = "Vpn"
      vpn_type = "RouteBased"

      active_active = false
      enable_bgp    = false
      sku           = "VpnGw1"

      ip_configuration {
        name                          = "vnetGatewayConfig"
        public_ip_address_id          = azurerm_public_ip.hub-vpn-gateway1-pip.id
        private_ip_address_allocation = "Dynamic"
        subnet_id                     = azurerm_subnet.hub-gateway-subnet.id
      }
      depends_on = ["azurerm_public_ip.hub-vpn-gateway1-pip"]
    }

    resource "azurerm_virtual_network_gateway_connection" "hub-onprem-conn" {
      name                = "hub-onprem-conn"
      location            = azurerm_resource_group.hub-vnet-rg.location
      resource_group_name = azurerm_resource_group.hub-vnet-rg.name

      type           = "Vnet2Vnet"
      routing_weight = 1

      virtual_network_gateway_id      = azurerm_virtual_network_gateway.hub-vnet-gateway.id
      peer_virtual_network_gateway_id = azurerm_virtual_network_gateway.onprem-vpn-gateway.id

      shared_key = local.shared-key
    }

    resource "azurerm_virtual_network_gateway_connection" "onprem-hub-conn" {
      name                = "onprem-hub-conn"
      location            = azurerm_resource_group.onprem-vnet-rg.location
      resource_group_name = azurerm_resource_group.onprem-vnet-rg.name
      type                            = "Vnet2Vnet"
      routing_weight = 1
      virtual_network_gateway_id      = azurerm_virtual_network_gateway.onprem-vpn-gateway.id
      peer_virtual_network_gateway_id = azurerm_virtual_network_gateway.hub-vnet-gateway.id

      shared_key = local.shared-key
    }
    ```
    
3. Mentse a fájlt, és zárja be a szerkesztőt.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"] 
> [Hub virtuális hálózati berendezés létrehozása Terraform az Azure-ban](./terraform-hub-spoke-hub-nva.md)