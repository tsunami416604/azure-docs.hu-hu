---
title: Az Azure-adatbázis PostgreSQL korlátozásai
description: Ez a cikk ismerteti az Azure adatbázis korlátozásai PostgreSQL, például a kapcsolat és a tárolási motor beállításai.
services: postgresql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 06/04/2018
ms.openlocfilehash: 5cd829236d8d8a58e68f7bf766790aa3f0cb656e
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757416"
---
# <a name="limitations-in-azure-database-for-postgresql"></a>Az Azure-adatbázis PostgreSQL korlátozásai
A következő szakaszok ismertetik a kapacitás és az adatbázis szolgáltatásban működik korlátok.

## <a name="maximum-connections"></a>A kapcsolatok maximális száma
Tarifacsomag és vCores-kapcsolatok maximális száma a következők: 

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

Kapcsolatok száma meghaladja a korlátot, ha a következő hibaüzenet jelenhet meg:
> Súlyos hiba: sajnos már túl sok ügyfél

Az Azure rendszer figyelése az Azure-adatbázishoz PostgreSQL-kiszolgáló öt kapcsolatot igényel. 

## <a name="functional-limitations"></a>Működési korlátai
### <a name="scale-operations"></a>A skálázási műveletek
1.  Dinamikus méretezés a kiszolgálók közötti tarifacsomagok jelenleg nem támogatott. Ez azt jelenti, hogy váltás alapvető, általános célú vagy Memóriaoptimalizált rétegek között.
2.  Kiszolgáló tároló méretének csökkentése jelenleg nem támogatott.

### <a name="server-version-upgrades"></a>Kiszolgáló verziófrissítések
- Fő adatbázis motor verziók közötti automatikus áttelepítési jelenleg nem támogatott.

### <a name="subscription-management"></a>Előfizetés-kezelés
- Dinamikusan előfizetésekhez és erőforráscsoportokhoz között a kiszolgálók áthelyezése jelenleg nem támogatott.

### <a name="point-in-time-restore-pitr"></a>Pont-a--visszaállítás egy korábbi időpontra (PITR)
1.  A PITR funkció használata esetén az új kiszolgáló ugyanazokat a konfigurációkat, mint a kiszolgáló alapul hozza létre.
2.  A Törölt kiszolgáló visszaállítása nem támogatott.

## <a name="next-steps"></a>További lépések
- Megértéséhez [egyes tarifacsomagok elérhető](concepts-pricing-tiers.md)
- További tudnivalók [támogatott PostgreSQL-adatbázis verziója](concepts-supported-versions.md)
- Felülvizsgálati [készítsen biztonsági másolatot, és a kiszolgáló Azure-adatbázis visszaállítása az Azure portál használatával PostgreSQL](howto-restore-server-portal.md)
