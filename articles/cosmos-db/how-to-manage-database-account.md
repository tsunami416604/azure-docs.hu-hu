---
title: Megismerheti, hogyan kezelhet adatbázisfiókokat az Azure Cosmos DB-ben.
description: Megismerheti, hogyan kezelhet adatbázisfiókokat az Azure Cosmos DB-ben.
author: christopheranderson
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/17/2018
ms.author: chrande
ms.openlocfilehash: 1486ab3240036abedea2cbb3cbe93e5c061691d8
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55467407"
---
# <a name="manage-an-azure-cosmos-account"></a>Egy Azure Cosmos-fiók kezelése

Ez a cikk ismerteti az Azure Cosmos DB-fiók kezeléséhez. Megismerheti, hogyan állítsa be a többkiszolgálós, hozzáadása vagy eltávolítása egy régióban, több írási régiót konfigurálása és feladatátvételi prioritások beállítása. 

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

### <a id="create-database-account-via-portal"></a>Azure Portal

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>Azure CLI

```bash
# Create an account
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group Name>
```

## <a name="configure-clients-for-multi-homing"></a>Ügyfelek konfigurálása többkiszolgálós környezethez

### <a id="configure-clients-multi-homing-dotnet"></a>.NET SDK

```csharp
// Create a new connection policy.
ConnectionPolicy policy = new ConnectionPolicy
    {
        // Note: These aren't required settings for multi-homing,
        // just suggested defaults.
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true,
    };
// Add regions to preferred locations.
// The name of the location will match what you see in the portal, etc.
policy.PreferredLocations.Add("East US");
policy.PreferredLocations.Add("North Europe");

// Pass the connection policy with the preferred locations on it to the client.
DocumentClient client = new DocumentClient(new Uri(this.accountEndpoint), this.accountKey, policy);
```

### <a id="configure-clients-multi-homing-java-async"></a>Java Async SDK

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setPreferredLocations(Collections.singleton("West US"));
AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKey(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConnectionPolicy(policy).build();
```

### <a id="configure-clients-multi-homing-java-sync"></a>Java Sync SDK

```java
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
Collection<String> preferredLocations = new ArrayList<String>();
preferredLocations.add("Australia East");
connectionPolicy.setPreferredLocations(preferredLocations);
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy);
```

### <a id="configure-clients-multi-homing-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Set up the connection policy with your preferred regions.
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.PreferredLocations = ["West US", "Australia East"];

// Pass that connection policy to the client.
const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy
});
```

### <a id="configure-clients-multi-homing-python"></a>Python SDK

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.PreferredLocations = ['West US', 'Japan West']
client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy)

```

## <a name="addremove-regions-from-your-database-account"></a>Régiók hozzáadása és eltávolítása az adatbázisfiókból

### <a id="add-remove-regions-via-portal"></a>Azure Portal

1. Nyissa meg az Azure Cosmos DB-fiókot, és nyissa meg a **adatok globális replikálása** menü.

2. Régiók hozzáadásához válassza a hatszögek a térképen a **+** címke, amelyek megfelelnek a kívánt régiót. Egy régióban hozzáadásához válassza a **+ Hozzáadás régió** lehetőséget, majd válassza ki a régiót a legördülő menüből.

3. Régiók, törölje a leképezés egy vagy több régióban a kék hatszögek pipákkal kiválasztásával. Vagy válassza az "Ez a" (🗑) ikon mellett a régiót, a jobb oldalon.

4. A módosítások mentéséhez válassza **OK**.

   ![Adja hozzá, vagy távolítsa el a régiók menü](./media/how-to-manage-database-account/add-region.png)

Egyetlen régióban írható módban az írási régió nem távolítható el. Kell átadja a feladatokat egy másik régióba, mielőtt törölheti, hogy az aktuális írási régióba.

Többrégiós írni módban, hozzáadhat és eltávolíthat bármelyik régióban, ha legalább egy régiót.

### <a id="add-remove-regions-via-cli"></a>Azure CLI

```bash
# Given an account created with 1 region like so
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations 'eastus=0'

# Add a new region by adding another region to the list
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations 'eastus=0 westus=1'

# Remove a region by removing a region from the list
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations 'westus=0'
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

A következő JSON-kód példázza az Azure Resource Manager-sablon. Egy Azure Cosmos DB-fiókot egy konzisztencia-szabályzat, korlátozott frissesség, üzembe helyezéséhez használható. A maximális frissesség időköze: 5 másodperc. A kérelmek maximális számát elavult, amely még elfogadható érték 100. A Resource Manager-sablon formátuma és a szintaxissal kapcsolatos további információkért lásd: [Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

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


## <a id="manual-failover"></a>Manuális feladatátvétel az Azure Cosmos DB-fiók engedélyezése

### <a id="enable-manual-failover-via-portal"></a>Azure Portal

1. Nyissa meg az Azure Cosmos DB-fiókot, és nyissa meg a **adatok globális replikálása** menü.

2. Válassza a menü tetején **kézi feladatátvételt**.

   ![Adatok globális replikálása menü](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Az a **kézi feladatátvételt** menüben válassza ki az új írási régiót. Válassza ki a jelölőnégyzet bejelölésével jelezze, hogy tudomásul veszi ezt a beállítást módosítja az írási régiót.

4. A feladatátvétel indításához válasszon **OK**.

   ![Manuális feladatátvétel menü a portálon](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Azure CLI

```bash
# Given your account currently has regions with priority like so: 'eastus=0 westus=1'
# Change the priority order to trigger a failover of the write region
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations 'eastus=1 westus=0'
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

## <a name="set-failover-priorities-for-your-azure-cosmos-db-account"></a>Az Azure Cosmos DB-fiókot a feladatátvételi prioritások beállítása

### <a id="set-failover-priorities-via-portal"></a>Azure Portal

1. Az Azure Cosmos DB-fiók megnyitása a **adatok globális replikálása** ablaktáblán. 

2. Válassza a panel tetején lévő **automatikus feladatátvétel**.

   ![Adatok globális replikálása menü](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Az a **automatikus feladatátvétel** ablaktáblán ellenőrizze, hogy **automatikus feladatátvétel engedélyezése** értékre van állítva **ON**. 

4. Feladatátvétel prioritásának módosítására, húzza az olvasási régiók a három ponttal jelölt keresztül jelennek meg, ha rámutat a sor bal oldalán. 

5. Kattintson a **Mentés** gombra.

   ![Automatikus feladatátvétel menü a portálon](./media/how-to-manage-database-account/automatic-failover.png)

Ebben a menüben az írási régió nem módosítható. Az írási régió manuális módosításához manuális feladatátvételt kell végrehajtania.

### <a id="set-failover-priorities-via-cli"></a>Azure CLI

```bash
az cosmosdb failover-priority-change --name <Azure Cosmos account name> --resource-group <Resource Group name> --failover-policies 'eastus=0 westus=2 southcentralus=1'
```

## <a name="next-steps"></a>További lépések

Ismerje meg az Azure Cosmos DB-szintek és adatok ütközések konzisztencia kezelése. Lásd az alábbi cikkeket:

* [Konzisztencia kezelése](how-to-manage-consistency.md)
* [Régiók közötti ütközések kezelése](how-to-manage-conflicts.md)

