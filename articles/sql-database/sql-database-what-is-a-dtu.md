---
title: 'SQL Database: Mi a DTU? | Microsoft Docs'
description: Megismerheti az Azure SQL Database Transaction Unit egységek fogalmát.
keywords: adatbázis-beállítások, adatbázis-teljesítmény
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 22337e412661172475a05f6fec31ae03683be988
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="database-transaction-units-dtus-and-elastic-database-transaction-units-edtus"></a>Adatbázis-tranzakciós egységek (dtu-k) és a rugalmas adatbázis-tranzakciós egységek (edtu-k)
Ez a cikk tartalmazza a Database Transaction Unitok (adatbázisok tranzakciós egységei, DTU-k) és a rugalmas Database Transaction Unitok (eDTU-k) ismertetését, valamint leírja, mi történik, ha a DTU-k vagy eDTU-k száma eléri a maximális értéket. Díjszabási információkért lásd: [Azure SQL Database – díjszabás](https://azure.microsoft.com/pricing/details/sql-database/single/).

## <a name="what-are-database-transaction-units-dtus"></a>Mik az adatbázis-tranzakciós egységek (dtu-k)?
Egy adott szinten belül egyetlen Azure SQL-adatbázis egy [szolgáltatásréteg](sql-database-single-database-resources.md), a Microsoft biztosítja, hogy az erőforrásokhoz, hogy az adatbázis (független bármely más adatbázis Azure felhőben), és így a kiszámítható teljesítmény szintű bizonyos szintű. Ez a mennyiség-erőforrások adatbázis-tranzakciós egységek vagy dtu-inak számaként és, így egy csomagolt mérték számítási, tárolási és IO erőforrások. Ezek az erőforrások között arány eredetileg határozza egy [teljesítményteszt OLTP-munkaterhelés](sql-database-benchmark-overview.md) jellemző OLTP-munkaterhelések valós kialakítva. A terhelés meghaladja az bármely ezeket az erőforrásokat, az átviteli sebesség esetén az szabályozottan halmozott – így a lassabb teljesítmény és időtúllépéseket okoz. A számítási feladatok által használt erőforrások nincs hatással a többi SQL adatbázis Azure felhőben számára elérhető erőforrások, és más munkaterhelésekhez használt erőforrások nem érintik az SQL-adatbázis számára elérhető erőforrások.

![határolókeret](./media/sql-database-what-is-a-dtu/bounding-box.png)

A relatív mennyiségű erőforrást más-más teljesítménybeli szinten Azure SQL-adatbázisok és a szolgáltatásszintek közötti ismertetése esetén a leghasznosabb-i nem. Például így dupla i adatbázis teljesítményszintjét növelésével megfelel így dupla a készlet számára, hogy az adatbázis elérhető erőforrás. Például egy 1750 DTU-val rendelkező Prémium P11 adatbázis 350x több DTU számítási teljesítményt nyújt, mint egy 5 DTU-val rendelkező Alapszintű adatbázis.  

Használja a munkaterhelésnek (DTU) erőforrás-felhasználásának mélyebb betekintést [Azure SQL adatbázis-lekérdezési Terheléselemző](sql-database-query-performance.md) számára:

- A leggyakoribb lekérdezések azonosíthatja a CPU/időtartama/végrehajtási száma, amely potenciálisan a jobb teljesítmény kell beállítani. Például egy IO intenzív lekérdezés előnye származhat használatát [memórián belüli optimalizálási technikákat](sql-database-in-memory.md) a rendelkezésre álló memória egy bizonyos szolgáltatás szintjén és teljesítményszintet jobb kihasználása érdekében.
- Részletekbe menően tárhatják fel a lekérdezés részleteit, és tekintse meg a szöveg- és erőforrás-használat előzményeit.
- Hozzáférés teljesítményhangolás által végrehajtott műveletek megjelenítéséhez javaslatokat [SQL Database Advisor](sql-database-advisor.md).

Az alkalmazás minimális állásideje (általában átlagosan kevesebb mint négy másodperc) mellett bármikor [módosíthatja a szolgáltatásszinteket](sql-database-service-tiers.md). Számos vállalkozás és alkalmazás számára elegendő az, ha adatbázisokat tudnak létrehozni, majd azok teljesítményét szükség szerint felfelé és lefelé tudják skálázni – különösen akkor, ha a használati minták viszonylag jól jelezhetők előre. Azonban előre nem látható használati minták esetén nehézségekbe ütközhet a költségek és az üzleti modell kezelése. Ebben a forgatókönyvben egy rugalmas készlet edtu-k között a készlet több adatbázis oszt meg egy bizonyos mennyiségű használja.

![Bevezetés az SQL Database-be: önálló adatbázis DTU-k rétegek és szintek szerint](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

## <a name="what-are-elastic-database-transaction-units-edtus"></a>Mik azok a rugalmas adatbázis-tranzakciós egységek (edtu-k)?
Ahelyett, hogy adjon meg egy dedikált erőforrások (dtu-k), amely mindig elérhető, függetlenül attól, hogy nem szükséges SQL-adatbázishoz, mint elhelyezheti az adatbázisok egy [rugalmas készlet](sql-database-elastic-pool.md) osztja meg az adott adatbázis között erőforrásokat SQL adatbázis-kiszolgálón. A megosztott erőforrások rugalmas adatbázis-tranzakciós egységek vagy edtu-k által mért rugalmas készlethez. A rugalmas készletek egyszerű, költséghatékony megoldást kínálnak az olyan adatbázisok teljesítménybeli céljainak kezelésére, amelyek felhasználási módja nagy mértékben és kiszámíthatatlanul változik. A rugalmas készletekben található garantálható, hogy egy adatbázis összes erőforrást használ a készletben, továbbá, hogy minimális mennyiségű erőforrást nyelv mindig elérhető a rugalmas készletekben található adatbázishoz. 

![Bevezetés az SQL Database-be: eDTU-k rétegek és szintek szerint](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Egy készlethez egy adott számú eDTU tartozik, amelyek egy adott áron vehetők igénybe. A rugalmas készleten belül az önálló adatbázisok az automatikus méretezés rugalmasságával rendelkeznek a konfigurált határok között. Nagy terhelés alatt az adatbázisok több eDTU-t használhatnak fel, hogy megfeleljenek az igényeknek. A kisebb terhelésű adatbázisok kevesebbet használnak, a terhelés alatt nem álló adatbázisok pedig egyáltalán nem használnak fel eDTU-kat. Az erőforrásoknak az egyes adatbázisok helyett a teljes készlet számára való elérhetővé tétele leegyszerűsíti a felügyeleti feladatokat, és a készlet költségei is kiszámíthatóvá válnak.

A meglévő készletekhez további eDTU-k is hozzáadhatók anélkül, hogy a készlet adatbázisai leállnának, vagy bármilyen hatás érné őket. Ugyanígy ha az eDTU-kra már nincs szükség, bármikor el is távolíthatók a meglévő készletekből. Adatbázisokat adhat a készlethez vagy vonhat ki belőlük, vagy korlátozhatja az egy adatbázis által nagy terhelés esetén használható eDTU-k mennyiségét, így megőrizheti az eDTU-kat más adatbázisok számára. Ha egy adatbázis kiszámítható módon nem használja fel az erőforrásokat, kiveheti a készletből, és konfigurálhatja önálló adatbázisként előrejelezhető erőforrás-mennyiséggel.

## <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>Hogyan állapíthatom meg a számítási feladataimhoz szükséges DTU-k számát?
Ha meglévő helyszíni vagy SQL-kiszolgálói virtuális gépeken futó számítási feladatokat kíván áthelyezni az Azure SQL Database-be, a [DTU-kalkulátor](http://dtucalculator.azurewebsites.net/) segítségével megbecsülheti a szükséges DTU-k számát. A létező Azure SQL Database-számításifeladatok esetében az [SQL Database Lekérdezési terheléselemző](sql-database-query-performance.md) segítségével mérheti fel az adatbázis erőforrás-használatát (DTU-használatát), ezzel mélyebb betekintést nyerve a számítási feladatok optimalizálásának lehetőségeibe. Használhatja a [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) DMV-t is, amely az elmúlt egy órára vonatkozóan mutat erőforrás-felhasználási adatokat. Emellett a [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx) katalógusnézetében is lekérdezheti ugyanezen adatokat az elmúlt 14 napra vonatkozóan, ezek azonban kevésbé pontosak, átlagosan öt perces átlagolással érhetők el.

## <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>Honnan tudom, hogy hasznát venném-e egy rugalmas erőforráskészletnek?
A készleteket nagy számú, speciális felhasználási mintákkal rendelkező adatbázishoz tervezték. Az egyes adatbázisok mintáit átlagosan alacsony, és viszonylag rendszertelen időközönkénti hirtelen megugró kihasználtság jellemzi. Az SQL Database automatikusan kiértékeli az SQL Database-kiszolgálók adatbázisainak erőforrás-használati előzményeit, és felajánlja a megfelelő készletkonfigurációt az Azure Portalon. További információkért lásd: [Mikor érdemes rugalmas készletet használni?](sql-database-elastic-pool.md)

## <a name="what-happens-when-i-hit-my-maximum-dtus"></a>Mi történik, ha szeretnék elérte a maximális i?
A teljesítményszintek arra vannak beállítva és utasítva, hogy biztosítsák az adatbázis futtatásához szükséges erőforrásokat a szolgáltatás-/teljesítményszint számára maximálisan megengedett korlátokon belül. Ha a számítási feladat eléri a processzor/adatátvitel/naplóátvitel számára beállított korlátok egyikét, az erőforrások továbbra is a maximálisan megengedett szinten állnak rendelkezésre, de a lekérések késleltetése valószínűleg megnövekszik. Ezek a korlátok nem okoznak hibákat, csak a számítási feladat elvégzésének lassulását. Egyes esetekben azonban a lassulás olyan jelentős lehet, hogy a lekérések időtúllépés miatt meghiúsulnak. Ha az egyidejű felhasználói munkamenetek/lekérések (munkaszálak) száma eléri a maximálisan engedélyezett korlátot, explicit hibaüzenetek jelennek meg. Lásd: [erőforrás-korlátozások az Azure SQL Database]( sql-database-dtu-resource-limits.md#what-happens-when-database-and-elastic-pool-resource-limits-are-reached) eltérő CPU erőforrások korlátozva információért memória, az adatok IO és a tranzakció jelentkezzen IO.

## <a name="next-steps"></a>További lépések
* Lásd: [szolgáltatásréteg](sql-database-service-tiers.md) Dtu és edtu-k érhető el, az önálló adatbázisok és a rugalmas információkat, valamint eltérő CPU erőforrások használati korlátait, memória, az adatok IO és a tranzakció jelentkezzen IO.
* A DTU-k felhasználásáról az [SQL Database lekérdezési terheléselemzőjének](sql-database-query-performance.md) leírásában talál további információt.
* A DTU összetételének megállapításához használt OLTP számítási mintafeladat módszertanával kapcsolatos további információkat [az SQL Database mérési módszereinek áttekintése](sql-database-benchmark-overview.md) tartalmazza.
