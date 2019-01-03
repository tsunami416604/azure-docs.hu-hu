---
title: Kiszolgálói naplók az Azure Database for postgresql-hez
description: Ez a cikk bemutatja, hogyan Azure-adatbázis PostgreSQL állít elő, lekérdezés és a hiba naplókat, és hogyan jelentkezzen az adatmegőrzési van konfigurálva.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 0e2dc2af6b4c7ddf531458136e6bcabb49be3b8f
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53538805"
---
# <a name="server-logs-in-azure-database-for-postgresql"></a>Kiszolgálói naplók az Azure Database for postgresql-hez 
Azure Database for PostgreSQL állít elő, lekérdezés és a hiba naplókat. Lekérdezés és a hiba a naplók segítségével azonosítása, elhárítása és konfigurációs hibák és az optimálisnál rosszabb teljesítmény javításához. (A tranzakciós naplókhoz való hozzáférést lehetőség nem része). 

## <a name="configure-logging"></a>Naplózás konfigurálása 
Konfigurálhatja a naplózás a kiszolgálón a naplózás kiszolgálói paraméterek használatával. Minden új kiszolgálón **log_checkpoints** és **log_connections** alapértelmezés szerint. Nincsenek további paraméterek, a naplózás igényeinek megfelelően módosíthatja: 

![Azure Database for PostgreSQL – a naplózási paraméterek](./media/concepts-server-logs/log-parameters.png)

További információ ezekről a paraméterekről tekintse meg a PostgreSQL [hibajelentés és a naplózás](https://www.postgresql.org/docs/current/static/runtime-config-logging.html) dokumentációját. Azure-adatbázis PostgreSQL paraméterek konfigurálása, lásd: a [portál dokumentációja](howto-configure-server-parameters-using-portal.md) vagy a [CLI dokumentációját](howto-configure-server-parameters-using-cli.md).

## <a name="access-server-logs-through-portal-or-cli"></a>Hozzáférés a kiszolgálói naplókhoz portálon vagy parancssori felületen keresztül
Ha engedélyezte a naplókat, akkor érhetők el az Azure Database for PostgreSQL log storage használatával a [az Azure portal](howto-configure-server-logs-in-portal.md), [Azure CLI-vel](howto-configure-server-logs-using-cli.md), és az Azure REST API-k. A naplófájlok elforgatása minden 1 óra vagy 100MB méretű, amelyiket hamarabb. A megőrzési ideje a log storage segítségével beállíthatja a **log\_megőrzési\_időszak** a kiszolgálóhoz társított paraméter. Az alapértelmezett érték 3 nap; a maximális értéke 7 nap. A kiszolgáló elegendő kell rendelkeznie, amely tárolja a naplófájlokat tároló lefoglalva. (A megőrzési paraméter nem szabályozza az Azure diagnosztikai naplók.)


## <a name="diagnostic-logs"></a>Diagnosztikai naplók
Azure Database for PostgreSQL integrálva van az Azure monitort, diagnosztikai naplók. Miután engedélyezte a naplók PostgreSQL-kiszolgálón, ha szeretné, azokat a kibocsátott [Log Analytics](../azure-monitor/log-query/log-query-overview.md), az Event Hubs vagy az Azure Storage. Diagnosztikai naplók engedélyezésével kapcsolatos további tudnivalókért lásd: útmutató szakasza a [diagnosztikai naplók dokumentáció](../azure-monitor/platform/diagnostic-logs-overview.md). 


A következő táblázat ismerteti, mi az egyes naplókhoz. Attól függően, a kimeneti végpont választja, a mezők és a sorrend, amelyben szerepelnek a eltérőek lehetnek. 

|**Mező** | **Leírás** |
|---|---|
| TenantId | A bérlő azonosítója |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | Időbélyeg mikor lett rögzítve a napló (UTC) |
| Típus | A napló típusa. Mindig `AzureDiagnostics` |
| SubscriptionId | GUID Azonosítóját az előfizetést, amelyhez a kiszolgáló tartozik. |
| ResourceGroup | A kiszolgáló tartozik az erőforráscsoport neve |
| ResourceProvider | Az erőforrás-szolgáltató neve. Mindig `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | Erőforrás-URI |
| Erőforrás | A kiszolgáló neve |
| Kategória | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| errorLevel | Naplózási szint, például: NAPLÓ, HIBA, FIGYELMEZTETÉS |
| Üzenet | Elsődleges naplófájlüzenetre | 
| Domain | Kiszolgáló verziója, például: postgres-10-es |
| Részlet | Másodlagos naplófájlüzenetre (ha van) |
| Oszlopnév | (Ha alkalmazható) oszlop neve |
| %{Schemaname/ | (Ha van ilyen) a séma neve |
| DatatypeName | Az adattípus (ha alkalmazható) neve |
| LogicalServerName | A kiszolgáló neve | 
| _ResourceId | Erőforrás-URI |

## <a name="next-steps"></a>További lépések
- További információ a naplóinak elérése a [az Azure portal](howto-configure-server-logs-in-portal.md) vagy [Azure CLI-vel](howto-configure-server-logs-using-cli.md).
- Tudjon meg többet [díjszabás az Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).
