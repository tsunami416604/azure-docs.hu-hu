---
title: Naplók - Azure-adatbázis a PostgreSQL-hez - Egykiszolgálós
description: A naplózási konfiguráció, a tárolás és az elemzés ismertetése az Azure Database for PostgreSQL - Single Server alkalmazásban
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: 2636e9a225002148e4cd79bb2176e0883aed623a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280494"
---
# <a name="logs-in-azure-database-for-postgresql---single-server"></a>Naplók az Azure-adatbázisban a PostgreSQL-hez – egyetlen kiszolgáló
A PostgreSQL Azure Database szolgáltatáslehetővé teszi a Postgres szabványos naplóinak konfigurálását és elérését. A naplók segítségével azonosíthatja, elháríthatja és javíthatja a konfigurációs hibákat és az optimálistól elmaradó teljesítményt. A konfigurálható és elérhető naplózási adatok közé tartoznak a hibák, a lekérdezési adatok, az automatikus vákuumrekordok, a kapcsolatok és az ellenőrzőpontok. (A tranzakciónaplókhoz való hozzáférés nem érhető el).

A naplózás postgres kiterjesztésen keresztül érhető el, a pgaudit. További információért látogasson el a [naplózási fogalmak](concepts-audit.md) cikkbe.


## <a name="configure-logging"></a>Naplózás konfigurálása 
A postgres szabványos naplózása a kiszolgálón a naplózási kiszolgáló paramétereivel konfigurálható. Minden Azure-adatbázis PostgreSQL-kiszolgáló, `log_checkpoints` és `log_connections` alapértelmezés szerint be vannak kapcsolva. Vannak további paraméterek, amelyeket a naplózási igényekhez igazíthat: 

![Azure Database for PostgreSQL – Naplózási paraméterek](./media/concepts-server-logs/log-parameters.png)

Ha többet szeretne megtudni a postgres naplóparaméterekről, látogasson el a Postgres dokumentáció [Mikor naplózandó](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN) és [milyen naplózandó](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT) szakaszában. A legtöbb, de nem az összes, Postgres naplózási paraméterek konfigurálhatók az Azure Database for PostgreSQL konfigurálható.

A paraméterek konfigurálásáról az Azure Database for PostgreSQL alkalmazásban a [portál dokumentációjában](howto-configure-server-parameters-using-portal.md) vagy a [CLI dokumentációjában](howto-configure-server-parameters-using-cli.md)olvashat. 

> [!NOTE]
> A naplók nagy mennyiségűsta (például kimutatásnaplózása) konfigurálása jelentős teljesítménytöbbletet okozhat. 

## <a name="access-log-files"></a>Access .log fájlok
A PostgreSQL Azure Database alapértelmezett naplóformátuma a .log. A naplóból származó mintasor a következőképpen néz ki:

```
2019-10-14 17:00:03 UTC-5d773cc3.3c-LOG: connection received: host=101.0.0.6 port=34331 pid=16216
```

A PostgreSQL Azure Database rövid távú tárolási helyet biztosít a .log fájlok számára. Az új fájl 1 óránként vagy 100 MB-onként kezdődik, amelyik előbb következik be. A naplók az aktuális fájlhoz fűzve, ahogy azok a Postgres-ből kerülnek kibocsátásra.  

A rövid távú naplótárolás megőrzési időszakát `log_retention_period` a paraméter használatával állíthatja be. Az alapértelmezett érték 3 nap, a megadható legnagyobb érték a 7 nap. A rövid távú tárolóhely legfeljebb 1 GB naplófájlt képes tárolni. 1 GB után a legrégebbi fájlok, függetlenül a megőrzési idő, törlődik, hogy helyet adjon az új naplók. 

A naplók hosszabb távú megőrzése és a naplóelemzés érdekében letöltheti a .log fájlokat, és áthelyezheti őket egy külső szolgáltatásba. A fájlokat az [Azure Portal](howto-configure-server-logs-in-portal.md), Azure [CLI](howto-configure-server-logs-using-cli.md)segítségével töltheti le. Azt is beállíthatja, hogy az Azure Monitor diagnosztikai beállításait, amelyek automatikusan kibocsátja a naplókat (JSON formátumban) a hosszabb távú helyeken. Erről a lehetőségről az alábbi szakaszban olvashat bővebben. 

A .log fájlok létrehozását leállíthatja, ha a paramétert `logging_collector` OFF-ra állítja. A .log fájl létrehozásának kikapcsolása ajánlott, ha az Azure Monitor diagnosztikai beállításait használja. Ez a konfiguráció csökkenti a további naplózás teljesítményre gyakorolt hatását.

## <a name="diagnostic-logs"></a>Diagnosztikai naplók
A PostgreSQL Azure Database integrálva van az Azure Monitor diagnosztikai beállításaival. A diagnosztikai beállítások lehetővé teszik, hogy a Postgres naplók JSON formátumban az Azure Monitor naplók elemzési és riasztási, Event Hubs streamelésre, és az Azure Storage archiváláshoz. 

> [!IMPORTANT]
> Ez a diagnosztikai szolgáltatás a kiszolgálónaplókhoz csak az Általános célú és a memória optimalizált [tarifaszinteken](concepts-pricing-tiers.md)érhető el.


### <a name="configure-diagnostic-settings"></a>Diagnosztikai beállítások konfigurálása
Az Azure Portal, a CLI, a REST API és a Powershell használatával engedélyezheti a Postgres-kiszolgáló diagnosztikai beállításait. A kiválasztható naplókategória a **PostgreSQLLogs**. (A [Query Store](concepts-query-store.md)használata esetén más naplókat is konfigurálhat.)

Diagnosztikai naplók engedélyezése az Azure Portalhasználatával:

   1. A portálon nyissa meg a Postgres kiszolgáló navigációs *menüjének Diagnosztikai beállítások lapját.*
   2. Válassza *a Diagnosztikai beállítás hozzáadása lehetőséget.*
   3. Nevezze el ezt a beállítást. 
   4. Válassza ki a kívánt végpontot (tárfiók, eseményközpont, naplóelemzés). 
   5. Válassza ki a **PostgreSQLLogs naplótípust.**
   7. Mentse el a beállítást.

A Diagnosztikai naplók Powershell, CLI vagy REST API használatával történő engedélyezéséhez keresse fel a [diagnosztikai beállításokról](../azure-monitor/platform/diagnostic-settings.md) szóló cikket.

### <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

A naplók elérésének módja attól függ, hogy melyik végpontot választja. Az Azure Storage,tekintse meg a [naplók tárfiók](../azure-monitor/platform/resource-logs-collect-storage.md) cikket. Az Event Hubs, tekintse meg az [Azure-naplók stream](../azure-monitor/platform/resource-logs-stream-event-hubs.md) cikket.

Az Azure Monitor naplók, naplók küldése a kiválasztott munkaterületre. A Postgres naplók az **AzureDiagnostics** gyűjtemény mód, így lekérdezhetők az AzureDiagnostics táblából. A táblázat mezőit az alábbiakban ismertetjük. További információ a lekérdezésről és a riasztásról az [Azure Monitor naplók lekérdezésáttekintésében.](../azure-monitor/log-query/log-query-overview.md)

Az alábbiakban az első lépésekhez használható lekérdezéseket használhatja. A riasztások lekérdezések alapján konfigurálhatók.

Egy adott kiszolgáló összes postgres naplójának keresése az utolsó napon
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
```

Keresés az összes nem localhost kapcsolati kísérletre
```
AzureDiagnostics
| where Message contains "connection received" and Message !contains "host=127.0.0.1"
| where Category == "PostgreSQLLogs" and TimeGenerated > ago(6h)
```
A fenti lekérdezés a munkaterületen lévő postgres kiszolgálók elmúlt 6 órájában jeleníti meg az eredményeket.

### <a name="log-format"></a>Napló formátuma

Az alábbi táblázat a **PostgreSQLLogs** típus mezőit ismerteti. A választott kimeneti végponttól függően a belefoglalt mezők és megjelenésük sorrendje eltérő lehet. 

|**Mező** | **Leírás** |
|---|---|
| TenantId | A bérlőazonosítója |
| SourceSystem | `Azure` |
| Időgenerálva [UTC] | Időbélyegző, amikor a naplót rögzítették UTC-ben |
| Típus | A napló típusa. Mindig`AzureDiagnostics` |
| SubscriptionId | Guid az előfizetéshez, amelyhez a kiszolgáló tartozik |
| ResourceGroup | Annak az erőforráscsoportnak a neve, amelyhez a kiszolgáló tartozik |
| ResourceProvider | Az erőforrás-szolgáltató neve. Mindig`MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | Erőforrás URI-ja |
| Erőforrás | A kiszolgáló neve |
| Kategória | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| Errorlevel | Naplózási szint, például: NAPLÓ, HIBA, FIGYELMEZTETÉS |
| Üzenet | Elsődleges naplóüzenet | 
| Domain | Kiszolgálói verzió, példa: postgres-10 |
| Részletek | Másodlagos naplóüzenet (ha van ilyen) |
| ColumnName | Az oszlop neve (ha van ilyen) |
| SchemaName (Sémanév) | A séma neve (ha van ilyen) |
| Adattípusneve | Az adattípus neve (ha van ilyen) |
| LogicalServerName | A kiszolgáló neve | 
| _ResourceId | Erőforrás URI-ja |
| Előtag | A naplósor előtagja |


## <a name="next-steps"></a>További lépések
- További információ a naplók azure [portalról](howto-configure-server-logs-in-portal.md) vagy az [Azure CLI-ből](howto-configure-server-logs-using-cli.md)való eléréséről.
- További információ az [Azure Monitor díjszabásáról.](https://azure.microsoft.com/pricing/details/monitor/)
- További információ a [naplókról](concepts-audit.md)
