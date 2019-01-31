---
title: Az Azure SQL Database erőforrások skálázása |} A Microsoft Docs
description: Ez a cikk ismerteti az adatbázis méretezése által lefoglalt erőforrások hozzáadásáról vagy eltávolításáról.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 49743130966589cceedb7756540c723a6f3276ff
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55471667"
---
# <a name="dynamically-scale-database-resources-with-minimal-downtime"></a>Dinamikusan méretezheti az adatbázis-erőforrások minimális állásidővel

Az Azure SQL Database lehetővé teszi az adatbázis minimális állásidővel dinamikusan rendeljen több erőforrást.

## <a name="overview"></a>Áttekintés

Ha az alkalmazás iránti kereslet néhány eszközzel és ügyféllel milliók, az Azure SQL Database menet közben, minimális üzemkimaradással méretezhető. Méretezhetőség az egyik legfontosabb jellemzőit PaaS, amely lehetővé teszi, hogy további erőforrásokat dinamikusan vehet fel a szolgáltatás szükség esetén. Az Azure SQL Database lehetővé teszi, hogy könnyedén módosíthatja az adatbázisok számára lefoglalt erőforrások (CPU-teljesítmény, a memória, i/o-teljesítmény és tárolás).

Teljesítménnyel kapcsolatos problémák miatt az alkalmazás, amely nem lehet kijavítani a indexelő használatával, vagy lekérdezési újraírási metódust megnövekedett használatának csökkentheti. További erőforrások hozzáadásával lehetővé teszi, hogy gyorsan reagál, amikor az eléri az aktuális erőforrás-korlátozások az adatbázis és a bejövő terhelés kezeléséhez további power. Az Azure SQL Database lehetővé teszi skálázási az erőforrások költségeinek nincs szükség esetén.

Nem kell aggódnia a hardver megvásárlása és az alapul szolgáló infrastruktúra módosítására. Méretezési adatbázis Azure Portalon, a csúszka használatával egyszerűen elvégezhető.

![Adatbázis-teljesítmény méretezése](media/sql-database-scalability/scale-performance.svg)

Az Azure SQL Database kínál a [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) és a [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md).

- A [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) számítási, memória és IO-erőforrások kis és nagy terhelést jelentő adatbázisokhoz database három szolgáltatásszintet kínálja: Alapszintű, Standard és prémium. Az egyes szolgáltatásszintek teljesítményszintjei ezen erőforrások különféle keverékét kínálják, amelyhez további tárterület-erőforrások is hozzáadhatók.
- A [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md) válassza ki a virtuális magok, a vagy a memória, és a számát és a storage sebességétől teszi lehetővé. A vásárlási modell három különböző szolgáltatásszinttel rendelkezik: Általános célú, üzletileg kritikus és a nagy kapacitású (előzetes verzió).

Hozza létre első alkalmazását egy egyedülálló, kisméretű adatbázison alacsony áron a Basic, Standard vagy általános célú szolgáltatási szinten a havonta, és ezután szolgáltatásszintet manuálisan vagy programon keresztül bármikor módosíthatja a prémium szintű és az üzletileg kritikus szolgáltatásszinthez megfelelni a ne a megoldás eds. Úgy módosíthatja a teljesítményt, hogy az nem jár leállással az alkalmazás vagy az ügyfelek számára. A dinamikus méretezhetőség révén az adatbázis átlátható módon reagál a gyorsan változó erőforrásigényekre, és lehetővé teszi, hogy csak azokért az erőforrásokért fizessen, amelyekre és amikor szüksége van.

> [!IMPORTANT]
> Nagy kapacitású szolgáltatási szint jelenleg nyilvános előzetes verzióban érhető el, és elérhető a korlátozott Azure-régióban. Nagy kapacitású adatbázis egyéb szolgáltatási csomagokra nem frissíthető. Tesztelési célra ajánlott másolatot készít az aktuális adatbázisban, és frissíti a példányt nagy kapacitású szolgáltatásszinthez.
> [!NOTE]
> A dinamikus méretezhetőség különbözik az automatikus skálázástól. Automatikus skálázás esetén a szolgáltatás adott feltételek alapján, automatikusan méretez, míg a dinamikus méretezhetőség lehetővé teszi a manuális méretezést, amely nem jár állásidővel.

Az önálló Azure SQL Database a manuális dinamikus méretezhetőséget támogatja, de az automatikus skálázást nem. Ha *automatikus* megoldást keres, érdemes megfontolni a rugalmas készletek használatát, amely lehetővé teszi, hogy az adatbázisok osztozzanak egy készlet erőforrásain az egyes adatbázisok egyedi igényei alapján.
Vannak azonban olyan parancsfájlok, amelyek segítségével automatizálhatja a méretezhetőség egyetlen Azure SQL-adatbázishoz. Erre az [Egyetlen SQL-adatbázis monitorozása és skálázása a PowerShell használatával](scripts/sql-database-monitor-and-scale-database-powershell.md) című témakörben láthat példát.

Módosíthatja [DTU szolgáltatásszintek](sql-database-service-tiers-dtu.md) vagy [virtuális mag – jellemzőket](sql-database-vcore-resource-limits-single-databases.md) minimális állásideje (általában átlagosan kevesebb mint négy másodperc) az alkalmazás tetszőleges időpontban. Számos vállalkozás és alkalmazás számára elegendő az, ha adatbázisokat tudnak létrehozni, majd azok teljesítményét szükség szerint felfelé és lefelé tudják skálázni – különösen akkor, ha a használati minták viszonylag jól jelezhetők előre. Azonban előre nem látható használati minták esetén nehézségekbe ütközhet a költségek és az üzleti modell kezelése. Ebben a forgatókönyvben egy rugalmas készlet és a egy bizonyos számú edtu-t, amely a készlet több adatbázis között vannak megosztva használja.

![Bevezetés az SQL Database: Önálló adatbázis dtu-k rétegek és szintek szerint](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

Az Azure SQL Database összes három változatban érhetők el ajánlat dinamikusan méretezheti az adatbázisok néhány képessége:

- Az egy [önálló adatbázis](sql-database-single-database-scale.md), használhat [DTU](sql-database-dtu-resource-limits-single-databases.md) vagy [virtuális mag](sql-database-vcore-resource-limits-single-databases.md) modellek meghatározásához rendeli hozzá az egyes adatbázisok erőforrások maximális mennyiségét.
- A [felügyelt példány](sql-database-managed-instance.md) használ [virtuális magok](sql-database-managed-instance.md#vcore-based-purchasing-model) módban, és lehetővé teszi, hogy meghatározza a maximális CPU-magok és a példányhoz lefoglalt tárterület maximális. A példány belüli összes adatbázist fog ugyanazokat az a példányhoz lefoglalt erőforrásokat.
- [Rugalmas készletek](sql-database-elastic-pool-scale.md) engedélyezése, hogy a készlet maximális erőforrás felső határ az egyes adatbázisok csoport definiálja.

## <a name="alternative-scale-methods"></a>Alternatív méretezési módszer

Erőforrások skálázása, a legegyszerűbb és a leghatékonyabb megoldást választja, az adatbázis teljesítményének növelése adatbázis vagy az alkalmazás a programkód módosítása nélkül. Egyes esetekben akár a legmagasabb szintű szolgáltatási csomagokra, számítási méretű és teljesítményoptimalizálás előfordulhat, hogy nem kezeli a számítási feladatok, a sikeres és költséghatékony módja. Adott esetben ezek az adatbázis méretezése további lehetősége van:

- [Horizontális felskálázás olvasása](sql-database-read-scale-out.md) érhető el a szolgáltatás hol azért kapta az adatok egy csak olvasható replikája ahol hajthat végre erőforrás-igényes csak olvasható lekérdezései, például a jelentések. A csak olvasható számítási feladat működésének megzavarása nélkül megtesztelheti az erőforrás-használata az elsődleges adatbázis csak olvasható replika kezeli.
- [Database sharding](sql-database-elastic-scale-introduction.md) olyan technikákat, amely lehetővé teszi az adatok felosztása több adatbázist, és egymástól független méretezését.

## <a name="next-steps"></a>További lépések

- Adatbázis teljesítményének növelése adatbázis kód módosításával kapcsolatos információkért lásd: [keresse meg és teljesítménnyel kapcsolatos javaslatok alkalmazása](sql-database-advisor-portal.md).
- Így optimalizálhatja adatbázisát adatbázis beépített intelligencia kapcsolatos információkért lásd: [az automatikus hangolás](sql-database-automatic-tuning.md).
- Olvasási horizontális felskálázás az Azure SQL Database szolgáltatásban kapcsolatos információkért lásd: hogyan [terheléselosztása csak olvasható lekérdezési számítási feladatok betöltése a csak olvasható replika segítségével](sql-database-read-scale-out.md).
- Egy adatbázis horizontális skálázási kapcsolatos információkért lásd: [horizontális felskálázás az Azure SQL Database](sql-database-elastic-scale-introduction.md).
