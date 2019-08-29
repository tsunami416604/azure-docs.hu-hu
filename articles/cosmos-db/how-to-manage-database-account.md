---
title: Megismerheti, hogyan kezelhet adatbázisfiókokat az Azure Cosmos DB-ben.
description: Megismerheti, hogyan kezelhet adatbázisfiókokat az Azure Cosmos DB-ben.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: db7746bc91935c0385e97d494a45d34819665ced
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093388"
---
# <a name="manage-an-azure-cosmos-account"></a>Azure Cosmos-fiók kezelése

Ez a cikk azt ismerteti, hogyan kezelhető az Azure Cosmos-fiók különböző feladatai a Azure Portal, a Azure PowerShell, az Azure CLI és a Azure Resource Manager sablonok használatával.

## <a name="create-an-account"></a>Fiók létrehozása

### <a id="create-database-account-via-portal"></a>Azure Portal

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>Azure CLI

```azurecli-interactive
# Create an account
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname' # must be lower case and < 31 characters

az cosmosdb create \
   --name $accountName \
   --resource-group $resourceGroupName \
   --kind GlobalDocumentDB \
   --default-consistency-level Session \
   --locations regionName=WestUS failoverPriority=0 isZoneRedundant=False \
   --locations regionName=EastUS failoverPriority=1 isZoneRedundant=False \
   --enable-multiple-write-locations true
```

### <a id="create-database-account-via-ps"></a>Azure PowerShell
```azurepowershell-interactive
# Create an Azure Cosmos account for Core (SQL) API
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case and < 31 characters

$locations = @(
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="East US"; "failoverPriority"=1 }
)

$consistencyPolicy = @{
    "defaultConsistencyLevel"="BoundedStaleness";
    "maxIntervalInSeconds"=300;
    "maxStalenessPrefix"=100000
}

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy;
    "enableMultipleWriteLocations"="true"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="create-database-account-via-arm-template"></a>Azure Resource Manager sablon

Ez a Azure Resource Manager sablon létrehoz egy Azure Cosmos-fiókot minden olyan támogatott API számára, amely két régióval van konfigurálva, és lehetőség van a konzisztencia, az automatikus feladatátvétel és a több főkiszolgáló kiválasztására. A sablon üzembe helyezéséhez kattintson az üzembe helyezés az Azure-ra lehetőségre a readme oldalon, [Azure Cosmos-fiók létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-create-multi-region-account)

## <a name="addremove-regions-from-your-database-account"></a>Régiók hozzáadása és eltávolítása az adatbázisfiókból

### <a id="add-remove-regions-via-portal"></a>Azure Portal

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com). 

1. Nyissa meg az Azure Cosmos-fiókját, és nyissa meg az adatreplikálás **globális** menüjét.

1. Régiók hozzáadásához válassza ki a térképen **+** a hatszögeket a kívánt régió (k) nek megfelelő címkével. Másik lehetőségként a régió hozzáadásához válassza a **+ régió hozzáadása** lehetőséget, és válasszon egy régiót a legördülő menüből.

1. A régiók eltávolításához törölje a térképen egy vagy több régiót úgy, hogy a kék hatszögeket jelöli a pipa jelekkel. Vagy válassza a jobb oldalon található régió🗑melletti "szemétkosár" () ikont.

1. A módosítások mentéséhez kattintson **az OK gombra**.

   ![Régiók hozzáadása vagy eltávolítása menü](./media/how-to-manage-database-account/add-region.png)

Egy egyrégiós írási módban nem távolíthatja el az írási régiót. Az aktuális írási régió törlése előtt a feladatátvételt egy másik régióba kell átadnia.

Többrégiós írási módban bármely régiót hozzáadhat vagy eltávolíthat, ha legalább egy régióval rendelkezik.

### <a id="add-remove-regions-via-cli"></a>Azure CLI

```azurecli-interactive
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname' # must be lower case and <31 characters

# Create an account with 1 region
az cosmosdb create --name $accountName --resource-group $resourceGroupName --locations regionName=westus failoverPriority=0 isZoneRedundant=False

# Add a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations regionName=westus failoverPriority=0 isZoneRedundant=False --locations regionName=EastUS failoverPriority=1 isZoneRedundant=False

# Remove a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations regionName=westus failoverPriority=0 isZoneRedundant=False
```

### <a id="add-remove-regions-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Create an account with 1 region
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case and <31 characters

$locations = @( @{ "locationName"="West US"; "failoverPriority"=0 } )
$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }
$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy
}
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties

# Add a region
$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$locations = @( 
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="East Us"; "failoverPriority"=1 } 
)

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties

# Azure Resource Manager does not wait on the resource update
Write-Host "Confirm region added before continuing..."

# Remove a region
$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$locations = @( @{ "locationName"="West US"; "failoverPriority"=0 } )

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a id="configure-multiple-write-regions"></a>Több írási régió konfigurálása

### <a id="configure-multiple-write-regions-portal"></a>Azure Portal

Nyissa meg az adatreplikálás globálisan lapot, és válassza az **Engedélyezés** lehetőséget a többrégiós írások engedélyezéséhez. Miután engedélyezte a többrégiós írást, az összes olyan olvasási régió, amelyre jelenleg a fiók tartozik, olvasási és írási régiók lesznek. 

> [!NOTE]
> A többrégiós írások engedélyezése után nem lehet letiltani. 

![Az Azure Cosmos-fiók több főkiszolgálós képernyőképet konfigurál](./media/how-to-manage-database-account/single-to-multi-master.png)

Ha további kérdése van a askcosmosdb@microsoft.com szolgáltatással kapcsolatban, lépjen kapcsolatba az aliassal. 

### <a id="configure-multiple-write-regions-cli"></a>Azure CLI

```azurecli-interactive
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'
az cosmosdb update --name $accountName --resource-group $resourceGroupName --enable-multiple-write-locations true
```

### <a id="configure-multiple-write-regions-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Update an Azure Cosmos account from single to multi-master

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$account.Properties.enableMultipleWriteLocations = "true"
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="configure-multiple-write-regions-arm"></a>Resource Manager-sablon

A fiók a fiók és a beállítás `enableMultipleWriteLocations: true`létrehozásához használt Resource Manager-sablon üzembe helyezésével telepíthető át egyetlen főkiszolgálóról a több főkiszolgálóra. A következő Azure Resource Manager sablon egy egyszerű sablon, amely az SQL API-hoz két régióval és több írási hellyel rendelkező Azure Cosmos-fiókot helyez üzembe.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String",
            "defaultValue": "[resourceGroup().location]"
        },
        "primaryRegion":{
            "type":"string",
            "metadata": {
                "description": "The primary replica region for the Cosmos DB account."
            }
        },
        "secondaryRegion":{
            "type":"string",
            "metadata": {
              "description": "The secondary replica region for the Cosmos DB account."
          }
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2015-04-08",
            "location": "[parameters('location')]",
            "tags": {},
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": { "defaultConsistencyLevel": "Session" },
                "locations":
                [
                    {
                        "locationName": "[parameters('primaryRegion')]",
                        "failoverPriority": 0
                    },
                    {
                        "locationName": "[parameters('secondaryRegion')]",
                        "failoverPriority": 1
                    }
                ],
                "enableMultipleWriteLocations": true
            }
        }
    ]
}
```

## <a id="automatic-failover"></a>Automatikus feladatátvétel engedélyezése az Azure Cosmos-fiókhoz

Az automatikus feladatátvételi beállítás lehetővé teszi, hogy az Azure Cosmos DB a legmagasabb feladatátvételi prioritással rendelkező régióba feladatátvételt hajtson végre, ha egy adott régió elérhetetlenné válik. Ha engedélyezve van az automatikus feladatátvétel, a régió prioritását módosíthatja. A fióknak két vagy több régióval kell rendelkeznie az automatikus feladatátvétel engedélyezéséhez.

### <a id="enable-automatic-failover-via-portal"></a>Azure Portal

1. Az Azure Cosmos-fiókjából nyissa meg az **adatok replikálása globális** ablaktáblát.

2. A panel tetején válassza az **automatikus feladatátvétel**lehetőséget.

   ![Adatok globális replikálása menü](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Az **automatikus feladatátvétel** ablaktáblán győződjön meg arról, hogy az **automatikus feladatátvétel engedélyezése** beállításbe értékre van állítva. 

4. Kattintson a **Mentés** gombra.

   ![Automatikus feladatátvétel menü a portálon](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="enable-automatic-failover-via-cli"></a>Azure CLI

```azurecli-interactive
# Enable automatic failover on an existing account
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

az cosmosdb update --name $accountName --resource-group $resourceGroupName --enable-automatic-failover true
```

### <a id="enable-automatic-failover-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$account.Properties.enableAutomaticFailover="true";
$CosmosDBProperties = $account.Properties;

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Feladatátvételi prioritások beállítása az Azure Cosmos-fiókhoz

Ha egy Cosmos-fiók automatikus feladatátvételre van konfigurálva, a régiók feladatátvételi prioritása módosítható.

> [!IMPORTANT]
> Az írási régió (nulla feladatátvételi prioritása) nem módosítható, ha a fiók konfigurálva van az automatikus feladatátvételre. Az írási régió módosításához le kell tiltania az automatikus feladatátvételt, és manuális feladatátvételt kell végrehajtania.

### <a id="set-failover-priorities-via-portal"></a>Azure Portal

1. Az Azure Cosmos-fiókjából nyissa meg az **adatok replikálása globális** ablaktáblát.

2. A panel tetején válassza az **automatikus feladatátvétel**lehetőséget.

   ![Adatok globális replikálása menü](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Az **automatikus feladatátvétel** ablaktáblán győződjön meg arról, hogy az **automatikus feladatátvétel engedélyezése** beállításbe értékre van állítva.

4. A feladatátvételi prioritás módosításához húzza az olvasási régiókat a sor bal oldalán lévő három pontra, amikor az egérmutatót a fölé viszi.

5. Kattintson a **Mentés** gombra.

   ![Automatikus feladatátvétel menü a portálon](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="set-failover-priorities-via-cli"></a>Azure CLI

```azurecli-interactive
# Assume region order is initially eastus=0 westus=1 southeastasia=2 on account creation
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

az cosmosdb failover-priority-change --name $accountName --resource-group $resourceGroupName --failover-policies eastus=0 southeastasia=1 westus=2
```

### <a id="set-failover-priorities-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Assume account currently has regions with priority: West US = 0, East US = 1, Southeast Asia = 2
$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$failoverPolicies = @(
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="Southeast Asia"; "failoverPriority"=1 },
    @{ "locationName"="East US"; "failoverPriority"=2 }
)

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

## <a id="manual-failover"></a>Manuális feladatátvétel végrehajtása Azure Cosmos-fiókon

> [!IMPORTANT]
> Ahhoz, hogy a művelet sikeres legyen, az Azure Cosmos-fióknak konfigurálva kell lennie a manuális feladatátvételhez.

A manuális feladatátvétel végrehajtásának folyamata magában foglalja a fiók írási régiójának (feladatátvételi prioritás = 0) a fiókhoz konfigurált másik régióba való módosítását.

> [!NOTE]
> A több főkiszolgálós fiókok feladatátvétele nem végezhető el manuálisan. Az Azure Cosmos SDK-t használó alkalmazások esetében az SDK felismeri, ha egy régió elérhetetlenné válik, majd automatikusan átirányítja a következő legközelebbi régióba, ha az SDK-ban több-vezérlési API-t használ.

### <a id="enable-manual-failover-via-portal"></a>Azure Portal

1. Nyissa meg az Azure Cosmos-fiókját, és nyissa meg az adatreplikálás **globális** menüjét.

2. A menü tetején válassza a **manuális feladatátvétel**lehetőséget.

   ![Adatok globális replikálása menü](./media/how-to-manage-database-account/replicate-data-globally.png)

3. A **manuális feladatátvétel** menüben válassza ki az új írási régiót. Jelölje be a jelölőnégyzetet, és jelezze, hogy ezt a lehetőséget az írási régió módosításakor adja meg.

4. A feladatátvétel elindításához kattintson **az OK gombra**.

   ![Manuális feladatátvétel menü a portálon](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Azure CLI

```azurecli-interactive
# Assume account currently has regions with priority: eastus=0 westus=1
# Change the priority order to trigger a failover of the write region
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations regionName=westus failoverPriority=0 isZoneRedundant=False --locations regionName=eastus failoverPriority=1 isZoneRedundant=False
```

### <a id="enable-manual-failover-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Assume account currently has regions with priority: West US = 0, East US = 1
# Change the priority order to trigger a failover of the write region
$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$locations = @(
    @{ "locationName"="East US"; "failoverPriority"=0 },
    @{ "locationName"="West US"; "failoverPriority"=1 }
)

$account.Properties.locations=$locations;
$CosmosDBProperties = $account.Properties;

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a name="next-steps"></a>További lépések

Az Azure Cosmos-fiók, valamint az adatbázis és a tárolók kezelésével kapcsolatos további információkért és példákért olvassa el a következő cikkeket:

* [Azure Cosmos DB kezelése Azure PowerShell használatával](manage-with-powershell.md)
* [Az Azure Cosmos DB kezelése az Azure CLI-vel](manage-with-cli.md)
