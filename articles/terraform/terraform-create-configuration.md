---
title: Rövid útmutató – Terraform-konfiguráció létrehozása az Azure-hoz
description: Ismerkedjen meg a Terraform az Azure-ban egy Azure Cosmos DB és Azure Container Instances üzembe helyezésével.
ms.topic: quickstart
ms.date: 10/26/2019
ms.openlocfilehash: 92f66fcfeac69b66eb8cdb0dc58b7a3171d45e10
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77470021"
---
# <a name="quickstart-create-a-terraform-configuration-for-azure"></a>Gyors útmutató: Terraform-konfiguráció létrehozása az Azure-hoz

Ebben a rövid útmutatóban Azure-erőforrásokat hoz létre a Terraform használatával. A cikk lépései végigvezetik a következő erőforrások létrehozásán:

> [!div class="checklist"]
> * Azure Cosmos DB-példány
> * Azure Container Instance
> * Az alkalmazás, amely a két erőforrás között működik

## <a name="create-first-configuration"></a>Első konfiguráció létrehozása

Ebben a szakaszban egy Azure Cosmos DB-példány konfigurációját hozza létre.

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Nyissa meg a Azure Cloud Shell.

1. A Cloud Shell-szerkesztő elindítása:

    ```bash
    code main.tf
    ```

1. Az ebben a lépésben szereplő konfiguráció néhány Azure-erőforrást modellez. Ezek az erőforrások egy Azure-erőforráscsoportot és egy Azure Cosmos DB példányt tartalmaznak. A rendszer véletlenszerű egész számot használ egyedi Cosmos DB példánynév létrehozásához. Számos Cosmos DB beállítás is be van állítva. További információ: [Cosmos db Terraform-hivatkozás](https://www.terraform.io/docs/providers/azurerm/r/cosmosdb_account.html). Másolja és illessze be a következő Terraform-konfigurációt a szerkesztőbe:

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

1. Mentse a fájlt (**&lt;CTRL>S**), és lépjen ki a szerkesztőből (**&lt;CTRL>Q**).

## <a name="run-the-configuration"></a>A konfiguráció futtatása

Ebben a szakaszban számos Terraform-parancsot használ a konfiguráció futtatásához.

1. A [Terraform init](https://www.terraform.io/docs/commands/init.html) parancs inicializálja a munkakönyvtárat. Futtassa a következő parancsot a Cloud Shellban:

    ```bash
    terraform init
    ```

1. A [Terraform terv](https://www.terraform.io/docs/commands/plan.html) parancs használatával ellenőrizheti a konfigurációs szintaxist. A `-out` paraméter egy fájlba irányítja az eredményeket. A kimeneti fájl később is használható a konfiguráció alkalmazásához. Futtassa a következő parancsot a Cloud Shellban:

    ```bash
    terraform plan --out plan.out
    ```

1. A [Terraform Apply](https://www.terraform.io/docs/commands/apply.html) parancs a konfiguráció alkalmazására szolgál. Az előző lépésben megadott kimeneti fájl meg van adva. Ez a parancs az Azure-erőforrások létrehozását okozza. Futtassa a következő parancsot a Cloud Shellban:

    ```bash
    terraform apply plan.out
    ```

1. A Azure Portal belüli eredmények ellenőrzéséhez keresse meg az új erőforráscsoportot. Az új Azure Cosmos DB példány az új erőforráscsoport.

## <a name="update-configuration"></a>Konfiguráció frissítése

Ez a szakasz bemutatja, hogyan frissítheti a konfigurációt, hogy tartalmazza az Azure Container Instancet. A tároló olyan alkalmazást futtat, amely adatokat olvas be és ír a Cosmos DBba.

1. A Cloud Shell-szerkesztő elindítása:

    ```bash
    code main.tf
    ```

1. Az ebben a lépésben szereplő konfiguráció két környezeti változót `COSMOS_DB_ENDPOINT` állít `COSMOS_DB_MASTERKEY`be: és. Ezek a változók megtartják az adatbázis elérésének helyét és kulcsát. A változók értékei az előző lépésben létrehozott adatbázis-példányból szerezhetők be. Ezt a folyamatot interpolációnak nevezzük. További információ az Terraform interpolációról: [interpolációs szintaxis](https://www.terraform.io/docs/configuration/interpolation.html). A konfiguráció tartalmaz egy kimeneti blokkot is, amely a Container példány teljes tartománynevét (FQDN) adja vissza. Másolja és illessze be a következő kódot a szerkesztőbe:

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

1. Mentse a fájlt (**&lt;CTRL>S**), és lépjen ki a szerkesztőből (**&lt;CTRL>Q**).

1. Ahogy az előző szakaszban is tette, futtassa a következő parancsot a szükséges módosítások megjelenítéséhez:

    ```bash
    terraform plan --out plan.out
    ```

1. Futtassa a `terraform apply` parancsot a konfiguráció alkalmazásához.

    ```bash
    terraform apply plan.out
    ```

1. Jegyezze fel a tároló példányának teljes tartománynevét.

## <a name="test-application"></a>Alkalmazás tesztelése

Az alkalmazás teszteléséhez navigáljon a tároló példány teljes tartománynevére. A következő kimenethez hasonló eredményeknek kell megjelennie:

![Azure vote-alkalmazás](media/terraform-quickstart/azure-vote.jpg)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a cikkben létrehozott erőforrásokat.

Futtassa az [Terraform Destroy](https://www.terraform.io/docs/commands/destroy.html) parancsot az oktatóanyagban létrehozott Azure-erőforrások eltávolításához:

```bash
terraform destroy -auto-approve
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Terraform telepítése és konfigurálása Azure-erőforrások](terraform-install-configure.md)kiépítéséhez.