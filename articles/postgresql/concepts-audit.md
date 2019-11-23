---
title: Naplózás naplózása a pgAudit használatával Azure Database for PostgreSQL – egyetlen kiszolgáló
description: A pgAudit naplózási naplózásának fogalmai Azure Database for PostgreSQL – egyetlen kiszolgálón.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: 49ad7334c418e29c821320608be729e060b4a8ae
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331335"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>Naplózás naplózása Azure Database for PostgreSQL – egyetlen kiszolgáló

Az adatbázis-tevékenységek naplózása Azure Database for PostgreSQL – az egyes kiszolgálók a PostgreSQL-naplózási bővítményen keresztül érhetők el: [pgAudit](https://www.pgaudit.org/). a pgAudit részletes munkamenet-és/vagy objektum-naplózási naplózást biztosít.

> [!NOTE]
> a pgAudit előzetes verzióban érhető el Azure Database for PostgreSQL.
> A bővítmény csak általános célú és a memóriára optimalizált kiszolgálókon engedélyezhető.

Ha az Azure-beli erőforrás-szintű naplókat szeretné használni a számítási és tárolási skálázási műveletekhez, tekintse meg az [Azure-tevékenység naplóját](../azure-monitor/platform/activity-logs-overview.md).

## <a name="usage-considerations"></a>Használati megfontolások
Alapértelmezés szerint a pgAudit naplóutasítások a normál naplóutasításokkal együtt vannak kibocsátva a Postgres standard naplózási eszközének használatával. Az Azure Database for PostgreSQL-ben ezek a .log-fájlok az Azure Portalról vagy a parancssori felületről tölthetők le. A fájlok gyűjteményének maximális tárterülete 1 GB, és minden fájl legfeljebb hét napig érhető el (az alapértelmezett érték három nap). Ez a szolgáltatás egy rövid távú tárolási lehetőség.

Azt is megteheti, hogy az összes naplót úgy állítja be, hogy az Azure Monitor a diagnosztikai napló szolgáltatására legyen kibocsátva. Ha engedélyezi Azure Monitor diagnosztikai naplózást, a rendszer automatikusan elküldi a naplókat (JSON formátumban) az Azure Storage-ba, Event Hubsba és/vagy Azure Monitor naplókba, attól függően, hogy Ön milyen választ.

A pgAudit engedélyezése nagy mennyiségű naplót eredményez a kiszolgálón, ami hatással van a teljesítményre és a naplózási tárkapacitásra. Azt javasoljuk, hogy az Azure diagnosztikai naplószolgáltatását használja, amely hosszú távú tárolási lehetőségeket, valamint elemzési és riasztási funkciókat nyújt. Javasoljuk a standard naplózás kikapcsolását, hogy a további naplózás ne rontsa a teljesítményt:

   1. Állítsa a paramétert `logging_collector` ki értékre. 
   2. A módosítás alkalmazásához indítsa újra a kiszolgálót.

Ha meg szeretné tudni, hogyan állíthatja be az Azure Storage-ba való naplózást, Event Hubs vagy Azure Monitor naplókat, látogasson el a [kiszolgálói naplók](concepts-server-logs.md)diagnosztikai naplók szakaszára.

## <a name="installing-pgaudit"></a>A pgAudit telepítése

A pgAudit telepítéséhez fel kell vennie azt a kiszolgáló megosztott előtelepítő könyvtáraiba. A postgres `shared_preload_libraries` paraméterének módosítása a kiszolgáló újraindítását igényli. A paramétereket a [Azure Portal](howto-configure-server-parameters-using-portal.md), az [Azure CLI](howto-configure-server-parameters-using-cli.md)vagy a [REST API](/rest/api/postgresql/configurations/createorupdate)használatával módosíthatja.

A [Azure Portal](https://portal.azure.com)használata:

   1. Válassza ki az Azure Database for PostgreSQL kiszolgálóját.
   2. Az oldalsávon válassza a **kiszolgálói paraméterek**lehetőséget.
   3. Keresse meg a `shared_preload_libraries` paramétert.
   4. Válassza a **pgaudit**lehetőséget.
   5. A módosítás alkalmazásához indítsa újra a kiszolgálót.

   6. Kapcsolódjon a kiszolgálóhoz egy ügyfél használatával (például psql), és engedélyezze az pgAudit-bővítményt
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> Ha hibaüzenet jelenik meg, ellenőrizze, hogy a `shared_preload_libraries`mentése után újraindította-e a kiszolgálót.

## <a name="pgaudit-settings"></a>pgAudit-beállítások

a pgAudit lehetővé teszi a munkamenet vagy az objektum naplózási naplózásának konfigurálását. A [munkamenet naplózási naplózása](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) a végrehajtott utasítások részletes naplóit bocsátja ki. Az [objektum naplózási naplózása](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) az adott kapcsolatokra terjed ki. Dönthet úgy is, hogy beállít egy vagy mindkét típusú naplózást. 

> [!NOTE]
> a pgAudit-beállítások megadott gloabally, és nem adhatók meg adatbázis vagy szerepkör szintjén.

A [pgAudit telepítése](#installing-pgaudit)után a paramétereket a naplózás megkezdéséhez is konfigurálhatja. Az [pgAudit dokumentációja](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) az egyes paraméterek definícióját tartalmazza. Először tesztelje a paramétereket, és győződjön meg róla, hogy a várt működést tapasztalja.

> [!NOTE]
> Ha a (z) be értékre állítja a `pgaudit.log_client`, a rendszer átirányítja a naplókat egy ügyfél-folyamatba (például psql) a fájlba való Ezt a beállítást általában javasolt letiltva hagyni.

> [!NOTE]
> `pgaudit.log_level` csak akkor engedélyezett, ha `pgaudit.log_client` be van kapcsolva. Emellett a Azure Portalban jelenleg van egy hiba a `pgaudit.log_level`: kombinált lista jelenik meg, ami azt jelenti, hogy több szint is kiválasztható. Azonban csak egy szint választható ki. 

> [!NOTE]
> A Azure Database for PostgreSQLban `pgaudit.log` nem állítható be a pgAudit dokumentációjában leírtak szerint `-` (mínusz). Az összes kötelező utasítási osztályt (olvasás, írás stb.) külön-külön kell megadni.

### <a name="audit-log-format"></a>Naplózási napló formátuma
A rendszer minden naplózási bejegyzést `AUDIT:` jelez a naplófájl elején. A bejegyzés további részének formátuma a [pgAudit dokumentációjában](https://github.com/pgaudit/pgaudit/blob/master/README.md#format)található.

Ha más mezőkre van szüksége a naplózási követelmények kielégítéséhez, használja a postgres paramétert `log_line_prefix`. `log_line_prefix` egy olyan karakterlánc, amely minden postgres elején kimenetet eredményez. A következő `log_line_prefix` beállítás például az időbélyeget, a felhasználónevet, az adatbázis nevét és a folyamat AZONOSÍTÓját tartalmazza:

```
t=%m u=%u db=%d pid=[%p]:
```

Ha többet szeretne megtudni a `log_line_prefix`ről, látogasson el a [PostgreSQL dokumentációjában](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX).

### <a name="getting-started"></a>Első lépések
A gyors kezdéshez állítsa be `pgaudit.log` `WRITE`re, és nyissa meg a naplókat a kimenet áttekintéséhez. 


## <a name="next-steps"></a>Következő lépések
- [Tudnivalók a Azure Database for PostgreSQL való bejelentkezésről](concepts-server-logs.md)
- Megtudhatja, hogyan állíthatja be a paramétereket a [Azure Portal](howto-configure-server-parameters-using-portal.md), az [Azure CLI](howto-configure-server-parameters-using-cli.md)vagy a [REST API](/rest/api/postgresql/configurations/createorupdate)használatával.
