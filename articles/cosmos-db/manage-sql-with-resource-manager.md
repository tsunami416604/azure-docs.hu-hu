---
title: Azure Cosmos DB létrehozása és kezelése Resource Manager-sablonokkal
description: Azure Resource Manager-sablonok használata az SQL (Core) API-hoz való Azure Cosmos DB létrehozásához és konfigurálásához
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 72a87c3b23e0eed6cfbf1614388702443f4e99d0
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588787"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-with-azure-resource-manager-templates"></a>Azure Cosmos DB SQL (Core) API-erőforrások kezelése Azure Resource Manager-sablonokkal

Ebben a cikkben megismerheti, hogyan segítheti elő az Azure Cosmos DB-fiókok, -adatbázisok és -tárolók felügyeletének automatizálását Azure Resource Manager-sablonok használatával.

Ez a cikk csak az SQL API-fiókok Azure Resource Manager sablonját mutatja be. A [Cassandra](manage-cassandra-with-resource-manager.md), a [Gremlin](manage-gremlin-with-resource-manager.md), a [MongoDB](manage-mongodb-with-resource-manager.md)és a [Table](manage-table-with-resource-manager.md) API-khoz példákat is találhat.

<a id="create-resource"></a>

## <a name="create-an-azure-cosmos-account-database-and-container"></a>Azure Cosmos-fiók,-adatbázis és-tároló létrehozása

Az alábbi Azure Resource Manager-sablon egy Azure Cosmos-fiókot hoz létre a következővel:

* Két tároló, amely az adatbázis szintjén a 400-as számú kért egység/másodperc (RU/s) adatátviteli sebességet használja.
* Egy tároló dedikált 400 RU/s átviteli sebességgel.

A Azure Cosmos DB erőforrások létrehozásához másolja a következő példa sablont, és telepítse azt a leírt módon, a [PowerShell](#deploy-via-powershell) vagy az [Azure CLI](#deploy-via-azure-cli)használatával.

* Igény szerint megtekintheti az [Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/) rövid útmutatóját, és üzembe helyezheti a sablont a Azure Portal.
* Le is töltheti a sablont a helyi számítógépre, vagy létrehozhat egy új sablont, és megadhatja a helyi elérési utat a `--template-file` paraméterrel.

> [!IMPORTANT]
>
> * Amikor helyet ad hozzá vagy távolít el egy Azure Cosmos-fiókhoz, a többi tulajdonságot nem módosíthatja egyidejűleg. Ezeket a műveleteket külön kell elvégezni.
> * A fiókok neve 44 karakterből állhat, és az összes kisbetűs értékre van korlátozva.
> * Az átviteli sebesség értékeinek módosításához küldje el újra a sablont a frissített RU/s értékkel.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

> [!NOTE]
> Nagyméretű partíciós kulccsal rendelkező tároló létrehozásához módosítsa az előző sablont úgy, hogy tartalmazza a `"version":2` tulajdonságot a `partitionKey` objektumon belül.

### <a name="deploy-via-powershell"></a>Üzembe helyezés a PowerShell használatával

A PowerShell használata a Azure Resource Manager sablon üzembe helyezéséhez:

1. **Másolja** a szkriptet.
2. Válassza a **kipróbálás** lehetőséget a Azure Cloud Shell megnyitásához.
3. Kattintson a jobb gombbal a Azure Cloud Shell ablakra, majd válassza a **Beillesztés**lehetőséget.

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

A sablont a Azure Cloud Shell helyett a PowerShell helyileg telepített verziójával is telepítheti. [Telepítenie kell a Azure PowerShell modult](/powershell/azure/install-az-ps). `Get-Module -ListAvailable Az` futtatásával keresse meg a szükséges verziót.

### <a name="deploy-via-azure-cli"></a>Üzembe helyezés az Azure CLI-n keresztül

Az Azure CLI használata a Azure Resource Manager sablon üzembe helyezéséhez:

1. **Másolja** a szkriptet.
2. Válassza a **kipróbálás** lehetőséget a Azure Cloud Shell megnyitásához.
3. Kattintson a jobb gombbal a Azure Cloud Shell ablakra, majd válassza a **Beillesztés**lehetőséget.

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

Az `az cosmosdb show` parancs az újonnan létrehozott Azure Cosmos-fiókot jeleníti meg az üzembe helyezés után. Megadhatja, hogy a sablont az Azure CLI helyileg telepített verziójával telepítse, Azure Cloud Shell. További információt az [Azure parancssori felület (CLI)](/cli/azure/) című cikkben talál.

<a id="create-sproc"></a>

## <a name="create-an-azure-cosmos-db-container-with-server-side-functionality"></a>Kiszolgálóoldali funkcióval rendelkező Azure Cosmos DB tároló létrehozása

Azure Resource Manager sablonnal létrehozhat egy Azure Cosmos DB tárolót egy tárolt eljárással, triggerrel és felhasználó által definiált függvénnyel.

Másolja a következő példa sablont, és telepítse azt a PowerShell vagy az [Azure CLI](#deploy-with-azure-cli) [használatával](#deploy-with-powershell) .

* Megtekintheti az [Azure Gyorsindítás](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-sprocs/) katalógusát, és üzembe helyezheti a sablont a Azure Portal.
* Le is töltheti a sablont a helyi számítógépre, vagy létrehozhat egy új sablont, és megadhatja a helyi elérési utat a `--template-file` paraméterrel.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json":::

### <a name="deploy-with-powershell"></a>Üzembe helyezés a PowerShell-lel

A PowerShell használata a Azure Resource Manager sablon üzembe helyezéséhez:

1. **Másolja** a szkriptet.
1. Válassza a **kipróbálás** lehetőséget a Azure Cloud Shell megnyitásához.
1. Kattintson a jobb gombbal a Azure Cloud Shell ablakra, majd válassza a **Beillesztés**lehetőséget.

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

A sablont a Azure Cloud Shell helyett a PowerShell helyileg telepített verziójával is telepítheti. [Telepítenie kell a Azure PowerShell modult](/powershell/azure/install-az-ps). `Get-Module -ListAvailable Az` futtatásával keresse meg a szükséges verziót.

### <a name="deploy-with-azure-cli"></a>Üzembe helyezés az Azure CLI-vel

Az Azure CLI használata a Azure Resource Manager sablon üzembe helyezéséhez:

1. **Másolja** a szkriptet.
2. Válassza a **kipróbálás** lehetőséget a Azure Cloud Shell megnyitásához.
3. Kattintson a jobb gombbal a Azure Cloud Shell ablakra, majd válassza a **Beillesztés**lehetőséget.

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

## <a name="next-steps"></a>Következő lépések

Íme néhány további erőforrás:

* [Azure Resource Manager dokumentáció](/azure/azure-resource-manager/)
* [Erőforrás-szolgáltatói séma Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Azure Cosmos DB gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [Gyakori Azure Resource Manager telepítési hibák elhárítása](../azure-resource-manager/templates/common-deployment-errors.md)
