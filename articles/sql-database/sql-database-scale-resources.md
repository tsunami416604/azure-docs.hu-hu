---
title: Az Azure SQL Database erőforrások skálázása |} A Microsoft Docs
description: Ez a cikk ismerteti az adatbázis méretezése által lefoglalt erőforrások hozzáadásáról vagy eltávolításáról.
services: sql-database
author: jovanpop-msft
ms.reviewer: carlrab
ms.service: sql-database
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: jovanpop
manager: craigg
ms.openlocfilehash: a6b987d9815cfabed6dd986a0d9842a97f5b5868
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39092052"
---
# <a name="scale-database-resources"></a>Adatbázis-erőforrások skálázása

Az Azure SQL Database lehetővé teszi az adatbázis minimális állásidővel dinamikusan rendeljen több erőforrást.

## <a name="overview"></a>Áttekintés

Ha az alkalmazás iránti kereslet néhány eszközzel és ügyféllel milliók, az Azure SQL Database menet közben, minimális üzemkimaradással méretezhető. Méretezhetőség az egyik legfontosabb jellemzőit PaaS, amely lehetővé teszi, hogy további erőforrásokat dinamikusan vehet fel a szolgáltatás szükség esetén. Az Azure SQL Database lehetővé teszi, hogy könnyedén módosíthatja az adatbázisok számára lefoglalt erőforrások (CPU-teljesítmény, a memória, i/o-teljesítmény és tárolás).  
Teljesítménnyel kapcsolatos problémák miatt az alkalmazás, amely nem lehet kijavítani a indexelő használatával, vagy lekérdezési újraírási metódust megnövekedett használatának csökkentheti. További erőforrások hozzáadásával lehetővé teszi, hogy gyorsan reagál, amikor az eléri az aktuális erőforrás-korlátozások az adatbázis és a bejövő terhelés kezeléséhez további power. Az Azure SQL Database lehetővé teszi skálázási az erőforrások költségeinek nincs szükség esetén.
Nem kell aggódnia a hardver megvásárlása és az alapul szolgáló infrastruktúra módosítására. Méretezési adatbázis Azure Portalon, a csúszka használatával egyszerűen elvégezhető.

![Adatbázis-teljesítmény méretezése](media/sql-database-scalability/scale-performance.svg)

Az Azure SQL Database kínál egy [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) vagy a [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md). 
-   A [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) számítási, memória és IO-erőforrások kis és nagy terhelést jelentő adatbázisokhoz database három szolgáltatásszintet kínálja: alapszintű, Standard és prémium. Az egyes szolgáltatásszintek teljesítményszintjei ezen erőforrások különféle keverékét kínálják, amelyhez további tárterület-erőforrások is hozzáadhatók.
-   A [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md) válassza ki a virtuális magok, a vagy a memória, és a számát és a storage sebességétől teszi lehetővé.
Csekély havi díjért létrehozhatja első alkalmazását egy egyedülálló, kisméretű adatbázison, majd később az aktuális szükségletek alapján manuálisan vagy programon keresztül bármikor módosíthatja a szolgáltatásszintet. Úgy módosíthatja a teljesítményt, hogy az nem jár leállással az alkalmazás vagy az ügyfelek számára. A dinamikus méretezhetőség révén az adatbázis átlátható módon reagál a gyorsan változó erőforrásigényekre, és lehetővé teszi, hogy csak azokért az erőforrásokért fizessen, amelyekre és amikor szüksége van.


> [!NOTE]
> A dinamikus méretezhetőség különbözik az automatikus skálázástól. Automatikus skálázás esetén a szolgáltatás adott feltételek alapján, automatikusan méretez, míg a dinamikus méretezhetőség lehetővé teszi a manuális méretezést, amely nem jár állásidővel.
>


Az önálló Azure SQL Database a manuális dinamikus méretezhetőséget támogatja, de az automatikus skálázást nem. Ha *automatikus* megoldást keres, érdemes megfontolni a rugalmas készletek használatát, amely lehetővé teszi, hogy az adatbázisok osztozzanak egy készlet erőforrásain az egyes adatbázisok egyedi igényei alapján.
Vannak azonban olyan parancsfájlok, amelyek segítségével automatizálhatja a méretezhetőség egyetlen Azure SQL-adatbázishoz. Erre az [Egyetlen SQL-adatbázis monitorozása és skálázása a PowerShell használatával](scripts/sql-database-monitor-and-scale-database-powershell.md) című témakörben láthat példát.


Az Azure SQL Database összes három változatban érhetők el ajánlat dinamikusan méretezheti az adatbázisok néhány képessége:
-   A [önálló Azure SQL adatbázist](sql-database-single-database-scale.md), használhat [DTU](sql-database-dtu-resource-limits-single-databases.md) vagy [virtuális mag](sql-database-vcore-resource-limits-single-databases.md) modellek meghatározásához rendeli hozzá az egyes adatbázisok erőforrások maximális mennyiségét.
-   [Az Azure SQL felügyelt példánya](sql-database-managed-instance.md) használ [virtuális magok](/azure/sql-database/sql-database-managed-instance#vcore-based-purchasing-model-preview) módban, és lehetővé teszi, hogy meghatározza a maximális CPU-magok és a példányhoz lefoglalt tárterület maximális. A példány belüli összes adatbázist fog ugyanazokat az a példányhoz lefoglalt erőforrásokat.
-   [Az Azure rugalmas SQL-készletek](sql-database-elastic-pool-scale.md) engedélyezése, hogy a készlet maximális erőforrás felső határ az egyes adatbázisok csoport definiálja.

## <a name="alternative-scale-methods"></a>Alternatív méretezési módszer
Erőforrások skálázása, a legegyszerűbb és a leghatékonyabb megoldást választja, az adatbázis teljesítményének növelése adatbázis vagy az alkalmazás a programkód módosítása nélkül.
Egyes esetekben akár a legmagasabb teljesítményi szintnél és teljesítményoptimalizálás előfordulhat, hogy nem kezeli a számítási feladatok, a sikeres és költséghatékony módja. Adott esetben más az adatbázis méretezése lehetősége van:
-   [Horizontális felskálázás olvasása](sql-database-read-scale-out.md) érhető el a szolgáltatás hol azért kapta az adatok egy csak olvasható replikája ahol hajthat végre erőforrás-igényes csak olvasható lekérdezései, például a jelentések. Csak Red replika fogja kezelni a csak olvasható számítási feladat működésének megzavarása nélkül megtesztelheti az elsődleges adatbázis erőforrás-használata.
-   [Database sharding](sql-database-elastic-scale-introduction.md) olyan technikákat, amely lehetővé teszi az adatok felosztása több adatbázist, és egymástól független méretezését.

## <a name="next-steps"></a>További lépések
- Adatbázis teljesítményének növelése adatbázis kód módosításával kapcsolatos információkért lásd: [keresse meg és teljesítménnyel kapcsolatos javaslatok alkalmazása](sql-database-advisor-portal.md).
- Így optimalizálhatja adatbázisát adatbázis beépített intelligencia kapcsolatos információkért lásd: [az automatikus hangolás](sql-database-automatic-tuning.md).
- Olvasási horizontális felskálázás az Azure SQL Database szolgáltatásban kapcsolatos információkért lásd: hogyan [terheléselosztása csak olvasható lekérdezési számítási feladatok betöltése a csak olvasható replika segítségével](sql-database-read-scale-out.md).
- Egy adatbázis horizontális skálázási kapcsolatos információkért lásd: [horizontális felskálázás az Azure SQL Database](sql-database-elastic-scale-introduction.md).

