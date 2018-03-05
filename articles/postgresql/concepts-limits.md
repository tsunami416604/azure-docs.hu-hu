---
title: "Az Azure-adatbázis PostgreSQL korlátozásai"
description: "Ez a cikk ismerteti az Azure adatbázis korlátozásai PostgreSQL, például a kapcsolat és a tárolási motor beállításai."
services: postgresql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: ba05308039e9743dd207333476e61a45c0ca166a
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="limitations-in-azure-database-for-postgresql"></a>Az Azure-adatbázis PostgreSQL korlátozásai
Az Azure-adatbázis PostgreSQL szolgáltatás nyilvános előzetes verzió van. A következő szakaszok ismertetik a kapacitás és az adatbázis szolgáltatásban működik korlátok.

## <a name="pricing-tier-maximums"></a>Árképzési szint méretkorlát
Azure PostgreSQL-adatbázishoz a kiszolgáló létrehozása választhat több tarifacsomagok rendelkezik. További információkért lásd: [Tarifacsomagjainak Azure-adatbázis a PostgreSQL](concepts-pricing-tiers.md).  

Van a kapcsolatok, a számítási egység és a tár az egyes tarifacsomagok maximális száma az alábbiak szerint: 

|Tarifacsomag| Számítási létrehozása| vCore(s)| Kapcsolatok maximális száma |
|---|---|---|---|
|Alapszintű| 4. generációból| 1| 50 |
|Alapszintű| 4. generációból| 2| 100 |
|Alapszintű| 5. generációból| 1| 50 |
|Alapszintű| 5. generációból| 2| 100 |
|Általános rendeltetés| 4. generációból| 2| 150|
|Általános rendeltetés| 4. generációból| 4| 250|
|Általános rendeltetés| 4. generációból| 8| 480|
|Általános rendeltetés| 4. generációból| 16| 950|
|Általános rendeltetés| 4. generációból| 32| 1500|
|Általános rendeltetés| 5. generációból| 2| 150|
|Általános rendeltetés| 5. generációból| 4| 250|
|Általános rendeltetés| 5. generációból| 8| 480|
|Általános rendeltetés| 5. generációból| 16| 950|
|Általános rendeltetés| 5. generációból| 32| 1500|
|Memóriára optimalizált| 5. generációból| 2| 150|
|Memóriára optimalizált| 5. generációból| 4| 250|
|Memóriára optimalizált| 5. generációból| 8| 480|
|Memóriára optimalizált| 5. generációból| 16| 950|
|Memóriára optimalizált| 5. generációból| 32| 1900|

Kapcsolatok száma meghaladja a korlátot, ha a következő hibaüzenet jelenhet meg:
> Súlyos hiba: sajnos már túl sok ügyfél

Az Azure rendszer figyelése az Azure-adatbázishoz PostgreSQL-kiszolgáló öt kapcsolatot igényel. 

## <a name="functional-limitations"></a>Működési korlátai
### <a name="scale-operations"></a>A skálázási műveletek
1.  Dinamikus méretezés a kiszolgálók közötti tarifacsomagok jelenleg nem támogatott. Ez azt jelenti, hogy váltás alapvető, általános célú vagy Memóriaoptimalizált rétegek között.
2.  Kiszolgáló tároló méretének csökkentése jelenleg nem támogatott.

### <a name="server-version-upgrades"></a>Kiszolgáló verziófrissítések
- Fő adatbázis motor verziók közötti automatikus áttelepítési jelenleg nem támogatott.

### <a name="subscription-management"></a>Előfizetés kezelése
- Dinamikusan előfizetésekhez és erőforráscsoportokhoz között a kiszolgálók áthelyezése jelenleg nem támogatott.

### <a name="point-in-time-restore-pitr"></a>Point-in-time-restore (PITR)
1.  A PITR funkció használata esetén az új kiszolgáló ugyanazokat a konfigurációkat, mint a kiszolgáló alapul hozza létre.
2.  A Törölt kiszolgáló visszaállítása nem támogatott.

## <a name="next-steps"></a>További lépések
- Megértéséhez [egyes tarifacsomagok elérhető](concepts-pricing-tiers.md)
- További tudnivalók [támogatott PostgreSQL-adatbázis verziója](concepts-supported-versions.md)
- Felülvizsgálati [készítsen biztonsági másolatot, és a kiszolgáló Azure-adatbázis visszaállítása az Azure portál használatával PostgreSQL](howto-restore-server-portal.md)
