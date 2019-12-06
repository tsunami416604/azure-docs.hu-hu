---
title: Megismerheti, hogyan kezelhet adatbázisfiókokat az Azure Cosmos DB-ben.
description: Megtudhatja, hogyan kezelheti Azure Cosmos DB erőforrásait a Azure Portal, a PowerShell, a CLI és a Azure Resource Manager sablonok használatával
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 61670d757611bd0c1dd11c389282b18edb3d7fa1
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873675"
---
# <a name="manage-an-azure-cosmos-account"></a>Azure Cosmos-fiók kezelése

Ez a cikk azt ismerteti, hogyan kezelhető az Azure Cosmos-fiók különböző feladatai a Azure Portal, a Azure PowerShell, az Azure CLI és a Azure Resource Manager sablonok használatával.

## <a name="create-an-account"></a>Fiók létrehozása

### <a id="create-database-account-via-portal"></a>Azure Portal

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>Azure CLI

Lásd: [Azure Cosmos db fiók létrehozása az Azure CLI-vel](manage-with-cli.md#create-an-azure-cosmos-db-account)

### <a id="create-database-account-via-ps"></a>Azure PowerShell

Tekintse meg [Azure Cosmos db fiók létrehozása a PowerShell használatával](manage-with-powershell.md#create-account) című témakört.

### <a id="create-database-account-via-arm-template"></a>Azure Resource Manager sablon

Ez a Azure Resource Manager-sablon létrehoz egy Azure Cosmos-fiókot az SQL API-hoz két régióval és lehetőséggel, hogy kiválassza a konzisztencia szintjét, az automatikus feladatátvételt és a több főkiszolgálót. A sablon üzembe helyezéséhez kattintson az üzembe helyezés az Azure-ra lehetőségre a readme oldalon, [Azure Cosmos-fiók létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-sql)

## <a name="addremove-regions-from-your-database-account"></a>Régiók hozzáadása és eltávolítása az adatbázisfiókból

### <a id="add-remove-regions-via-portal"></a>Azure Portal

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).

1. Nyissa meg az Azure Cosmos-fiókját, és nyissa meg az **adatreplikálás globális** menüjét.

1. Régiók hozzáadásához válassza ki a térképen a hatszögeket a kívánt régió (k) nek megfelelő **+** címkével. Másik lehetőségként a régió hozzáadásához válassza a **+ régió hozzáadása** lehetőséget, és válasszon egy régiót a legördülő menüből.

1. A régiók eltávolításához törölje a térképen egy vagy több régiót úgy, hogy a kék hatszögeket jelöli a pipa jelekkel. Vagy válassza a jobb oldalon található régió melletti "szemétkosár" (🗑) ikont.

1. A módosítások mentéséhez kattintson **az OK gombra**.

   ![Régiók hozzáadása vagy eltávolítása menü](./media/how-to-manage-database-account/add-region.png)

Egy egyrégiós írási módban nem távolíthatja el az írási régiót. Az aktuális írási régió törlése előtt a feladatátvételt egy másik régióba kell átadnia.

Többrégiós írási módban bármely régiót hozzáadhat vagy eltávolíthat, ha legalább egy régióval rendelkezik.

### <a id="add-remove-regions-via-cli"></a>Azure CLI

További információ: [régiók hozzáadása vagy eltávolítása az Azure CLI-vel](manage-with-cli.md#add-or-remove-regions)

### <a id="add-remove-regions-via-ps"></a>Azure PowerShell

További információ: [régiók hozzáadása vagy eltávolítása a PowerShell](manage-with-powershell.md#update-account) -lel

## <a id="configure-multiple-write-regions"></a>Több írási régió konfigurálása

### <a id="configure-multiple-write-regions-portal"></a>Azure Portal

Nyissa meg az **adatreplikálás globálisan** lapot, és válassza az **Engedélyezés** lehetőséget a többrégiós írások engedélyezéséhez. Miután engedélyezte a többrégiós írást, az összes olyan olvasási régió, amelyre jelenleg a fiók tartozik, olvasási és írási régiók lesznek.

![Az Azure Cosmos-fiók több főkiszolgálós képernyőképet konfigurál](./media/how-to-manage-database-account/single-to-multi-master.png)

### <a id="configure-multiple-write-regions-cli"></a>Azure CLI

Lásd: [több írási régió engedélyezése az Azure CLI-vel](manage-with-cli.md#enable-multiple-write-regions)

### <a id="configure-multiple-write-regions-ps"></a>Azure PowerShell

Lásd: [több írási régió engedélyezése a PowerShell](manage-with-powershell.md#multi-master) -lel

### <a id="configure-multiple-write-regions-arm"></a>Resource Manager-sablon

A fiók a fiók létrehozásához és a `enableMultipleWriteLocations: true`beállításához használt Resource Manager-sablon üzembe helyezésével telepíthető át egy-egy főkiszolgálóról a több főkiszolgálóra. A következő Azure Resource Manager sablon egy egyszerű sablon, amely az SQL API-hoz két régióval és több írási hellyel rendelkező Azure Cosmos-fiókot helyez üzembe.

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
            "apiVersion": "2019-08-01",
            "location": "[parameters('location')]",
            "tags": {},
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": { "defaultConsistencyLevel": "Session" },
                "locations":
                [
                    {
                        "locationName": "[parameters('primaryRegion')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    },
                    {
                        "locationName": "[parameters('secondaryRegion')]",
                        "failoverPriority": 1,
                        "isZoneRedundant": false
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

3. Az **automatikus feladatátvétel** ablaktáblán **Győződjön meg arról**, hogy az **automatikus feladatátvétel engedélyezése** beállítás be értékre van állítva. 

4. Kattintson a **Mentés** gombra.

   ![Automatikus feladatátvétel menü a portálon](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="enable-automatic-failover-via-cli"></a>Azure CLI

Lásd: [az automatikus feladatátvétel engedélyezése az Azure CLI-vel](manage-with-cli.md#enable-automatic-failover)

### <a id="enable-automatic-failover-via-ps"></a>Azure PowerShell

Lásd: [az automatikus feladatátvétel engedélyezése a PowerShell](manage-with-powershell.md#enable-automatic-failover) -lel

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Feladatátvételi prioritások beállítása az Azure Cosmos-fiókhoz

Ha egy Cosmos-fiók automatikus feladatátvételre van konfigurálva, a régiók feladatátvételi prioritása módosítható.

> [!IMPORTANT]
> Az írási régió (nulla feladatátvételi prioritása) nem módosítható, ha a fiók konfigurálva van az automatikus feladatátvételre. Az írási régió módosításához le kell tiltania az automatikus feladatátvételt, és manuális feladatátvételt kell végrehajtania.

### <a id="set-failover-priorities-via-portal"></a>Azure Portal

1. Az Azure Cosmos-fiókjából nyissa meg az **adatok replikálása globális** ablaktáblát.

2. A panel tetején válassza az **automatikus feladatátvétel**lehetőséget.

   ![Adatok globális replikálása menü](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Az **automatikus feladatátvétel** ablaktáblán **Győződjön meg arról**, hogy az **automatikus feladatátvétel engedélyezése** beállítás be értékre van állítva.

4. A feladatátvételi prioritás módosításához húzza az olvasási régiókat a sor bal oldalán lévő három pontra, amikor az egérmutatót a fölé viszi.

5. Kattintson a **Mentés** gombra.

   ![Automatikus feladatátvétel menü a portálon](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="set-failover-priorities-via-cli"></a>Azure CLI

Lásd: [a feladatátvételi prioritás beállítása az Azure CLI-vel](manage-with-cli.md#set-failover-priority)

### <a id="set-failover-priorities-via-ps"></a>Azure PowerShell

Lásd: [a feladatátvételi prioritás beállítása a PowerShell](manage-with-powershell.md#modify-failover-priority) -lel

## <a id="manual-failover"></a>Manuális feladatátvétel végrehajtása Azure Cosmos-fiókon

> [!IMPORTANT]
> Ahhoz, hogy a művelet sikeres legyen, az Azure Cosmos-fióknak konfigurálva kell lennie a manuális feladatátvételhez.

A manuális feladatátvétel végrehajtásának folyamata magában foglalja a fiók írási régiójának (feladatátvételi prioritás = 0) a fiókhoz konfigurált másik régióba való módosítását.

> [!NOTE]
> A több főkiszolgálós fiókok feladatátvétele nem végezhető el manuálisan. Az Azure Cosmos SDK-t használó alkalmazások esetében az SDK felismeri, ha egy régió elérhetetlenné válik, majd automatikusan átirányítja a következő legközelebbi régióba, ha az SDK-ban több-vezérlési API-t használ.

### <a id="enable-manual-failover-via-portal"></a>Azure Portal

1. Nyissa meg az Azure Cosmos-fiókját, és nyissa meg az **adatreplikálás globális** menüjét.

2. A menü tetején válassza a **manuális feladatátvétel**lehetőséget.

   ![Adatok globális replikálása menü](./media/how-to-manage-database-account/replicate-data-globally.png)

3. A **manuális feladatátvétel** menüben válassza ki az új írási régiót. Jelölje be a jelölőnégyzetet, és jelezze, hogy ezt a lehetőséget az írási régió módosításakor adja meg.

4. A feladatátvétel elindításához kattintson **az OK gombra**.

   ![Manuális feladatátvétel menü a portálon](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Azure CLI

Lásd: [a manuális feladatátvétel elindítása az Azure CLI-vel](manage-with-cli.md#trigger-manual-failover)

### <a id="enable-manual-failover-via-ps"></a>Azure PowerShell

Lásd: [a manuális feladatátvétel elindítása a PowerShell](manage-with-powershell.md#trigger-manual-failover) -lel

## <a name="next-steps"></a>Következő lépések

Az Azure Cosmos-fiók, valamint az adatbázis és a tárolók kezelésével kapcsolatos további információkért és példákért olvassa el a következő cikkeket:

* [Azure Cosmos DB kezelése Azure PowerShell használatával](manage-with-powershell.md)
* [Az Azure Cosmos DB kezelése az Azure CLI-vel](manage-with-cli.md)
