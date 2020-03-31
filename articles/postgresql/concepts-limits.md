---
title: Korlátok - Azure-adatbázis a PostgreSQL számára - Egykiszolgálós
description: Ez a cikk az Azure Database for PostgreSQL – Single Server korlátozásokat ismerteti, például a kapcsolatés a tárolómotor-beállítások számát.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 047e722a0e0ade60d1eb93a48e37333fffafd674
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76836456"
---
# <a name="limits-in-azure-database-for-postgresql---single-server"></a>Korlátok az Azure Database for PostgreSQL-ben – Egykiszolgálós
A következő szakaszok az adatbázis-szolgáltatás kapacitás- és funkcionális korlátait ismertetik. Ha szeretne többet megtudni az erőforrás-(számítási, memória, tárolási) szintekről, tekintse meg a [tarifacsomagokról](concepts-pricing-tiers.md) szóló cikket.


## <a name="maximum-connections"></a>Kapcsolatok maximális száma
A tarifacsomagonkénti és virtuális magok szerinti kapcsolatok maximális száma az alábbiakban látható. Az Azure-rendszer öt kapcsolatra van szükség az Azure Database for PostgreSQL-kiszolgáló figyeléséhez. 

|**Árképzési szint**| **virtuális mag(k)**| **Kapcsolatok maximális száma** | **Felhasználói kapcsolatok maximális száma** |
|---|---|---|---|
|Basic| 1| 55 | 50|
|Basic| 2| 105 | 100|
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
> VÉGZETES: sajnálom, túl sok ügyfél már

> [!IMPORTANT]
> A legjobb élmény érdekében azt javasoljuk, hogy a kapcsolatok hatékony kezeléséhez használja a pgBouncer hez hasonló kapcsolatgyűjtőt.

A PostgreSQL kapcsolat, még tétlen, elfoglalhatja mintegy 10MB memória. Az új kapcsolatok létrehozása is időt vesz igénybe. A legtöbb alkalmazás sok rövid életű kapcsolatot kér, ami összetette ezt a helyzetet. Az eredmény kevesebb erőforrás áll rendelkezésre a tényleges munkaterheléshez, ami a teljesítmény csökkenéséhez vezet. Az alapjárati kapcsolatokat csökkentő és a meglévő kapcsolatokat újrafeltöltő kapcsolatgyűjtő segít elkerülni ezt. Ha többet szeretne megtudni, látogasson el [blogbejegyzést](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717).

## <a name="functional-limitations"></a>Funkcionális korlátozások
### <a name="scale-operations"></a>Méretezési műveletek
- Az alapszintű tarifacsomagok dinamikus skálázása jelenleg nem támogatott.
- A kiszolgáló tárhelyének csökkentése jelenleg nem támogatott.

### <a name="server-version-upgrades"></a>Kiszolgálóverzió-frissítések
- A fő adatbázis-motorverziók közötti automatikus áttelepítés jelenleg nem támogatott. Ha a következő főverzióra szeretne frissíteni, fogjon egy [memóriaképet, és állítsa vissza](./howto-migrate-using-dump-and-restore.md) egy olyan kiszolgálóra, amelyet az új motorverzióval hoztak létre.

> Ne feledje, hogy a PostgreSQL 10-es verziója előtt a [PostgreSQL verziószámozási politikája](https://www.postgresql.org/support/versioning/) úgy tekintette, hogy a _főverziófrissítése_ az első _vagy_ a második szám növekedését jelent (például a 9,5-9,6-ot _főverziófrissítésnek_ tekintették).
> A 10-es verziótól csak az első szám módosítása számít főverzió-frissítésnek (például a 10.0–10.1 _verziófrissítés_ alverziófrissítés, a 10–11 pedig _a főverziófrissítése)._

### <a name="vnet-service-endpoints"></a>VNet-szolgáltatásvégpontok
- A Virtuálishálózat-szolgáltatás végpontjainak támogatása csak általános célú és memóriaoptimalizált kiszolgálókhoz érhető el.

### <a name="restoring-a-server"></a>Kiszolgáló visszaállítása
- A PITR szolgáltatás használatakor az új kiszolgáló ugyanazokkal a tarifaréteg-konfigurációkkal jön létre, mint az a kiszolgáló, amelyen alapul.
- A visszaállítás során létrehozott új kiszolgáló nem rendelkezik az eredeti kiszolgálón létező tűzfalszabályokkal. Az új kiszolgálóhoz külön kell beállítani a tűzfalszabályokat.
- A törölt kiszolgáló visszaállítása nem támogatott.

### <a name="utf-8-characters-on-windows"></a>UTF-8 karakter a Windows rendszeren
- Bizonyos esetekben az UTF-8 karakterek nem támogatottak teljes mértékben a nyílt forráskódú PostgreSQL windowsrendszeren, amely hatással van az Azure Database for PostgreSQL.In some scenarios UTF-8 characters are not be fully in open source PostgreSQL on Windows, which affects Azure Database for PostgreSQL. Kérjük, olvassa el a téma a [Bug #15476 a postgresql-archívum](https://www.postgresql-archive.org/BUG-15476-Problem-on-show-trgm-with-4-byte-UTF-8-characters-td6056677.html) további információkért.

## <a name="next-steps"></a>További lépések
- Ismerje [meg, hogy mi érhető el az egyes tarifacsomagokban](concepts-pricing-tiers.md)
- További információ a [támogatott PostgreSQL adatbázis-verziókról](concepts-supported-versions.md)
- Tekintse [át, hogyan lehet biztonsági másolatot küldeni és visszaállítani egy kiszolgálót a PostgreSQL Azure-adatbázisában az Azure Portalhasználatával](howto-restore-server-portal.md)
