---
title: Megismerheti, hogyan kezelhet adatbázisfiókokat az Azure Cosmos DB-ben.
description: Megismerheti, hogyan kezelhet adatbázisfiókokat az Azure Cosmos DB-ben.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: 57116327168a76f971a22b61144850199cb0cbae
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65068811"
---
# <a name="manage-an-azure-cosmos-account"></a>Egy Azure Cosmos-fiók kezelése

Ez a cikk egy Azure Cosmos-fiókot az Azure Portalon, az Azure PowerShell, az Azure parancssori felület és Azure Resource Manager-sablonok a különböző feladatok kezelését ismerteti.

## <a name="create-an-account"></a>Fiók létrehozása

### <a id="create-database-account-via-portal"></a>Azure Portal

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>Azure CLI

```azurecli-interactive
# Create an account
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname' # must be lower case.

az cosmosdb create \
   --name $accountName \
   --resource-group $resourceGroupName \
   --kind GlobalDocumentDB \
   --default-consistency-level Session \
   --locations WestUS=0 EastUS=1 \
   --enable-multiple-write-locations true
```

### <a id="create-database-account-via-ps"></a>Azure PowerShell
```azurepowershell-interactive
# Create an Azure Cosmos Account for Core (SQL) API
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case.

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

### <a id="create-database-account-via-arm-template"></a>Az Azure Resource Manager-sablon

Az Azure Resource Manager-sablon bármely támogatott API-hoz konfigurált két régióban, valamint a beállításokat, válassza ki a konzisztencia szintjét, az Automatikus feladatátvétel és több főkiszolgálós hoz létre Azure Cosmos DB-fiókot. Ez a sablon üzembe helyezése, kattintson az üzembe helyezés az Azure az információs lapon [létrehozása az Azure Cosmos DB-fiók](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-create-multi-region-account)

## <a name="addremove-regions-from-your-database-account"></a>Régiók hozzáadása és eltávolítása az adatbázisfiókból

### <a id="add-remove-regions-via-portal"></a>Azure Portal

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com). 

1. Nyissa meg az Azure Cosmos-fiók, és nyissa meg a **adatok globális replikálása** menü.

1. Régiók hozzáadásához válassza a hatszögek a térképen a **+** felel meg a kívánt régió(k) címkét. Másik lehetőségként egy régió hozzáadásához válassza a **+ Hozzáadás régió** lehetőséget, majd válassza ki a régiót a legördülő menüből.

1. Régiók, törölje a leképezés egy vagy több régióban a kék hatszögek pipákkal kiválasztásával. Vagy válassza az "Ez a" (🗑) ikon mellett a régiót, a jobb oldalon.

1. A módosítások mentéséhez válassza **OK**.

   ![Adja hozzá, vagy távolítsa el a régiók menü](./media/how-to-manage-database-account/add-region.png)

Egy adott régióban írási módban, az írási régió nem távolítható el. Akkor kell feladatátvételt egy másik régióban az aktuális írási régióba törlése előtt.

Egy több régióban írási módban, hozzáadhat és eltávolíthat a bármely régió, ha legalább egy régiót.

### <a id="add-remove-regions-via-cli"></a>Azure CLI

```azurecli-interactive
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

# Create an account with 1 region
az cosmosdb create --name $accountName --resource-group $resourceGroupName --locations westus=0

# Add a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations westus=0 eastus=1

# Remove a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations westus=0
```

### <a id="add-remove-regions-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Create an account with 1 region
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case.

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

## <a id="configure-multiple-write-regions"></a>Több írási régiót konfigurálása

### <a id="configure-multiple-write-regions-portal"></a>Azure Portal

Nyissa meg a **adatok globális replikálása** lapot, és válasszon **engedélyezése** többrégiós írási műveletek engedélyezéséhez. Miután engedélyezte a többrégiós írást, az olvasási régióban, amely jelenleg a fiók rendelkezik fog válik és olvasási régiók. 

> [!NOTE]
> Miután engedélyezte a többrégiós írások, akkor nem tiltható le. 

![Az Azure Cosmos-fiók konfigurálása több főkiszolgálós képernyőképe](./media/how-to-manage-database-account/single-to-multi-master.png)

Vegye fel a kapcsolatot a askcosmosdb@microsoft.com alias ezen szolgáltatásáról kapcsolatos további kérdéseit. 

### <a id="configure-multiple-write-regions-cli"></a>Azure CLI

```azurecli-interactive
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'
az cosmosdb update --name $accountName --resource-group $resourceGroupName --enable-multiple-write-locations true
```

### <a id="configure-multiple-write-regions-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Update an Azure Cosmos Account from single to multi-master

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$account.Properties.enableMultipleWriteLocations = "true"
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="configure-multiple-write-regions-arm"></a>Resource Manager-sablon

Egy fiók telepíthető át egyetlen főágból több főkiszolgálós beállítás és a fiók létrehozására szolgáló Resource Manager-sablon üzembe helyezésével `enableMultipleWriteLocations: true`. A következő Azure Resource Manager-sablon egy operációs rendszer nélküli helyreállításra minimális sablont, amely helyezi üzembe az SQL API-hoz az Azure Cosmos DB-fiók egyetlen régióban, valamint egy több főkiszolgálós.

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
                "locations": [
                    {
                        "locationName": "[parameters('location')]",
                        "failoverPriority": 0
                    }
                ],
                "enableMultipleWriteLocations": true
            }
        }
    ]
}
```

## <a id="automatic-failover"></a>Az Azure Cosmos DB-fiók automatikus feladatátvétel engedélyezése

Az Automatikus feladatátvétel beállítás lehetővé teszi az Azure Cosmos DB a régió, felhasználói beavatkozás nélkül feladatátvételi legmagasabb prioritású feladatokat kell egy régió elérhetetlenné válik. Ha automatikus feladatátvétel engedélyezve van, régió prioritását módosíthatja. Fióknak rendelkeznie kell két vagy több régió automatikus feladatátvétel engedélyezéséhez.

### <a id="enable-automatic-failover-via-portal"></a>Azure Portal

1. Az Azure Cosmos DB-fiók megnyitása a **adatok globális replikálása** ablaktáblán.

2. Válassza a panel tetején lévő **automatikus feladatátvétel**.

   ![Adatok globális replikálása menü](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Az a **automatikus feladatátvétel** ablaktáblán ellenőrizze, hogy **automatikus feladatátvétel engedélyezése** értékre van állítva **ON**. 

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

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Állítsa be a feladatátvételi prioritásokat az Azure Cosmos-fiók

Miután egy Cosmos-fiók automatikus feladatátvétel van beállítva, régiók feladatátvételi prioritását módosíthatja.

> [!IMPORTANT]
> Az írási régió (feladatátvételi prioritás értéke nulla) nem módosítható. Ha a fiók automatikus feladatátvételre van konfigurálva. Az írási régió módosítása, tiltsa le az automatikus feladatátvételt, és manuális feladatátvételt hajt végre.

### <a id="set-failover-priorities-via-portal"></a>Azure Portal

1. Az Azure Cosmos-fiók megnyitása a **adatok globális replikálása** ablaktáblán.

2. Válassza a panel tetején lévő **automatikus feladatátvétel**.

   ![Adatok globális replikálása menü](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Az a **automatikus feladatátvétel** ablaktáblán ellenőrizze, hogy **automatikus feladatátvétel engedélyezése** értékre van állítva **ON**.

4. Feladatátvétel prioritásának módosítására, húzza az olvasási régiók a három ponttal jelölt keresztül jelennek meg, ha rámutat a sor bal oldalán.

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

## <a id="manual-failover"></a>Hajtsa végre manuális feladatátvételt az Azure Cosmos-fiók

> [!IMPORTANT]
> A művelet sikeres lehessen a manuális feladatátvételt kell állítani az Azure Cosmos-fiók.

A fiók írási régió módosítása is hozzátartozik a folyamat manuális feladatátvétel végrehajtásához (feladatátvételi prioritás = 0) a fiókhoz konfigurált egy másik régióba.

> [!NOTE]
> Több főkiszolgálós fiókok nem manuális feladatátvételt kell végrehajtani. Alkalmazások az Azure Cosmos DB SDK segítségével az SDK szolgáltatás észleli, ha egy régió elérhetetlenné válik, majd irányítja át automatikusan a következő legközelebb eső régióban, többkiszolgálós API használata az SDK-ban.

### <a id="enable-manual-failover-via-portal"></a>Azure Portal

1. Nyissa meg az Azure Cosmos-fiók, és nyissa meg a **adatok globális replikálása** menü.

2. Válassza a menü tetején **kézi feladatátvételt**.

   ![Adatok globális replikálása menü](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Az a **kézi feladatátvételt** menüben válassza ki az új írási régiót. Válassza ki a jelölőnégyzet bejelölésével jelezze, hogy tudomásul veszi ezt a beállítást módosítja az írási régiót.

4. A feladatátvétel indításához válasszon **OK**.

   ![Manuális feladatátvétel menü a portálon](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Azure CLI

```azurecli-interactive
# Assume account currently has regions with priority: eastus=0 westus=1
# Change the priority order to trigger a failover of the write region
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations westus=0 eastus=1
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

További információt és példákat az Azure Cosmos-fiók, valamint az adatbázis és a tárolók kezelésének módját olvassa el a következő cikkeket:

* [Azure PowerShell-lel az Azure Cosmos DB kezelése](manage-with-powershell.md)
* [Azure CLI-vel az Azure Cosmos DB kezelése](manage-with-cli.md)
