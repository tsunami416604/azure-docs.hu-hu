---
title: Terraform telepítése és konfigurálása Azure-erőforrások kiépítéséhez
description: Ismerje meg, hogyan telepítheti és konfigurálhatja az Azure-erőforrások létrehozásához szükséges Terraform
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tomarchermsft
manager: gwallace
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/20/2019
ms.author: tarcher
ms.openlocfilehash: efba440448ac912b7656eeab017eef947ab25e95
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034687"
---
# <a name="install-and-configure-terraform-to-provision-azure-resources"></a>Terraform telepítése és konfigurálása Azure-erőforrások kiépítéséhez
 
A Terraform segítségével egyszerűen meghatározhatja, megtekintheti és üzembe helyezheti a felhőalapú infrastruktúrát egy [egyszerű sablonos nyelv](https://www.terraform.io/docs/configuration/syntax.html)használatával. Ez a cikk a Terraform Azure-beli erőforrások kiépítéséhez szükséges lépéseit ismerteti.

Ha többet szeretne megtudni arról, hogyan használható az Terraform az Azure-ban, látogasson el az [Terraform hub](/azure/terraform)webhelyére.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

A Terraform alapértelmezés szerint telepítve van a [Cloud Shell](/azure/terraform/terraform-cloud-shell). Ha úgy dönt, hogy helyileg telepíti a Terraform-t, hajtsa végre a következő lépést, máskülönben folytassa a [Terraform-hozzáférés beállítását az Azure-hoz](#set-up-terraform-access-to-azure).

## <a name="install-terraform"></a>A Terraform telepítése

A Terraform telepítéséhez [töltse le](https://www.terraform.io/downloads.html) az operációs rendszerének megfelelő csomagot egy különálló telepítési könyvtárba. A letöltés egyetlen végrehajtható fájlt tartalmaz, amelynek globális elérési utat is meg kell határoznia. A Linux-és Mac-eszközök elérési útjának beállításával kapcsolatos útmutatásért keresse fel [ezt a weblapot](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux). Az elérési út Windows rendszeren való beállításával kapcsolatos utasításokért keresse fel [ezt a weblapot](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows).

Ellenőrizze az elérési út konfigurációját a `terraform` paranccsal. Megjelenik az elérhető Terraform lehetőségek listája, ahogy az alábbi példában is látható:

```console
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>Terraform-hozzáférés beállítása az Azure-hoz

Ahhoz, hogy a Terraform erőforrásokat lehessen kiépíteni az Azure-ba, hozzon létre egy [Azure ad egyszerű szolgáltatásnevet](/cli/azure/create-an-azure-service-principal-azure-cli). Az egyszerű szolgáltatásnév az Azure-előfizetésében lévő erőforrások kiépítéséhez biztosít Terraform-parancsfájlokat.

Ha több Azure-előfizetéssel rendelkezik, először kérdezze le fiókját az [az Account List](/cli/azure/account#az-account-list) paranccsal, és kérje le az előfizetés-azonosító és a bérlői azonosító értékeinek listáját:

```azurecli-interactive
az account list --query "[].{name:name, subscriptionId:id, tenantId:tenantId}"
```

Ha a kiválasztott előfizetést szeretné használni, állítsa be ennek a munkamenetnek az előfizetését az [az Account set](/cli/azure/account#az-account-set)paranccsal. Állítsa be a `SUBSCRIPTION_ID` környezeti változót a használni kívánt előfizetéshez tartozó visszaadott `id` mező értékének megtartásához:

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

Most létrehozhat egy egyszerű szolgáltatásnevet a Terraform-hez való használatra. Használja az [az ad SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac), és állítsa be a *hatókört* az előfizetésre a következőképpen:

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

A rendszer `appId`, `password`, `sp_name`és `tenant` ad vissza. Jegyezze fel a `appId` és a `password`.

## <a name="configure-terraform-environment-variables"></a>Terraform környezeti változók konfigurálása

Ha a Terraform-t az Azure AD-szolgáltatásnév használatára szeretné konfigurálni, állítsa be az alábbi környezeti változókat, amelyeket az [Azure Terraform-modulok](https://registry.terraform.io/modules/Azure)használnak. A környezetet akkor is beállíthatja, ha az Azure-felhőn kívül más Azure-felhővel dolgozik.

- `ARM_SUBSCRIPTION_ID`
- `ARM_CLIENT_ID`
- `ARM_CLIENT_SECRET`
- `ARM_TENANT_ID`
- `ARM_ENVIRONMENT`

A következő minta rendszerhéj-parancsfájl használatával állíthatja be a változókat:

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

Hozzon létre egy fájlt `test.tf` egy üres könyvtárban, és illessze be az alábbi szkriptet.

```hcl
provider "azurerm" {
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

Mentse a fájlt, majd inicializálja a Terraform-telepítést. Ez a lépés letölti az Azure-erőforráscsoport létrehozásához szükséges Azure-modulokat.

```bash
terraform init
```

A kimenet a következő példához hasonló:

```console
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

Megtekintheti a Terraform-parancsfájl által a `terraform plan`használatával végrehajtandó műveleteket. Ha készen áll az erőforráscsoport létrehozására, alkalmazza a Terraform tervet a következőképpen:

```bash
terraform apply
```

A kimenet a következő példához hasonló:

```console
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

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben telepítette a Terraform-t, vagy használta a Cloud Shell az Azure-beli hitelesítő adatok konfigurálásához és az erőforrások létrehozásának megkezdéséhez az Azure-előfizetésében. A Terraform üzembe helyezéséhez az Azure-ban a következő cikkben talál további információt:

> [!div class="nextstepaction"]
> [Azure-beli virtuális gép létrehozása a Terraform](terraform-create-complete-vm.md)
