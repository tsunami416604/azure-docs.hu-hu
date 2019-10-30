---
title: Erőforrások Azure SQL Database méretezése | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan méretezheti az adatbázist a lefoglalt erőforrások hozzáadásával vagy eltávolításával.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnik, carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 98d24b4f497f09e982101917296b572a5c381f42
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053595"
---
# <a name="dynamically-scale-database-resources-with-minimal-downtime"></a>Adatbázis-erőforrások dinamikus méretezése minimális állásidővel

Azure SQL Database lehetővé teszi, hogy a minimális [állásidővel](https://azure.microsoft.com/support/legal/sla/sql-database/v1_2/)dinamikusan vegyen fel több erőforrást az adatbázisba. van azonban egy olyan időszak, amelyben a kapcsolat megszakadt az adatbázisba rövid idő alatt, ami az újrapróbálkozási logika használatával enyhíthető.

## <a name="overview"></a>Áttekintés

Ha az alkalmazás iránti kereslet néhány eszközről és ügyfélről millióra nő, Azure SQL Database menet közben, minimális állásidővel. A méretezhetőség a Pásti egyik legfontosabb jellemzője, amely lehetővé teszi, hogy szükség esetén dinamikusan vegyen fel további erőforrásokat a szolgáltatásba. A Azure SQL Database lehetővé teszi az adatbázisok számára lefoglalt erőforrások (CPU-teljesítmény, memória, IO-átviteli sebesség és tárterület) egyszerű módosítását.

A teljesítményproblémák enyhítése az alkalmazás nagyobb kihasználtsága miatt, amely nem oldható fel az indexelés vagy a lekérdezés Újraírási módszereivel. A további erőforrások hozzáadásával gyorsan reagálhat, ha az adatbázis eléri az aktuális erőforrás-korlátokat, és nagyobb teljesítményre van szüksége a bejövő munkaterhelés kezeléséhez. A Azure SQL Database az erőforrások méretezését is lehetővé teszi, ha a költségeket nem szükséges csökkenteni.

Nem kell aggódnia a hardver megvásárlása és a mögöttes infrastruktúra megváltozása miatt. A skálázási adatbázis egyszerűen elvégezhető Azure Portal egy csúszka használatával.

![Az adatbázis teljesítményének méretezése](media/sql-database-scalability/scale-performance.svg)

Azure SQL Database a [DTU-alapú vásárlási modellt](sql-database-service-tiers-dtu.md) és a [virtuális mag-alapú vásárlási modellt](sql-database-service-tiers-vcore.md)kínálja.

- A [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) a számítási, a memória-és az i/o-erőforrások keverékét kínálja három szolgáltatási szinten a könnyű és a nehéz adatbázis-számítási feladatok támogatásához: alapszintű, standard és prémium. Az egyes szolgáltatásszintek teljesítményszintjei ezen erőforrások különféle keverékét kínálják, amelyhez további tárterület-erőforrások is hozzáadhatók.
- A [virtuális mag-alapú vásárlási modell](sql-database-service-tiers-vcore.md) segítségével kiválaszthatja a virtuális mag számát, a mennyiségét vagy a memóriát, valamint a tárterület mennyiségét és sebességét. Ez a vásárlási modell három szolgáltatási szintet kínál: általános célú, üzletileg kritikus és nagy kapacitású.

Az alapszintű, standard vagy általános célú szolgáltatási szinten egy kis méretű, önálló adatbázison hozhatja létre az első alkalmazását, majd a szolgáltatási szintet manuálisan vagy programozott módon módosíthatja a prémium vagy a üzletileg kritikus szolgáltatási szinten, hogy megfeleljen a ne a megoldás EDS-je. Úgy módosíthatja a teljesítményt, hogy az nem jár leállással az alkalmazás vagy az ügyfelek számára. A dinamikus méretezhetőség révén az adatbázis átlátható módon reagál a gyorsan változó erőforrásigényekre, és lehetővé teszi, hogy csak azokért az erőforrásokért fizessen, amelyekre és amikor szüksége van.

> [!NOTE]
> A dinamikus méretezhetőség különbözik az automatikus skálázástól. Az automatikus skálázás akkor történik meg, amikor a szolgáltatás automatikusan a feltételek alapján méretezi a szolgáltatásokat, míg a dinamikus méretezhetőség lehetővé teszi a manuális skálázást minimális állásidővel.

Az önálló Azure SQL Database a manuális dinamikus méretezhetőséget támogatja, de az automatikus skálázást nem. Ha *automatikus* megoldást keres, érdemes megfontolni a rugalmas készletek használatát, amely lehetővé teszi, hogy az adatbázisok osztozzanak egy készlet erőforrásain az egyes adatbázisok egyedi igényei alapján.
Vannak azonban olyan parancsfájlok, amelyek segítségével automatizálható egyetlen Azure SQL Database skálázhatósága. Erre az [Egyetlen SQL-adatbázis monitorozása és skálázása a PowerShell használatával](scripts/sql-database-monitor-and-scale-database-powershell.md) című témakörben láthat példát.

A [DTU-szolgáltatásszinteket](sql-database-service-tiers-dtu.md) vagy a [virtuális magok jellemzőit](sql-database-vcore-resource-limits-single-databases.md) bármikor módosíthatja, az alkalmazás minimális állásideje (általában átlagosan kevesebb mint négy másodperc) mellett. Számos vállalkozás és alkalmazás számára elegendő az, ha adatbázisokat tudnak létrehozni, majd azok teljesítményét szükség szerint felfelé és lefelé tudják skálázni – különösen akkor, ha a használati minták viszonylag jól jelezhetők előre. Azonban előre nem látható használati minták esetén nehézségekbe ütközhet a költségek és az üzleti modell kezelése. Ebben a forgatókönyvben egy rugalmas készletet használ bizonyos számú Edtu, amelyek a készlet több adatbázisa között vannak megosztva.

![Bevezetés az SQL Database-be: önálló adatbázis DTU-k rétegek és szintek szerint](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

A Azure SQL Database mindhárom teljes választéka az adatbázisok dinamikus méretezésének lehetőségét kínálja:

- [Egyetlen adatbázis](sql-database-single-database-scale.md)esetén [DTU](sql-database-dtu-resource-limits-single-databases.md) -vagy [virtuális mag](sql-database-vcore-resource-limits-single-databases.md) -modelleket is használhat az egyes adatbázisokhoz hozzárendelhető erőforrások maximális mennyiségének meghatározásához.
- A [felügyelt példányok](sql-database-managed-instance.md) [virtuális mag](sql-database-managed-instance.md#vcore-based-purchasing-model) módot használnak, és lehetővé teszi a maximális CPU-magok és a példányhoz lefoglalt maximális tárterület meghatározását. A példányon belüli összes adatbázis osztozik a példányhoz lefoglalt erőforrásokon.
- A [rugalmas készletek](sql-database-elastic-pool-scale.md) lehetővé teszik a készletben lévő adatbázisok csoportjára vonatkozó maximális erőforrás-korlátozás meghatározását.

A vertikális fel-vagy leskálázási műveletek kezdeményezése bármely aroma esetében újraindítja az adatbázismotor folyamatát, és szükség esetén áthelyezi egy másik virtuális gépre. Ha az adatbázismotor folyamatát egy új virtuális gépre helyezi át, az **online folyamat** , ahol továbbra is használhatja a meglévő Azure SQL Database szolgáltatást, miközben a folyamat folyamatban van. Ha a célként megadott adatbázis-motor teljesen inicializálva van, és készen áll a lekérdezések feldolgozására, a rendszer a [forrásról a célként megadott adatbázis-motorra vált](sql-database-single-database-scale.md#impact-of-changing-service-tier-or-rescaling-compute-size).

> [!NOTE]
> A vertikális Felskálázási/skálázási folyamat befejeződése után rövid kapcsolódási szünet várható. Ha implementálta az [újrapróbálkozási logikát a normál átmeneti hibákhoz](sql-database-connectivity-issues.md#retry-logic-for-transient-errors), akkor nem fogja észrevenni a feladatátvételt.

## <a name="alternative-scale-methods"></a>Alternatív méretezési módszerek

Az erőforrások méretezése a legegyszerűbb és a leghatékonyabb módszer az adatbázis teljesítményének javítására az adatbázis vagy az alkalmazás kódjának módosítása nélkül. Bizonyos esetekben a legmagasabb szintű szolgáltatási szintek, a számítási méretek és a teljesítmény-optimalizálás nem kezelheti a munkaterhelést sikeres és költséghatékony módon. Ebben az esetben ezekkel a további beállításokkal méretezheti az adatbázist:

- Az [olvasási felskálázás](sql-database-read-scale-out.md) egy olyan szolgáltatás, ahol egy olyan írásvédett replikát kap az adataihoz, ahol a csak olvasható lekérdezéseket, például jelentéseket futtathat. A csak olvasási jogosultsággal rendelkező replikák az elsődleges adatbázis erőforrás-használatának befolyásolása nélkül kezelik az írásvédett munkaterhelést.
- Az [adatbázis](sql-database-elastic-scale-introduction.md) -skálázás olyan technikák összessége, amelyek lehetővé teszik az adatmegosztást több adatbázisra, és egymástól függetlenül méretezheti azokat.

## <a name="next-steps"></a>Következő lépések

- További információ az adatbázis-teljesítmény javításáról az adatbázis kódjának módosításával: [teljesítményre vonatkozó javaslatok keresése és alkalmazása](sql-database-advisor-portal.md).
- További információ a beépített adatbázis-intelligenciával való optimalizálásáról: [automatikus hangolás](sql-database-automatic-tuning.md).
- További információ a Azure SQL Database szolgáltatásban végzett olvasási felskálázásról: az írásvédett [replikák használata az írásvédett lekérdezési feladatok terheléselosztásához](sql-database-read-scale-out.md).
- További információ az adatbázisok horizontális [Felskálázásáról: horizontális felskálázás Azure SQL Database](sql-database-elastic-scale-introduction.md).
