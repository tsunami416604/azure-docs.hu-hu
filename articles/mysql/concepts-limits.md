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
ms.date: 12/09/2017
ms.openlocfilehash: e16982e4e57ba9f2f11e9ee59f88f24b3fe3fe3f
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2018
---
# <a name="limitations-in-azure-database-for-mysql"></a>MySQL az Azure-adatbázis korlátozásai
Az Azure-adatbázishoz a MySQL-szolgáltatás nyilvános előzetes verziójában van. A következő szakaszok ismertetik a kapacitás, tárolási motor támogatja, jogosultság, adatok adatkezelési utasítás támogatásának, és az adatbázis szolgáltatásban működik korlátok. Lásd még: [általános korlátozások](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) a MySQL adatbázis-kezelő alkalmazandó.

## <a name="service-tier-maximums"></a>Szolgáltatási szint méretkorlát
Azure MySQL-adatbázis egy kiszolgáló létrehozásakor választhat több szolgáltatásszinttel rendelkezik. További információkért lásd: [az egyes szolgáltatásszinteken elérhető](concepts-service-tiers.md).  

Létezik egy maximális száma érték a kapcsolatok, a számítási egység és a tárolás, az egyes szolgáltatásszinteken előzetes, az alábbiak szerint: 

|                            |                   |
| :------------------------- | :---------------- |
| **Kapcsolatok maximális száma**        |                   |
| Alapszintű 50 számítási egység     | 50 kapcsolatok    |
| Alapszintű 100 számítási egység    | 100 kapcsolat   |
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

## <a name="storage-engine-support"></a>Storage engine támogatása

### <a name="supported"></a>Támogatott
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MEMÓRIA](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Nem támogatott
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARCHÍV](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [ÖSSZEVONT](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>Jogosultság-támogatás

### <a name="unsupported"></a>Nem támogatott
- DBA szerepkör számos Server paraméterek és beállítások véletlenül rontják a kiszolgáló teljesítményét vagy az adatbázis-kezelő ACID tulajdonságainak semlegesítsék. Így a szolgáltatás integritás-és SLA-t, a termék szinten nem elérhetővé kell tenni a DBA-szerepkört, az ügyfél számára. Az alapértelmezett felhasználói fiók, amely egy új adatbázis-példány létrehozásakor összeállított, lehetővé teszi az ügyfelek a felügyelt adatbázispéldány DDL és DML-utasítások hajthatók végre. 
- SUPER jogosultsággal hasonlóképpen [SUPER jogosultság](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) is korlátozódik.

## <a name="data-manipulation-statement-support"></a>Adatok adatkezelési utasítás támogatása

### <a name="supported"></a>Támogatott
- TERHELÉSELOSZTÁSI adatok BEMENETI_FÁJL - támogatott, de adjon meg a [helyi] paraméter, amely egy UNC elérési utat (Azure storage XSMB keresztül csatlakoztatott) van átirányítva.

### <a name="unsupported"></a>Nem támogatott
- VÁLASSZON... AZ EREDMÉNYFÁJL

## <a name="preview-functional-limitations"></a>Előzetes verzió működési korlátozásai

### <a name="scale-operations"></a>A skálázási műveletek
- Dinamikus méretezés kiszolgálók között szolgáltatásszintek jelenleg nem támogatott. Ez azt jelenti, hogy Basic és Standard szolgáltatásszintek közötti váltás.
- Dinamikus igény szerinti növelését tárolási előre létrehozott kiszolgálón jelenleg nem támogatott.
- Kiszolgáló tároló méretének csökkentése nem támogatott.

### <a name="server-version-upgrades"></a>Kiszolgáló verziófrissítések
- Fő adatbázis motor verziók közötti automatikus áttelepítési jelenleg nem támogatott.

### <a name="point-in-time-restore"></a>Időponthoz kötött visszaállítás
- Különböző szolgáltatási rétegben és/vagy számítási egység és a tárhely mérete visszaállítása nem engedélyezett.
- A Törölt kiszolgáló visszaállítása nem támogatott.

## <a name="functional-limitations"></a>Működési korlátai

### <a name="subscription-management"></a>Előfizetés kezelése
- Dinamikusan áthelyezése előfizetés és az erőforráscsoport előre létrehozott kiszolgálók jelenleg nem támogatott.

## <a name="current-known-issues"></a>Jelenlegi ismert problémák:
- Kapcsolat létrejötte után a MySQL server-példány helytelen verzióját jeleníti meg. A megfelelő kiszolgálói példány versioning használatához válassza version(); a MySQL-parancssorba parancsot.

## <a name="next-steps"></a>További lépések
- [Az egyes szolgáltatásszinteken elérhető](concepts-service-tiers.md)
- [MySQL-adatbázis támogatott verziói](concepts-supported-versions.md)
