---
title: "Telepítheti az Azure-felhőbe rendszerhéj Bash Terraform |} Microsoft Docs"
description: "Az Azure-felhőbe rendszerhéj Bash Terraform üzembe helyezéséhez"
services: Azure
documentationcenter: 
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
ms.openlocfilehash: 6df6a3a5242e0a5fc5c03136e1cd20967a93487a
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="deploy-with-terraform-from-bash-in-azure-cloud-shell"></a>Az Azure-felhőbe rendszerhéj Bash Terraform üzembe helyezéséhez
Ez a cikk bemutatja, hogyan nevű erőforráscsoport létrehozása a [Terraform AzureRM szolgáltató](https://www.terraform.io/docs/providers/azurerm/index.html). 

[Hashicorp Terraform](https://www.terraform.io/) nyílt forráskódú eszköz, amely egységes API-k, amely lehet szerkeszteni, tekintse át a csoport tagjai között megosztott és rendszerverzióval ellátott deklaratív konfigurációs fájlok. A Microsoft-AzureRM szolgáltató Azure Resource Manager segítségével a AzureRM API-k által támogatott erőforrások interakcióra használatos. 

## <a name="automatic-authentication"></a>Automatikus hitelesítéshez
Terraform alapértelmezés szerint telepítve van a felhő rendszerhéj Bash. Emellett a felhő rendszerhéj automatikusan az alapértelmezett Azure CLI 2.0 előfizetés erőforrásoknak a Terraform Azure modulok telepítése hitelesíti.

Terraform használja az alapértelmezett Azure CLI 2.0 előfizetés, amely be van állítva. Alapértelmezett előfizetések frissítéséhez futtassa:

```azurecli-interactive
az account set --subscription mySubscriptionName
```

## <a name="walkthrough"></a>Útmutatás
### <a name="launch-bash-in-cloud-shell"></a>Indítsa el a felhő rendszerhéj Bash
1. Indítsa el a felhő rendszerhéjat az elsődleges helyről
2. Ellenőrizze, hogy az előnyben részesített előfizetés van beállítva

```azurecli-interactive
az account show
```

### <a name="create-a-terraform-template"></a>Terraform sablon létrehozása
Az előnyben részesített szövegszerkesztővel main.tf nevű új Terraform sablont létrehozni.

```
vim main.tf
```

Másolja és illessze be az alábbi kód felhő rendszerhéjat.

```
resource "azurerm_resource_group" "myterraformgroup" {
    name = "myRgName"
    location = "West US"
}
```

Mentse a fájlt, és zárja be a szövegszerkesztőben.

### <a name="terraform-init"></a>Terraform init
Futtassa a Begin `terraform init`.

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

A [terraform init parancs](https://www.terraform.io/docs/commands/init.html) inicializáló egy Terraform konfigurációs fájlokat tartalmazó könyvtárat. A `terraform init` az első parancs, amely egy új Terraform konfiguráció írásakor, vagy egy meglévő verziókezelést a Klónozás után kell futtatni. Biztonságos, a parancs futtatásához többször is.

### <a name="terraform-plan"></a>Terraform terv
Tekintse meg az erőforrások kell létrehozni a Terraform sablon `terraform plan`.

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

A [terraform terv parancs](https://www.terraform.io/docs/commands/plan.html) egy végrehajtási terv létrehozására szolgál. Terraform hajt végre a frissítést, kifejezetten tiltja, és majd határozza meg, milyen műveletek szükségesek a kívánt állapot, a konfigurációs fájlok eléréséhez. A csomag használatával is menthető-out, és majd – terraform alkalmazni annak érdekében, hogy csak az előre tervezett végrehajtás.

### <a name="terraform-apply"></a>Terraform alkalmazása
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

A [terraform alkalmazása parancs](https://www.terraform.io/docs/commands/apply.html) elérni a kívánt állapot, a konfiguráció szükséges módosítások alkalmazására szolgál.

### <a name="verify-deployment-with-azure-cli-20"></a>Az Azure CLI 2.0 telepítés ellenőrzése
Futtatás `az group show -n myRgName` az erőforrás-kiépítés sikerességéről meggyőződhet.

```azcliinteractive
az group show -n myRgName
```

### <a name="clean-up-with-terraform-destroy"></a>Karbantartása a terraform megszüntetése
A létrehozott erőforráscsoport karbantartása a [Terraform semmisítse meg a parancs](https://www.terraform.io/docs/commands/destroy.html) Terraform által létrehozott infrastruktúra karbantartása.

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

Sikeresen létrehozott egy Azure-erőforrás Terraform keresztül. Látogasson el a következő lépések és folytathatja az felhő rendszerhéj.

## <a name="next-steps"></a>További lépések
[További tudnivalók az Terraform Azure-szolgáltató](https://www.terraform.io/docs/providers/azurerm/#)<br>
[A felhő rendszerhéj gyors üzembe helyezés bash](quickstart.md)