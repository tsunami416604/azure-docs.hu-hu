---
title: Kiszolgáló-naplók Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk bemutatja, hogyan hozza létre a Azure Database for PostgreSQL-Single Server lekérdezési és hibanapló-naplókat, és hogyan konfigurálja a naplózási adatmegőrzést.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: b295ab442e70772a86d6699e1063c7a1c728f1a7
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091119"
---
# <a name="server-logs-in-azure-database-for-postgresql---single-server"></a>Kiszolgáló-naplók Azure Database for PostgreSQL – egyetlen kiszolgáló
Az Azure Database for PostgreSQL lekérdezési és hibanaplókat hoz létre. A lekérdezési és a hibanapló segítségével azonosíthatja, elháríthatja és kijavíthatja a konfigurációs hibákat és az optimális teljesítményt. (A tranzakciós naplókhoz való hozzáférés nincs belefoglalva). 

## <a name="configure-logging"></a>Naplózás konfigurálása 
A naplózást a kiszolgálón a naplózási kiszolgáló paramétereinek használatával konfigurálhatja. Az egyes új kiszolgálók **log_checkpoints** és **log_connections** alapértelmezés szerint be vannak kapcsolva. További paraméterek is megadhatók a naplózási igényeknek megfelelően: 

![Azure Database for PostgreSQL – naplózási paraméterek](./media/concepts-server-logs/log-parameters.png)

További információ ezekről a paraméterekről: PostgreSQL [hibajelentési és naplózási](https://www.postgresql.org/docs/current/static/runtime-config-logging.html) dokumentációja. A Azure Database for PostgreSQL paraméterek konfigurálásának megismeréséhez tekintse meg a [portál dokumentációját](howto-configure-server-parameters-using-portal.md) vagy a [CLI dokumentációját](howto-configure-server-parameters-using-cli.md).

## <a name="access-server-logs-through-portal-or-cli"></a>Hozzáférési kiszolgáló naplófájljai a portálon vagy a CLI-n keresztül
Ha engedélyezte a naplókat, a [Azure Portal](howto-configure-server-logs-in-portal.md), az [Azure CLI](howto-configure-server-logs-using-cli.md)és az Azure REST API-k használatával érheti el azokat a Azure Database for PostgreSQL log Storage-ból. A naplófájlok 1 óránként vagy a 100 MB-os méret elérése után rotálódnak, attól függően, hogy melyik következik be először. A naplófájl megőrzési időtartamát a kiszolgálóhoz társított **napló\_megőrzési\_időtartam** paramétere alapján állíthatja be. Az alapértelmezett érték 3 nap; a maximális érték 7 nap. A kiszolgálónak elegendő lefoglalt tárterülettel kell rendelkeznie a naplófájlok tárolásához. (Ez a megőrzési paraméter nem szabályozza az Azure diagnosztikai naplókat.)


## <a name="diagnostic-logs"></a>Diagnosztikai naplók
Azure Database for PostgreSQL integrálva van Azure Monitor diagnosztikai naplókba. Miután engedélyezte a naplókat a PostgreSQL-kiszolgálón, kiválaszthatja, hogy a [naplókat](../azure-monitor/log-query/log-query-overview.md), Event Hubsokat vagy az Azure Storage-t Azure monitor. 

> [!IMPORTANT]
> A kiszolgálói naplókhoz tartozó diagnosztikai funkció csak a általános célú és a memória optimalizált [díjszabási szintjein](concepts-pricing-tiers.md)érhető el.

Diagnosztikai naplók engedélyezése a Azure Portal használatával:

   1. A portálon lépjen a *diagnosztikai beállítások* elemre a postgres-kiszolgáló navigációs menüjében.
   2. Válassza a *diagnosztikai beállítás hozzáadása*lehetőséget.
   3. Nevezze el ezt a beállítást. 
   4. Válassza ki az előnyben részesített alsóbb szintű helyet (Storage-fiók, Event hub, log Analytics). 
   5. Válassza ki a kívánt adattípusokat.
   6. Mentse a beállítást.

Az alábbi táblázat az egyes naplókban található tudnivalókat ismerteti. A kiválasztott kimeneti végponttól függően a befoglalt mezők és a megjelenő sorrend eltérő lehet. 

|**Mező** | **Leírás** |
|---|---|
| TenantId | A bérlő azonosítója |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | A napló UTC-ben való rögzítésének időbélyegzője |
| Type | A napló típusa. Mindig `AzureDiagnostics` |
| SubscriptionId | Annak az előfizetésnek a GUID azonosítója, amelyhez a kiszolgáló tartozik |
| Erőforráscsoport | Azon erőforráscsoport neve, amelyhez a kiszolgáló tartozik |
| ResourceProvider | Az erőforrás-szolgáltató neve. Mindig `MICROSOFT.DBFORPOSTGRESQL` |
| Erőforrástípus | `Servers` |
| ResourceId | Erőforrás URI-ja |
| Resource | A kiszolgáló neve |
| Category | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| errorLevel | Naplózási szint, példa: NAPLÓ, HIBA, FIGYELMEZTETÉS |
| Message | Elsődleges napló üzenete | 
| Domain | Kiszolgáló verziója, példa: postgres-10 |
| Részletek | Másodlagos napló üzenete (ha van ilyen) |
| ColumnName | Az oszlop neve (ha van ilyen) |
| SchemaName | A séma neve (ha van ilyen) |
| DatatypeName | Az adattípus neve (ha van ilyen) |
| LogicalServerName | A kiszolgáló neve | 
| _ResourceId | Erőforrás URI-ja |
| Előtag | A naplózási sor előtagja |



## <a name="next-steps"></a>További lépések
- További információ a naplók [Azure Portal](howto-configure-server-logs-in-portal.md) vagy az [Azure CLI](howto-configure-server-logs-using-cli.md)-ből való eléréséről.
- További információ a [Azure monitor díjszabásáról](https://azure.microsoft.com/pricing/details/monitor/).
