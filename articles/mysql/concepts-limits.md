---
title: "MySQL az Azure-adatbázis korlátozásai |} Microsoft Docs"
description: "A MySQL az Azure-adatbázis előzetes verzió korlátozásai ismerteti."
services: mysql
author: jasonh
ms.author: kamathsun
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 10/26/2017
ms.openlocfilehash: b3fba38cacf5b5abcdea7f0def8c1d39e653f0a8
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2017
---
# <a name="limitations-in-azure-database-for-mysql-preview"></a>Az Azure-adatbázis korlátozásai MySQL (előzetes verzió)
Az Azure-adatbázishoz a MySQL-szolgáltatás nyilvános előzetes verziójában van. A következő szakaszok ismertetik a kapacitás és az adatbázis szolgáltatásban működik korlátok. Lásd még: [általános korlátozások](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) a MySQL adatbázis-kezelő alkalmazandó.

## <a name="service-tier-maximums"></a>Szolgáltatási szint méretkorlát
Azure MySQL-adatbázis egy kiszolgáló létrehozásakor választhat több szolgáltatásszinttel rendelkezik. További információkért lásd: [az egyes szolgáltatásszinteken elérhető](concepts-service-tiers.md).  

Létezik egy maximális száma érték a kapcsolatok, a számítási egység és a tárolás, az egyes szolgáltatásszinteken előzetes, az alábbiak szerint: 

|                            |                   |
| :------------------------- | :---------------- |
| **Kapcsolatok maximális száma**        |                   |
| Alapszintű 50 számítási egység     | 50 kapcsolatok    |
| Alapszintű 100 számítási egység    | 100 kapcsolatok száma   |
| Standard 100 számítási egység | 200 kapcsolatok   |
| Standard 200 számítási egység | 400 kapcsolatok   |
| Standard 400 számítási egység | 800 kapcsolatok   |
| Standard 800 számítási egység | 1600 kapcsolatok  |
| **Maximális számítási egység**      |                   |
| Alapszintű szolgáltatásszint         | 100 számítási egység |
| Standard szolgáltatásszint      | 800 számítási egység |
| **Maximális tárolási**            |                   |
| Alapszintű szolgáltatásszint         | 1 TB              |
| Standard szolgáltatásszint      | 1 TB              |

Túl sok a kapcsolat elérésekor a következő hibaüzenet jelenhet meg:
> 1040 (08004). hiba: Túl sok a kapcsolat

## <a name="preview-functional-limitations"></a>Előzetes verzió működési korlátozásai

### <a name="scale-operations"></a>A skálázási műveletek
- Dinamikus méretezés kiszolgálók között szolgáltatásszintek jelenleg nem támogatott. Ez azt jelenti, hogy Basic és Standard szolgáltatásszintek közötti váltás.
- Dinamikus igény szerinti növelését tárolási előre létrehozott kiszolgálón jelenleg nem támogatott.
- Kiszolgáló tároló méretének csökkentése nem támogatott.

### <a name="server-version-upgrades"></a>Kiszolgáló verziófrissítések
- Fő adatbázis motor verziók közötti automatikus áttelepítési jelenleg nem támogatott.

### <a name="subscription-management"></a>Előfizetés-kezelés
- Dinamikusan áthelyezése előfizetés és az erőforráscsoport előre létrehozott kiszolgálók jelenleg nem támogatott.

### <a name="point-in-time-restore"></a>Időponthoz kötött visszaállítás
- Különböző szolgáltatási rétegben és/vagy számítási egység és a tárhely mérete visszaállítása nem engedélyezett.
- Az eldobott kiszolgáló visszaállítása nem támogatott.

## <a name="next-steps"></a>Következő lépések
- [Az egyes szolgáltatásszinteken elérhető](concepts-service-tiers.md)
- [MySQL-adatbázis támogatott verziói](concepts-supported-versions.md)
