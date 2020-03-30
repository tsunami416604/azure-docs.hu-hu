---
title: Erőforrások méretezése
description: Ez a cikk bemutatja, hogyan méretezheti az adatbázist a lefoglalt erőforrások hozzáadásával vagy eltávolításával.
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
ms.openlocfilehash: c4366b2718271b1e27325e6946c5016e9230cea4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76835912"
---
# <a name="dynamically-scale-database-resources-with-minimal-downtime"></a>Az adatbázis-erőforrások dinamikus méretezése minimális állásidővel

Az Azure SQL Database lehetővé teszi, hogy dinamikusan több erőforrást adjon az adatbázishoz minimális [állásidővel;](https://azure.microsoft.com/support/legal/sla/sql-database/v1_2/) azonban van egy kapcsoló időszakon keresztül, ahol a kapcsolat megszakad az adatbázishoz egy rövid ideig, amely az újrapróbálkozási logika használatával mérséklődhet.

## <a name="overview"></a>Áttekintés

Ha az alkalmazás iránti kereslet néhány eszközről és ügyfélről millióra nő, az Azure SQL Database menet közben, minimális állásidővel méretezhető. A Méretezhetőség a PaaS egyik legfontosabb jellemzője, amely lehetővé teszi, hogy szükség esetén dinamikusan adjon hozzá több erőforrást a szolgáltatáshoz. Az Azure SQL Database lehetővé teszi az adatbázisokhoz rendelt erőforrások (CPU-teljesítmény, memória, I/O-átviteli és tárolási) egyszerű módosítását.

Csökkentheti a teljesítményproblémákat az alkalmazás megnövekedett használata miatt, amely nem javítható indexelési vagy lekérdezés-újraírási módszerekkel. További erőforrások hozzáadása lehetővé teszi, hogy gyorsan reagáljon, ha az adatbázis eléri az aktuális erőforrás-korlátokat, és több energiát igényel a bejövő számítási feladatok kezeléséhez. Az Azure SQL Database azt is lehetővé teszi, hogy az erőforrások csökkentése, ha azok nem szükségesek a költségek csökkentéséhez.

Nem kell aggódnia a hardverek vásárlása és az alapul szolgáló infrastruktúra módosítása miatt. Az adatbázis méretezése könnyen elvégezhető az Azure Portalon keresztül egy csúszka használatával.

![Adatbázis-teljesítmény méretezése](media/sql-database-scalability/scale-performance.svg)

Az Azure SQL Database a [DTU-alapú vásárlási modellt](sql-database-service-tiers-dtu.md) és a [virtuálismag-alapú vásárlási modellt](sql-database-service-tiers-vcore.md)kínálja.

- A [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) számítási, memória- és Io-erőforrások keverékét kínálja három szolgáltatási szinten a könnyű és a nehéz méretű adatbázis-munkaterhelések támogatásához: alapszintű, standard és prémium szintű. Az egyes szolgáltatásszintek teljesítményszintjei ezen erőforrások különféle keverékét kínálják, amelyhez további tárterület-erőforrások is hozzáadhatók.
- A [virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md) segítségével kiválaszthatja a virtuális magok számát, az összeget vagy a memóriát, valamint a tárhely mennyiségét és sebességét. Ez a vásárlási modell három szolgáltatási szintet kínál: általános célú, üzleti legkritikusabb és nagy méretű.

Az első alkalmazást egy kis méretű, egyetlen adatbázisra hozhatja létre alacsony havi költséggel az Alapszintű, a Standard vagy az Általános célú szolgáltatási szinten, majd bármikor manuálisan vagy programozott módon módosíthatja a szolgáltatási szintet a prémium vagy az üzleti legkritikusabb szolgáltatási szintre, hogy megfeleljen a megoldásának szükségleteit. Úgy módosíthatja a teljesítményt, hogy az nem jár leállással az alkalmazás vagy az ügyfelek számára. A dinamikus méretezhetőség révén az adatbázis átlátható módon reagál a gyorsan változó erőforrásigényekre, és lehetővé teszi, hogy csak azokért az erőforrásokért fizessen, amelyekre és amikor szüksége van.

> [!NOTE]
> A dinamikus méretezhetőség különbözik az automatikus skálázástól. Az automatikus skálázás az, amikor egy szolgáltatás feltételek alapján automatikusan skálázódik, míg a dinamikus skálázhatóság lehetővé teszi a manuális skálázást minimális állásidővel.

Az önálló Azure SQL Database a manuális dinamikus méretezhetőséget támogatja, de az automatikus skálázást nem. Ha *automatikus* megoldást keres, érdemes megfontolni a rugalmas készletek használatát, amely lehetővé teszi, hogy az adatbázisok osztozzanak egy készlet erőforrásain az egyes adatbázisok egyedi igényei alapján.
Vannak azonban olyan parancsfájlok, amelyek segítségével automatizálhatja a méretezhetőséget egyetlen Azure SQL-adatbázis hoz. Erre az [Egyetlen SQL-adatbázis monitorozása és skálázása a PowerShell használatával](scripts/sql-database-monitor-and-scale-database-powershell.md) című témakörben láthat példát.

A [DTU-szolgáltatásszinteket](sql-database-service-tiers-dtu.md) vagy a [virtuális magok jellemzőit](sql-database-vcore-resource-limits-single-databases.md) bármikor módosíthatja, az alkalmazás minimális állásideje (általában átlagosan kevesebb mint négy másodperc) mellett. Számos vállalkozás és alkalmazás számára elegendő az, ha adatbázisokat tudnak létrehozni, majd azok teljesítményét szükség szerint felfelé és lefelé tudják skálázni – különösen akkor, ha a használati minták viszonylag jól jelezhetők előre. Azonban előre nem látható használati minták esetén nehézségekbe ütközhet a költségek és az üzleti modell kezelése. Ebben a forgatókönyvben egy rugalmas készlet et használ egy bizonyos számú eDT-k, amelyek a készlet több adatbázis között vannak megosztva.

![Bevezetés az SQL Database-be: önálló adatbázis DTU-k rétegek és szintek szerint](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

Az Azure SQL Database mindhárom változata lehetővé teszi az adatbázisok dinamikus méretezését:

- Egyetlen [adatbázis](sql-database-single-database-scale.md)használatával [DTU](sql-database-dtu-resource-limits-single-databases.md) vagy [virtuálismag-modellek](sql-database-vcore-resource-limits-single-databases.md) segítségével határozhatja meg az egyes adatbázisokhoz hozzárendelt erőforrások maximális mennyiségét.
- A [felügyelt példány](sql-database-managed-instance.md) [virtuálismag-módot](sql-database-managed-instance.md#vcore-based-purchasing-model) használ, és lehetővé teszi a maximális CPU-magok és a példány számára lefoglalt tárterület meghatározását. A példányon belüli összes adatbázis megosztja a példányhoz rendelt erőforrásokat.
- Rugalmas készletek lehetővé [teszik,](sql-database-elastic-pool-scale.md) hogy a készletben lévő adatbázisok csoportjánkénti maximális erőforráskorlátot határozzon meg.

A méretezési vagy horizontális felskálázási művelet bármely ízben történő megkezdése újraindítaná az adatbázis-motor folyamatát, és szükség esetén áthelyezné egy másik virtuális gépre. Az adatbázismotor-folyamat áthelyezése egy új virtuális gépre olyan **online folyamat,** amelyen a folyamat közben is használhatja a meglévő Azure SQL Database-szolgáltatást. Miután a céladatbázis-motor teljesen inicializálódott, és készen áll a lekérdezések feldolgozására, a kapcsolatok a forrásról a [céladatbázis-motorra váltanak.](sql-database-single-database-scale.md#impact) 


> [!NOTE]
> A skálázási/leskálázási folyamat befejezésekor rövid kapcsolattörésre számíthat. Ha a [szabványos átmeneti hibák újrapróbálkozási logikáját valósította](sql-database-connectivity-issues.md#retry-logic-for-transient-errors)meg, nem fogja észrevenni a feladatátvételt.

## <a name="alternative-scale-methods"></a>Alternatív méretezési módszerek

Az erőforrások méretezése a legegyszerűbb és leghatékonyabb módja az adatbázis teljesítményének növelésének az adatbázis vagy az alkalmazáskód módosítása nélkül. Bizonyos esetekben még a legmagasabb szolgáltatási szintek, számítási méretek és teljesítményoptimalizálások sem biztos, hogy sikeresen és költséghatékonyan kezelik a számítási feladatokat. Ebben az esetben az alábbi további lehetőségek kelthetők az adatbázis méretezésére:

- [Az olvasási horizontális felskálázás](sql-database-read-scale-out.md) egy olyan szolgáltatás, ahol az adatok egy csak olvasható replikáját kapja, ahol igény szerinti írásvédett lekérdezéseket, például jelentéseket hajthat végre. Az írásvédett replika az elsődleges adatbázis erőforrás-felhasználásának befolyásolása nélkül kezeli az írásvédett számítási feladatokat.
- [Az adatbázis horizontális skálázása](sql-database-elastic-scale-introduction.md) olyan technikák összese, amelyek lehetővé teszik az adatok több adatbázisra való felosztását és egymástól független méretezését.

## <a name="next-steps"></a>További lépések

- Az adatbázis teljesítményének az adatbáziskódjának módosításával kapcsolatos tudnivalókról a [Teljesítményjavaslatok keresése és alkalmazása](sql-database-advisor-portal.md)című témakörben talál.
- A beépített adatbázis-intelligencia adatbázis-optimalizálásának lehetővé tévő engedélyezése az [Automatikus hangolás című témakörben](sql-database-automatic-tuning.md)található.
- Az Azure SQL Database szolgáltatás horizontális felskálázása olvasása című témakörből megtudhatja, hogyan [használhatja az írásvédett replikákat az írásvédett lekérdezési számítási feladatok terheléséhez.](sql-database-read-scale-out.md)
- Az adatbázis-horizontális skálázásról a [Horizontális felskálázás az Azure SQL Database használatával](sql-database-elastic-scale-introduction.md)című témakörben talál.
