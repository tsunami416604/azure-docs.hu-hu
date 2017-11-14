---
title: "A Virtuálisgép-fürt létrehozása az Azure Terraform modulok segítségével"
description: "Terraform modulok használata a Windows virtuális gép fürt létrehozása az Azure-ban"
keywords: "terraform, a devops, a virtuális gépet, a hálózati, a modulok"
author: rloutlaw
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure
ms.date: 10/19/2017
ms.custom: devops
ms.author: routlaw
ms.openlocfilehash: 23d79fa4a1794a6dea69e6ae24da714babf54e62
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2017
---
# <a name="create-a-vm-cluster-with-terraform-using-the-module-registry"></a>A modul beállításjegyzékkel Terraform hozzon létre egy Virtuálisgép-fürt

Ez a cikk végigvezeti egy kis Virtuálisgép-fürt létrehozása a Terraform [Azure számítási modul](https://registry.terraform.io/modules/Azure/compute/azurerm/1.0.2). Ezen oktatóanyag segítségével megtanulhatja a következőket: 

> [!div class="checklist"]
> * Az Azure-ral hitelesítés beállítása
> * A Terraform sablon létrehozása
> * A módosítások tervvel megjelenítése
> * A Virtuálisgép-fürt létrehozása a konfiguráció alkalmazásához

Terraform további információkért tekintse meg a [Terraform dokumentáció](https://www.terraform.io/docs/index.html).

## <a name="set-up-authentication-with-azure"></a>Az Azure-ral hitelesítés beállítása

> [!TIP]
> Ha Ön [Terraform környezeti változókkal](/azure/virtual-machines/linux/terraform-install-configure#set-environment-variables) , vagy futtassa az oktatóanyag a [Azure Cloud rendszerhéj](/azure/cloud-shell/overview), kihagyhatja ezt a lépést.

 Felülvizsgálati [Terraform telepítése és konfigurálása az Azure-hozzáférést](/azure/virtual-machines/linux/terraform-install-configure) hozzon létre egy Azure szolgáltatás egyszerű. A szolgáltatás egyszerű használata egy új fájl feltöltéséhez `azureProviderAndCreds.tf` egy üres Directory, az alábbi kódra:

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

Hozzon létre egy új Terraform sablont nevű `main.tf` az alábbi kódra:

```tf
module mycompute {
    source = "Azure/compute/azurerm"
    resource_group_name = "myResourceGroup"
    location = "East US 2"
    admin_password = "ComplxP@assw0rd!"
    vm_os_simple = "WindowsServer"
    remote_port = "3389"
    nb_instances = 2
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

output = "vm_public_ip" {
    value = "${module.mycompute.public_ip_address}"
}

output "vm_private_ips" {
    value = "${module.mycompute.network_interface_private_ip}"
}
```

Futtatás `terraform init` a konfigurációs könyvtárban. Legalább 0.10.6 Terraform verzióját használja a következő eredményét mutatja:

![Terraform Init](media/terraformInitWithModules.png)

## <a name="visualize-the-changes-with-plan"></a>A módosítások tervvel megjelenítése

Futtatás `terraform plan` előzetes a virtuális gép infrastruktúra, a sablon által létrehozott.

![Terraform terv](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)


## <a name="create-the-virtual-machines-with-apply"></a>Az alkalmazás a virtuális gépek létrehozása

Futtatás `terraform apply` az Azure virtuális gépek telepítéséhez.

![Terraform alkalmazása](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Következő lépések

- Keresse meg a listában a [Azure Terraform modulok](https://registry.terraform.io/modules/Azure)
- Hozzon létre egy [virtuálisgép-méretezési rendelkező Terraform](terraform-create-vm-scaleset-network-disks-hcl.md)