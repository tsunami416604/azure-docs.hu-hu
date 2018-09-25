---
title: Terraform az Azure Cloud Shell Bash az üzembe helyezés |} A Microsoft Docs
description: Terraform az Azure Cloud Shell Bash az üzembe helyezés
services: Azure
documentationcenter: ''
author: tomarcher
manager: routlaw
tags: azure-cloud-shell
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: tarcher
ms.openlocfilehash: 8512c04cb0efc698ca688724c3806291bb02d200
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46947647"
---
# <a name="deploy-with-terraform-from-bash-in-azure-cloud-shell"></a>Terraform az Azure Cloud Shell Bash az üzembe helyezés
Ez a cikk végigkalauzolja egy erőforráscsoportot a [Terraform AzureRM szolgáltató](https://www.terraform.io/docs/providers/azurerm/index.html). 

[A Hashicorp Terraform](https://www.terraform.io/) egy nyílt forráskódú eszköz, amely egységes API-k deklaratív konfigurációs fájlokat, amelyek többek között a szerkesztett, tekintse át a csapattagok megosztott, és a rendszerverzióval ellátott be. A Microsoft-AzureRM-szolgáltató segítségével lépjen kapcsolatba az Azure Resource Manager az AzureRM-API-kon keresztül által támogatott erőforrásokkal. 

## <a name="automatic-authentication"></a>Automatikus hitelesítéshez
A Terraform alapértelmezés szerint telepítve van a Cloud Shell bashben. Emellett a Cloud Shell automatikusan hitelesíti az alapértelmezett Azure CLI-vel előfizetés erőforrásai a a Terraform az Azure-modulok telepítéséhez.

A Terraform használja az alapértelmezett Azure CLI-előfizetést, amely be van állítva. Alapértelmezett előfizetések frissítéséhez futtassa:

```azurecli-interactive
az account set --subscription mySubscriptionName
```

## <a name="walkthrough"></a>Útmutatás
### <a name="launch-bash-in-cloud-shell"></a>Indítsa el a Cloud Shell Bash
1. Az elsődleges helyről a Cloud Shell indítása
2. Az előnyben részesített előfizetés ellenőrzése

```azurecli-interactive
az account show
```

### <a name="create-a-terraform-template"></a>Terraform-sablon létrehozása
Terraform az előnyben részesített szövegszerkesztővel main.tf nevű új sablont létrehozni.

```
vim main.tf
```

Másolja és illessze be a következő kód Cloud shellbe.

```
resource "azurerm_resource_group" "myterraformgroup" {
    name = "myRgName"
    location = "West US"
}
```

Mentse a fájlt, és lépjen ki a szövegszerkesztőben.

### <a name="terraform-init"></a>A Terraform init
Első lépésként futó `terraform init`.

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

A [terraform init paranccsal](https://www.terraform.io/docs/commands/init.html) inicializálni egy működő könyvtárba, Terraform konfigurációs fájljait tartalmazó szolgál. A `terraform init` az első parancs, amely egy új Terraform konfigurációjának írása, vagy egy meglévőt a verziókezeléshez klónozása után kell futtatni. Biztonságosan futtatható a parancs többször.

### <a name="terraform-plan"></a>Terraform plan
A Terraform sablont kell létrehoznia az erőforrások előzetes `terraform plan`.

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

A [terraform plan parancs](https://www.terraform.io/docs/commands/plan.html) egy végrehajtási tervet hoz létre. A Terraform hajt végre egy frissítés, kivéve, ha explicit módon le van tiltva, és ezután határozza meg, milyen műveletek szükségesek a kívánt állapot konfigurációs fájlokban megadott eléréséhez. A terv használatával menthetők-ki, és a terraform, feltéve, hogy a alkalmazni annak érdekében, hogy csak az előre tervezett műveletek végrehajtása.

### <a name="terraform-apply"></a>Terraform apply
Az Azure-erőforrások kiépítése `terraform apply`.

```
justin@Azure:~$ terraform apply
azurerm_resource_group.demo: Creating...
  location: "" => "westus"
  name:     "" => "myRGName"
  tags.%:   "" => "<computed>"
azurerm_resource_group.demo: Creation complete after 0s (ID: /subscriptions/mySubIDmysub/resourceGroups/myRGName)

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

A [terraform a alkalmazni parancs](https://www.terraform.io/docs/commands/apply.html) szolgál az elérni kívánt állapotát, a konfiguráció szükséges módosítások alkalmazásához.

### <a name="verify-deployment-with-azure-cli"></a>Az Azure CLI-vel a telepítés ellenőrzése
Futtatás `az group show -n myRgName` ellenőrizze az erőforrás-kiépítés sikeres.

```azcliinteractive
az group show -n myRgName
```

### <a name="clean-up-with-terraform-destroy"></a>Távolítsa el a terraform használatával megszüntetése
Távolítsa el a létrehozott erőforráscsoportot a [Terraform semmisítse meg a parancs](https://www.terraform.io/docs/commands/destroy.html) infrastruktúra a Terraform által létrehozott karbantartása.

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

Sikeresen létrehozott egy Azure-erőforrás a Terraform használatával. Látogasson el a következő lépések többet szeretne megtudni a Cloud Shellben.

## <a name="next-steps"></a>További lépések
[További tudnivalók az Terraform Azure-szolgáltató](https://www.terraform.io/docs/providers/azurerm/#)<br>
[A rövid útmutatóban a Cloud Shell bash](quickstart.md)
