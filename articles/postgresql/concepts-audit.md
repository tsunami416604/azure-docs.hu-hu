---
title: Naplózás naplózása - Azure Database for PostgreSQL - Single Server
description: A pgAudit naplózási naplózásának fogalmai az Azure Database for PostgreSQL - Single Server szolgáltatásban.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 45490e398abd8b5bd3c10adb95b56e1019d2bb94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842469"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>Naplózás naplózása a PostgreSQL Azure-adatbázisában – Egykiszolgálós

Az adatbázis-tevékenységek naplózása az Azure Database for PostgreSQL – Single Server rendszerben a PostgreSQL Audit Extension: [pgAudit](https://www.pgaudit.org/)bővítményen keresztül érhető el. A pgAudit részletes munkamenet- és/vagy objektumnaplózást biztosít.

> [!NOTE]
> A pgAudit előzetes verzióban érhető el a PostgreSQL Azure Database for PostgreSQL webhelyen.
> A bővítmény csak általános célú és memóriaoptimalizált kiszolgálókon engedélyezhető.

Ha azure-szintű naplókat szeretne az olyan műveletekhez, mint a számítási és tárolási méretezés, tekintse meg az [Azure-tevékenységnaplót.](../azure-monitor/platform/platform-logs-overview.md)

## <a name="usage-considerations"></a>Használati szempontok
Alapértelmezés szerint a pgAudit naplóutasítások a normál naplóutasításokkal együtt vannak kibocsátva a Postgres standard naplózási eszközének használatával. Az Azure Database for PostgreSQL-ben ezek a .log-fájlok az Azure Portalról vagy a parancssori felületről tölthetők le. A fájlok gyűjteményének maximális tárhelye 1 GB, és minden fájl legfeljebb hét napig érhető el (az alapértelmezett érték három nap). Ez a szolgáltatás egy rövid távú tárolási lehetőség.

Azt is beállíthatja, hogy az Azure Monitor diagnosztikai naplószolgáltatása minden naplót kibocsátjon. Ha engedélyezi az Azure Monitor diagnosztikai naplózását, a naplók automatikusan elküldésre kerülnek (JSON formátumban) az Azure Storage, az Event Hubs és/vagy az Azure Monitor naplóiba, a ttól függően, hogy ön választotta.If you enable Azure Monitor diagnostic logging, your logs will automatically sent (in JSON format) to Azure Storage, Event Hubs, and/or Azure Monitor logs, függően az Ön választása.

A pgAudit engedélyezése nagy mennyiségű naplót eredményez a kiszolgálón, ami hatással van a teljesítményre és a naplózási tárkapacitásra. Azt javasoljuk, hogy az Azure diagnosztikai naplószolgáltatását használja, amely hosszú távú tárolási lehetőségeket, valamint elemzési és riasztási funkciókat nyújt. Javasoljuk a standard naplózás kikapcsolását, hogy a további naplózás ne rontsa a teljesítményt:

   1. Állítsa a `logging_collector` paramétert OFF-ra. 
   2. A módosítás alkalmazásához indítsa újra a kiszolgálót.

Ha meg szeretné tudni, hogyan állíthatja be a naplózást az Azure Storage, az Event Hubs vagy az Azure Monitor naplóiba, olvassa el a [kiszolgálónaplókról szóló cikk](concepts-server-logs.md)diagnosztikai naplókra vonatkozó szakaszát.

## <a name="installing-pgaudit"></a>A pgAudit telepítése

A pgAudit telepítéséhez fel kell tüntetnie azt a kiszolgáló megosztott előtöltő könyvtáraiban. A Postgres paraméterének `shared_preload_libraries` módosítása a kiszolgáló újraindításához szükséges. A paramétereket módosíthatja az [Azure Portal](howto-configure-server-parameters-using-portal.md), az [Azure CLI](howto-configure-server-parameters-using-cli.md)vagy a [REST API](/rest/api/postgresql/configurations/createorupdate)használatával.

Az [Azure Portal használata:](https://portal.azure.com)

   1. Válassza ki az Azure Database for PostgreSQL kiszolgálóját.
   2. Az oldalsávon válassza a **Kiszolgálóparaméterei lehetőséget.**
   3. Keresse meg `shared_preload_libraries` a paramétert.
   4. Válassza a **pgaudit lehetőséget.**
   5. A módosítás alkalmazásához indítsa újra a kiszolgálót.

   6. Csatlakozás a kiszolgálóhoz egy ügyfél (például a psql) használatával, és engedélyezze a pgAudit bővítményt
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> Ha hibaüzenet et lát, győződjön meg arról, hogy a mentés után újraindította a kiszolgálót. `shared_preload_libraries`

## <a name="pgaudit-settings"></a>pgAudit-beállítások

PgAudit lehetővé teszi a munkamenet- vagy objektumnaplózás naplózásának konfigurálását. [A munkamenet-naplózás](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) részletes naplókat bocsát ki a végrehajtott utasításokról. [Az objektumnaplózás](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) naplózása adott kapcsolatokra vonatkozik. Beállíthatja az egyik vagy mindkét típusú naplózást. 

> [!NOTE]
> PgAudit beállítások meg vannak adva gloabally, és nem adható meg egy adatbázis vagy szerepkör szintjén.

Miután [telepítette a pgAudit](#installing-pgaudit)alkalmazást, beállíthatja a paramétereket a naplózás megkezdéséhez. A [pgAudit dokumentáció](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) az egyes paraméterek definícióját tartalmazza. Először tesztelje a paramétereket, és győződjön meg arról, hogy a várt viselkedést kapja.

> [!NOTE]
> Ha `pgaudit.log_client` a be beállításra van állítva, a naplókat egy ügyfélfolyamatra (például a psql-re) irányítja át, ahelyett, hogy fájlba írnák. Ezt a beállítást általában javasolt letiltva hagyni. <br> <br>
> `pgaudit.log_level`csak akkor `pgaudit.log_client` van engedélyezve, ha be van kapcsolva.

> [!NOTE]
> A PostgreSQL Azure `pgaudit.log` Database-ben nem `-` állítható be a pgAudit dokumentációban leírtak szerint (mínusz) jelparancsikon használatával. Az összes kötelező utasítási osztályt (olvasás, írás stb.) külön-külön kell megadni.

### <a name="audit-log-format"></a>Napló formátuma
Minden naplóbejegyzést a `AUDIT:` naplósor kezdete jelzi. A bejegyzés többi részének formátumát a [pgAudit dokumentáció](https://github.com/pgaudit/pgaudit/blob/master/README.md#format)részletezi .

Ha a naplózási követelmények teljesítéséhez más mezőkre `log_line_prefix`van szüksége, használja a Postgres paramétert. `log_line_prefix`egy karakterlánc, amely minden postgres naplósor elején kerül ki. A következő `log_line_prefix` beállítás például időbélyeget, felhasználónevet, adatbázisnevet és folyamatazonosítót biztosít:

```
t=%m u=%u db=%d pid=[%p]:
```

További információ: `log_line_prefix`a [PostgreSQL dokumentációja](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX).

### <a name="getting-started"></a>Első lépések
A gyors kezdéshez `pgaudit.log` `WRITE`állítsa a , és nyissa meg a naplókat, hogy vizsgálja felül a kimenetet. 

## <a name="viewing-audit-logs"></a>Naplónaplók megtekintése
Ha .log fájlokat használ, a naplói ugyanabban a fájlban lesznek, mint a PostgreSQL hibanaplók. Naplófájlokat tölthet le az Azure [Portalról](howto-configure-server-logs-in-portal.md) vagy a [CLI-ről.](howto-configure-server-logs-using-cli.md) 

Ha Az Azure diagnosztikai naplózás, a naplók elérésének módja attól függ, hogy melyik végpontot választja. Az Azure Storage,tekintse meg a [naplók tárfiók](../azure-monitor/platform/resource-logs-collect-storage.md) cikket. Az Event Hubs, tekintse meg az [Azure-naplók stream](../azure-monitor/platform/resource-logs-stream-event-hubs.md) cikket.

Az Azure Monitor naplók, naplók küldése a kiválasztott munkaterületre. A Postgres naplók az **AzureDiagnostics** gyűjtemény mód, így lekérdezhetők az AzureDiagnostics táblából. A táblázat mezőit az alábbiakban ismertetjük. További információ a lekérdezésről és a riasztásról az [Azure Monitor naplók lekérdezésáttekintésében.](../azure-monitor/log-query/log-query-overview.md)

Ezzel a lekérdezéssel kezdheti a kezdést. A riasztások lekérdezések alapján konfigurálhatók.

Egy adott kiszolgáló összes postgres naplójának keresése az utolsó napon
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>További lépések
- [További információ a PostgreSQL Azure Database for PostgreSQL-ben való naplózásról](concepts-server-logs.md)
- Ismerje meg, hogyan állíthatja be a paramétereket az [Azure Portal](howto-configure-server-parameters-using-portal.md), az [Azure CLI](howto-configure-server-parameters-using-cli.md)vagy a [REST API](/rest/api/postgresql/configurations/createorupdate)használatával.
