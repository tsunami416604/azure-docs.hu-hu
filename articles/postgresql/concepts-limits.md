---
title: Azure Database for postgresql-hez korlátozásai
description: Ez a cikk ismerteti a korlátozások az Azure Database for postgresql-hez, például kapcsolat és a tárolási motort lehetőségek számát.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 06/30/2018
ms.openlocfilehash: 52dfac826de86f67b3143cce49c35088547c4b39
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171772"
---
# <a name="limitations-in-azure-database-for-postgresql"></a>Azure Database for postgresql-hez korlátozásai
A következő szakaszok ismertetik a kapacitás és a működési korlátai az adatbázis-szolgáltatás.

## <a name="maximum-connections"></a>Kapcsolatok maximális száma
Tarifacsomag és virtuális magok száma kapcsolatok maximális száma a következők: 

|**Tarifacsomag**| **vCore(s)**| **Kapcsolatok maximális száma** |
|---|---|---|
|Alapszintű| 1| 50 |
|Alapszintű| 2| 100 |
|Általános rendeltetés| 2| 150|
|Általános rendeltetés| 4| 250|
|Általános rendeltetés| 8| 480|
|Általános rendeltetés| 16| 950|
|Általános rendeltetés| 32| 1500|
|Memóriára optimalizált| 2| 150|
|Memóriára optimalizált| 4| 250|
|Memóriára optimalizált| 8| 480|
|Memóriára optimalizált| 16| 950|

Amikor kapcsolatokat meghaladják a korlátot, a következő hiba jelenhet meg:
> VÉGZETES: sajnos már túl sok ügyfél

Az Azure rendszer öt kapcsolatok figyelése az Azure Database for PostgreSQL-kiszolgáló szükséges. 

## <a name="functional-limitations"></a>Működési korlátai
### <a name="scale-operations"></a>A skálázási műveletek
- Dinamikus méretezés, és onnan az alapszintű tarifacsomagban is kapható jelenleg nem támogatott.
- Kiszolgáló-tároló méretének csökkentése jelenleg nem támogatott.

### <a name="server-version-upgrades"></a>Kiszolgáló verziófrissítések
- Fő database engine verziói között az automatikus migrálási jelenleg nem támogatott.

### <a name="subscription-management"></a>Előfizetés-kezelés
- Dinamikusan mozgatása kiszolgálók között, előfizetések és -erőforráscsoportok jelenleg nem támogatott.

### <a name="vnet-service-endpoints"></a>Virtuális hálózati Szolgáltatásvégpontok
- Virtuális hálózati Szolgáltatásvégpontok támogatása csak az általános célú és memóriahasználatra optimalizált kiszolgálók esetében érhető el.

### <a name="restoring-a-server"></a>Kiszolgáló visszaállítása
- A PITR a funkció használata esetén az azonos árképzési szint konfigurációval, mint a kiszolgáló-alapú, az új kiszolgáló jön létre.
- A visszaállítás során létrehozott új kiszolgáló nem rendelkezik a tűzfalszabályt, amely létezett az eredeti kiszolgálón. Tűzfalszabályok kell lennie külön beállítja azt az új kiszolgáló számára.
- Törölt kiszolgáló visszaállítása nem támogatott.

## <a name="next-steps"></a>További lépések
- Megismerheti [érhető el az egyes tarifacsomagja](concepts-pricing-tiers.md)
- Ismerje meg [támogatott PostgreSQL-adatbázis verziója](concepts-supported-versions.md)
- Felülvizsgálat [biztonsági mentése és visszaállítása egy kiszolgálót az Azure Database for postgresql-hez az Azure portal használatával](howto-restore-server-portal.md)
