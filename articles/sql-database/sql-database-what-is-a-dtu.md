---
title: 'SQL Database: Mi a DTU? | Microsoft Docs'
description: Megismerheti az Azure SQL Database Transaction Unit egységek fogalmát.
keywords: adatbázis-beállítások, adatbázis-teljesítmény
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 50b12de2400f0db90108e3eeb158357039ac92ec
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34649822"
---
# <a name="database-transaction-units-dtus-and-elastic-database-transaction-units-edtus"></a>Adatbázis-tranzakciós egységek (dtu-k) és a rugalmas adatbázis-tranzakciós egységek (edtu-k)
Ez a cikk tartalmazza a Database Transaction Unitok (adatbázisok tranzakciós egységei, DTU-k) és a rugalmas Database Transaction Unitok (eDTU-k) ismertetését, valamint leírja, mi történik, ha a DTU-k vagy eDTU-k száma eléri a maximális értéket. Díjszabási információkért lásd: [Azure SQL Database – díjszabás](https://azure.microsoft.com/pricing/details/sql-database/single/).

## <a name="what-are-database-transaction-units-dtus"></a>Mik az adatbázis-tranzakciós egységek (dtu-k)?
Egy adott szinten belül egyetlen Azure SQL-adatbázis egy [szolgáltatásréteg](sql-database-single-database-resources.md), Microsoft garantálja, hogy az adatbázis (vagy bármely más adatbázis Azure felhőben független), az erőforrások bizonyos szintű biztosít egy kiszámítható teljesítmény szintje. Erőforrások mennyisége akkor a program egy adatbázis-tranzakciós egységek vagy dtu-inak száma és számítási, tárolási és IO erőforrások csomagolt mértékét. Ezek az erőforrások között arány eredetileg alapján egy [teljesítményteszt OLTP-munkaterhelés](sql-database-benchmark-overview.md), tervezett jellemző az OLTP-munkaterhelések valós. A terhelés meghaladja az bármely ezeket az erőforrásokat, az átviteli sebesség esetén az szabályozottan halmozott – így a lassabb teljesítmény és időtúllépéseket okoz. A számítási feladatok által használt erőforrások nincs hatással a többi SQL adatbázis Azure felhőben számára elérhető erőforrások, és más munkaterhelések által használt erőforrások nem érintik az SQL-adatbázis számára elérhető erőforrások.

![határolókeret](./media/sql-database-what-is-a-dtu/bounding-box.png)

A relatív mennyiségű erőforrást más-más teljesítménybeli szinten Azure SQL-adatbázisok és a szolgáltatásszintek közötti ismertetése esetén a leghasznosabb-i nem. Például így dupla i adatbázis teljesítményszintjét növelésével megfelel így dupla, hogy az adatbázis számára elérhető erőforrások készletét. Például egy 1750 DTU-val rendelkező Prémium P11 adatbázis 350x több DTU számítási teljesítményt nyújt, mint egy 5 DTU-val rendelkező Alapszintű adatbázis.  

Használja a munkaterhelésnek (DTU) erőforrás-felhasználásának mélyebb betekintést [Azure SQL adatbázis-lekérdezési Terheléselemző](sql-database-query-performance.md) számára:

- A leggyakoribb lekérdezések azonosíthatja a CPU/időtartama/végrehajtási száma, amely potenciálisan a jobb teljesítmény kell beállítani. Például egy IO intenzív lekérdezés előnye származhat használatát [memórián belüli optimalizálási technikákat](sql-database-in-memory.md) a rendelkezésre álló memória egy bizonyos szolgáltatás szintjén és teljesítményszintet jobb kihasználása érdekében.
- Részletekbe menően tárhatják fel a lekérdezés részleteit, és tekintse meg a szöveg- és erőforrás-használat előzményeit.
- Hozzáférés teljesítményhangolás által végrehajtott műveletek megjelenítéséhez javaslatokat [SQL Database Advisor](sql-database-advisor.md).

Módosíthatja [DTU szolgáltatásszintek](sql-database-service-tiers-dtu.md) bármikor minimális állásidővel az alkalmazáshoz (általában átlagolási a négy másodperc). Számos vállalkozás és alkalmazás számára elegendő az, ha adatbázisokat tudnak létrehozni, majd azok teljesítményét szükség szerint felfelé és lefelé tudják skálázni – különösen akkor, ha a használati minták viszonylag jól jelezhetők előre. Azonban előre nem látható használati minták esetén nehézségekbe ütközhet a költségek és az üzleti modell kezelése. Ebben a forgatókönyvben egy rugalmas készlet edtu-k között a készlet több adatbázis oszt meg egy bizonyos mennyiségű használja.

![Bevezetés az SQL Database-be: önálló adatbázis DTU-k rétegek és szintek szerint](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

## <a name="what-are-elastic-database-transaction-units-edtus"></a>Mik azok a rugalmas adatbázis-tranzakciós egységek (edtu-k)?
Ahelyett, hogy adjon meg egy dedikált erőforrások (dtu-k), előfordulhat, hogy nem mindig szükséges, amely mindig elérhető SQL-adatbázis, mint elhelyezheti az adatbázisok egy [rugalmas készlet](sql-database-elastic-pool.md) osztja meg az erőforrásokat között SQL adatbázis-kiszolgálón Ezeket az adatbázisokat. A rugalmas készletekben található megosztott erőforrások rugalmas adatbázis-tranzakciós egységek vagy edtu-k mérik. Rugalmas készletek kezelése több adatbázisból, amelyek jelentősen eltérő és előre nem látható használati minták teljesítményértékeket egyszerű költséghatékony megoldást nyújt. Egy rugalmas készlet biztosítja, hogy az erőforrások nem tudja feldolgozni egy adatbázis a tárolókészletben, míg a készlet minden egyes adatbázis mindig biztosítása a minimálisan szükséges rendelkezésre álló erőforrások mennyiségét. 

![Bevezetés az SQL Database-be: eDTU-k rétegek és szintek szerint](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Egy készlet edtu-k meghatározott számú rögzített áron kap. A rugalmas készleten belül az önálló adatbázisok az automatikus méretezés rugalmasságával rendelkeznek a konfigurált határok között. Egy adatbázis nehezebb terhelés alatt szükség további edtu-k fog használni. Világosabb terhelések tartozó adatbázisok fogyaszt kevesebb edtu-k. Adatbázisok esetén nem fog használni, nincs edtu-k. Által használt dinamikus kiosztásnál a teljes készlet erőforrások helyett adatbázisonként, felügyeleti feladatok egyszerűsítettek, előre jelezhető költségvetést biztosítva a készletben.

A meglévő készletekhez további eDTU-k is hozzáadhatók anélkül, hogy a készlet adatbázisai leállnának, vagy bármilyen hatás érné őket. Ugyanígy ha az eDTU-kra már nincs szükség, bármikor el is távolíthatók a meglévő készletekből. Adhat hozzá vagy adatbázisokat a készlethez kivonása vagy korlát edtu / adatbázis mérete túl nagy terhelés alatt számára más adatbázisok edtu-k lefoglalni. Ha egy adatbázis van kiszámítható alatt-okhoz erőforrásokat, akkor is vegye ki a készletbe és a szükséges erőforrások előre meghatározott egyetlen adatbázis.

## <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>Hogyan állapíthatom meg a számítási feladataimhoz szükséges DTU-k számát?
Ha meglévő helyszíni vagy SQL-kiszolgálói virtuális gépeken futó számítási feladatokat kíván áthelyezni az Azure SQL Database-be, a [DTU-kalkulátor](http://dtucalculator.azurewebsites.net/) segítségével megbecsülheti a szükséges DTU-k számát. Egy meglévő Azure SQL Database-munkaterhelés használható [SQL adatbázis-lekérdezési Terheléselemző](sql-database-query-performance.md) az adatbázis erőforrás-felhasználás (dtu-k) áttekintésével mélyebb betekintést a számítási feladatok optimalizálási megértése. Használhatja a [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) DMV megtekintéséhez az elmúlt egy óra hálózatierőforrás-fogyasztás. Másik lehetőségként a katalógusnézet [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx) megjeleníti az elmúlt 14 napban, de egy alacsonyabb hűség ötperces átlagok hálózatierőforrás-fogyasztás.

## <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>Honnan tudom, hogy hasznát venném-e egy rugalmas erőforráskészletnek?
A készleteket nagy számú, speciális felhasználási mintákkal rendelkező adatbázishoz tervezték. Egy adott adatbázisnak az ebben a mintában jellemzőek, egy alacsony kihasználtságú átlagos viszonylag ritkán előforduló tüskéket rendelkező. Az SQL Database automatikusan kiértékeli az SQL Database-kiszolgálók adatbázisainak erőforrás-használati előzményeit, és felajánlja a megfelelő készletkonfigurációt az Azure Portalon. További információkért lásd: [Mikor érdemes rugalmas készletet használni?](sql-database-elastic-pool.md)

## <a name="what-happens-when-i-hit-my-maximum-dtus"></a>Mi történik, ha szeretnék elérte a maximális i?
Teljesítményszintet kalibrálni, és a legfeljebb megengedett a kiválasztott szolgáltatási szint/teljesítménye szint a adatbázis számítási feladatok futtatásához szükséges erőforrások az irányadó. Ha a terhelést van elérte-e a CPU/adat IO/Log IO korlátok egyikét, Ön továbbra is megkapják azt a erőforrások megengedett maximális szintet, de valószínűleg is fog tapasztalni megnövekedett lekérdezés késések fordulnak elő. Ezek a korlátok nem okoznak hibákat, csak a számítási feladat elvégzésének lassulását. Egyes esetekben azonban a lassulás olyan jelentős lehet, hogy a lekérések időtúllépés miatt meghiúsulnak. Ha eléri a maximális engedélyezett egyidejű felhasználói munkamenetek/kérelmek (munkavégző szál), látni fogja a explicit hibák. Lásd: [erőforrás-korlátozások az Azure SQL Database]( sql-database-dtu-resource-limits.md#what-happens-when-database-and-elastic-pool-resource-limits-are-reached) nem kapcsolódik a CPU, a memória, a adat IO vagy a tranzakciós napló IO erőforrás-korlátozások információt.

## <a name="next-steps"></a>További lépések
* Lásd: [alapjául szolgáló vásárlási modell DTU-alapú](sql-database-service-tiers-dtu.md) Dtu és edtu-k érhető el, az önálló adatbázisok és a rugalmas információkat, valamint eltérő CPU erőforrások használati korlátait, memória, az adatok IO és a tranzakció jelentkezzen IO.
* Lásd: [vCore-alapú alapjául szolgáló vásárlási modell (előzetes verzió)](sql-database-service-tiers-vcore.md) vCore-alapú erőforrás lemezfoglalási és -szolgáltatás rétegek olvashat. 
* A DTU-k felhasználásáról az [SQL Database lekérdezési terheléselemzőjének](sql-database-query-performance.md) leírásában talál további információt.
* A DTU összetételének megállapításához használt OLTP számítási mintafeladat módszertanával kapcsolatos további információkat [az SQL Database mérési módszereinek áttekintése](sql-database-benchmark-overview.md) tartalmazza.
