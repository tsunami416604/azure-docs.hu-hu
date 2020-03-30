---
title: Rövid útmutató – Terraform-konfiguráció létrehozása az Azure-hoz
description: Az Azure Cosmos DB és az Azure Container Instances üzembe helyezésével ismerkedhet meg a Terraform azure-beli használatával.
ms.topic: quickstart
ms.date: 10/26/2019
ms.openlocfilehash: 92f66fcfeac69b66eb8cdb0dc58b7a3171d45e10
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77470021"
---
# <a name="quickstart-create-a-terraform-configuration-for-azure"></a>Rövid útmutató: Terraform-konfiguráció létrehozása az Azure-hoz

Ebben a rövid útmutatóban a Terraform használatával hoz létre Azure-erőforrásokat. A cikkben ismertetett lépések a következő erőforrások létrehozását ismertetik:

> [!div class="checklist"]
> * Az Azure Cosmos DB-példánya
> * Azure Container Instance
> * Alkalmazás, amely a két erőforrás között működik

## <a name="create-first-configuration"></a>Első konfiguráció létrehozása

Ebben a szakaszban hozzon létre egy Azure Cosmos DB-példány konfigurációját.

1. Jelentkezzen be az [Azure Portalra.](https://go.microsoft.com/fwlink/p/?LinkID=525040)

1. Nyissa meg az Azure Cloud Shellt.

1. A Cloud Shell-szerkesztő indítása:

    ```bash
    code main.tf
    ```

1. Ebben a lépésben a konfiguráció néhány Azure-erőforrást modellez. Ezek az erőforrások közé tartozik egy Azure-erőforráscsoport és egy Azure Cosmos DB-példány. Egy véletlenszerű egész szám egy egyedi Cosmos DB-példány név létrehozásához használatos. Számos Cosmos DB-beállítás is konfigurálva van. További információt a [Cosmos DB Terraform referencia című témakörben talál.](https://www.terraform.io/docs/providers/azurerm/r/cosmosdb_account.html) Másolja és illessze be a következő Terraform konfigurációt a szerkesztőbe:

    ```hcl
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
      location            = azurerm_resource_group.vote-resource-group.location
      resource_group_name = azurerm_resource_group.vote-resource-group.name
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

1. Mentse a fájlt (**&lt;Ctrl>S),** és lépjen ki a szerkesztőből (**&lt;Ctrl>Q**).

## <a name="run-the-configuration"></a>A konfiguráció futtatása

Ebben a szakaszban a konfiguráció futtatásához több Terraform-parancsot is használhat.

1. A [terraform init](https://www.terraform.io/docs/commands/init.html) parancs inicializálja a munkakönyvtárat. Futtassa a következő parancsot a Cloud Shellben:

    ```bash
    terraform init
    ```

1. A [terraform terv](https://www.terraform.io/docs/commands/plan.html) parancs a konfigurációs szintaxis érvényesítésére használható. A `-out` paraméter az eredményeket egy fájlba irányítja. A kimeneti fájl később is használható a konfiguráció alkalmazásához. Futtassa a következő parancsot a Cloud Shellben:

    ```bash
    terraform plan --out plan.out
    ```

1. A [terraform apply](https://www.terraform.io/docs/commands/apply.html) parancs a konfiguráció alkalmazására szolgál. Az előző lépés kimeneti fájlja meg van adva. Ez a parancs az Azure-erőforrások létrehozását eredményezi. Futtassa a következő parancsot a Cloud Shellben:

    ```bash
    terraform apply plan.out
    ```

1. Az eredmények ellenőrzése az Azure Portalon belül, keresse meg az új erőforráscsoport. Az új Azure Cosmos DB-példány az új erőforráscsoportban található.

## <a name="update-configuration"></a>Konfiguráció frissítése

Ez a szakasz bemutatja, hogyan frissítheti a konfigurációt egy Azure Container Instance.This section shows how to update the configuration to include an Azure Container Instance. A tároló egy alkalmazást futtat, amely beolvassa és adatokat ír a Cosmos DB-nek.

1. A Cloud Shell-szerkesztő indítása:

    ```bash
    code main.tf
    ```

1. A konfiguráció ebben a lépésben `COSMOS_DB_ENDPOINT` két `COSMOS_DB_MASTERKEY`környezeti változót állít be: és . Ezek a változók az adatbázis eléréséhez a helyet és a kulcsot tartják. Ezeknek a változóknak az értékei az előző lépésben létrehozott adatbázispéldányból származnak. Ezt a folyamatot interpolációnak nevezzük. A Terraform interpolációról az [Interpolációs szintaxis](https://www.terraform.io/docs/configuration/interpolation.html)ban olvashat bővebben. A konfiguráció tartalmaz egy kimeneti blokkot is, amely a tárolópéldány teljesen minősített tartománynevét (FQDN) adja vissza. Másolja a következő kódot a szerkesztőbe:

    ```hcl
    resource "azurerm_container_group" "vote-aci" {
      name                = "vote-aci"
      location            = azurerm_resource_group.vote-resource-group.location
      resource_group_name = azurerm_resource_group.vote-resource-group.name
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
          "COSMOS_DB_ENDPOINT"  = azurerm_cosmosdb_account.vote-cosmos-db.endpoint
          "COSMOS_DB_MASTERKEY" = azurerm_cosmosdb_account.vote-cosmos-db.primary_master_key
          "TITLE"               = "Azure Voting App"
          "VOTE1VALUE"          = "Cats"
          "VOTE2VALUE"          = "Dogs"
        }
      }
    }

    output "dns" {
      value = azurerm_container_group.vote-aci.fqdn
    }
    ```

1. Mentse a fájlt (**&lt;Ctrl>S),** és lépjen ki a szerkesztőből (**&lt;Ctrl>Q**).

1. Az előző szakaszhoz ugyanis futtassa a következő parancsot a módosítások megjelenítéséhez:

    ```bash
    terraform plan --out plan.out
    ```

1. Futtassa a `terraform apply` parancsot a konfiguráció alkalmazásához.

    ```bash
    terraform apply plan.out
    ```

1. Jegyezze fel a tárolópéldány fqdn.

## <a name="test-application"></a>Alkalmazás tesztelése

Az alkalmazás teszteléséhez keresse meg a tárolópéldány teljes tartománynát. A következő kimenethez hasonló eredményeket kell látnia:

![Azure szavazási alkalmazás](media/terraform-quickstart/azure-vote.jpg)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a cikkben létrehozott erőforrásokat.

Futtassa a [terraform megsemmisítési](https://www.terraform.io/docs/commands/destroy.html) parancsot az ebben az oktatóanyagban létrehozott Azure-erőforrások eltávolításához:

```bash
terraform destroy -auto-approve
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Telepítse és konfigurálja a Terraformot az Azure-erőforrások kiépítéséhez.](terraform-install-configure.md)