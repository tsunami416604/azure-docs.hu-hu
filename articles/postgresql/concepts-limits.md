---
title: Korlátok – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk Azure Database for PostgreSQL – egyetlen kiszolgáló korlátozásait ismerteti, például a kapcsolatok számát és a tárolási motor beállításait.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 6f48245983898c542197deb7e0b3cd53bd39be33
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91707523"
---
# <a name="limits-in-azure-database-for-postgresql---single-server"></a>Korlátok Azure Database for PostgreSQL – egyetlen kiszolgáló
Az alábbi szakaszok az adatbázis-szolgáltatás kapacitását és működési korlátait ismertetik. Ha szeretne többet megtudni az erőforrásról (számítási, memória-, tárolási) szintjeiről, tekintse meg a [díjszabási szintek](concepts-pricing-tiers.md) című cikket.


## <a name="maximum-connections"></a>Kapcsolatok maximális száma
Alább láthatók a kapcsolatok maximális száma az árképzési szinten és a virtuális mag. Az Azure-rendszernek öt kapcsolattal kell rendelkeznie a Azure Database for PostgreSQL-kiszolgáló figyeléséhez. 

|**Tarifacsomag**| **Virtuális mag (ok)**| **Kapcsolatok maximális száma** | **Felhasználói kapcsolatok maximális száma** |
|---|---|---|---|
|Alapszintű| 1| 55 | 50|
|Alapszintű| 2| 105 | 100|
|Általános célú| 2| 150| 145|
|Általános célú| 4| 250| 245|
|Általános célú| 8| 480| 475|
|Általános célú| 16| 950| 945|
|Általános célú| 32| 1500| 1495|
|Általános célú| 64| 1900| 1895|
|Memóriaoptimalizált| 2| 300| 295|
|Memóriaoptimalizált| 4| 500| 495|
|Memóriaoptimalizált| 8| 960| 955|
|Memóriaoptimalizált| 16| 1900| 1895|
|Memóriaoptimalizált| 32| 1987| 1982|

Ha a kapcsolatok túllépik a korlátot, a következő hibaüzenet jelenhet meg:
> VÉGZETES: Sajnos túl sok ügyfél már

> [!IMPORTANT]
> A legjobb megoldás érdekében javasoljuk, hogy használjon olyan kapcsolati Pooler, mint a pgBouncer, hogy hatékonyan kezelhesse a kapcsolatokat.

A PostgreSQL-kapcsolatok, akár tétlenek is, körülbelül 10 MB memóriát foglalnak magukban. Emellett az új kapcsolatok létrehozása időt vesz igénybe. A legtöbb alkalmazás sok rövid életű kapcsolatot igényel, amely ezt a helyzetet összeképezi. Ennek eredményeképpen kevesebb erőforrás érhető el a tényleges munkaterheléshez, ami csökkenti a teljesítményt. Egy kapcsolati Pooler, amely csökkenti az üresjárati kapcsolatokat, és a meglévő kapcsolatokat újra felhasználva segít elkerülni ezt. További információért látogasson el a [blogbejegyzésbe](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717).

## <a name="functional-limitations"></a>Funkcionális korlátozások
### <a name="scale-operations"></a>Skálázási műveletek
- A dinamikus skálázás az alapszintű díjszabási szintekre és a rendszerből nem támogatott.
- A kiszolgáló tárolási méretének csökkentése jelenleg nem támogatott.

### <a name="server-version-upgrades"></a>Kiszolgáló verziófrissítése
- A fő adatbázismotor-verziók közötti automatikus áttelepítés jelenleg nem támogatott. Ha a következő főverzióra szeretne frissíteni, hozzon létre egy [memóriaképet, és állítsa vissza](./howto-migrate-using-dump-and-restore.md) egy olyan kiszolgálóra, amely az új motor verziójával lett létrehozva.

> Vegye figyelembe, hogy a PostgreSQL-es verzió előtt a [PostgreSQL verziószámozási házirendje](https://www.postgresql.org/support/versioning/) _jelentős_ verziófrissítést eredményezett, hogy az első _vagy_ a második szám (például 9,5 – 9,6 _) nagyobb legyen_ .
> A 10-es verziótól kezdve a rendszer csak az első szám változását tekinti jelentős verziófrissítésnek (például 10,0 – 10,1 egy alverzió verziófrissítése, és 10 – _11 a_ _főverzió frissítése_ ).

### <a name="vnet-service-endpoints"></a>VNet-szolgáltatásvégpontok
- A VNet szolgáltatás-végpontok támogatása csak a általános célú és a memóriára optimalizált kiszolgálók esetében támogatott.

### <a name="restoring-a-server"></a>Kiszolgáló visszaállítása
- A PITR szolgáltatás használatakor az új kiszolgáló ugyanazzal az árképzési szintű konfigurációval jön létre, mint a kiszolgáló.
- A visszaállítás során létrehozott új kiszolgáló nem rendelkezik az eredeti kiszolgálón található tűzfalszabályok szabályaival. Ehhez az új kiszolgálóhoz külön kell beállítani a tűzfalszabályok beállításait.
- A törölt kiszolgáló visszaállítása nem támogatott.

### <a name="utf-8-characters-on-windows"></a>UTF-8 karakter a Windowsban
- Bizonyos esetekben az UTF-8 karakterek nem támogatottak teljes mértékben a nyílt forráskódú PostgreSQL-ben Windows rendszeren, amely hatással van a Azure Database for PostgreSQLra. További információért tekintse meg a [hiba #15476 a PostgreSQL-Archive](https://www.postgresql-archive.org/BUG-15476-Problem-on-show-trgm-with-4-byte-UTF-8-characters-td6056677.html) webhelyen.

### <a name="gss-error"></a>GSS hiba
Ha a **GSS**kapcsolatos hiba jelenik meg, valószínű, hogy egy újabb ügyfél-vagy illesztőprogram-verziót használ, amelyet az Azure postgres egyetlen kiszolgáló még nem támogat. Ez a hiba ismert a [JDBC illesztőprogram-verziók 42.2.15 és 42.2.16](https://github.com/pgjdbc/pgjdbc/issues/1868)való befolyásolásához.
   - A frissítés befejezését november végére tervezzük. Időközben érdemes lehet egy működő illesztőprogram-verziót használni.
   - Vagy vegye fontolóra a GSS-kérelem letiltását.  Használjon például a következőhöz: `gssEncMode=disable` .

## <a name="next-steps"></a>Következő lépések
- [Az egyes díjszabási szinten elérhető](concepts-pricing-tiers.md) tartalmak ismertetése
- További információ a [PostgreSQL-adatbázisok támogatott verzióiról](concepts-supported-versions.md)
- Tekintse át [Azure Database for PostgreSQL kiszolgáló biztonsági mentését és visszaállítását a Azure Portal használatával](howto-restore-server-portal.md)
