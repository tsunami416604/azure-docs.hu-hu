---
title: Azure Data Explorer-fürt és-adatbázis létrehozása Azure Resource Manager-sablon használatával
description: Ismerje meg, hogyan hozhat létre Azure Data Explorer-fürtöt és -adatbázist egy Azure Resource Manager-sablon használatával
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: 56639d8a29ad8eac465845c8d354d04b31ba6093
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75911965"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-an-azure-resource-manager-template"></a>Azure Data Explorer-fürt és-adatbázis létrehozása Azure Resource Manager-sablon használatával

> [!div class="op_single_selector"]
> * [Portál](create-cluster-database-portal.md)
> * [parancssori felület](create-cluster-database-cli.md)
> * [Powershell](create-cluster-database-powershell.md)
> * [C #](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Azure Resource Manager-sablon](create-cluster-database-resource-manager.md)

Az Azure Adatkezelő egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. Az Azure Data Explorer használatához először egy fürtöt hozunk létre, majd egy vagy több adatbázist a fürtben. Ezután adatokat töltünk be az adatbázisba, hogy lekérdezéseket futtathassunk rajta. 

Ebben a cikkben egy Azure Data Explorer-fürtöt és -adatbázist hoz létre egy [Azure Resource Manager-sablon](../azure-resource-manager/management/overview.md)használatával. A cikk bemutatja, hogyan határozhatja meg, hogy mely erőforrások vannak telepítve, és hogyan határozhatja meg a központi telepítés végrehajtásakor megadott paramétereket. Ez a sablont használhatja a saját környezeteiben, vagy testre is szabhatja a saját követelményeinek megfelelően. A sablonok létrehozásáról az [Azure Resource Manager-sablonok szerzői című témakörben olvashat.](/azure/azure-resource-manager/resource-group-authoring-templates) A JSON szintaxisát és a sablonban használandó tulajdonságokat a [Microsoft.Kusto erőforrástípusok](/azure/templates/microsoft.kusto/allversions)című témakörben olvashatja.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="azure-resource-manager-template-for-cluster-and-database-creation"></a>Azure Resource Manager-sablon fürt- és adatbázis-létrehozáshoz

Ebben a cikkben egy [meglévő rövid útmutató sablont](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json) használ

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

További sablonmintákat az [Azure gyorsútmutató-sablonok](https://azure.microsoft.com/resources/templates/)ban talál.

## <a name="deploy-the-template-and-verify-template-deployment"></a>A sablon telepítése és a sablon telepítésének ellenőrzése

Az Azure Resource Manager-sablont az [Azure Portalon](#use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment) vagy a [PowerShell használatával](#use-powershell-to-deploy-the-template-and-verify-template-deployment)telepítheti.

### <a name="use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment"></a>Az Azure Portal használata a sablon üzembe helyezéséhez és a sablon üzembe helyezésének ellenőrzéséhez

1. Fürt és adatbázis létrehozásához az alábbi gombbal indítsa el a központi telepítést. Kattintson a jobb gombbal, és válassza **a Megnyitás új ablakban**parancsot, így a cikk ben ismertetett további lépéseket követheti.

    [![Üzembe helyezés az Azure-ban](media/create-cluster-database-resource-manager/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-kusto-cluster-database%2Fazuredeploy.json)

    Az **Üzembe helyezés az Azure-ban** gombra kattintva megnyílik az Azure Portal, ahol egy üzembehelyezési űrlapot kell kitöltenie.

    ![Üzembe helyezés az Azure-ban](media/create-cluster-database-resource-manager/deploy-2-azure.png)

    A [sablon t szerkesztheti és üzembe helyezheti az Azure Portalon](/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal#edit-and-deploy-the-template) az űrlap használatával.

1. **Basics** és **SETTINGS** szakaszok kiteljesedése. Válassza ki az egyedi fürt- és adatbázisneveket.
Az Azure Data Explorer-fürt és -adatbázis létrehozása néhány percet vesz igénybe.

1. A központi telepítés ellenőrzéséhez nyissa meg az erőforráscsoportot az [Azure Portalon](https://portal.azure.com) az új fürt és adatbázis megkereséséhez. 

### <a name="use-powershell-to-deploy-the-template-and-verify-template-deployment"></a>A powershell használata a sablon üzembe helyezéséhez és a sablon központi telepítésének ellenőrzéséhez

#### <a name="deploy-the-template-using-powershell"></a>A sablon üzembe helyezése a PowerShell használatával

1. Válassza **a Próbálja ki** a következő kódblokkból, majd kövesse az utasításokat az Azure Cloud rendszerhéjba való bejelentkezéshez.

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

1. Válassza **a Másolás lehetőséget** a PowerShell-parancsfájl másolásához.
1. Kattintson a jobb gombbal a rendszerhéj-konzolra, és válassza **a Beillesztés parancsot.**
Az Azure Data Explorer-fürt és -adatbázis létrehozása néhány percet vesz igénybe.

#### <a name="verify-the-deployment-using-powershell"></a>A központi telepítés ellenőrzése a PowerShell használatával

A központi telepítés ellenőrzéséhez használja a következő Azure PowerShell-parancsfájlt.  Ha a Cloud Shell még nyitva van, nem kell másolni/futtatni az első sort (Read-Host). Az Azure Data Explorer-erőforrások PowerShellben való kezelésével kapcsolatos további információkért olvassa el [az Az.Kusto](/powershell/module/az.kusto/?view=azps-2.7.0)című. 

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"

Install-Module -Name Az.Kusto
$resourceGroupName = "${projectName}rg"
$clusterName = "${projectName}cluster"

Get-AzKustoCluster -ResourceGroupName $resourceGroupName -Name $clusterName
Write-Host "Press [ENTER] to continue ..."
```

[!INCLUDE [data-explorer-clean-resources](../../includes/data-explorer-clean-resources.md)]

## <a name="next-steps"></a>További lépések

[Adatok betöltése az Azure Data Explorer-fürtbe és -adatbázisba](ingest-data-overview.md)
