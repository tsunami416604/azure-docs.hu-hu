---
title: Naplók – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ismerteti a naplózás konfigurációját, tárolását és elemzését Azure Database for PostgreSQL – egyetlen kiszolgálón
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: 87f79f0ed21ec1f6a550c47f9f60d18511883300
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74768214"
---
# <a name="logs-in-azure-database-for-postgresql---single-server"></a>Naplók Azure Database for PostgreSQL – egyetlen kiszolgáló
Azure Database for PostgreSQL lehetővé teszi a postgres szabványos naplófájljainak konfigurálását és elérését. A naplók használatával azonosíthatja, elháríthatja és kijavíthatja a konfigurációs hibákat és az optimális teljesítményt. A konfigurálható és elérhető naplózási információk közé tartoznak a hibák, a lekérdezési információk, az autoporszívós rekordok, a kapcsolatok és az ellenőrzőpontok. (A tranzakciós naplókhoz való hozzáférés nem érhető el).

A naplózás a pgaudit postgres-bővítménnyel érhető el. További tudnivalókért tekintse meg a [naplózási fogalmakat](concepts-audit.md) ismertető cikket.


## <a name="configure-logging"></a>Naplózás konfigurálása 
A postgres standard naplózást a kiszolgálón a naplózási kiszolgáló paramétereinek használatával konfigurálhatja. Az egyes Azure Database for PostgreSQL-kiszolgálókon a `log_checkpoints` és a `log_connections` alapértelmezés szerint be van kapcsolva. További paraméterek is megadhatók a naplózási igényeknek megfelelően: 

![Azure Database for PostgreSQL – naplózási paraméterek](./media/concepts-server-logs/log-parameters.png)

Ha többet szeretne megtudni a postgres, tekintse meg a [Mikor jelentkezzen be](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN) , és [Mi a naplózási](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT) szakaszt a postgres dokumentációjában. A postgres naplózási paramétereinek többsége a legtöbb esetben elérhető a Azure Database for PostgreSQLban való konfiguráláshoz.

Ha meg szeretné tudni, hogyan konfigurálhatja a paramétereket a Azure Database for PostgreSQLban, tekintse meg a [portál dokumentációját](howto-configure-server-parameters-using-portal.md) vagy a [CLI dokumentációját](howto-configure-server-parameters-using-cli.md). 

> [!NOTE]
> A nagy mennyiségű napló, például az utasítások naplózásának konfigurálása jelentős teljesítménybeli terhelést vehet igénybe. 

## <a name="access-log-files"></a>Access. log fájlok
Az alapértelmezett naplózási formátum a Azure Database for PostgreSQL. log. A naplóból a következőhöz hasonló minta sor jelenik meg:

```
2019-10-14 17:00:03 UTC-5d773cc3.3c-LOG: connection received: host=101.0.0.6 port=34331 pid=16216
```

Azure Database for PostgreSQL rövid távú tárolási helyet biztosít a. log fájlok számára. Egy új fájl 1 óránként vagy 100 MB-ban kezdődik, attól függően, hogy melyik érkezik először. A rendszer a naplófájlokat a postgres-ből kibocsátva az aktuális fájlhoz fűzi.  

A `log_retention_period` paraméterrel állíthatja be a rövid távú naplózás megőrzési időtartamát. Az alapértelmezett érték 3 nap; a maximális érték 7 nap. A rövid távú tárolási hely legfeljebb 1 GB naplófájlt tud tárolni. 1 GB után a rendszer törli a legrégebbi fájlokat a megőrzési időtartamtól függetlenül, hogy helyet szabadítson fel az új naplók számára. 

A naplók és a napló-elemzések hosszú távú megőrzése érdekében letöltheti a. log fájlokat, és áthelyezheti azokat egy külső szolgáltatásba. A fájlokat a [Azure Portal](howto-configure-server-logs-in-portal.md), az [Azure CLI](howto-configure-server-logs-using-cli.md)használatával töltheti le. Azt is megteheti, hogy Azure Monitor diagnosztikai beállításokat konfigurál, amelyek automatikusan kibocsátják a naplókat (JSON formátumban) a hosszú távú helyekre. Erről a lehetőségről az alábbi részben olvashat bővebben. 

A. log fájlok létrehozásához állítsa a (z) paramétert `logging_collector` kikapcsolva értékre. A kikapcsolás. a naplófájl létrehozása ajánlott, ha Azure Monitor diagnosztikai beállításokat használ. Ez a konfiguráció csökkenti a további naplózás teljesítményére gyakorolt hatást.

## <a name="diagnostic-logs"></a>Diagnosztikai naplók
A Azure Database for PostgreSQL Azure Monitor diagnosztikai beállításokkal van integrálva. A diagnosztikai beállítások lehetővé teszik, hogy JSON formátumban küldje el a postgres-naplókat, hogy Azure Monitor naplókat az elemzéshez és a riasztásokhoz, Event Hubs a folyamatos átvitelhez és az Azure Storage-hoz az archiváláshoz. 

> [!IMPORTANT]
> A kiszolgálói naplókhoz tartozó diagnosztikai funkció csak a általános célú és a memória optimalizált [díjszabási szintjein](concepts-pricing-tiers.md)érhető el.


### <a name="configure-diagnostic-settings"></a>Diagnosztikai beállítások konfigurálása
A postgres-kiszolgáló diagnosztikai beállításait a Azure Portal, a CLI, az REST API és a PowerShell használatával engedélyezheti. A kijelölni kívánt **PostgreSQLLogs**. (Más naplók is konfigurálhatók, ha a [query Store](concepts-query-store.md)-t használja.)

Diagnosztikai naplók engedélyezése a Azure Portal használatával:

   1. A portálon lépjen a *diagnosztikai beállítások* elemre a postgres-kiszolgáló navigációs menüjében.
   2. Válassza a *diagnosztikai beállítás hozzáadása*lehetőséget.
   3. Nevezze el ezt a beállítást. 
   4. Válassza ki az előnyben részesített végpontot (Storage-fiók, Event hub, log Analytics). 
   5. Válassza ki a napló típusát **PostgreSQLLogs**.
   7. Mentse a beállítást.

Ha a PowerShell, a CLI vagy a REST API használatával szeretné engedélyezni a diagnosztikai naplókat, keresse fel a [diagnosztikai beállítások](../azure-monitor/platform/diagnostic-settings.md) című cikket.

### <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

A naplók elérésének módja attól függ, hogy melyik végpontot választja. Az Azure Storage esetében a séma a [naplók Storage-fiókja](../azure-monitor/platform/resource-logs-collect-storage.md) című cikkben található. Event Hubs esetében tekintse meg a [stream Azure-naplók](../azure-monitor/platform/resource-logs-stream-event-hubs.md) című cikket.

Azure Monitor naplók esetében a naplók a kiválasztott munkaterületre kerülnek. A postgres-naplók a **AzureDiagnostics** -gyűjtési módot használják, így a AzureDiagnostics táblából is lekérdezhető. A táblázatban szereplő mezők a következőkben olvashatók. További információ a lekérdezésekről és a riasztásokról: [Azure monitor naplók lekérdezése](../azure-monitor/log-query/log-query-overview.md) – áttekintés.

Az alábbi lekérdezésekből megpróbálkozhat a kezdéshez. A riasztásokat lekérdezések alapján is konfigurálhatja.

Egy adott kiszolgáló összes postgres-naplójának keresése az elmúlt nap során
```
AzureDiagnostics
| where LogicalServerName_s == 'myservername'
| where TimeGenerated > ago(1d) 
```

Az összes nem localhost csatlakozási kísérlet keresése
```
AzureDiagnostics
| where Message contains "connection received" and Message !contains "host=127.0.0.1"
| where Category == "PostgreSQLLogs" and TimeGenerated > ago(6h)
```
A fenti lekérdezés a munkaterület összes postgres-kiszolgálójának utolsó 6 órájában megjeleníti az eredményeket.

### <a name="log-format"></a>Napló formátuma

A következő táblázat a **PostgreSQLLogs** típusának mezőit ismerteti. A kiválasztott kimeneti végponttól függően a befoglalt mezők és a megjelenő sorrend eltérő lehet. 

|**Mező** | **Leírás** |
|---|---|
| TenantId | A bérlő azonosítója |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | A napló UTC-ben való rögzítésének időbélyegzője |
| Type (Típus) | A napló típusa. Mindig `AzureDiagnostics` |
| SubscriptionId | Annak az előfizetésnek a GUID azonosítója, amelyhez a kiszolgáló tartozik |
| ResourceGroup | Azon erőforráscsoport neve, amelyhez a kiszolgáló tartozik |
| ResourceProvider | Az erőforrás-szolgáltató neve. Mindig `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | Erőforrás URI-ja |
| Erőforrás | A kiszolgáló neve |
| Kategória | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| errorLevel | Naplózási szint, példa: napló, hiba, figyelmeztetés |
| Üzenet | Elsődleges napló üzenete | 
| Domain | Kiszolgáló verziója, példa: postgres-10 |
| részletes | Másodlagos napló üzenete (ha van ilyen) |
| ColumnName | Az oszlop neve (ha van ilyen) |
| SchemaName | A séma neve (ha van ilyen) |
| DatatypeName | Az adattípus neve (ha van ilyen) |
| LogicalServerName | A kiszolgáló neve | 
| _ResourceId | Erőforrás URI-ja |
| Körzetszám | A naplózási sor előtagja |


## <a name="next-steps"></a>Következő lépések
- További információ a naplók [Azure Portal](howto-configure-server-logs-in-portal.md) vagy az [Azure CLI](howto-configure-server-logs-using-cli.md)-ből való eléréséről.
- További információ a [Azure monitor díjszabásáról](https://azure.microsoft.com/pricing/details/monitor/).
- További információ a [naplókról](concepts-audit.md)
