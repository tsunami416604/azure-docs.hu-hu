---
title: Korlátok Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk Azure Database for PostgreSQL – egyetlen kiszolgáló korlátozásait ismerteti, például a kapcsolatok számát és a tárolási motor beállításait.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/25/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: b9cef4753b6fd324b38d7254139fe288463a0c0c
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123897"
---
# <a name="limits-in-azure-database-for-postgresql---single-server"></a>Korlátok Azure Database for PostgreSQL – egyetlen kiszolgáló
Az alábbi szakaszok az adatbázis-szolgáltatás kapacitását és működési korlátait ismertetik. Ha szeretne többet megtudni az erőforrásról (számítási, memória-, tárolási) szintjeiről, tekintse meg a [díjszabási szintek](concepts-pricing-tiers.md) című cikket.


## <a name="maximum-connections"></a>Kapcsolatok maximális száma
Tarifacsomag és virtuális magok száma kapcsolatok maximális száma a következők: 

|**Tarifacsomag**| **vCore(s)**| **Kapcsolatok maximális száma** | **Felhasználói kapcsolatok maximális száma** |
|---|---|---|---|
|Alapszintű| 1| 55 | 50|
|Alapszintű| 2| 105 | 100|
|Általános célú| 2| 150| 145|
|Általános célú| 4| 250| 245|
|Általános célú| 8| 480| 475|
|Általános célú| 16| 950| 945|
|Általános célú| 32| 1500| 1495|
|Általános célú| 64| 1900| 1895|
|Memóriára optimalizált| 2| 300| 295|
|Memóriára optimalizált| 4| 500| 495|
|Memóriára optimalizált| 8| 960| 955|
|Memóriára optimalizált| 16| 1900| 1895|
|Memóriára optimalizált| 32| 1987| 1982|

Amikor kapcsolatokat meghaladják a korlátot, a következő hiba jelenhet meg:
> VÉGZETES: Sajnos túl sok ügyfél már

Az Azure-rendszernek öt kapcsolattal kell rendelkeznie a Azure Database for PostgreSQL-kiszolgáló figyeléséhez. 

## <a name="functional-limitations"></a>Működési korlátai
### <a name="scale-operations"></a>A skálázási műveletek
- Dinamikus méretezés, és onnan az alapszintű tarifacsomagban is kapható jelenleg nem támogatott.
- A kiszolgáló tárolási méretének csökkentése jelenleg nem támogatott.

### <a name="server-version-upgrades"></a>Kiszolgáló verziófrissítések
- Fő database engine verziói között az automatikus migrálási jelenleg nem támogatott. Ha szeretné, a következő fő verzióra való frissítéshez, egy [memóriakép és visszaállítás](./howto-migrate-using-dump-and-restore.md) , hogy egy kiszolgálót, amely az új motor verziójával lett létrehozva.

> Vegye figyelembe, hogy a PostgreSQL-es verzió előtt a [PostgreSQL verziószámozási házirendje](https://www.postgresql.org/support/versioning/) _jelentős_ verziófrissítést eredményezett, hogy az első _vagy_ a második szám (például 9,5 – 9,6 _) nagyobb legyen_ .
> A 10-es verziótól kezdve a rendszer csak az első szám változását tekinti jelentős verziófrissítésnek (például 10,0 – 10,1 egy alverzió verziófrissítése, és 10 – _11 a_ _főverzió frissítése_ ).

### <a name="vnet-service-endpoints"></a>Virtuális hálózati Szolgáltatásvégpontok
- Virtuális hálózati Szolgáltatásvégpontok támogatása csak az általános célú és memóriahasználatra optimalizált kiszolgálók esetében érhető el.

### <a name="restoring-a-server"></a>Kiszolgáló visszaállítása
- A PITR szolgáltatás használatakor az új kiszolgáló ugyanazzal az árképzési szintű konfigurációval jön létre, mint a kiszolgáló.
- A visszaállítás során létrehozott új kiszolgáló nem rendelkezik az eredeti kiszolgálón található tűzfalszabályok szabályaival. Ehhez az új kiszolgálóhoz külön kell beállítani a tűzfalszabályok beállításait.
- Törölt kiszolgáló visszaállítása nem támogatott.

### <a name="utf-8-characters-on-windows"></a>UTF-8 karakter a Windowsban
- Bizonyos esetekben az UTF-8 karakterek nem támogatottak teljes mértékben a nyílt forráskódú PostgreSQL-ben Windows rendszeren, amely hatással van a Azure Database for PostgreSQLra. További információért tekintse meg a [hiba #15476 a PostgreSQL-Archive](https://www.postgresql-archive.org/BUG-15476-Problem-on-show-trgm-with-4-byte-UTF-8-characters-td6056677.html) webhelyen.

## <a name="next-steps"></a>További lépések
- [Az egyes díjszabási szinten elérhető](concepts-pricing-tiers.md) tartalmak ismertetése
- További információ a [PostgreSQL-adatbázisok támogatott verzióiról](concepts-supported-versions.md)
- Tekintse át [Azure Database for PostgreSQL kiszolgáló biztonsági mentését és visszaállítását a Azure Portal használatával](howto-restore-server-portal.md)
