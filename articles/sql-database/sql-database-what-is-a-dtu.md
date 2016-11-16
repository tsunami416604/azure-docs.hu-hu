---
title: 'SQL Database: Mi a DTU? | Microsoft Docs'
description: "Megismerheti az Azure SQL Database Transaction Unit egységek fogalmát."
keywords: "adatbázis-beállítások, adatbázis-teljesítmény"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: CarlRabeler
ms.assetid: 89e3e9ce-2eeb-4949-b40f-6fc3bf520538
ms.service: sql-database
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: NA
ms.date: 09/06/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 91942517a481a252d631af15e532389256b64108


---
# <a name="explaining-database-transaction-units-dtus-and-elastic-database-transaction-units-edtus"></a>A Database Transaction Unitok (adatbázisok tranzakciós egységei, DTU-k) és a rugalmas Database Transaction Unitok (eDTU-k) ismertetése
Ez a cikk tartalmazza a Database Transaction Unitok (adatbázisok tranzakciós egységei, DTU-k) és a rugalmas Database Transaction Unitok (eDTU-k) ismertetését, valamint leírja, mi történik, ha a DTU-k vagy eDTU-k száma eléri a maximális értéket.  

## <a name="what-are-database-transaction-units-dtus"></a>Mi az a Database Transaction Unit (DTU)?
A DTU azon erőforrások mértékegysége, amelyek egy [önálló adatbázisokra vonatkozó szolgáltatási szint](sql-database-service-tiers.md#standalone-database-service-tiers-and-performance-levels) adott teljesítményszintjén garantáltan rendelkezésre állnak egy önálló Azure SQL-adatbázis számára. A DTU egyszerre méri a processzorteljesítményt, a memóriát, valamint az adatok és a tranzakciós naplók átvitelét, amelyek arányát egy, a valós OLTP számítási feladatok tipikus jellemzőivel bíró számítási próbafeladat határozza meg. A DTU megduplázása egy adatbázis teljesítményszintjének növelésével úgy lehetséges, ha megduplázzuk az adatbázis számára elérhető erőforrások készletét. Például egy 1750 DTU-val rendelkező Prémium P11 adatbázis 350x több DTU számítási teljesítményt nyújt, mint egy 5 DTU-val rendelkező Alapszintű adatbázis. A DTU összetételének megállapításához használt próba OLTP-számításifeladat módszertanával kapcsolatos további információkat az [SQL Database mérési módszereinek áttekintésében](sql-database-benchmark-overview.md) találja.

![Bevezetés az SQL Database-be: önálló adatbázis DTU-k rétegek és szintek szerint](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

Az alkalmazás minimális állásideje (általában átlagosan kevesebb mint négy másodperc) mellett bármikor [módosíthatja a szolgáltatásszinteket](sql-database-scale-up.md). Számos vállalkozás és alkalmazás számára elegendő az, ha adatbázisokat tudnak létrehozni, majd az önálló adatbázisok teljesítményét szükség szerint felfelé és lefelé tudják skálázni – különösen akkor, ha a használati minták viszonylag jól jelezhetők előre. Azonban előre nem látható használati minták esetén nehézségekbe ütközhet a költségek és az üzleti modell kezelése. Ebben a forgatókönyvben egy bizonyos számú eDTU-val rendelkező rugalmas készletet használunk.

## <a name="what-are-elastic-database-transaction-units-edtus"></a>Mi az a rugalmas Database Transaction Unit (eDTU)?
Az eDTU azon erőforráskészletek (DTU-k) mértékegysége, amelyek megoszthatók egy Azure SQL Server adatbázisai között – ezeket az erőforrásokat összefoglalva [rugalmas készletnek](sql-database-elastic-pool.png) nevezzük. A rugalmas készletek egyszerű, költséghatékony megoldást kínálnak az olyan adatbázisok teljesítménybeli céljainak kezelésére, amelyek felhasználási módja nagy mértékben és kiszámíthatatlanul változik. További információt a [rugalmas készletek és szolgáltatásszintek](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus) leírásában talál.

![Bevezetés az SQL Database-be: eDTU-k rétegek és szintek szerint](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Egy készlethez egy adott számú eDTU tartozik, amelyek egy adott áron vehetők igénybe. A készleten belül az önálló adatbázisok az automatikus méretezés rugalmasságával rendelkeznek. Nagy terhelés alatt az adatbázisok több eDTU-t használhatnak fel, hogy megfeleljenek az igényeknek. A kisebb terhelésű adatbázisok kevesebbet, a terhelés alatt nem álló adatbázisok pedig egyáltalán nem használnak fel eDTU-kat. Az erőforrásoknak az egyes adatbázisok helyett a teljes készlet számára hozzáférhetővé tétele jelentősen leegyszerűsíti a felügyeleti feladatokat. Emellett a készlet költségei is kiszámíthatóak lesznek.

A létező készletekhez további eDTU-k is hozzáadhatók anélkül, hogy a rugalmas készlet adatbázisai leállnának, vagy bármilyen hatás érné őket. Ugyanígy ha az eDTU-kra már nincs szükség, bármikor el is távolíthatók a létező készletből. A készlethez adatbázisok hozzáadhatók vagy kivonhatók. Ha egy adatbázis kiszámítható módon nem használja ki az erőforrásokat, helyezze át az adatbázist.

## <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>Hogyan állapíthatom meg a számítási feladataimhoz szükséges DTU-k számát?
Ha meglévő helyszíni vagy SQL-kiszolgálói virtuális gépeken futó számítási feladatokat kíván áthelyezni az Azure SQL Database-be, a [DTU-kalkulátor](http://dtucalculator.azurewebsites.net/) segítségével megbecsülheti a szükséges DTU-k számát. A létező Azure SQL Database-számításifeladatok esetében az [SQL Database Lekérdezési terheléselemző](sql-database-query-performance.md) segítségével mérheti fel az adatbázis erőforrás-használatát (DTU-használatát), ezzel mélyebb betekintést nyerve a számítási feladatok optimalizálásának lehetőségeibe. Használhatja a [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) DMV-t is, amely az elmúlt egy órára vonatkozóan mutat erőforrás-felhasználási adatokat. Emellett a [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx) katalógusnézetében is lekérdezheti ugyanezen adatokat az elmúlt 14 napra vonatkozóan, ezek azonban kevésbé pontosak, átlagosan öt perces átlagolással érhetők el.

## <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>Honnan tudom, hogy hasznát venném-e egy rugalmas erőforráskészletnek?
A készleteket nagy számú, speciális felhasználási mintákkal rendelkező adatbázishoz tervezték. Az egyes adatbázisok mintáit átlagosan alacsony, és viszonylag rendszertelen időközönkénti hirtelen megugró kihasználtság jellemzi. Az SQL Database automatikusan kiértékeli az SQL Database-kiszolgálók adatbázisainak erőforrás-használati előzményeit, és felajánlja a megfelelő készletkonfigurációt az Azure Portalon. További információ: [When should an elastic database pool be used?](sql-database-elastic-pool-guidance.md) (Mikor használjak rugalmas adatbáziskészletet?)

## <a name="what-happens-when-i-hit-my-maximum-dtus"></a>Mi történik, ha elérem a DTU-k maximális számát?
A teljesítményszintek arra vannak beállítva és utasítva, hogy biztosítsák az adatbázis futtatásához szükséges erőforrásokat a szolgáltatás-/teljesítményszint számára maximálisan megengedett korlátokon belül. Ha a számítási feladat eléri a processzor/adatátvitel/naplóátvitel számára beállított korlátok egyikét, az erőforrások továbbra is a maximálisan megengedett szinten állnak rendelkezésre, de a lekérések késleltetése valószínűleg megnövekszik. Ezek a korlátok nem okoznak hibákat, csak a számítási feladat elvégzésének lassulását. Egyes esetekben azonban a lassulás olyan jelentős lehet, hogy a lekérések időtúllépés miatt meghiúsulnak. Ha az egyidejű felhasználói munkamenetek/lekérések (munkaszálak) száma eléri a maximálisan engedélyezett korlátot, explicit hibaüzenetek jelennek meg. További információk a processzoron, a memórián, illetve az adat- és naplóátvitelen kívüli egyéb erőforrásokra vonatkozó korlátokkal kapcsolatban az [Azure SQL Database resource limits](sql-database-resource-limits.md) (Az Azure SQL Database erőforráskorlátai) cikk tartalmaz további információt.

## <a name="next-steps"></a>Következő lépések
* Az önálló adatbázisokhoz és rugalmas készletekhez elérhető DTU-kkal és eDTU-kkal kapcsolatban a [szolgáltatásszint](sql-database-service-tiers.md) leírása tartalmaz további információt.
* További információk a processzoron, a memórián, illetve az adat- és naplóátvitelen kívüli egyéb erőforrásokra vonatkozó korlátokkal kapcsolatban az [Azure SQL Database resource limits](sql-database-resource-limits.md) (Az Azure SQL Database erőforráskorlátai) cikk tartalmaz további információt.
* A DTU-k felhasználásáról az [SQL Database lekérdezési terheléselemzőjének](sql-database-query-performance.md) leírásában talál további információt.
* A DTU összetételének megállapításához használt OLTP számítási mintafeladat módszertanával kapcsolatos további információkat [az SQL Database mérési módszereinek áttekintése](sql-database-benchmark-overview.md) tartalmazza.




<!--HONumber=Nov16_HO2-->


