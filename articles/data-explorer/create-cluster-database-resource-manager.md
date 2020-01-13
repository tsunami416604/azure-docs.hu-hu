---
title: Azure Adatkezelő-fürt és-adatbázis létrehozása Azure Resource Manager sablon használatával
description: Ismerje meg, hogyan hozhat létre Azure Adatkezelő-fürtöt és-adatbázist Azure Resource Manager sablon használatával
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: 56639d8a29ad8eac465845c8d354d04b31ba6093
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911965"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-an-azure-resource-manager-template"></a>Azure Adatkezelő-fürt és-adatbázis létrehozása Azure Resource Manager sablon használatával

> [!div class="op_single_selector"]
> * [Portál](create-cluster-database-portal.md)
> * [Parancssori felület](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Azure Resource Manager-sablon](create-cluster-database-resource-manager.md)

Az Azure Adatkezelő egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. Az Azure Adatkezelő használatához először létre kell hoznia egy fürtöt, és létre kell hoznia egy vagy több adatbázist a fürtben. Ezután betöltheti az adatterhelést egy adatbázisba, így lekérdezéseket futtathat. 

Ebben a cikkben egy Azure Adatkezelő-fürtöt és-adatbázist hoz létre egy [Azure Resource Manager sablon](../azure-resource-manager/management/overview.md)használatával. A cikk bemutatja, hogyan határozható meg, hogy mely erőforrások legyenek telepítve, és Hogyan határozható meg a központi telepítés végrehajtásakor megadott paraméterek. Ez a sablont használhatja a saját környezeteiben, vagy testre is szabhatja a saját követelményeinek megfelelően. További információ a sablonok létrehozásáról: [Azure Resource Manager-sablonok készítése](/azure/azure-resource-manager/resource-group-authoring-templates). A sablonban használandó JSON-szintaxis és-tulajdonságok megtekintéséhez lásd: [Microsoft. Kusto-erőforrástípusok](/azure/templates/microsoft.kusto/allversions).

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="azure-resource-manager-template-for-cluster-and-database-creation"></a>Azure Resource Manager sablon a fürt és az adatbázis létrehozásához

Ebben a cikkben egy meglévő rövid útmutató [sablont](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json) használ

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "clusters_kustocluster_name": {
          "type": "string",
          "defaultValue": "[concat('kusto', uniqueString(resourceGroup().id))]",
          "metadata": {
            "description": "Name of the cluster to create"
          }
      },
      "databases_kustodb_name": {
          "type": "string",
          "defaultValue": "kustodb",
          "metadata": {
            "description": "Name of the database to create"
          }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Location for all resources."
        }
      }
  },
  "variables": {},
  "resources": [
      {
          "name": "[parameters('clusters_kustocluster_name')]",
          "type": "Microsoft.Kusto/clusters",
          "sku": {
              "name": "Standard_D13_v2",
              "tier": "Standard",
              "capacity": 2
          },
          "apiVersion": "2019-09-07",
          "location": "[parameters('location')]",
          "tags": {
            "Created By": "GitHub quickstart template"
          }
      },
      {
          "name": "[concat(parameters('clusters_kustocluster_name'), '/', parameters('databases_kustodb_name'))]",
          "type": "Microsoft.Kusto/clusters/databases",
          "apiVersion": "2019-09-07",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[resourceId('Microsoft.Kusto/clusters', parameters('clusters_kustocluster_name'))]"
          ],
          "properties": {
              "softDeletePeriodInDays": 365,
              "hotCachePeriodInDays": 31
          }
      }
  ]
}
```

További sablon-példákat az [Azure Gyorsindítás sablonjaiban](https://azure.microsoft.com/resources/templates/)talál.

## <a name="deploy-the-template-and-verify-template-deployment"></a>A sablon üzembe helyezése és a sablon központi telepítésének ellenőrzése

A Azure Resource Manager sablont [a Azure Portal vagy a](#use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment) [PowerShell használatával](#use-powershell-to-deploy-the-template-and-verify-template-deployment)is telepítheti.

### <a name="use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment"></a>A sablon üzembe helyezése és a sablonok központi telepítésének ellenőrzése a Azure Portal használatával

1. Fürt és adatbázis létrehozásához használja a következő gombot a telepítés elindításához. Kattintson a jobb gombbal, és válassza a **Megnyitás új ablakban**lehetőséget, így követheti a cikk további lépéseit.

    [![Üzembe helyezés az Azure-ban](media/create-cluster-database-resource-manager/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-kusto-cluster-database%2Fazuredeploy.json)

    Az **Üzembe helyezés az Azure-ban** gombra kattintva megnyílik az Azure Portal, ahol egy üzembehelyezési űrlapot kell kitöltenie.

    ![Üzembe helyezés az Azure-ban](media/create-cluster-database-resource-manager/deploy-2-azure.png)

    A [sablont a Azure Portal szerkesztheti és telepítheti](/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal#edit-and-deploy-the-template) az űrlap használatával.

1. Fejezze be az **alapok** és **Beállítások** szakaszt. Válassza ki az egyedi fürtöt és az adatbázis nevét.
Az Azure Adatkezelő-fürt és-adatbázis létrehozása néhány percet vesz igénybe.

1. Az üzembe helyezés ellenőrzéséhez nyissa meg az erőforráscsoportot a [Azure Portalban](https://portal.azure.com) , és keresse meg az új fürtöt és adatbázist. 

### <a name="use-powershell-to-deploy-the-template-and-verify-template-deployment"></a>A sablon üzembe helyezése és a sablon központi telepítésének ellenőrzése a PowerShell használatával

#### <a name="deploy-the-template-using-powershell"></a>A sablon üzembe helyezése a PowerShell használatával

1. Válassza a **kipróbálás** a következő kódrészletből lehetőséget, majd kövesse az utasításokat az Azure Cloud shellbe való bejelentkezéshez.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"
    $clusterName = "${projectName}cluster"
    $parameters = @{}
    $parameters.Add("clusters_kustocluster_name", $clusterName)
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json"
    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -TemplateParameterObject $parameters
    Write-Host "Press [ENTER] to continue ..."
    ```

1. A PowerShell-szkript másolásához válassza a **Másolás** lehetőséget.
1. Kattintson a jobb gombbal a rendszerhéj-konzolra, majd válassza a **Beillesztés**lehetőséget.
Az Azure Adatkezelő-fürt és-adatbázis létrehozása néhány percet vesz igénybe.

#### <a name="verify-the-deployment-using-powershell"></a>A telepítés ellenőrzése a PowerShell használatával

A központi telepítés ellenőrzéséhez használja a következő Azure PowerShell parancsfájlt.  Ha a Cloud Shell továbbra is nyitva van, nem kell átmásolnia/futtatnia az első sort (olvasás-gazdagép). Az Azure Adatkezelő-erőforrások PowerShellben való kezelésével kapcsolatos további információkért olvassa el [az az. Kusto](/powershell/module/az.kusto/?view=azps-2.7.0). 

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"

Install-Module -Name Az.Kusto
$resourceGroupName = "${projectName}rg"
$clusterName = "${projectName}cluster"

Get-AzKustoCluster -ResourceGroupName $resourceGroupName -Name $clusterName
Write-Host "Press [ENTER] to continue ..."
```

[!INCLUDE [data-explorer-clean-resources](../../includes/data-explorer-clean-resources.md)]

## <a name="next-steps"></a>Következő lépések

[Az Azure Adatkezelő-fürt és-adatbázis betöltése](ingest-data-overview.md)
