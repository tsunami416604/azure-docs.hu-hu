---
title: Üzembe helyezés az Azure Cloud Shell Terraform szolgáltatásával | Microsoft dokumentumok
description: Üzembe helyezés az Azure Cloud Shell Terraform szolgáltatásával
services: Azure
documentationcenter: ''
author: tomarchermsft
manager: routlaw
tags: azure-cloud-shell
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: tarcher
ms.openlocfilehash: 8bacadd8941131f608411e61cc15c120c1b2bc60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79458154"
---
# <a name="deploy-with-terraform-from-bash-in-azure-cloud-shell"></a>Üzembe helyezés a Terraform segítségével az Azure Cloud Shellben
Ez a cikk bemutatja, hogy hozzon létre egy erőforráscsoportot a [Terraform AzureRM-szolgáltatóval.](https://www.terraform.io/docs/providers/azurerm/index.html)

[A Hashicorp Terraform](https://www.terraform.io/) egy nyílt forráskódú eszköz, amely az API-kat deklaratív konfigurációs fájlokká kodifikálja, amelyek megoszthatók a csapattagok között, hogy szerkeszthetők, felülvizsgálhatók és verziószámba járjanak. A Microsoft AzureRM-szolgáltató az Azure Resource Manager által az AzureRM API-kon keresztül támogatott erőforrásokkal való interakcióra szolgál.

## <a name="automatic-authentication"></a>Automatikus hitelesítés
A Terraform alapértelmezés szerint a Bash in Cloud Shell ben van telepítve. Emellett a Cloud Shell automatikusan hitelesíti az alapértelmezett Azure CLI-előfizetést, hogy erőforrásokat telepítsen a Terraform Azure-modulokon keresztül.

Terraform az alapértelmezett Azure CLI-előfizetés, amely be van állítva. Az alapértelmezett előfizetések frissítéséhez futtassa a következőt:

```azurecli-interactive
az account set --subscription mySubscriptionName
```

## <a name="walkthrough"></a>Útmutatás
### <a name="launch-bash-in-cloud-shell"></a>Bash indítása a Cloud Shellben
1. Indítsa el a Cloud Shellt az ön által preferált helyről
2. Az előnyben részesített előfizetés beállítása

```azurecli-interactive
az account show
```

### <a name="create-a-terraform-template"></a>Terraform sablon létrehozása
Hozzon létre egy új Terraform sablont, amelynek neve main.tf a kívánt szövegszerkesztővel.

```
vim main.tf
```

Másolja/illessze be a következő kódot a Cloud Shellbe.

```
resource "azurerm_resource_group" "myterraformgroup" {
    name = "myRgName"
    location = "West US"
}
```

Mentse a fájlt, és lépjen ki a szövegszerkesztőből.

### <a name="terraform-init"></a>Terraform init
Kezdje a `terraform init`futtatásával.

```
justin@Azure:~$ terraform init

Initializing provider plugins...

The following providers do not have any version constraints in configuration,
so the latest version was installed.

To prevent automatic upgrades to new major versions that may contain breaking
changes, it is recommended to add version = "..." constraints to the
corresponding provider blocks in configuration, with the constraint strings
suggested below.

* provider.azurerm: version = "~> 0.2"

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

A [terraform init parancs](https://www.terraform.io/docs/commands/init.html) terraform konfigurációs fájlokat tartalmazó munkakönyvtár inicializálására szolgál. A `terraform init` parancs az első parancs, amelyet egy új Terraform-konfiguráció megírása vagy egy meglévő klónozása után kell futtatni a verziókövetésből. A parancs többször is biztonságos.

### <a name="terraform-plan"></a>Terraform plan
Tekintse meg a Terraform sablon által `terraform plan`létrehozandó erőforrások előnézetét a segítségével.

```
justin@Azure:~$ terraform plan
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


------------------------------------------------------------------------

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.demo
      id:       <computed>
      location: "westus"
      name:     "myRGName"
      tags.%:   <computed>


Plan: 1 to add, 0 to change, 0 to destroy.

------------------------------------------------------------------------

Note: You didn't specify an "-out" parameter to save this plan, so Terraform
can't guarantee that exactly these actions will be performed if
"terraform apply" is subsequently run.
```

A [terraform plan parancs](https://www.terraform.io/docs/commands/plan.html) egy végrehajtási tervet hoz létre. A Terraform frissítést hajt végre, kivéve, ha kifejezetten le van tiltva, majd meghatározza, hogy milyen műveletek szükségesek a konfigurációs fájlokban megadott kívánt állapot eléréséhez. A terv menthető a -out használatával, majd a terraform-hoz biztosítva, hogy csak az előre tervezett műveletek et hajtsák végre.

### <a name="terraform-apply"></a>Terraform apply
Az Azure-erőforrások `terraform apply`kiépítése a használatával.

```
justin@Azure:~$ terraform apply
azurerm_resource_group.demo: Creating...
  location: "" => "westus"
  name:     "" => "myRGName"
  tags.%:   "" => "<computed>"
azurerm_resource_group.demo: Creation complete after 0s (ID: /subscriptions/mySubIDmysub/resourceGroups/myRGName)

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

A [terraform apply parancs](https://www.terraform.io/docs/commands/apply.html) a konfiguráció kívánt állapotának eléréséhez szükséges módosítások alkalmazására szolgál.

### <a name="verify-deployment-with-azure-cli"></a>Üzembe helyezés ellenőrzése az Azure CLI-vel
Futtassa `az group show -n myRgName` az erőforrás kiépítésének sikeres ellenőrzését.

```azurecli-interactive
az group show -n myRgName
```

### <a name="clean-up-with-terraform-destroy"></a>Tisztítsák meg terraform megsemmisítése
A [Terraform megsemmisítési paranccsal](https://www.terraform.io/docs/commands/destroy.html) létrehozott erőforráscsoport karbantartása a Terraform által létrehozott infrastruktúra megtisztításához.

```
justin@Azure:~$ terraform destroy
azurerm_resource_group.demo: Refreshing state... (ID: /subscriptions/mySubID/resourceGroups/myRGName)

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  - azurerm_resource_group.demo


Plan: 0 to add, 0 to change, 1 to destroy.

Do you really want to destroy?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

azurerm_resource_group.demo: Destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 10s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 20s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 30s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 40s elapsed)
azurerm_resource_group.demo: Destruction complete after 45s

Destroy complete! Resources: 1 destroyed.
```

Sikeresen létrehozott egy Azure-erőforrást a Terraform segítségével. Látogasson el a következő lépésekre, hogy tovább tudjon többet a Cloud Shellről.

## <a name="next-steps"></a>További lépések
[További információ a Terraform Azure-szolgáltatóról](https://www.terraform.io/docs/providers/azurerm/#)<br>
[Bash a Cloud Shell rövid útmutató](quickstart.md)
