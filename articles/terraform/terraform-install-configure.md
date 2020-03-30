---
title: Rövid útmutató – A Terraform telepítése és konfigurálása az Azure-erőforrások kiépítéséhez
description: Ebben a quicstartban telepíti és konfigurálja a Terraformot az Azure-erőforrások létrehozásához
keywords: azure devops terraform telepítési konfigurálás
ms.topic: quickstart
ms.date: 03/09/2020
ms.openlocfilehash: 82635f59ec8165add2046a230a040b06f89d9898
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78943505"
---
# <a name="quickstart-install-and-configure-terraform-to-provision-azure-resources"></a>Rövid útmutató: A Terraform telepítése és konfigurálása az Azure-erőforrások kiépítéséhez
 
A Terraform egyszerű módszerrel definiálhatja, megtekintheti és üzembe helyezheti a felhőalapú infrastruktúrát egy [egyszerű templating nyelv](https://www.terraform.io/docs/configuration/syntax.html)használatával. Ez a cikk ismerteti a Terraform használatával erőforrások azure-ban való kiépítéséhez szükséges lépéseket.

Ha többet szeretne megtudni aRról, hogyan használhatja a Terraformot az Azure-ral, látogasson el a [Terraform Hub ba.](/azure/terraform)
> [!NOTE]
> A Terraform specifikus támogatás, kérjük, forduljon terraform közvetlenül az egyik közösségi csatornák:
>
>    * A közösségi portál [Terraform szakasza](https://discuss.hashicorp.com/c/terraform-core) kérdéseket, használati eseteket és hasznos mintákat tartalmaz.
>
>    * A szolgáltatóval kapcsolatos kérdéseivel kérjük, látogasson el a közösségi portál [Terraform Szolgáltatók](https://discuss.hashicorp.com/c/terraform-providers) részébe.



[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

A Terraform alapértelmezés szerint a [Cloud Shellben](/azure/terraform/terraform-cloud-shell)van telepítve. Ha úgy dönt, hogy helyileg telepíti a Terraformot, végezze el a következő lépést, ellenkező esetben folytassa [a Terraform-hozzáférés beállítása az Azure-hoz](#set-up-terraform-access-to-azure).

## <a name="install-terraform"></a>Terraform telepítése

A Terraform telepítéséhez [töltse le](https://www.terraform.io/downloads.html) az operációs rendszerének megfelelő csomagot egy külön telepítési könyvtárba. A letöltés egyetlen végrehajtható fájlt tartalmaz, amelyhez globális elérési utat is meg kell határoznia. Az útvonal Linux és Mac rendszeren való beállítására vonatkozó utasításokért látogasson el [erre a weboldalra.](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux) Az elérési út Windows rendszeren való beállításáról a weblapon talál [útmutatást.](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows)

Ellenőrizze az elérési `terraform` út konfigurációját a paranccsal. Megjelenik az elérhető Terraform-beállítások listája, mint a következő példa kimenetben:

```console
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>Terraform-hozzáférés beállítása az Azure-hoz

Ha engedélyezni szeretné, hogy a Terraform erőforrásokat építsen ki az Azure-ba, hozzon létre egy [egyszerű Azure AD-szolgáltatást.](/cli/azure/create-an-azure-service-principal-azure-cli) Az egyszerű szolgáltatás a Terraform-parancsfájlokat adja az Azure-előfizetésben lévő erőforrások kiépítéséhez.

Ha több Azure-előfizetéssel rendelkezik, először kérdezze le a fiókját [az az-fióklistával](/cli/azure/account#az-account-list) az előfizetési azonosító és a bérlői azonosító értékek listájának lekérdezéséhez:

```azurecli-interactive
az account list --query "[].{name:name, subscriptionId:id, tenantId:tenantId}"
```

A kijelölt előfizetés használatához állítsa be az előfizetést ehhez a munkamenethez [az az fiók beállításával.](/cli/azure/account#az-account-set) Állítsa `SUBSCRIPTION_ID` be a környezeti változót `id` úgy, hogy a használni kívánt előfizetésből származó visszaadott mező értékét tartsa:

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

Most már létrehozhat egy egyszerű szolgáltatás t terraform használatra. Használja [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac), és állítsa be a *hatókört* az előfizetésaz alábbiak szerint:

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

A `appId` `password`, `sp_name`, `tenant` , és vissza. Jegyezze fel `appId` a `password`és a .

## <a name="configure-terraform-environment-variables"></a>Terraform környezeti változók konfigurálása

Ha a Terraformot az Azure AD egyszerű szolgáltatásának használatára szeretné konfigurálni, állítsa be a következő környezeti változókat, amelyeket ezután az [Azure Terraform modulok](https://registry.terraform.io/modules/Azure)használnak. A környezetet akkor is beállíthatja, ha nem azure-felhővel dolgozik nyilvános azure-tól.

- `ARM_SUBSCRIPTION_ID`
- `ARM_CLIENT_ID`
- `ARM_CLIENT_SECRET`
- `ARM_TENANT_ID`
- `ARM_ENVIRONMENT`

A következő mintarendszerhéj-parancsfájl segítségével állíthatja be ezeket a változókat:

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

## <a name="run-a-sample-script"></a>Mintaparancsfájl futtatása

Hozzon `test.tf` létre egy fájlt egy üres könyvtárban, és illessze be a következő parancsfájlba.

```hcl
provider "azurerm" {
  # The "feature" block is required for AzureRM provider 2.x. 
  # If you are using version 1.x, the "features" block is not allowed.
  version = "~>2.0"
  features {}
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

Mentse a fájlt, majd inicializálja a Terraform központi telepítését. Ez a lépés letölti az Azure-erőforrásokcsoport létrehozásához szükséges Azure-modulokat.

```bash
terraform init
```

A kimenet a következő példához hasonló:

```console
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

Megtekintheti a Terraform parancsfájl által végrehajtandó `terraform plan`műveletekelőnézetét a segítségével. Ha készen áll az erőforráscsoport létrehozására, alkalmazza a Terraform-tervet az alábbiak szerint:

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

## <a name="next-steps"></a>További lépések

Ebben a cikkben telepítette a Terraformot, vagy a Cloud Shell használatával konfigurálta az Azure-hitelesítő adatokat, és megkezdheti az erőforrások létrehozását az Azure-előfizetésében. Ha teljesebb Terraform-telepítést szeretne létrehozni az Azure-ban, olvassa el az alábbi cikket:

> [!div class="nextstepaction"]
> [Hozzon létre egy Azure virtuális gép terraform](terraform-create-complete-vm.md)