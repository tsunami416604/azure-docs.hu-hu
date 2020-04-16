---
title: Az Azure Cosmos DB létrehozása és kezelése erőforrás-kezelői sablonokkal
description: Azure Resource Manager-sablonok használata az Azure Cosmos DB SQL (Core) API-hoz való létrehozásához és konfigurálásához
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mjbrown
ms.openlocfilehash: 3514b3e77781010fd56b43229f87854ea2d591e8
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390890"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-with-azure-resource-manager-templates"></a>Az Azure Cosmos DB SQL (Core) API-erőforrások kezelése az Azure Resource Manager-sablonokkal

Ebben a cikkben megismerheti, hogyan segítheti elő az Azure Cosmos DB-fiókok, -adatbázisok és -tárolók felügyeletének automatizálását Azure Resource Manager-sablonok használatával.

Ez a cikk csak az Azure Resource Manager-sablonpéldákat jeleníti meg az SQL API-fiókokhoz. [A Cassandra,](manage-cassandra-with-resource-manager.md) [Gremlin,](manage-gremlin-with-resource-manager.md) [MongoDB](manage-mongodb-with-resource-manager.md)és [Table](manage-table-with-resource-manager.md) API-k sablonpéldáit is megtalálhatja.

<a id="create-resource"></a>

## <a name="create-an-azure-cosmos-account-database-and-container"></a>Hozzon létre egy Azure Cosmos-fiókot, -adatbázist és -tárolót

A következő Azure Resource Manager-sablon létrehoz egy Azure Cosmos-fiókot:

* Két tároló, amelyek 400 kért egység /másodperc (RU/s) átviteli sebességgel rendelkeznek az adatbázis szintjén.
* Egy konténer dedikált 400 RU/s átviteli fokkal.

Az Azure Cosmos DB-erőforrások létrehozásához másolja a következő példasablont, és telepítse a leírt módon, akár [a PowerShell,](#deploy-via-powershell) akár [az Azure CLI](#deploy-via-azure-cli)használatával.

* Szükség esetén felléphet az [Azure gyorsútmutató galériába,](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/) és üzembe helyezheti a sablont az Azure Portalról.
* A sablont letöltheti a helyi számítógépre, vagy létrehozhat egy új `--template-file` sablont, és megadhatja a helyi elérési utat a paraméterrel.

> [!IMPORTANT]
>
> * Amikor helyet ad hozzá vagy távolít el egy Azure Cosmos-fiókhoz, nem módosíthatja egyidejűleg más tulajdonságokat. Ezeket a műveleteket külön kell elvégezni.
> * A fióknevek legfeljebb 44 karakterből állhatnak, az összes kisbetűs.
> * Az átviteli értékek módosításához küldje el újra a sablont frissített RU/s-okkal.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

> [!NOTE]
> Nagy partíciókulccsal rendelkező tároló létrehozásához módosítsa `"version":2` az `partitionKey` előző sablont úgy, hogy az objektumba belefoglalja a tulajdonságot.

### <a name="deploy-via-powershell"></a>Üzembe helyezés a PowerShellen keresztül

Az Azure Resource Manager-sablon üzembe helyezéséhez használja a PowerShellt:

1. **Másolja** a forgatókönyvet.
2. Válassza **a Próbálja ki az** Azure Cloud Shell megnyitásához válassza a Próbálja ki.
3. Kattintson a jobb gombbal az Azure Cloud Shell ablakban, és válassza **a Beillesztés parancsot.**

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$sharedThroughput = Read-Host -Prompt "Enter the shared database throughput (i.e. 400)"
$sharedContainer1Name = Read-Host -Prompt "Enter the first shared container name"
$sharedContainer2Name = Read-Host -Prompt "Enter the second shared container name"
$dedicatedContainer1Name = Read-Host -Prompt "Enter the dedicated container name"
$dedicatedThroughput = Read-Host -Prompt "Enter the dedicated container throughput (i.e. 400)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -sharedThroughput $ $sharedThroughput `
    -sharedContainer1Name $sharedContainer1Name `
    -sharedContainer2Name $sharedContainer2Name `
    -dedicatedContainer1Name $dedicatedContainer1Name `
    -dedicatedThroughput $dedicatedThroughput

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2019-08-01" --ResourceGroupName $resourceGroupName).name
```

Választhat, hogy a sablon tanusítsa a sablont a PowerShell helyileg telepített verziójával az Azure Cloud Shell helyett. Telepítenie kell [az Azure PowerShell-modult.](/powershell/azure/install-az-ps) Futtassa `Get-Module -ListAvailable Az` a kívánt verzió megkereséséhez.

### <a name="deploy-via-azure-cli"></a>Üzembe helyezés az Azure CLI-n keresztül

Az Azure CLI használata az Azure Resource Manager-sablon üzembe helyezéséhez:

1. **Másolja** a forgatókönyvet.
2. Válassza **a Próbálja ki az** Azure Cloud Shell megnyitásához válassza a Próbálja ki.
3. Kattintson a jobb gombbal az Azure Cloud Shell ablakban, és válassza **a Beillesztés parancsot.**

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the shared database throughput: sharedThroughput
read -p 'Enter the first shared container name: ' sharedContainer1Name
read -p 'Enter the second shared container name: ' sharedContainer2Name
read -p 'Enter the dedicated container name: ' dedicatedContainer1Name
read -p 'Enter the dedicated container throughput: dedicatedThroughput

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json \
   --parameters accountName=$accountName \
   primaryRegion=$primaryRegion \
   secondaryRegion=$secondaryRegion \
   databaseName=$databaseName \
   sharedThroughput=$sharedThroughput \
   sharedContainer1Name=$sharedContainer1Name \
   sharedContainer2Name=$sharedContainer2Name \
   dedicatedContainer1Name=$dedicatedContainer1Name \
   dedicatedThroughput=$dedicatedThroughput

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

A `az cosmosdb show` parancs az újonnan létrehozott Azure Cosmos-fiókot jeleníti meg a kiépítés után. Választhat, hogy a sablon tanusíthatja az Azure CLI helyileg telepített verzióját az Azure Cloud Shell helyett. További információt az [Azure parancssori felület (CLI)](/cli/azure/) cikkében talál.

<a id="create-sproc"></a>

## <a name="create-an-azure-cosmos-db-container-with-server-side-functionality"></a>Azure Cosmos DB-tároló létrehozása kiszolgálóoldali funkciókkal

Egy Azure Resource Manager-sablon használatával létrehozhat egy Azure Cosmos DB-tárolót egy tárolt eljárással, eseményindítóval és felhasználó által definiált függvénnyel.

Másolja a következő példasablont, és telepítse a leírt módon, akár [a PowerShell,](#deploy-with-powershell) akár [az Azure CLI](#deploy-with-azure-cli)használatával.

* Szükség esetén felkeresheti [az Azure Gyorsútmutató galériát,](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-sprocs/) és üzembe helyezheti a sablont az Azure Portalról.
* A sablont letöltheti a helyi számítógépre, vagy létrehozhat egy új `--template-file` sablont, és megadhatja a helyi elérési utat a paraméterrel.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json":::

### <a name="deploy-with-powershell"></a>Üzembe helyezés a PowerShell-lel

Az Azure Resource Manager-sablon üzembe helyezéséhez használja a PowerShellt:

1. **Másolja** a forgatókönyvet.
1. Válassza **a Próbálja ki az** Azure Cloud Shell megnyitásához válassza a Próbálja ki.
1. Kattintson a jobb gombbal az Azure Cloud Shell ablakra, és válassza **a Beillesztés parancsot.**

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$containerName = Read-Host -Prompt "Enter the container name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -containerName $containerName

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2019-08-01" --ResourceGroupName $resourceGroupName).name
```

Választhat, hogy a sablon tanusítsa a sablont a PowerShell helyileg telepített verziójával az Azure Cloud Shell helyett. Telepítenie kell [az Azure PowerShell-modult.](/powershell/azure/install-az-ps) Futtassa `Get-Module -ListAvailable Az` a kívánt verzió megkereséséhez.

### <a name="deploy-with-azure-cli"></a>Üzembe helyezés az Azure CLI-n keresztül

Az Azure CLI használata az Azure Resource Manager-sablon üzembe helyezéséhez:

1. **Másolja** a forgatókönyvet.
2. Válassza **a Próbálja ki az** Azure Cloud Shell megnyitásához válassza a Próbálja ki.
3. Kattintson a jobb gombbal az Azure Cloud Shell ablakban, és válassza **a Beillesztés parancsot.**

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the container name: ' containerName

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   containerName=$containerName

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

## <a name="next-steps"></a>További lépések

Néhány további információforrás:

* [Az Azure Resource Manager dokumentációja](/azure/azure-resource-manager/)
* [Az Azure Cosmos DB erőforrás-szolgáltatósémája](/azure/templates/microsoft.documentdb/allversions)
* [Az Azure Cosmos DB rövid útmutató sablonjai](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [Gyakori Azure Resource Manager-telepítési hibák elhárítási hibáinak elhárítása](../azure-resource-manager/templates/common-deployment-errors.md)
