---
title: Virtuálisgép-fürt létrehozása az Azure-ban Terraform-modulokkal
description: Megtudhatja, hogyan használhatók a Terraform-modulok Windows rendszerű virtuálisgép-fürtök létrehozásra az Azure-ban
services: terraform
ms.service: terraform
keywords: terraform, devops, virtuális gép, hálózat, modulok
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/19/2017
ms.openlocfilehash: d5a820da94254fe0da416d9219f7582d3a4241cb
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55696166"
---
# <a name="create-a-vm-cluster-with-terraform-using-the-module-registry"></a>Virtuálisgép-fürt létrehozása a Terraformmal a Modul Registryvel

Ez a cikk lépésről lépésre bemutatja, hogyan hozható létre kisméretű virtuálisgép-fürt a Terraform [Azure Compute-moduljával](https://registry.terraform.io/modules/Azure/compute/azurerm/1.0.2). Ezen oktatóanyag segítségével megtanulhatja a következőket: 

> [!div class="checklist"]
> * Hitelesítés beállítása az Azure-ral
> * Terraform-sablon létrehozása
> * A módosítások vizualizációja egy tervben
> * A konfiguráció alkalmazása a virtuálisgép-fürt létrehozásához

A Terraformról a [Terraform dokumentációjában](https://www.terraform.io/docs/index.html) talál további információt.

## <a name="set-up-authentication-with-azure"></a>Hitelesítés beállítása az Azure-ral

> [!TIP]
> Ha [Terraform környezeti változókat használ](/azure/virtual-machines/linux/terraform-install-configure) vagy az [Azure Cloud Shellben](/azure/cloud-shell/overview) futtatja ezt az oktatóanyagot, hagyja ki ezt a lépést.

 Tekintse át a [Terraform telepítésével és az Azure-hoz való hozzáféréssel](/azure/virtual-machines/linux/terraform-install-configure) foglalkozó cikket az Azure-szolgáltatásnév létrehozásához. Használja ezt a szolgáltatásnevet egy új `azureProviderAndCreds.tf` fájl kitöltéséhez egy üres könyvtárban az alábbi kóddal:

```tf
variable subscription_id {}
variable tenant_id {}
variable client_id {}
variable client_secret {}

provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

## <a name="create-the-template"></a>A sablon létrehozása

Hozzon létre egy új, `main.tf` nevű Terraform-sablont az alábbi paranccsal:

```tf
module mycompute {
    source = "Azure/compute/azurerm"
    resource_group_name = "myResourceGroup"
    location = "East US 2"
    admin_password = "ComplxP@assw0rd!"
    vm_os_simple = "WindowsServer"
    remote_port = "3389"
    nb_instances = 2
    public_ip_dns = ["unique_dns_name"]
    vnet_subnet_id = "${module.network.vnet_subnets[0]}"
}

module "network" {
    source = "Azure/network/azurerm"
    location = "East US 2"
    resource_group_name = "myResourceGroup"
}

output "vm_public_name" {
    value = "${module.mycompute.public_ip_dns_name}"
}

output "vm_public_ip" {
    value = "${module.mycompute.public_ip_address}"
}

output "vm_private_ips" {
    value = "${module.mycompute.network_interface_private_ip}"
}
```

Futtassa a `terraform init` parancsot a konfiguráció könyvtárában. Ha a Terraform legalább 0.10.6-os verzióját használja, az alábbi kimenet jelenik meg:

![Terraform Init](media/terraformInitWithModules.png)

## <a name="visualize-the-changes-with-plan"></a>A módosítások vizualizációja egy tervben

Futtassa a `terraform plan` parancsot a sablon által létrehozott virtuálisgép-infrastruktúra előnézetéhez.

![Terraform-terv](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)


## <a name="create-the-virtual-machines-with-apply"></a>Virtuális gépek létrehozása a sablon alkalmazásával

Futtassa a `terraform apply` parancsot a virtuális gépek kiépítéséhez az Azure-on.

![Terraform alkalmazása](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>További lépések

- Az [Azure Terraform-modulok](https://registry.terraform.io/modules/Azure) listájának áttekintése
- Hozzon létre egy [virtuálisgép-méretezési csoportot a Terraformmal](terraform-create-vm-scaleset-network-disks-hcl.md)
