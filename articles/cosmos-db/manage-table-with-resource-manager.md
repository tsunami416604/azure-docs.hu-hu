---
title: Erőforrás-kezelősablonok az Azure Cosmos DB Table API-hoz
description: Az Azure Resource Manager-sablonok használatával hozhatja létre és konfigurálhatja az Azure Cosmos DB Table API-t.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 6ab54e56368e7e26e807e4d1dc0592536dc9374a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246707"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>Az Azure Cosmos DB Table API-erőforrások kezelése az Azure Resource Manager-sablonok használatával

Ez a cikk ismerteti, hogyan hajthatja végre a különböző műveleteket az Azure Cosmos DB-fiókok, adatbázisok és tárolók felügyeletének automatizálására az Azure Resource Manager-sablonok használatával. Ez a cikk csak a Table API-fiókok példákat, példákat más API-típusú fiókok lásd: az Azure Resource Manager-sablonok használata az Azure Cosmos DB API [cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md), [SQL-cikkek.](manage-sql-with-resource-manager.md)

## <a name="create-azure-cosmos-account-and-table"></a>Azure Cosmos-fiók és tábla létrehozása<a id="create-resource"></a>

Hozzon létre Azure Cosmos DB-erőforrásokat egy Azure Resource Manager-sablon használatával. Ez a sablon létrehoz egy Azure Cosmos-fiókot table API-hoz egy tábla 400 RU/s átviteli sebességgel. Másolja a sablont, és telepítse az alábbiak szerint, vagy keresse fel az [Azure Gyorsútmutató galériát,](https://azure.microsoft.com/resources/templates/101-cosmosdb-table/) és telepítse az Azure Portalról. A sablont letöltheti a helyi számítógépre, vagy létrehozhat egy új `--template-file` sablont, és megadhatja a helyi elérési utat a paraméterrel.

> [!NOTE]
> A fiókneveknek kisbetűsnek és 44 vagy kevesebb karakternek kell lenniük.
> A RU/s frissítéséhez küldje el újra a sablont a frissített átviteli tulajdonságértékekkel.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-table/azuredeploy.json":::

### <a name="deploy-via-powershell"></a>Üzembe helyezés a PowerShellen keresztül

A Resource Manager-sablon PowerShell használatával történő üzembe helyezéséhez **másolja** a parancsfájlt, és válassza a **Próbálja ki az** Azure Cloud Shell megnyitásához lehetőséget. A parancsfájl beillesztéséhez kattintson a jobb gombbal a rendszerhéjra, majd válassza a **Beillesztés parancsot:**

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$tableName = Read-Host -Prompt "Enter the table name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-table/azuredeploy.json" `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -tableName $tableName

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2015-04-08" --ResourceGroupName $resourceGroupName).name
```

Ha úgy dönt, hogy a PowerShell helyileg telepített verzióját használja az Azure Cloud rendszerhéj helyett, [telepítenie](/powershell/azure/install-az-ps) kell az Azure PowerShell-modult. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`.

### <a name="deploy-via-the-azure-cli"></a>Üzembe helyezés az Azure CLI-n keresztül

Az Azure Resource Manager-sablon azure CLI használatával történő üzembe helyezéséhez másolja a **parancsfájlt,** és válassza a **Próbálja ki az** Azure Cloud Shell megnyitásához lehetőséget. A parancsfájl beillesztéséhez kattintson a jobb gombbal a rendszerhéjra, majd válassza a **Beillesztés parancsot:**

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the table name: ' tableName

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-table/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion tableName=$tableName

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

A `az cosmosdb show` parancs az újonnan létrehozott Azure Cosmos-fiókot jeleníti meg a kiépítés után. Ha úgy dönt, hogy az Azure CLI helyileg telepített verzióját használja a Cloud Shell használata helyett, tekintse meg az [Azure CLI](/cli/azure/) cikket.

## <a name="next-steps"></a>További lépések

Néhány további információforrás:

- [Az Azure Resource Manager dokumentációja](/azure/azure-resource-manager/)
- [Az Azure Cosmos DB erőforrás-szolgáltatósémája](/azure/templates/microsoft.documentdb/allversions)
- [Az Azure Cosmos DB rövid útmutató sablonjai](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Gyakori Azure Resource Manager-telepítési hibák elhárítási hibáinak elhárítása](../azure-resource-manager/templates/common-deployment-errors.md)