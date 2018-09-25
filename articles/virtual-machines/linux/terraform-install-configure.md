---
title: Terraform telepítése és konfigurálása az Azure-ral való használathoz |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre Azure-erőforrások Terraform telepítése és konfigurálása
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/19/2018
ms.author: echuvyrov
ms.openlocfilehash: 0943bd1bffb3df7beda97ea0619f1aced4ca3a41
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946782"
---
# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>Virtuális gépek és egyéb infrastruktúra kiépítéséhez az Azure-bA Terraform telepítése és konfigurálása
 
A Terraform, előzetes verzió, és üzembe helyezése a felhő-infrastruktúra használatával egyszerű megoldást kínál a [egyszerű sablonalapú nyelvi](https://www.terraform.io/docs/configuration/syntax.html). Ez a cikk bemutatja az erőforrások kiépítése az Azure-ban a Terraform használata szükséges lépéseket.

A Terraform használata az Azure-ral kapcsolatos további tudnivalókért látogasson el a [Terraform Hub](/azure/terraform).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

A Terraform alapértelmezés szerint telepítve van a [Cloud Shell](/azure/terraform/terraform-cloud-shell). Ha helyi Terraform telepítése választja, a következő lépés végrehajtása, ellenkező esetben továbbra is [állítsa be az Azure Terraform hozzáférés](#set-up-terraform-access-to-azure).

## <a name="install-terraform"></a>A Terraform telepítése

Terraform, telepítendő [letöltése](https://www.terraform.io/downloads.html) a megfelelő csomag egy külön az operációs rendszer telepítési könyvtár. A letöltés egy egyetlen végrehajtható fájl, amelynek Ön is meg kell határozniuk globális elérési utat tartalmaz. Linux és Mac rendszerben az elérési út beállításával kapcsolatos utasításokért ugorjon [a weblap](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux). Hogyan állítható be az elérési út a Windows kapcsolatos utasításokért ugorjon [a weblap](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows).

Ellenőrizze az elérési út konfigurációját a a `terraform` parancsot. A Terraform rendelkezésre álló beállítások listája látható, az alábbi példa kimenetében látható módon:

```bash
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>Az Azure-ban a Terraform hozzáférés beállítása

Az erőforrások kiépítése a Terraform engedélyezéséhez az Azure-ba, hozzon létre egy [az Azure AD-szolgáltatásnév](/cli/azure/create-an-azure-service-principal-azure-cli). Egyszerű szolgáltatás engedélyezi a Terraform szkriptek erőforrások kiépítése az Azure-előfizetésében.

Ha több Azure-előfizetéssel rendelkezik, először lekérdezése fiókját [az fiók show](/cli/azure/account#az-account-show) előfizetés listáját azonosítója és bérlő azonosítója értékek beolvasásához:

```azurecli-interactive
az account show --query "{subscriptionId:id, tenantId:tenantId}"
```

A kijelölt előfizetésben használatához állítsa a munkamenet az előfizetés [fiók beállítása az](/cli/azure/account#az-account-set). Állítsa be a `SUBSCRIPTION_ID` környezeti változót, amely tárolja az értéket a visszaadott `id` mezőt a használni kívánt előfizetést:

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

Használja az egyszerű szolgáltatás most már a terraform használatával hozhat létre. Használata [az ad sp create-for-rbac]/cli/azure/ad/sp#az-ad-sp-create-for-rbac), és állítsa be a *hatókör* az előfizetéshez, az alábbiak szerint:

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

A `appId`, `password`, `sp_name`, és `tenant` adja vissza. Jegyezze fel a `appId` és `password`.

## <a name="configure-terraform-environment-variables"></a>A Terraform környezeti változók konfigurálása

Konfigurálja a Terraform használata az Azure AD egyszerű szolgáltatást, állítsa be az alábbi környezeti változókat, majd által használt a [Azure Terraform moduljaival](https://registry.terraform.io/modules/Azure). Ha egy Azure-felhő más, mint az Azure nyilvános is beállítaná a környezetet.

- `ARM_SUBSCRIPTION_ID`
- `ARM_CLIENT_ID`
- `ARM_CLIENT_SECRET`
- `ARM_TENANT_ID`
- `ARM_ENVIRONMENT`

Az alábbi shell-parancsprogram segítségével állítsa be ezeket a változókat:

```bash
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id

# Not needed for public, required for usgovernment, german, china
export ARM_ENVIRONMENT=public
```

## <a name="run-a-sample-script"></a>Minta parancsfájl futtatása

Hozzon létre egy fájlt `test.tf` egy üres könyvtárat és illessze be az alábbi parancsfájlt.

```tf
provider "azurerm" {
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

Mentse a fájlt, majd inicializálja a Terraform központi telepítés. Ezzel a lépéssel letölti az Azure-modulokat az Azure-erőforráscsoport létrehozásához szükséges.

```bash
terraform init
```

A kimenet a következő példához hasonló:

```bash
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

Megtekintheti, hogy a Terraform parancsfájlt kell kitöltenie a műveletek `terraform plan`. Amikor készen áll a hozza létre az erőforráscsoportot, a Terraform terv a következőképpen alkalmazni:

```bash
terraform apply
```

A kimenet a következő példához hasonló:

```bash
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.rg
      id:       <computed>
      location: "westus"
      name:     "testResourceGroup"
      tags.%:   <computed>

azurerm_resource_group.rg: Creating...
  location: "" => "westus"
  name:     "" => "testResourceGroup"
  tags.%:   "" => "<computed>"
azurerm_resource_group.rg: Creation complete after 1s
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben a Terraform telepítve, vagy a Cloud Shellben használt Azure hitelesítő adatainak konfigurálása, és kezdjen el erőforrásokat az Azure-előfizetésében. Részletesebb információ a Terraform üzembe helyezés létrehozása Azure-ban, tekintse meg a következő cikket:

> [!div class="nextstepaction"]
> [Egy Azure virtuális gép létrehozása terraform](terraform-create-complete-vm.md)
