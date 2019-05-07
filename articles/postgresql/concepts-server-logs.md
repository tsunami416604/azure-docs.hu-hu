---
title: Kiszolgálói naplók az Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk ismerteti az Azure Database for postgresql-hez – egyetlen kiszolgálót állít elő, lekérdezés és a hibanaplókat és a naplófájlok megőrzése konfigurálását.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 4d1cf2c59e324cedd9b747b1ac65d6edcb9deb45
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65067389"
---
# <a name="server-logs-in-azure-database-for-postgresql---single-server"></a>Kiszolgálói naplók az Azure Database for PostgreSQL – egyetlen kiszolgáló
Azure Database for PostgreSQL állít elő, lekérdezés és a hiba naplókat. Lekérdezés és a hiba a naplók segítségével azonosítása, elhárítása és konfigurációs hibák és az optimálisnál rosszabb teljesítmény javításához. (A tranzakciós naplókhoz való hozzáférést lehetőség nem része). 

## <a name="configure-logging"></a>Naplózás konfigurálása 
Konfigurálhatja a naplózás a kiszolgálón a naplózás kiszolgálói paraméterek használatával. Minden új kiszolgálón **log_checkpoints** és **log_connections** alapértelmezés szerint. Nincsenek további paraméterek, a naplózás igényeinek megfelelően módosíthatja: 

![Azure Database for PostgreSQL – a naplózási paraméterek](./media/concepts-server-logs/log-parameters.png)

További információ ezekről a paraméterekről tekintse meg a PostgreSQL [hibajelentés és a naplózás](https://www.postgresql.org/docs/current/static/runtime-config-logging.html) dokumentációját. Azure-adatbázis PostgreSQL paraméterek konfigurálása, lásd: a [portál dokumentációja](howto-configure-server-parameters-using-portal.md) vagy a [CLI dokumentációját](howto-configure-server-parameters-using-cli.md).

## <a name="access-server-logs-through-portal-or-cli"></a>Hozzáférés a kiszolgálói naplókhoz portálon vagy parancssori felületen keresztül
Ha engedélyezte a naplókat, akkor érhetők el az Azure Database for PostgreSQL log storage használatával a [az Azure portal](howto-configure-server-logs-in-portal.md), [Azure CLI-vel](howto-configure-server-logs-using-cli.md), és az Azure REST API-k. A naplófájlok elforgatása minden 1 óra vagy 100MB méretű, amelyiket hamarabb. A megőrzési ideje a log storage segítségével beállíthatja a **log\_megőrzési\_időszak** a kiszolgálóhoz társított paraméter. Az alapértelmezett érték 3 nap; a maximális értéke 7 nap. A kiszolgáló elegendő kell rendelkeznie, amely tárolja a naplófájlokat tároló lefoglalva. (A megőrzési paraméter nem szabályozza az Azure diagnosztikai naplók.)


## <a name="diagnostic-logs"></a>Diagnosztikai naplók
Azure Database for PostgreSQL integrálva van az Azure monitort, diagnosztikai naplók. Miután engedélyezte a naplók PostgreSQL-kiszolgálón, ha szeretné, azokat a kibocsátott [naplózza az Azure Monitor](../azure-monitor/log-query/log-query-overview.md), az Event Hubs vagy az Azure Storage. Diagnosztikai naplók engedélyezésével kapcsolatos további tudnivalókért lásd: útmutató szakasza a [diagnosztikai naplók dokumentáció](../azure-monitor/platform/diagnostic-logs-overview.md). 

> [!IMPORTANT]
> Ezt a diagnosztikai naplók funkciót csak érhető el az általános célú és memóriahasználatra optimalizált [tarifacsomagok](concepts-pricing-tiers.md).

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
| Resource | A kiszolgáló neve |
| Category | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| errorLevel | Naplózási szint, például: LOG, ERROR, NOTICE |
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
