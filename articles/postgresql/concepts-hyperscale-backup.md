---
title: Biztonsági mentés és visszaállítás – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: A véletlen sérülés vagy törlés adatainak védelme
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 2d781ca7293d4bd95ae62eadc50295ca14c2d381
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91314930"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---hyperscale-citus"></a>Biztonsági mentés és visszaállítás a Azure Database for PostgreSQL-nagy kapacitású (Citus)

Azure Database for PostgreSQL – a nagy kapacitású (Citus) automatikusan létrehozza az egyes csomópontok biztonsági másolatait, és helyileg redundáns tárolóban tárolja őket. A biztonsági mentések segítségével visszaállíthatja a nagy kapacitású-(Citus-) fürtöt egy adott időpontra. A biztonsági mentés és a visszaállítás fontos részét képezi az üzletmenet folytonossági stratégiájának, mivel ezek az adatok a véletlen sérüléstől vagy törléstől védve vannak.

## <a name="backups"></a>Biztonsági másolatok

Naponta legalább egyszer Azure Database for PostgreSQL pillanatfelvételt készít az adatfájlokról és az adatbázis-tranzakciós naplóról. A biztonsági mentések lehetővé teszik a kiszolgálók bármely időpontra történő visszaállítását a megőrzési időtartamon belül. (A megőrzési időtartam jelenleg 35 nap az összes fürthöz.) Az összes biztonsági mentés titkosítása AES 256 bites titkosítás használatával történik.

A rendelkezésre állási zónákat támogató Azure-régiókban a biztonsági mentési Pillanatképek három rendelkezésre állási zónában tárolódnak. Amíg legalább egy rendelkezésre állási zóna online állapotú, a nagy kapacitású (Citus) fürt visszaállítható.

Nem lehet exportálni a biztonságimásolat-fájlokat. Csak a Azure Database for PostgreSQL-ben történő visszaállítási műveletekhez használhatók.

### <a name="backup-storage-cost"></a>Biztonsági mentési tárolási díj

Az aktuális biztonsági mentési tár díjszabását a Azure Database for PostgreSQL-nagy kapacitású (Citus) [díjszabási oldalán](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/)tekintheti meg.

## <a name="restore"></a>Visszaállítás

Azure Database for PostgreSQL egy nagy kapacitású-(Citus-) fürt visszaállítása új fürtöt hoz létre az eredeti csomópontok biztonsági másolatai közül. 

> [!IMPORTANT]
>A nagy kapacitású-(Citus-) fürtöt csak ugyanabban az előfizetésben és erőforráscsoporthoz, illetve egy másik fürt nevével állíthatja vissza.


> [!IMPORTANT]
> A törölt nagy kapacitású-(Citus-) fürtök nem állíthatók vissza. Ha törli a fürtöt, a fürthöz tartozó összes csomópont törölve lesz, és nem állítható helyre. A fürt erőforrásainak védelmét, az üzembe helyezést követően a véletlen törlés vagy a váratlan módosítások miatt a rendszergazdák kihasználhatják a [felügyeleti zárolásokat](/azure/azure-resource-manager/management/lock-resources).

### <a name="point-in-time-restore-pitr"></a>Időponthoz tartozó visszaállítás (PITR)

Az elmúlt 35 napon belül bármikor visszaállíthat egy fürtöt bármely időpontra.
Az időponthoz való visszaállítás több esetben is hasznos lehet. Ha például egy felhasználó véletlenül törli az adatvesztést, elveszít egy fontos táblát vagy adatbázist, vagy ha egy alkalmazás véletlenül felülírja a megfelelő adatvesztést.

A visszaállítási folyamat egy új fürtöt hoz létre ugyanabban az Azure-régióban, előfizetésben és erőforráscsoporthoz, mint az eredeti. A fürt rendelkezik az eredeti konfigurációval: a csomópontok száma, a virtuális mag száma, a tárterület mérete, a felhasználói szerepkörök, a PostgreSQL-verzió és a Citus-bővítmény verziója.

A tűzfalbeállítások és a PostgreSQL-kiszolgáló paraméterei nem őrződnek meg az eredeti kiszolgáló csoportjából, alapértékre állnak vissza. A tűzfal megakadályozza az összes kapcsolatot. A visszaállítás után manuálisan kell módosítania ezeket a beállításokat.

> [!IMPORTANT]
> Meg kell nyitnia egy támogatási kérést a nagy kapacitású-(Citus-) fürt időponthoz való visszaállításának végrehajtásához.

### <a name="post-restore-tasks"></a>Post-restore tasks

A helyreállítási mechanizmusból való visszaállítás után a következőket kell tennie a felhasználók és alkalmazások biztonsági mentésének és futtatásának visszaszerzéséhez:

* Ha az új kiszolgáló lecseréli az eredeti kiszolgálót, átirányítja az ügyfeleket és az ügyfélalkalmazások az új kiszolgálóra
* Gondoskodjon arról, hogy a felhasználók csatlakozzanak a megfelelő kiszolgálói szintű tűzfal-és VNet-szabályokhoz. Ezek a szabályok nem másolódnak át az eredeti kiszolgáló csoportjából.
* Szükség szerint módosítsa a PostgreSQL-kiszolgáló paramétereit. A paraméterek nem másolódnak át az eredeti kiszolgáló csoportjából.
* Győződjön meg arról, hogy a megfelelő bejelentkezések és az adatbázis-szintű engedélyek vannak érvényben
* Konfigurálja a riasztásokat, ha szükséges.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg az [Azure rendelkezésre állási zónáit](/azure/availability-zones/az-overview).
* A [javasolt riasztások](/azure/postgresql/howto-hyperscale-alert-on-metric#suggested-alerts) beállítása nagy kapacitású-(Citus-) kiszolgálócsoportok esetén.
