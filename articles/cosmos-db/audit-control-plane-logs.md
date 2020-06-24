---
title: Azure Cosmos DB vezérlési sík műveleteinek naplózása
description: Megtudhatja, hogyan naplózhatja a vezérlési sík műveleteit, például egy régió hozzáadását, a frissítés sebességét, a régió feladatátvételét, a VNet stb. hozzáadását Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/23/2020
ms.author: sngun
ms.openlocfilehash: cb6a27c0f03b7c0c41d8f323609df612363cfd9e
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85262650"
---
# <a name="how-to-audit-azure-cosmos-db-control-plane-operations"></a>Azure Cosmos DB vezérlési sík műveleteinek naplózása

A Azure Cosmos DB vezérlő síkja egy REST-szolgáltatás, amely lehetővé teszi különféle műveletek végrehajtását az Azure Cosmos-fiókban. Egy nyilvános erőforrás-modellt (például adatbázis, fiók) és különböző műveleteket tesz közzé a végfelhasználók számára az erőforrás-modell műveleteinek elvégzéséhez. A vezérlési sík műveletei az Azure Cosmos-fiók vagy-tároló módosításait tartalmazzák. Például olyan műveletek, mint például az Azure Cosmos-fiók létrehozása, a régió hozzáadása, az átviteli sebesség frissítése, a régió feladatátvétele, VNet stb. a vezérlési sík egyes műveletei. Ez a cikk azt ismerteti, hogyan lehet naplózni a vezérlési sík műveleteit a Azure Cosmos DBban. Az Azure Cosmos-fiókokon futtathatja a vezérlési sík műveleteit az Azure CLI, a PowerShell vagy a Azure Portal, míg a tárolók esetében az Azure CLI vagy a PowerShell használatával.

A következő példák olyan forgatókönyveket mutatnak be, amelyekben a naplózási vezérlési sík műveletei hasznosak lehetnek:

* Riasztást szeretne kapni az Azure Cosmos-fiókhoz tartozó tűzfalszabályok módosításakor. A riasztásnak meg kell keresnie az Azure Cosmos-fiók hálózati biztonságát szabályozó szabályok jogosulatlan módosításait, és gyors műveletet kell végeznie.

* Riasztást szeretne kapni, ha új régiót ad hozzá vagy távolít el az Azure Cosmos-fiókjából. A régiók hozzáadása vagy eltávolítása hatással van a számlázási és az adatszuverenitási követelményekre. Ez a riasztás segítséget nyújt a régió véletlen hozzáadásának vagy eltávolításának észleléséhez a fiókjában.

* További részletekért tekintse meg a változásokkal kapcsolatos diagnosztikai naplókat. Például egy VNet módosult.

## <a name="disable-key-based-metadata-write-access"></a>Kulcs alapú metaadatok írási hozzáférésének letiltása

Mielőtt naplózza a vezérlési sík műveleteit a Azure Cosmos DBban, tiltsa le a kulcs alapú metaadatok írási hozzáférését a fiókjában. Ha a kulcs alapú metaadatok írási hozzáférése le van tiltva, a fiók kulcsain keresztül az Azure Cosmos-fiókhoz csatlakozó ügyfelek nem férnek hozzá a fiókhoz. A tulajdonság Igaz értékre állításával letilthatja az írási hozzáférést `disableKeyBasedMetadataWriteAccess` . A tulajdonság beállítása után bármely erőforrás módosítása a megfelelő szerepköralapú hozzáférés-vezérlési (RBAC) szerepkörrel és a hitelesítő adatokkal rendelkező felhasználótól történhet. Ha többet szeretne megtudni ennek a tulajdonságnak a beállításáról, olvassa el az [SDK-k változásainak megakadályozása](role-based-access-control.md#preventing-changes-from-cosmos-sdk) című cikket. 

Ha a `disableKeyBasedMetadataWriteAccess` be van kapcsolva, ha az SDK-alapú ügyfelek létrehozási vagy frissítési műveletet futtatnak, a " *ContainerNameorDatabaseName" erőforrás "művelet" bejegyzése nem engedélyezett Azure Cosmos db végponton keresztül* . Be kell kapcsolnia a fiókjához való hozzáférést, vagy a létrehozás/frissítés műveletet a Azure Resource Manager, az Azure CLI vagy az Azure PowerShell használatával kell végrehajtania. A visszaállításhoz állítsa a disableKeyBasedMetadataWriteAccess **hamis** értékre az Azure CLI használatával, a [változások megakadályozása a Cosmos SDK](role-based-access-control.md#preventing-changes-from-cosmos-sdk) -ban című cikkben leírtak szerint. Ügyeljen arra, hogy a True érték helyett false értékűre módosítsa a értéket `disableKeyBasedMetadataWriteAccess` .

A metaadatok írási hozzáférésének kikapcsolásakor vegye figyelembe a következő szempontokat:

* Értékelje ki és győződjön meg arról, hogy az alkalmazások nem tesznek lehetővé olyan metaadat-hívásokat, amelyek a fenti erőforrásokat módosítják (például a gyűjtemény létrehozása, az átviteli sebesség, a...) az SDK-val vagy a fiók kulcsainak használatával.

* A Azure Portal jelenleg a metaadatokat használja, ezért ezek a műveletek le lesznek tiltva. Az ilyen műveletek végrehajtásához használhatja az Azure CLI, SDK-k vagy Resource Manager-sablonok központi telepítését is.

## <a name="enable-diagnostic-logs-for-control-plane-operations"></a>Diagnosztikai naplók engedélyezése a vezérlési sík műveleteihez

A Azure Portal használatával engedélyezheti a diagnosztikai naplókat a vezérlési sík műveleteihez. Az engedélyezést követően a diagnosztikai naplók rögzítik a műveletet az indítási és a teljes eseményekre vonatkozó részletekkel. A *RegionFailoverStart* és a *RegionFailoverComplete* például elvégzi a régió feladatátvételi eseményét.

A következő lépésekkel engedélyezheti a naplózást a vezérlési sík műveleteinél:

1. Jelentkezzen be [Azure Portalba](https://portal.azure.com) , és navigáljon az Azure Cosmos-fiókjához.

1. Nyissa meg a **diagnosztikai beállítások** ablaktáblát, adja meg a létrehozandó naplók **nevét** .

1. Válassza a **ControlPlaneRequests** lehetőséget, majd kattintson a **Küldés log Analytics** lehetőségre.

A naplókat egy Storage-fiókban vagy egy Event hub-adatfolyamban is tárolhatja. Ez a cikk bemutatja, hogyan küldhet naplókat a log Analyticsnek, majd hogyan kérdezheti le őket. Az engedélyezést követően néhány percet vesz igénybe, amíg a diagnosztikai naplók érvénybe lépnek. Az ezen pont után végrehajtott összes vezérlő sík nyomon követhető. A következő képernyőképen a vezérlési sík naplófájljainak engedélyezése látható:

:::image type="content" source="./media/audit-control-plane-logs/enable-control-plane-requests-logs.png" alt-text="Vezérlési sík kérelmek naplózásának engedélyezése":::

## <a name="view-the-control-plane-operations"></a>A vezérlő síkja műveleteinek megtekintése

A naplózás bekapcsolását követően kövesse az alábbi lépéseket egy adott fiók műveleteinek nyomon követéséhez:

1. Jelentkezzen be [Azure Portalba](https://portal.azure.com).

1. Nyissa meg a **figyelés** lapot a bal oldali navigációs sávon, majd válassza a **naplók** panelt. Megnyílik egy felhasználói felület, amelyben egyszerűen futtathat lekérdezéseket az adott fiókkal a hatókörben. Futtassa a következő lekérdezést a vezérlési sík naplófájljainak megtekintéséhez:

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="ControlPlaneRequests"
   | where TimeGenerated >= ago(1h)
   ```

Az alábbi képernyőképek rögzítik a naplókat, ha egy Azure Cosmos-fiókhoz módosul egy konzisztencia-szint:

:::image type="content" source="./media/audit-control-plane-logs/add-ip-filter-logs.png" alt-text="Vezérlési sík naplói a VNet hozzáadásakor":::

A következő képernyőképek rögzítik a Cassandra-tábla átviteli sebességét:

:::image type="content" source="./media/audit-control-plane-logs/throughput-update-logs.png" alt-text="Vezérlési sík naplói az átviteli sebesség frissítésekor":::

## <a name="identify-the-identity-associated-to-a-specific-operation"></a>Egy adott művelethez társított identitás azonosítása

Ha további hibakeresést szeretne végezni, akkor a tevékenység azonosítója vagy a művelet időbélyegzője segítségével azonosíthatja a **tevékenységek naplójában** megadott műveletet. Az időbélyeg olyan Resource Manager-ügyfelek esetében használatos, amelyekben a tevékenység azonosítója nem lett explicit módon átadva. A tevékenység naplója részletesen ismerteti a műveletet kezdeményező identitást. Az alábbi képernyőfelvételen a tevékenység-azonosító használata látható, és megkeresheti az ahhoz társított műveleteket a tevékenység naplójában:

:::image type="content" source="./media/audit-control-plane-logs/find-operations-with-activity-id.png" alt-text="A tevékenység-azonosító használata és a műveletek megkeresése":::

## <a name="control-plane-operations-for-azure-cosmos-account"></a>Vezérlési sík műveletei az Azure Cosmos-fiókhoz

Az alábbiakban a vezérlési sík a fiók szintjén elérhető műveletei láthatók. A műveletek többségét a fiók szintjén követjük nyomon. Ezek a műveletek metrikaként érhetők el az Azure monitorban:

* Régió hozzáadva
* Régió eltávolítva
* Fiók törölve
* A régió feladatátvétele megtörtént
* Fiók létrehozva
* Virtuális hálózat törölve
* A fiók hálózati beállításai frissítve
* A fiók replikációs beállításainak frissítése megtörtént
* A fiók kulcsai frissítve
* A fiók biztonsági mentési beállításai frissítve
* Fiók diagnosztikai beállításainak frissítése

## <a name="control-plane-operations-for-database-or-containers"></a>Az adatbázishoz vagy a tárolóhoz tartozó sík-műveletek vezérlése

A vezérlési sík műveletei az adatbázis és a tároló szintjén érhetők el. Ezek a műveletek metrikaként érhetők el az Azure monitorban:

* SQL Database frissítve
* SQL-tároló frissítve
* SQL Database átviteli sebesség frissítve
* SQL-tároló átviteli sebessége frissítve
* SQL Database törölve
* SQL-tároló törölve
* Cassandra Space frissítve
* Cassandra-tábla frissítve
* Cassandra Space-átviteli sebesség frissítve
* Cassandra Table átviteli sebesség frissítve
* Cassandra szóköz törölve
* Cassandra-tábla törölve
* Gremlin-adatbázis frissítve
* Gremlin gráf frissítve
* Gremlin adatbázis átviteli sebessége frissítve
* Gremlin gráf átviteli sebessége frissítve
* Gremlin-adatbázis törölve
* Gremlin gráf törölve
* Mongo-adatbázis frissítve
* Mongo-gyűjtemény frissítve
* Mongo adatbázis átviteli sebessége frissítve
* A Mongo-gyűjtési átviteli sebesség frissítve
* Mongo-adatbázis törölve
* Mongo-gyűjtemény törölve
* AzureTable tábla frissítve
* AzureTable-tábla átviteli sebessége frissítve
* AzureTable-tábla törölve

## <a name="diagnostic-log-operations"></a>Diagnosztikai naplózási műveletek

A következő műveletek nevei a különböző műveletekhez tartozó diagnosztikai naplókban találhatók:

* RegionAddStart, RegionAddComplete
* RegionRemoveStart, RegionRemoveComplete
* AccountDeleteStart, AccountDeleteComplete
* RegionFailoverStart, RegionFailoverComplete
* AccountCreateStart, AccountCreateComplete
* AccountUpdateStart, AccountUpdateComplete
* VirtualNetworkDeleteStart, VirtualNetworkDeleteComplete
* DiagnosticLogUpdateStart, DiagnosticLogUpdateComplete

Az API-specifikus műveletek esetében a művelet a következő formátumban lesz elnevezve:

* ApiKind + ApiKindResourceType + OperationType + indítás/Befejezés
* ApiKind + ApiKindResourceType + "átviteli sebesség" + operationType + Kezdés/Befejezés

**Példa** 

* CassandraKeyspacesUpdateStart, CassandraKeyspacesUpdateComplete
* CassandraKeyspacesThroughputUpdateStart, CassandraKeyspacesThroughputUpdateComplete
* SqlContainersUpdateStart, SqlContainersUpdateComplete

A *ResourceDetails* tulajdonság a teljes erőforrás törzsét tartalmazza kérelem hasznos adataiként, és tartalmazza a frissítéshez kért összes tulajdonságot.

## <a name="diagnostic-log-queries-for-control-plane-operations"></a>Diagnosztikai naplók lekérdezése vezérlési sík műveleteihez

Az alábbiakban néhány példát talál a vezérlési sík műveleteihez szükséges diagnosztikai naplók beszerzésére:

```kusto
AzureDiagnostics 
| where Category =="ControlPlaneRequests"
| where  OperationName startswith "SqlContainersUpdateStart"
```

```kusto
AzureDiagnostics 
| where Category =="ControlPlaneRequests"
| where  OperationName startswith "SqlContainersThroughputUpdateStart"
```

## <a name="next-steps"></a>További lépések

* [Azure Cosmos DB Azure Monitor megismerése](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* [A Azure Cosmos DB metrikáinak monitorozása és hibakeresése](use-metrics.md)
