---
title: Az Azure-ban a Terraform bemutatása.
description: Ismerkedés az Azure-on futtatott Terraform az Azure Cosmos DB és az Azure Container Instances üzembe helyezésével.
services: terraform
author: neilpeterson
ms.service: terraform
ms.topic: quickstart
ms.date: 02/04/2019
ms.author: nepeters
ms.openlocfilehash: b3d872008c95d94a8401e6f38665c3c88616a168
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56736923"
---
# <a name="create-a-terraform-configuration-for-azure"></a>Az Azure Terraform konfiguráció létrehozása

Ebben a példában a Terraform konfigurációt hoz létre, és ez a konfiguráció üzembe helyezése az Azure felhasználói élményt kaphat. Amikor elkészült, fog telepített egy Azure Cosmos DB-példányra, az Azure-Tárolópéldányon és egy alkalmazás, amely a két erőforrás között működik. Jelen dokumentum céljából feltételezzük, hogy az összes munkahelyi befejeződött-e az Azure Cloud Shellt, amelyben előre telepítve van a Terraform eszközök rendelkezik. Ha szeretné, hogy a saját rendszerén keresztül a példa működéséhez, Terraform telepíthető-e utasításait követve [Itt](../virtual-machines/linux/terraform-install-configure.md).

## <a name="create-first-configuration"></a>Első konfiguráció létrehozása

Ebben a szakaszban létrehoz egy Azure Cosmos DB-példány konfigurációját.

Válassza ki **kipróbálás** az Azure cloud shell megnyitásához. Ha meg van nyitva, adja meg a `code .` megnyitása a cloud shell Kódszerkesztő.

```azurecli-interactive
code .
```

Másolja és illessze be a következő Terraform-konfigurációt.

Ez a konfiguráció modellek az Azure-erőforráscsoporttal, véletlenszerű egész szám és egy Azure Cosmos DB-példányra. A véletlenszerű egész szolgál a Cosmos DB-példány neve. Cosmos DB több beállítást is vannak konfigurálva. Cosmos DB Terraform konfigurációk teljes listáját lásd: a [Cosmos DB Terraform-referencia](https://www.terraform.io/docs/providers/azurerm/r/cosmosdb_account.html).

Mentse a fájlt az `main.tf` végeztével. Ezt a műveletet a három pontot használ a Kódszerkesztő jobb felső részén.

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

A [terraform init](https://www.terraform.io/docs/commands/init.html) parancs inicializálja a munkakönyvtárat. Futtatás `terraform init` terminál központi telepítését az új konfiguráció előkészítése a cloud shellben.

```azurecli-interactive
terraform init
```

A [terraform terv](https://www.terraform.io/docs/commands/plan.html) parancs segítségével ellenőrizze, hogy a konfiguráció megfelelően van formázva, és milyen erőforrásokat hoz létre, frissített, és megsemmisíteni megjelenítése. Az eredményeket egy fájlban, és egy későbbi időpontban a alkalmazni a konfigurációt használt is.

Futtatás `terraform plan` az új Terraform konfiguráció tesztelése.

```azurecli-interactive
terraform plan --out plan.out
```

A használt konfiguráció alkalmazása [terraform a alkalmazni](https://www.terraform.io/docs/commands/apply.html) , és adja meg a terv fájl nevét. Ez a parancs üzembe helyezi az erőforrásokat az Azure-előfizetésében.

```azurecli-interactive
terraform apply plan.out
```

Ha végzett, láthatja, hogy létrejött az erőforráscsoport és az erőforráscsoportba helyezni egy Azure Cosmos DB-példányra.

## <a name="update-configuration"></a>Konfiguráció frissítése

Frissítse a konfigurációt, hogy tartalmazza az Azure Container Instance. A tároló fut olyan alkalmazás, amely adatokat olvas be ír a Cosmos DB-hez.

Másolja a következő konfigurációt alján a `main.tf` fájlt. Mentse a fájlt, ha ezzel elkészült.

Két környezeti változók vannak beállítva, `COSMOS_DB_ENDPOINT` és `COSMOS_DB_MASTERKEY`. Ezeket a változókat tartsa a helyet, és az adatbázis eléréséhez szükséges kulcs. Ezek a változók értékeit kérhetők le az előző lépésben létrehozott az adatbázis-példány. Ez a folyamat interpolációs néven ismert. A Terraform interpolációs kapcsolatos további információkért lásd: [interpolációs szintaxis](https://www.terraform.io/docs/configuration/interpolation.html).


A konfiguráció is tartalmaz egy kimeneti blokkhoz, amely a tárolópéldányt teljesen minősített tartománynevét (FQDN) adja vissza.

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
    ports  = {
      port     = 80
      protocol = "TCP"
    }

    secure_environment_variables {
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

Futtatás `terraform plan` a frissített terv létrehozásához, és jelenítheti meg kell tenni a módosításokat. Megtekintheti, hogy egy Azure Container Instance-erőforrást a konfigurációs van adva.

```azurecli-interactive
terraform plan --out plan.out
```

Végezetül futtassa `terraform apply` alkalmazni a konfigurációt.

```azurecli-interactive
terraform apply plan.out
```

Ha befejeződött, jegyezze fel a tárolópéldányt teljes Tartománynevét.

## <a name="test-application"></a>Alkalmazás tesztelése

Keresse meg a tárolópéldányt teljes Tartománynevét. Ha minden megfelelően lett konfigurálva, megtekintheti a következő alkalmazás.

![Az Azure vote-alkalmazás](media/terraform-quickstart/azure-vote.jpg)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült, az Azure-erőforrások és az erőforráscsoport segítségével állíthatók be a [megsemmisíteni a terraform](https://www.terraform.io/docs/commands/destroy.html) parancsot.

```azurecli-interactive
terraform destroy -auto-approve
```

## <a name="next-steps"></a>További lépések

Ebben a példában létrehozott, telepítve, és a Terraform konfiguráció megsemmisül. További információ az Azure-ban a Terraform használatával az Azure Terraform szolgáltató dokumentációjában talál.

> [!div class="nextstepaction"]
> [Az Azure Terraform-szolgáltató](https://www.terraform.io/docs/providers/azurerm/)
