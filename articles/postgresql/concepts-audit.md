---
title: Naplózás naplózása a pgAudit használatával Azure Database for PostgreSQL – egyetlen kiszolgáló
description: A pgAudit naplózási naplózásának fogalmai Azure Database for PostgreSQL – egyetlen kiszolgálón.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 198ab5f567652a76d209168041f305b9da4d0b43
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71147166"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>Naplózás naplózása Azure Database for PostgreSQL – egyetlen kiszolgáló

Az adatbázis-tevékenységek naplózása Azure Database for PostgreSQL – az egyes kiszolgálók a PostgreSQL-naplózási bővítményen keresztül érhetők el: [pgAudit](https://www.pgaudit.org/). a pgAudit részletes munkamenet-és/vagy objektum-naplózási naplózást biztosít.

> [!NOTE]
> a pgAudit előzetes verzióban érhető el Azure Database for PostgreSQL.
> A bővítmény csak általános célú és a memóriára optimalizált kiszolgálókon engedélyezhető.

## <a name="usage-considerations"></a>Használati megfontolások
Alapértelmezés szerint a rendszer a pgAudit-napló utasításait az postgres szabványos naplózási létesítményének használatával, a normál naplós utasításokkal együtt bocsátja ki. A Azure Database for PostgreSQL a. log fájlokat a Azure Portal vagy a parancssori felületről töltheti le. A fájlok gyűjteményének maximális tárterülete 1 GB, és minden fájl legfeljebb hét napig érhető el (az alapértelmezett érték három nap). Ez a szolgáltatás egy rövid távú tárolási lehetőség.

Azt is megteheti, hogy az összes naplót úgy állítja be, hogy az Azure Monitor a diagnosztikai napló szolgáltatására legyen kibocsátva. Ha engedélyezi Azure Monitor diagnosztikai naplózást, a rendszer automatikusan elküldi a naplókat (JSON formátumban) az Azure Storage-ba, Event Hubsba és/vagy Azure Monitor naplókba, attól függően, hogy Ön milyen választ.

A pgAudit engedélyezése nagy mennyiségű naplózást generál egy kiszolgálón, amely hatással van a teljesítményre és a naplózási tárolóra. Javasoljuk, hogy az Azure diagnosztikai napló szolgáltatást használja, amely hosszú távú tárolási lehetőségeket, valamint elemzési és riasztási funkciókat kínál. Javasoljuk, hogy kapcsolja ki a szabványos naplózást, hogy csökkentse a további naplózás teljesítményére gyakorolt hatást:

   1. Állítsa a paramétert `logging_collector` kikapcsolt értékre. 
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
> Ha hibaüzenet jelenik meg, ellenőrizze, hogy a Mentés `shared_preload_libraries`után újraindította-e a kiszolgálót.

## <a name="pgaudit-settings"></a>pgAudit-beállítások

a pgAudit lehetővé teszi a munkamenet vagy az objektum naplózási naplózásának konfigurálását. A [munkamenet naplózási naplózása](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) a végrehajtott utasítások részletes naplóit bocsátja ki. Az [objektum naplózási naplózása](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) az adott kapcsolatokra terjed ki. Dönthet úgy is, hogy beállít egy vagy mindkét típusú naplózást. 

> [!NOTE]
> a pgAudit-beállítások megadott gloabally, és nem adhatók meg adatbázis vagy szerepkör szintjén.

A [pgAudit telepítése](#installing-pgaudit)után a paramétereket a naplózás megkezdéséhez is konfigurálhatja. Az [pgAudit dokumentációja](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) az egyes paraméterek definícióját tartalmazza. Először tesztelje a paramétereket, és győződjön meg róla, hogy a várt működést tapasztalja.

> [!NOTE]
> Ha `pgaudit.log_client` a be értékre állítja, a rendszer átirányítja a naplókat egy ügyfél-folyamatba (például psql) a fájlba való írás helyett. Ennek a beállításnak általában le kell maradnia.

> [!NOTE]
> `pgaudit.log_level`csak akkor engedélyezett, `pgaudit.log_client` ha be van kapcsolva. Emellett a Azure Portalban jelenleg egy hiba `pgaudit.log_level`van: kombinált lista jelenik meg, ami azt jelenti, hogy több szint is kiválasztható. Azonban csak egy szintet kell kiválasztani. 

> [!NOTE]
> A Azure Database for PostgreSQLban `pgaudit.log` nem állítható be `-` (mínusz) az pgAudit dokumentációjában leírtak szerint. Az összes kötelező utasítási osztályt (olvasás, írás stb.) egyedileg meg kell adni.

### <a name="audit-log-format"></a>Naplózási napló formátuma
Az egyes naplózási bejegyzéseket a `AUDIT:` rendszer a naplósor elejéhez közel jelzi. A bejegyzés további részének formátuma a [pgAudit dokumentációjában](https://github.com/pgaudit/pgaudit/blob/master/README.md#format)található.

Ha más mezőkre van szüksége a naplózási követelmények kielégítéséhez, használja a postgres `log_line_prefix`paramétert. `log_line_prefix`egy olyan karakterlánc, amely minden postgres elején kimenetként szerepel. A következő `log_line_prefix` beállítás például az időbélyeget, a felhasználónevet, az adatbázis nevét és a folyamat azonosítóját tartalmazza:

```
t=%m u=%u db=%d pid=[%p]:
```

További információ `log_line_prefix`a [PostgreSQL dokumentációjában](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX)található.

### <a name="getting-started"></a>Első lépések
A gyors kezdéshez állítsa `pgaudit.log` `WRITE`a következőt:, majd nyissa meg a naplókat a kimenet áttekintéséhez. 


## <a name="next-steps"></a>További lépések
- [Tudnivalók a Azure Database for PostgreSQL való bejelentkezésről](concepts-server-logs.md)
- Megtudhatja, hogyan állíthatja be a paramétereket a [Azure Portal](howto-configure-server-parameters-using-portal.md), az [Azure CLI](howto-configure-server-parameters-using-cli.md)vagy a [REST API](/rest/api/postgresql/configurations/createorupdate)használatával.
