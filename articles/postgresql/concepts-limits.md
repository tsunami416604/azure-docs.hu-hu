---
title: "PostgreSQL-adatbázisban az Azure korlátozásai |} Microsoft Docs"
description: "Azure-adatbázis korlátozásai PostgreSQL ismerteti."
services: postgresql
author: kamathsun
ms.author: sukamat
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc
ms.topic: article
ms.date: 12/04/2017
ms.openlocfilehash: 6dbed1a834d74047178a9f996683d65520047e66
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2018
---
# <a name="limitations-in-azure-database-for-postgresql"></a>Az Azure-adatbázis PostgreSQL korlátozásai
Az Azure-adatbázis PostgreSQL szolgáltatás nyilvános előzetes verzió van. A következő szakaszok ismertetik a kapacitás és az adatbázis szolgáltatásban működik korlátok.

## <a name="service-tier-maximums"></a>Szolgáltatási szint méretkorlát
Azure PostgreSQL-adatbázishoz a kiszolgáló létrehozása választhat több szolgáltatásszinttel rendelkezik. További információkért lásd: [az egyes szolgáltatásszinteken elérhető](concepts-service-tiers.md).  

Nincs kapcsolatok, a számítási egység és a tárolás, az egyes szolgáltatásszintek tartalmának maximális száma a szolgáltatás előzetes az alábbiak szerint: 

| | |
| :------------------------- | :---------------- |
| **Kapcsolatok maximális száma**        |                   |
| Alapszintű 50 számítási egység     | 55 kapcsolatok    |
| Alapszintű 100 számítási egység    | 105 kapcsolatok   |
| Standard 100 számítási egység | 150 kapcsolatok   |
| Standard 200 számítási egység | 250 kapcsolatok   |
| Standard 400 számítási egység | 480 kapcsolatok   |
| Standard 800 számítási egység | 950 kapcsolatok   |
| **Maximális számítási egység**      |                   |
| Alapszintű szolgáltatásszint         | 100 számítási egység |
| Standard szolgáltatásszint      | 800 számítási egység |
| **Maximális tárolási**            |                   |
| Alapszintű szolgáltatásszint         | 1 TB              |
| Standard szolgáltatásszint      | 1 TB              |

Az Azure rendszer figyelése az Azure-adatbázishoz PostgreSQL-kiszolgáló öt kapcsolatot igényel. Túl sok a kapcsolat elérésekor a következő hibaüzenet jelenhet meg:
> Súlyos hiba: sajnos már túl sok ügyfél


## <a name="preview-functional-limitations"></a>Előzetes verzió működési korlátozásai
### <a name="scale-operations"></a>A skálázási műveletek
1.  Dinamikus méretezés kiszolgálók között szolgáltatásszintek jelenleg nem támogatott. Ez azt jelenti, hogy Basic és Standard szolgáltatásszintek közötti váltás.
2.  Dinamikus igény szerinti növelését tárolási előre létrehozott kiszolgálón jelenleg nem támogatott.
3.  Kiszolgáló tároló méretének csökkentése nem támogatott.

### <a name="server-version-upgrades"></a>Kiszolgáló verziófrissítések
- Fő adatbázis motor verziók közötti automatikus áttelepítési jelenleg nem támogatott.

### <a name="subscription-management"></a>Előfizetés kezelése
- Dinamikusan áthelyezése előfizetés és az erőforráscsoport előre létrehozott kiszolgálók jelenleg nem támogatott.

### <a name="point-in-time-restore"></a>Időponthoz kötött visszaállítás
1.  Különböző szolgáltatási rétegben és/vagy számítási egység és a tárhely mérete visszaállítása nem engedélyezett.
2.  Az eldobott kiszolgáló visszaállítása nem támogatott.

## <a name="next-steps"></a>További lépések
- Megértéséhez [egyes tarifacsomagok elérhető](concepts-service-tiers.md)
- Megértéséhez [támogatott PostgreSQL-adatbázis verziója](concepts-supported-versions.md)
- Felülvizsgálati [készítsen biztonsági másolatot, és a kiszolgáló Azure-adatbázis visszaállítása az Azure portál használatával PostgreSQL](howto-restore-server-portal.md)
