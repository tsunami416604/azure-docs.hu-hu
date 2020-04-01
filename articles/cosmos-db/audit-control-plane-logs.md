---
title: Az Azure Cosmos DB vezérlőrepülőgép-műveleteinek naplózása
description: Megtudhatja, hogyan naplózhatja a vezérlősík-műveleteket, például egy régió hozzáadása, frissítési átviteli, régiófeladat-átvétel, virtuális hálózat hozzáadása stb.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: sngun
ms.openlocfilehash: 64ad8e6b1101d8486268c857b3a7752e1801f52c
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420285"
---
# <a name="how-to-audit-azure-cosmos-db-control-plane-operations"></a>Az Azure Cosmos DB vezérlőrepülőgép-műveleteinek naplózása

A vezérlősík-műveletek közé tartozik az Azure Cosmos-fiók vagy -tároló módosításai. Például hozzon létre egy Azure Cosmos-fiókot, adjon hozzá egy régiót, frissítse az átviteli, régiófeladat-átvétel, adjunk hozzá egy virtuális hálózatot, stb néhány a vezérlősík-műveletek. Ez a cikk bemutatja, hogyan naplózhatja a vezérlősík-műveleteket az Azure Cosmos DB-ben.

## <a name="disable-key-based-metadata-write-access"></a>Kulcsalapú metaadat-írási hozzáférés letiltása
 
Mielőtt naplózza a vezérlősík-műveleteket az Azure Cosmos DB-ben, tiltsa le a kulcsalapú metaadat-írási hozzáférést a fiókjában. Ha a kulcsalapú metaadat-írási hozzáférés le van tiltva, az Azure Cosmos-fiókhoz fiókkulcsokon keresztül csatlakozó ügyfelek nem férhetnek hozzá a fiókhoz. Az írási hozzáférést letilthatja, ha a `disableKeyBasedMetadataWriteAccess` tulajdonságot true értékre állítja. A tulajdonság beállítása után bármely erőforrás módosítása történhet a megfelelő szerepköralapú hozzáférés-vezérlési (RBAC) szerepkörrel és hitelesítő adatokkal rendelkező felhasználótól. A tulajdonság beállításáról a Módosítások megakadályozása az [SDK-kból](role-based-access-control.md#preventing-changes-from-cosmos-sdk) című cikkben olvashat bővebben.

 A metaadat-írási hozzáférés kikapcsolásakor vegye figyelembe a következő pontokat:

* Értékelje ki és győződjön meg arról, hogy az alkalmazások nem végeznek olyan metaadat-hívásokat, amelyek megváltoztatják a fenti erőforrásokat (például gyűjteményt, frissítési átviteli értéket hoznak létre,...) az SDK- vagy fiókkulcsok használatával.

* Jelenleg az Azure Portal fiókkulcsokat használ a metaadat-műveletekhez, és így ezek a műveletek blokkolva lesznek. Másik lehetőségként használja az Azure CLI, SDK vagy Resource Manager sablon központi telepítések ilyen műveletek végrehajtásához.

## <a name="enable-diagnostic-logs-for-control-plane-operations"></a>Diagnosztikai naplók engedélyezése a vezérlősík műveleteihez

Az Azure Portal használatával engedélyezheti a vezérlősík-műveletek diagnosztikai naplóit. A vezérlősík-műveletek naplózásának engedélyezéséhez az alábbi lépésekkel engedélyezheti a naplózást:

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com) és keresse meg az Azure Cosmos-fiókját.

1. Nyissa **meg** a Diagnosztikai beállítások ablaktáblát, és adja meg a létrehozandó naplók **nevét.**

1. Válassza **a ControlPlaneRequests** parancsot a naplótípushoz, és válassza a **Küldés a loganalytics-nek** lehetőséget.

A naplókat egy tárfiókban is tárolhatja, vagy egy eseményközpontba továbbíthatja. Ez a cikk bemutatja, hogyan küldhet naplókat a naplóelemzéshez, majd lekérdezheti őket. Miután engedélyezte, a diagnosztikai naplók érvénybe léptetése néhány percet vesz igénybe. Az ezt követően végrehajtott összes vezérlősík-művelet nyomon követhető. Az alábbi képernyőkép bemutatja, hogyan engedélyezhetvezérlő síknaplókat:

![Vezérlősík-kérelmek naplózásának engedélyezése](./media/audit-control-plane-logs/enable-control-plane-requests-logs.png)

## <a name="view-the-control-plane-operations"></a>A vezérlősík műveleteinek megtekintése

A naplózás bekapcsolása után az alábbi lépésekkel nyomon követheti egy adott fiók műveleteit:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Nyissa **meg** a Monitor lapot a bal oldali navigációs sávon, és válassza a **Naplók** ablaktáblát. Megnyit egy felhasználói felületet, ahol könnyedén futtathat lekérdezéseket az adott fiók hatókörében. Futtassa a következő lekérdezést a vezérlősík-naplók megtekintéséhez:

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="ControlPlaneRequests"
   | where TimeGenerated >= ago(1h)
   ```

A következő képernyőképek naplót rögzítnek, amikor egy virtuális hálózatot adnak hozzá egy Azure Cosmos-fiókhoz:

![Vezérlősík naplók, ha egy virtuális hálózat hozzáadása](./media/audit-control-plane-logs/add-ip-filter-logs.png)

A következő képernyőképek rögzítik a naplókat, amikor egy Cassandra-tábla átviteli fazekadusa frissül:

![Vezérlősík naplók átviteli frissítéskor](./media/audit-control-plane-logs/throughput-update-logs.png)

## <a name="identify-the-identity-associated-to-a-specific-operation"></a>Egy adott művelethez társított identitás azonosítása

Ha további hibakeresést szeretne, azonosíthat egy adott műveletet a **tevékenységnaplóban** a Tevékenységazonosító vagy a művelet időbélyege segítségével. Az időbélyeg egyes Erőforrás-kezelő-ügyfelek nél használatos, ahol a tevékenységazonosító explicit módon nem halad át. A tevékenységnapló a művelet indításának identitásával kapcsolatos részleteket tartalmazza. Az alábbi képernyőkép bemutatja, hogyan használhatja a tevékenységazonosítót, és hogyan keresheti meg a tevékenységnaplóban a hozzá kapcsolódó műveleteket:

![Használja a tevékenységazonosítót, és keresse meg a műveleteket](./media/audit-control-plane-logs/find-operations-with-activity-id.png)

## <a name="next-steps"></a>További lépések

* [Az Azure Cosmos DB Azure Monitorszolgáltatás felfedezése](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* [Az Azure Cosmos DB metrikák figyelése és hibakeresése](use-metrics.md)