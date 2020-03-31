---
title: Megismerheti, hogyan kezelhet adatbázisfiókokat az Azure Cosmos DB-ben.
description: Megtudhatja, hogyan kezelheti az Azure Cosmos DB-erőforrásait az Azure Portal, a PowerShell, a CLI és az Azure Resource Manager-sablonok használatával
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 61670d757611bd0c1dd11c389282b18edb3d7fa1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247383"
---
# <a name="manage-an-azure-cosmos-account"></a>Azure Cosmos-fiók kezelése

Ez a cikk az Azure Cosmos-fiók különböző feladatainak az Azure Portal, az Azure PowerShell, az Azure CLI és az Azure Resource Manager-sablonok használatával történő kezelését ismerteti.

## <a name="create-an-account"></a>Fiók létrehozása

### <a name="azure-portal"></a><a id="create-database-account-via-portal"></a>Azure-portál

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a name="azure-cli"></a><a id="create-database-account-via-cli"></a>Azure CLI

Lásd: [Azure Cosmos DB-fiók létrehozása az Azure CLI-vel című témakörben](manage-with-cli.md#create-an-azure-cosmos-db-account)

### <a name="azure-powershell"></a><a id="create-database-account-via-ps"></a>Azure PowerShell

Tekintse [meg: Azure Cosmos DB-fiók létrehozása powershellnel](manage-with-powershell.md#create-account)

### <a name="azure-resource-manager-template"></a><a id="create-database-account-via-arm-template"></a>Azure Resource Manager-sablon

Ez az Azure Resource Manager-sablon létrehoz egy Azure Cosmos-fiókot az SQL API-hoz, amely két régióval és beállítással van konfigurálva a konzisztenciaszint, az automatikus feladatátvétel és a többfőkiszolgálós beállítások kiválasztásához. A sablon üzembe helyezéséhez kattintson az Üzembe helyezés az Azure-ba a következő című webhelyen: [Azure Cosmos-fiók létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-sql)

## <a name="addremove-regions-from-your-database-account"></a>Régiók hozzáadása és eltávolítása az adatbázisfiókból

### <a name="azure-portal"></a><a id="add-remove-regions-via-portal"></a>Azure-portál

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).

1. Nyissa meg az Azure Cosmos-fiókot, és nyissa meg az **adatok globális replikálása menüt.**

1. Régiók hozzáadásához jelölje ki a hatszögeket a **+** térképen a kívánt terület(ek)nek megfelelő címkével. Másik lehetőségként válasszon egy régió hozzáadásához, válassza a **+ Régió hozzáadása** lehetőséget, és válasszon egy régiót a legördülő menüből.

1. A régiók eltávolításához törölje a jelet egy vagy több területből a térképről a kék hatszögek pipákkal történő kiválasztásával. Vagy válassza a "wastebasket" (🗑) ikont a régió mellett a jobb oldalon.

1. A módosítások mentéséhez kattintson az **OK gombra.**

   ![Területek hozzáadása és eltávolítása menü](./media/how-to-manage-database-account/add-region.png)

Egyrégiós írási módban nem távolíthatja el az írási régiót. Az aktuális írási régió törlése előtt feladatátvételt kell végeznie egy másik régióba.

Többrégiós írási módban szabadon adhat hozzá és távolíthat el régiókat, ha marad legalább egy régió.

### <a name="azure-cli"></a><a id="add-remove-regions-via-cli"></a>Azure CLI

Tekintse meg a [Régiók hozzáadása vagy eltávolítása az Azure CLI-vel](manage-with-cli.md#add-or-remove-regions)

### <a name="azure-powershell"></a><a id="add-remove-regions-via-ps"></a>Azure PowerShell

Tekintse meg a [Régiók hozzáadása és eltávolítása a Powershell használatával](manage-with-powershell.md#update-account)

## <a name="configure-multiple-write-regions"></a><a id="configure-multiple-write-regions"></a>Több írási régió konfigurálása

### <a name="azure-portal"></a><a id="configure-multiple-write-regions-portal"></a>Azure-portál

Nyissa meg az **Adatok replikálása globálisan** lapot, és válassza **az Engedélyezés** lehetőséget a többrégiós írások engedélyezéséhez. Miután engedélyezte a többrégiós írások, a fiókban jelenleg lévő összes olvasási régió olvasási és írási régiókká válik.

![Az Azure Cosmos-fiók konfigurálja a többfős képernyőképet](./media/how-to-manage-database-account/single-to-multi-master.png)

### <a name="azure-cli"></a><a id="configure-multiple-write-regions-cli"></a>Azure CLI

Tekintse meg a [Többírású régiók engedélyezése az Azure CLI-vel](manage-with-cli.md#enable-multiple-write-regions)

### <a name="azure-powershell"></a><a id="configure-multiple-write-regions-ps"></a>Azure PowerShell

Tekintse meg a [Többírású régiók engedélyezése a Powershell használatával](manage-with-powershell.md#multi-master)

### <a name="resource-manager-template"></a><a id="configure-multiple-write-regions-arm"></a>Erőforrás-kezelő sablon

Egy fiók áttelepíthető egyfős mesterrendszerről többfős kiszolgálóra a fiók létrehozásához `enableMultipleWriteLocations: true`használt Erőforrás-kezelő sablon telepítésével és a beállítással. A következő Azure Resource Manager-sablon egy minimális sablon, amely két régióval és több írási helynel rendelkező Azure Cosmos-fiókot telepít az SQL API-hoz.

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

## <a name="enable-automatic-failover-for-your-azure-cosmos-account"></a><a id="automatic-failover"></a>Automatikus feladatátvétel engedélyezése az Azure Cosmos-fiókhoz

Az automatikus feladatátvételi beállítás lehetővé teszi, hogy az Azure Cosmos DB feladatátvételt a régió a legmagasabb feladatátvételi prioritást felhasználói művelet nélkül, ha egy régió elérhetetlenné válik. Ha az automatikus feladatátvétel engedélyezve van, a régió prioritása módosítható. Az automatikus feladatátvétel engedélyezéséhez a fióknak két vagy több régióval kell rendelkeznie.

### <a name="azure-portal"></a><a id="enable-automatic-failover-via-portal"></a>Azure-portál

1. Az Azure Cosmos-fiókból nyissa meg az **adatok replikálása globálisan** ablaktáblát.

2. Az ablaktábla tetején válassza az **Automatikus feladatátvétel**lehetőséget.

   ![Adatok globális replikálása menü](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Az **Automatikus feladatátvétel** ablaktáblán győződjön meg arról, hogy az **Automatikus feladatátvétel engedélyezése** beállítás be van **kapcsolva.** 

4. Kattintson a **Mentés** gombra.

   ![Automatikus feladatátvétel menü a portálon](./media/how-to-manage-database-account/automatic-failover.png)

### <a name="azure-cli"></a><a id="enable-automatic-failover-via-cli"></a>Azure CLI

Tekintse: [Automatikus feladatátvétel engedélyezése az Azure CLI-vel](manage-with-cli.md#enable-automatic-failover)

### <a name="azure-powershell"></a><a id="enable-automatic-failover-via-ps"></a>Azure PowerShell

Lásd: [Automatikus feladatátvétel engedélyezése a Powershell használatával](manage-with-powershell.md#enable-automatic-failover)

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Az Azure Cosmos-fiók feladatátvételi prioritásainak beállítása

Miután a Cosmos-fiók automatikus feladatátvételre van konfigurálva, a régiók feladatátvételi prioritása módosítható.

> [!IMPORTANT]
> Az írási régió (nulla feladatátvételi prioritás) nem módosítható, ha a fiók automatikus feladatátvételre van konfigurálva. Az írási régió módosításához le kell tiltania az automatikus feladatátvételt, és manuális feladatátvételt kell végeznie.

### <a name="azure-portal"></a><a id="set-failover-priorities-via-portal"></a>Azure-portál

1. Az Azure Cosmos-fiókból nyissa meg az **adatok replikálása globálisan** ablaktáblát.

2. Az ablaktábla tetején válassza az **Automatikus feladatátvétel**lehetőséget.

   ![Adatok globális replikálása menü](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Az **Automatikus feladatátvétel** ablaktáblán győződjön meg arról, hogy az **Automatikus feladatátvétel engedélyezése** beállítás be van **kapcsolva.**

4. A feladatátvételi prioritás módosításához húzza az olvasási területeket a sor bal oldalán lévő három ponton keresztül, amelyek akkor jelennek meg, amikor föléjük viszi az egérmutatót.

5. Kattintson a **Mentés** gombra.

   ![Automatikus feladatátvétel menü a portálon](./media/how-to-manage-database-account/automatic-failover.png)

### <a name="azure-cli"></a><a id="set-failover-priorities-via-cli"></a>Azure CLI

Tekintse meg [a Feladatátvételi prioritás beállítása az Azure CLI-vel( Feladatátvételi prioritás beállítása) témakört.](manage-with-cli.md#set-failover-priority)

### <a name="azure-powershell"></a><a id="set-failover-priorities-via-ps"></a>Azure PowerShell

Tekintse meg [a Feladatátvételi prioritás beállítása powershellnel( Feladatátvételi prioritás beállítása) témakört.](manage-with-powershell.md#modify-failover-priority)

## <a name="perform-manual-failover-on-an-azure-cosmos-account"></a><a id="manual-failover"></a>Manuális feladatátvétel végrehajtása Azure Cosmos-fiókon

> [!IMPORTANT]
> Az Azure Cosmos-fiók konfigurálva kell lennie a manuális feladatátvételhez a művelet sikeres működéséhez.

A manuális feladatátvétel folyamata magában foglalja a fiók írási régiójának (feladatátvételi prioritás = 0) módosítását a fiókhoz konfigurált másik régióra.

> [!NOTE]
> A többfős fiókok nem adhatók át manuálisan. Az Azure Cosmos SDK-t használó alkalmazások esetében az SDK észleli, ha egy régió elérhetetlenné válik, majd automatikusan átirányítja a következő legközelebbi régióba, ha több-homing API-t használ az SDK-ban.

### <a name="azure-portal"></a><a id="enable-manual-failover-via-portal"></a>Azure-portál

1. Nyissa meg az Azure Cosmos-fiókot, és nyissa meg az **adatok globális replikálása menüt.**

2. A menü tetején válassza a **Kézi feladatátvétel**lehetőséget.

   ![Adatok globális replikálása menü](./media/how-to-manage-database-account/replicate-data-globally.png)

3. A **Kézi feladatátvétel** menüben válassza ki az új írási területet. Jelölje be a jelölőnégyzetet, ha azt szeretné, hogy tisztában van azzal, hogy ez a beállítás módosítja az írási területet.

4. A feladatátvétel elindításához kattintson az **OK gombra.**

   ![Manuális feladatátvétel menü a portálon](./media/how-to-manage-database-account/manual-failover.png)

### <a name="azure-cli"></a><a id="enable-manual-failover-via-cli"></a>Azure CLI

Tekintse meg az Azure CLI manuális [feladatátvételi eseményindítóját.](manage-with-cli.md#trigger-manual-failover)

### <a name="azure-powershell"></a><a id="enable-manual-failover-via-ps"></a>Azure PowerShell

Tekintse meg [az Eseményindító manuális feladatátvételt a PowerShell használatával kapcsolatban.](manage-with-powershell.md#trigger-manual-failover)

## <a name="next-steps"></a>További lépések

Az Azure Cosmos-fiók, valamint az adatbázis és a tárolók kezelésével kapcsolatos további információkért és példákért olvassa el az alábbi cikkeket:

* [Az Azure Cosmos DB kezelése az Azure PowerShell használatával](manage-with-powershell.md)
* [Az Azure Cosmos DB kezelése az Azure CLI-vel](manage-with-cli.md)
