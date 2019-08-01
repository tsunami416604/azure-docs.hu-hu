---
title: Bevezetés az Azure-beli Terraform.
description: Ismerkedjen meg a Terraform az Azure-ban egy Azure Cosmos DB és Azure Container Instances üzembe helyezésével.
services: terraform
author: neilpeterson
ms.service: azure
ms.topic: quickstart
ms.date: 02/04/2019
ms.author: nepeters
ms.openlocfilehash: 57ab3fbc584932cb7d08bda76530bbe95ce61a6f
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699086"
---
# <a name="create-a-terraform-configuration-for-azure"></a>Terraform-konfiguráció létrehozása az Azure-hoz

Ebben a példában a Terraform-konfiguráció létrehozásával és a konfiguráció Azure-ba történő üzembe helyezésével kapcsolatos tapasztalatokat szerezhet. Ha elkészült, üzembe kell helyeznie egy Azure Cosmos DB példányt, egy Azure Container-példányt és egy alkalmazást, amely ezen a két erőforráson működik. Ez a dokumentum azt feltételezi, hogy a Azure Cloud Shell összes munkája befejeződött, és a Terraform-eszközök előre telepítve vannak. Ha a példát a saját rendszerén szeretné használni, a Terraform az [itt](../virtual-machines/linux/terraform-install-configure.md)található utasításokat követve telepítheti.

## <a name="create-first-configuration"></a>Első konfiguráció létrehozása

Ebben a szakaszban egy Azure Cosmos DB-példány konfigurációját fogja létrehozni.

Válassza a **kipróbálás most** lehetőséget az Azure Cloud Shell megnyitásához. A Megnyitás után írja be `code .` a be lehetőséget a Cloud Shell Kódszerkesztő megnyitásához.

```azurecli-interactive
code .
```

Másolja és illessze be a következő Terraform-konfigurációt.

Ez a konfiguráció egy Azure-erőforráscsoportot, egy véletlenszerű egész számot és egy Azure Cosmos DB példányt modellez. A véletlenszerű egész számot Cosmos DB-példány nevében kell használni. Számos Cosmos DB beállítás is be van állítva. Cosmos DB Terraform-konfigurációk teljes listájáért tekintse meg a [Cosmos db Terraform](https://www.terraform.io/docs/providers/azurerm/r/cosmosdb_account.html)-referenciát.

Mentse a fájlt `main.tf` a kész gombra. Ez a művelet a Kódszerkesztő jobb felső részében található ellipszisekkel végezhető el.

```azurecli-interactive
resource "azurerm_resource_group" "vote-resource-group" {
  name     = "vote-resource-group"
  location = "westus"
}

resource "random_integer" "ri" {
  min = 10000
  max = 99999
}

resource "azurerm_cosmosdb_account" "vote-cosmos-db" {
  name                = "tfex-cosmos-db-${random_integer.ri.result}"
  location            = "${azurerm_resource_group.vote-resource-group.location}"
  resource_group_name = "${azurerm_resource_group.vote-resource-group.name}"
  offer_type          = "Standard"
  kind                = "GlobalDocumentDB"

  consistency_policy {
    consistency_level       = "BoundedStaleness"
    max_interval_in_seconds = 10
    max_staleness_prefix    = 200
  }

  geo_location {
    location          = "westus"
    failover_priority = 0
  }
}
```

A [Terraform init](https://www.terraform.io/docs/commands/init.html) parancs inicializálja a munkakönyvtárat. Az `terraform init` új konfiguráció üzembe helyezésének előkészítéséhez futtassa a parancsot a Cloud Shell terminalban.

```azurecli-interactive
terraform init
```

A [Terraform terv](https://www.terraform.io/docs/commands/plan.html) parancs segítségével ellenőrizheti, hogy a konfiguráció megfelelően van-e formázva, valamint hogy milyen erőforrások jönnek létre, frissülnek vagy megsemmisülnek. Az eredményeket egy fájlban tárolhatja, és később is használhatja a konfiguráció alkalmazásához.

Az `terraform plan` új Terraform-konfiguráció teszteléséhez futtassa a parancsot.

```azurecli-interactive
terraform plan --out plan.out
```

Alkalmazza a konfigurációt a [Terraform](https://www.terraform.io/docs/commands/apply.html) alkalmazással, és adja meg a csomag nevét. Ez a parancs üzembe helyezi az Azure-előfizetésében lévő erőforrásokat.

```azurecli-interactive
terraform apply plan.out
```

Ha elkészült, láthatja, hogy az erőforráscsoport létrejött, és egy Azure Cosmos DB példányt helyezett el az erőforráscsoporthoz.

## <a name="update-configuration"></a>Konfiguráció frissítése

Frissítse a konfigurációt, hogy tartalmazza az Azure Container Instancet. A tároló olyan alkalmazást futtat, amely adatokat olvas be és ír a Cosmos DBba.

Másolja a következő konfigurációt a `main.tf` fájl aljára. Ha elkészült, mentse a fájlt.

Két környezeti változó van beállítva `COSMOS_DB_ENDPOINT` és. `COSMOS_DB_MASTERKEY` Ezek a változók megtartják az adatbázis elérésének helyét és kulcsát. A változók értékei az utolsó lépésben létrehozott adatbázis-példányból szerezhetők be. Ezt a folyamatot interpolációnak nevezzük. További információ az Terraform interpolációról: interpolációs [szintaxis](https://www.terraform.io/docs/configuration/interpolation.html).


A konfiguráció tartalmaz egy kimeneti blokkot is, amely a Container példány teljes tartománynevét (FQDN) adja vissza.

```azurecli-interactive
resource "azurerm_container_group" "vote-aci" {
  name                = "vote-aci"
  location            = "${azurerm_resource_group.vote-resource-group.location}"
  resource_group_name = "${azurerm_resource_group.vote-resource-group.name}"
  ip_address_type     = "public"
  dns_name_label      = "vote-aci"
  os_type             = "linux"

  container {
    name   = "vote-aci"
    image  = "microsoft/azure-vote-front:cosmosdb"
    cpu    = "0.5"
    memory = "1.5"
    ports {
      port     = 80
      protocol = "TCP"
    }

    secure_environment_variables = {
      "COSMOS_DB_ENDPOINT"  = "${azurerm_cosmosdb_account.vote-cosmos-db.endpoint}"
      "COSMOS_DB_MASTERKEY" = "${azurerm_cosmosdb_account.vote-cosmos-db.primary_master_key}"
      "TITLE"               = "Azure Voting App"
      "VOTE1VALUE"          = "Cats"
      "VOTE2VALUE"          = "Dogs"
    }
  }
}

output "dns" {
  value = "${azurerm_container_group.vote-aci.fqdn}"
}
```

Futtassa `terraform plan` a parancsot a frissített terv létrehozásához és a végrehajtott módosítások megjelenítéséhez. Látnia kell, hogy az Azure Container instance-erőforrás hozzá lett adva a konfigurációhoz.

```azurecli-interactive
terraform plan --out plan.out
```

Végül futtassa a `terraform apply` parancsot a konfiguráció alkalmazásához.

```azurecli-interactive
terraform apply plan.out
```

Ha elkészült, jegyezze fel a Container instance FQDN-jét.

## <a name="test-application"></a>Alkalmazás tesztelése

Navigáljon a Container-példány teljes tartománynevéhez. Ha minden megfelelően van konfigurálva, a következő alkalmazásnak kell megjelennie.

![Azure vote-alkalmazás](media/terraform-quickstart/azure-vote.jpg)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült, az Azure-erőforrások és-erőforráscsoportok a [Terraform Destroy](https://www.terraform.io/docs/commands/destroy.html) paranccsal távolíthatók el.

```azurecli-interactive
terraform destroy -auto-approve
```

## <a name="next-steps"></a>További lépések

Ebben a példában létrehozta, telepítette és megsemmisített egy Terraform-konfigurációt. A Terraform Azure-beli használatáról további információt az Azure Terraform-szolgáltató dokumentációjában talál.

> [!div class="nextstepaction"]
> [Azure Terraform-szolgáltató](https://www.terraform.io/docs/providers/azurerm/)
