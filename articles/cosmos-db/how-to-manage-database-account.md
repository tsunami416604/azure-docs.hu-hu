---
title: Megismerheti, hogyan kezelhet adatbázisfiókokat az Azure Cosmos DB-ben.
description: Megismerheti, hogyan kezelhet adatbázisfiókokat az Azure Cosmos DB-ben.
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/08/2019
ms.author: rimman
ms.openlocfilehash: b2b5e58ca480aa3abaa0766319977b8d1160ebeb
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59283001"
---
# <a name="manage-an-azure-cosmos-account"></a>Egy Azure Cosmos-fiók kezelése

Ez a cikk ismerteti az Azure Cosmos-fiók kezeléséhez. Megtudhatja, hogyan állítsa be a többhelyű, adja hozzá vagy távolítsa el a régióban, több írási régiót konfigurálása és feladatátvételi prioritások beállítása. 

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

### <a id="create-database-account-via-portal"></a>Azure Portal

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>Azure CLI

```bash
# Create an account
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group Name>
```

## <a name="configure-clients-for-multi-homing"></a>Ügyfelek konfigurálása többkiszolgálós környezethez

### <a id="configure-clients-multi-homing-dotnet"></a>.NET SDK-val v2

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");

// Pass the connection policy with the preferred locations on it to the client.
DocumentClient client = new DocumentClient(new Uri(this.accountEndpoint), this.accountKey, policy);
```

### <a id="configure-clients-multi-homing-dotnet-v3"></a>.NET SDK-val v3 (előzetes verzió)

```csharp
CosmosConfiguration config = new CosmosConfiguration("endpoint", "key");
config.UseCurrentRegion("West US");
CosmosClient client = new CosmosClient(config);
```

### <a id="configure-clients-multi-homing-java-async"></a>Java Async SDK

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

### <a id="configure-clients-multi-homing-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

### <a id="configure-clients-multi-homing-python"></a>Python SDK

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="addremove-regions-from-your-database-account"></a>Régiók hozzáadása és eltávolítása az adatbázisfiókból

### <a id="add-remove-regions-via-portal"></a>Azure Portal

1. Nyissa meg az Azure Cosmos-fiók, és nyissa meg a **adatok globális replikálása** menü.

2. Régiók hozzáadásához válassza a hatszögek a térképen a **+** , amelyek megfelelnek a kívánt régió(k) címkét. Másik lehetőségként egy régió hozzáadásához válassza a **+ Hozzáadás régió** lehetőséget, majd válassza ki a régiót a legördülő menüből.

3. Régiók, törölje a leképezés egy vagy több régióban a kék hatszögek pipákkal kiválasztásával. Vagy válassza az "Ez a" (🗑) ikon mellett a régiót, a jobb oldalon.

4. A módosítások mentéséhez válassza **OK**.

   ![Adja hozzá, vagy távolítsa el a régiók menü](./media/how-to-manage-database-account/add-region.png)

Egy adott régióban írási módban, az írási régió nem távolítható el. Akkor kell feladatátvételt egy másik régióban az aktuális írási régióba törlése előtt.

Egy több régióban írási módban, hozzáadhat és eltávolíthat a bármely régió, ha legalább egy régiót.

### <a id="add-remove-regions-via-cli"></a>Azure CLI

```bash
# Create an account with 1 region
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations eastus=0

# Add a region
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations eastus=0 westus=1

# Remove a region
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations westus=0
```

## <a name="configure-multiple-write-regions"></a>Több írási régió konfigurálása

### <a id="configure-multiple-write-regions-portal"></a>Azure Portal

Adatbázisfiók létrehozásakor győződjön meg arról, hogy a **Többrégiós írás** beállítás engedélyezve van.

![Az Azure Cosmos-fiók létrehozása képernyőképe](./media/how-to-manage-database-account/account-create.png)

### <a id="configure-multiple-write-regions-cli"></a>Azure CLI

```bash
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-multiple-write-locations true
```

### <a id="configure-multiple-write-regions-arm"></a>Resource Manager-sablon

A következő JSON-kód egyik példája egy [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) sablont. Használhatja egy Azure Cosmos-fiókra a telepítendő [korlátozott frissesség konzisztenciaszint](consistency-levels.md). A maximális frissesség időköze 5 másodperc. A kérelmek maximális számát elavult, amely még elfogadható 100 értékre van állítva. A Resource Manager-sablon formátuma és a szintaxissal kapcsolatos további információkért lásd: [Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String"
        },
        "locationName": {
            "type": "String"
        },
        "defaultExperience": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2015-04-08",
            "location": "[parameters('location')]",
            "tags": {
                "defaultExperience": "[parameters('defaultExperience')]"
            },
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": {
                    "defaultConsistencyLevel": "BoundedStaleness",
                    "maxIntervalInSeconds": 5,
                    "maxStalenessPrefix": 100
                },
                "locations": [
                    {
                        "id": "[concat(parameters('name'), '-', parameters('location'))]",
                        "failoverPriority": 0,
                        "locationName": "[parameters('locationName')]"
                    }
                ],
                "isVirtualNetworkFilterEnabled": false,
                "enableMultipleWriteLocations": true,
                "virtualNetworkRules": [],
                "dependsOn": []
            }
        }
    ]
}
```


## <a id="manual-failover"></a>Manuális feladatátvétel az Azure Cosmos-fiók engedélyezése

### <a id="enable-manual-failover-via-portal"></a>Azure Portal

1. Nyissa meg az Azure Cosmos-fiók, és nyissa meg a **adatok globális replikálása** menü.

2. Válassza a menü tetején **kézi feladatátvételt**.

   ![Adatok globális replikálása menü](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Az a **kézi feladatátvételt** menüben válassza ki az új írási régiót. Válassza ki a jelölőnégyzet bejelölésével jelezze, hogy tudomásul veszi ezt a beállítást módosítja az írási régiót.

4. A feladatátvétel indításához válasszon **OK**.

   ![Manuális feladatátvétel menü a portálon](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Azure CLI

```bash
# Given your account currently has regions with priority: eastus=0 westus=1
# Change the priority order to trigger a failover of the write region
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations westus=0 eastus=1
```

## <a id="automatic-failover"></a>Az Azure Cosmos DB-fiók automatikus feladatátvétel engedélyezése

### <a id="enable-automatic-failover-via-portal"></a>Azure Portal

1. Az Azure Cosmos DB-fiók megnyitása a **adatok globális replikálása** ablaktáblán. 

2. Válassza a panel tetején lévő **automatikus feladatátvétel**.

   ![Adatok globális replikálása menü](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Az a **automatikus feladatátvétel** ablaktáblán ellenőrizze, hogy **automatikus feladatátvétel engedélyezése** értékre van állítva **ON**. 

4. Kattintson a **Mentés** gombra.

   ![Automatikus feladatátvétel menü a portálon](./media/how-to-manage-database-account/automatic-failover.png)

Is beállíthat a feladatátvételi prioritások ebben a menüben.

### <a id="enable-automatic-failover-via-cli"></a>Azure CLI

```bash
# Enable automatic failover on account creation
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover true

# Enable automatic failover on an existing account
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover true

# Disable automatic failover on an existing account
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover false
```

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Állítsa be a feladatátvételi prioritásokat az Azure Cosmos-fiók

### <a id="set-failover-priorities-via-portal"></a>Azure Portal

1. Az Azure Cosmos-fiók megnyitása a **adatok globális replikálása** ablaktáblán. 

2. Válassza a panel tetején lévő **automatikus feladatátvétel**.

   ![Adatok globális replikálása menü](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Az a **automatikus feladatátvétel** ablaktáblán ellenőrizze, hogy **automatikus feladatátvétel engedélyezése** értékre van állítva **ON**. 

4. Feladatátvétel prioritásának módosítására, húzza az olvasási régiók a három ponttal jelölt keresztül jelennek meg, ha rámutat a sor bal oldalán. 

5. Kattintson a **Mentés** gombra.

   ![Automatikus feladatátvétel menü a portálon](./media/how-to-manage-database-account/automatic-failover.png)

Ebben a menüben nem módosíthatja az írási régiót. Az írási régió manuális módosításához manuális feladatátvételt kell végrehajtania.

### <a id="set-failover-priorities-via-cli"></a>Azure CLI

```bash
# Assume region order is initially eastus=0 westus=1 automatic failover on account creation
az cosmosdb failover-priority-change --name <Azure Cosmos account name> --resource-group <Resource Group name> --failover-policies westus=0 eastus=1
```

## <a name="next-steps"></a>További lépések

Olvassa el a következő cikkeket:

* [Konzisztencia kezelése](how-to-manage-consistency.md)
* [Régiók közötti ütközések kezelése](how-to-manage-conflicts.md)
* [Globális terjesztés – technikai részletek](global-dist-under-the-hood.md)
* [Az alkalmazások több főkiszolgálós konfigurálása](how-to-multi-master.md)
* [Ügynökönkénti ügyfelek konfigurálása](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Régiók hozzáadása vagy eltávolítása az Azure Cosmos DB-fiókból](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Hozzon létre egy egyéni ütközésfeloldási házirend](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)

