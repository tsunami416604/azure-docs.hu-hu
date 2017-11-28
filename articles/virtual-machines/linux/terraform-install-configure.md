---
title: "Telepítse és konfigurálja a virtuális gépek és más Azure-infrastruktúra kiépítése Terraform |} Microsoft Docs"
description: "Megtudhatja, hogyan telepítse és konfigurálja az Azure-erőforrások létrehozásához Terraform"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jtalkar
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/23/2017
ms.author: echuvyrov
ms.openlocfilehash: c156776103a466af8923ba7249d96835ff339268
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>Telepítse és konfigurálja az Azure virtuális gépek és egyéb infrastruktúra kiépítéséhez Terraform
 
Terraform segítségével határozza meg, tekintse meg és központi telepítése a felhő-infrastruktúra használatával egyszerűen egy [egyszerű templating nyelvi](https://www.terraform.io/docs/configuration/syntax.html). Ez a cikk ismerteti a szükséges lépéseket az Azure-erőforrások kiépítése Terraform használandó. 

> [!TIP]
Az Azure-ral Terraform használatával kapcsolatos további tudnivalókért keresse fel a [Terraform Hub](/azure/terraform). Alapértelmezés szerint telepítve van a Terraform a [felhő rendszerhéj](/azure/terraform/terraform-cloud-shell). Felhő-rendszerhéj használatával kihagyhatja a dokumentum telepítőben/részeit.

## <a name="install-terraform"></a>Terraform telepítése

Terraform, telepítendő [letöltése](https://www.terraform.io/downloads.html) egy különálló, az operációs rendszerhez megfelelő csomag telepítési könyvtár. A letöltés egy egyetlen végrehajtható fájlt, amelynek meg kell is meg a globális elérési utat tartalmaz. Hogyan kell beállítani az elérési utat a Linux és Mac útmutatásra van szüksége, látogasson el [a weblap](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux). Hogyan lehet beállítani az elérési út a Windows útmutatásra van szüksége, látogasson el [a weblap](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows). 

Ellenőrizze az elérési út konfigurációját, és a `terraform` parancsot. Az elérhető Terraform listáját output típusúként kell megjelennie:

```bash
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>Az Azure-bA Terraform hozzáférés beállítása

Konfigurálása [egy Azure AD szolgáltatás egyszerű](/cli/azure/create-an-azure-service-principal-azure-cli) Terraform az Azure erőforrások kiépítése lehetővé teszi, hogy. Az egyszerű szolgáltatásnév biztosít a Terraform parancsfájlok erőforrások kiépítése hitelesítő adatok használatát az Azure-előfizetéshez.

Több módon is hozzon létre egy Azure AD-alkalmazást és az Azure AD szolgáltatás egyszerű. A legegyszerűbb leggyorsabb módon ma pedig úgy, hogy használja az Azure CLI 2.0, amely [, töltse le és telepítse a Windows, Linux és Mac](/cli/azure/install-azure-cli).

Jelentkezzen be az Azure-előfizetéshez felügyeletéhez a következő parancsot:

   `az login`

Ha több Azure-előfizetéssel rendelkezik, azok adatai által visszaadott a `az login` parancsot. Állítsa be a `SUBSCRIPTION_ID` ahhoz, hogy a visszaadott érték környezeti változó `id` mező szeretné használni az előfizetésből. 

Állítsa be az ehhez a munkamenethez használni kívánt előfizetést.

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

A lekérdezés a fiókot az előfizetés-azonosító és a bérlői azonosító értékek lekérése.

```azurecli-interactive
az account show --query "{subscriptionId:id, tenantId:tenantId}"
```

Következő lépésként hozzon létre külön hitelesítő adatokat Terraform.

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

Visszaadja a appId, a jelszót, a sp_name és a bérlői. Jegyezze fel az appId és a jelszót.

A hitelesítő adatok ellenőrzéséhez nyisson meg egy új kezelőfelületet, és futtassa a következő parancsot, a visszaadott értékek használata sp_name, jelszót és bérlői:

```azurecli-interactive
az login --service-principal -u SP_NAME -p PASSWORD --tenant TENANT
az vm list-sizes --location westus
```

## <a name="configure-terraform-environment-variables"></a>Környezeti változók Terraform konfigurálása

A bérlő azonosítója, előfizetés-azonosító, ügyfél-Azonosítót és titkos a szolgáltatás egyszerű az ügyfél használni, Azure-erőforrások létrehozásakor Terraform konfigurálása. Állítsa be az alábbi környezeti változókat, amelyek automatikusan a [Azure Terraform modulok](https://registry.terraform.io/modules/Azure).

- ARM_SUBSCRIPTION_ID
- ARM_CLIENT_ID
- ARM_CLIENT_SECRET
- ARM_TENANT_ID

Ez a parancsfájlpélda rendszerhéj segítségével állítsa be ezeket a változókat:

```bash
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id
```

## <a name="run-a-sample-script"></a>Egy minta-parancsfájl futtatása

Hozzon létre egy fájlt `test.tf` egy üres könyvtárak és illessze be az alábbi parancsfájlt. 

```tf
provider "azurerm" {
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

Mentse a fájlt, majd futtassa `terraform init`. Ez a parancs letölti az Azure modulok egy Azure erőforráscsoport létrehozásához szükséges. A következő eredmény jelenik meg:

```
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

Tekintse meg a parancsprogram `terraform plan`, majd hozza létre a `testResouceGroup` erőforráscsoport `terraform apply`:

```
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

Rendelkezik Terraform telepített és konfigurált Azure hitelesítő adataival, így elindíthatja az Azure-előfizetéséhez olyan infrastruktúra üzembe. A telepítés hozzon létre egy üres Azure erőforráscsoport majd tesztelni.

> [!div class="nextstepaction"]
> [Hozzon létre egy Azure virtuális gép Terraform](terraform-create-complete-vm.md)

