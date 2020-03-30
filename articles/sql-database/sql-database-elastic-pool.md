---
title: Több adatbázis kezelése rugalmas készletekkel
description: Rugalmas készletek használatával több SQL-adatbázist – százakat és ezreket – kezelhet és méretezhet. Egy ár az erőforrások, ahol lehet osztani, ahol szükséges.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: ninarn, carlrab
ms.date: 08/06/2019
ms.openlocfilehash: 3c476393153f6bc1d18d5c163bcd69484583eb15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256275"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-azure-sql-databases"></a>Több Azure SQL-adatbázis kezelése és méretezése rugalmas készletek használatával

Az SQL Database rugalmas készletei egyszerű, költséghatékony megoldást kínálnak egyszerre több olyan adatbázis kezelésére és méretezésére, amelyek felhasználási igénye nagy mértékben és kiszámíthatatlanul változik. A rugalmas készlethez tartozó adatbázisok egyetlen Azure SQL Database-kiszolgálón találhatók, és meghatározott számú erőforrással rendelkeznek előre megállapított áron. Az Azure SQL Database rugalmas készleteivel az SaaS-fejlesztők az előre meghatározott költségvetésen belül maradva optimalizálhatják az adatbáziscsoportok ár-teljesítmény arányát, és rugalmas teljesítményt biztosíthatnak az egyes adatbázisokhoz.

## <a name="what-are-sql-elastic-pools"></a>Mik azok az SQL rugalmas készletek

A SaaS-fejlesztők több adatbázisból álló nagyméretű adatrétegekre építenek alkalmazásokat. Gyakori alkalmazásminta az önálló adatbázis biztosítása minden egyes ügyfél számára. De a különböző ügyfelek gyakran különböző és előre nem látható használati minták, és nehéz megjósolni az egyes adatbázis-felhasználók erőforrás-követelményeinek. Hagyományosan két lehetőséged volt:

- Túl-ellátás források alapján csúcshasználat és a túlfizetés, vagy
- Alulképzettség a költségek megtakarítása érdekében, a teljesítmény és az ügyfelek elégedettségének rovására a csúcsidőszakokban.

A rugalmas készletek úgy oldják meg ezt a problémát, hogy biztosítják, hogy az adatbázisok megkapják a szükséges teljesítményerőforrásokat, amikor szükségük van rá. Egy egyszerű erőforrás-lefoglalási mechanizmust biztosítanak, kiszámítható költségekkel. A rugalmas készleteket használó SaaS-alkalmazások szerkezeti kialakításainak alaposabb megismeréséhez olvassa el a [Tervminták több-bérlős SaaS-alkalmazásokhoz Azure SQL Database esetén](sql-database-design-patterns-multi-tenancy-saas-applications.md) című részt.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

> [!IMPORTANT]
> A rugalmas készletek adatbázisonkénti díja nem áll fenn. A rendszer minden óráért számláz, ahol a készlet a legmagasabb eDTU-n vagy virtuális magon található, függetlenül a használattól, illetve attól, hogy a készlet egy óránál rövidebb ideig volt aktív.

Rugalmas készletek lehetővé teszik a fejlesztő számára, hogy erőforrásokat vásároljon egy készlet több adatbázis által megosztott, hogy az egyes adatbázisok előre nem látható használati időszakainak megfelelően. A készlet erőforrásait a [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) vagy a [virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md)alapján konfigurálhatja. A készlet erőforrás-szükségletét az adatbázisok összesített kihasználtsága határozza meg. A készlet számára rendelkezésre álló erőforrások mennyiségét a fejlesztői költségvetés szabályozza. A fejlesztő egyszerűen adatbázisokat ad a készlethez, beállítja az adatbázisok minimális és maximális erőforrásait (a minimális és maximális DIT-eket, vagy a minimális vagy maximális virtuális magokat a választott erőforrás-típustól függően), majd a készlet erőforrásait a készletük alapján állítja be Költségvetés. A készletek segítségével a fejlesztő zökkenőmentesen és fokozatosan növelheti szolgáltatásának teljesítményét a korlátozott erőforrásokkal bíró startupok szintjéről az érett vállalkozások szintjére.

A készleten belül az önálló adatbázisok az automatikus méretezés rugalmasságával rendelkeznek. Nagy terhelés alatt egy adatbázis több erőforrást használhat az igények kielégítésére. A könnyű terhelésalatt található adatbázisok kevesebbet fogyasztanak, és a terhelés nélküli adatbázisok nem használnak erőforrásokat. Az erőforrásoknak az egyes adatbázisok helyett a teljes készlet számára hozzáférhetővé tétele jelentősen leegyszerűsíti a felügyeleti feladatokat. Plusz, van egy kiszámítható költségvetést a medence. További erőforrások adhatók hozzá egy meglévő készlethez adatbázis-leállás nélkül, azzal a különbséggel, hogy az adatbázisokat át kell helyezni az új eDTU-foglalás további számítási erőforrásainak biztosításához. Hasonlóképpen, ha már nincs szükség további erőforrásokra, akkor bármikor eltávolíthatók egy meglévő készletből. Ezenfelül a készlethez adatbázisok adhatók hozzá vagy vonhatók ki belőle. Ha egy adatbázis kiszámítható módon nem használja ki az erőforrásokat, helyezze át az adatbázist.

> [!NOTE]
> Adatbázisok rugalmas készletbe vagy onnan való áthelyezésekor nincs állásidő, kivéve egy rövid ideig (másodpercek sorrendjében) a művelet végén, amikor az adatbázis-kapcsolatok megszakadnak.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>Mikor érdemes megfontolni egy SQL Database rugalmas készlet

A készleteket kifejezetten a nagy számú, speciális felhasználási mintákkal rendelkező adatbázisokhoz tervezték. Az egyes adatbázisok mintáit átlagosan alacsony, és viszonylag rendszertelen időközönkénti hirtelen megugró kihasználtság jellemzi.

Minél több adatbázist tud hozzáadni egy készlethez, annál többet takaríthat meg. Az alkalmazás kihasználtsági mintájától függően akár két S3-adatbázissal is megtekinthetőak a megtakarítások.

A következő szakaszokból megtudhatja, hogyan mérje fel, hogy előnyös-e, ha egy adott adatbázis-gyűjtemény egy készlethez tartozik. A példákban Standard készletek szerepelnek, de ugyanezen elvek alkalmazhatók az Alapszintű és a Premium készletekre is.

### <a name="assessing-database-utilization-patterns"></a>Az adatbázis felhasználási mintáinak felmérése

Az alábbi ábra egy olyan adatbázist mutat be, amely sok időt tölt tétlenül, miközben időszakosan hirtelen megugró kihasználtság jellemzi. Az ilyen kihasználtsági minta esetén használhatók készletek:

   ![egy készletbe illő önálló adatbázis](./media/sql-database-elastic-pool/one-database.png)

Az ábrázolt ötperces periódus során a DB1 adatbázis maximális DTU-használata 90, de az összesített átlagos használati érték öt DTU alatt van. Egy S3 számítási méret szükséges a számítási feladatok futtatásához egyetlen adatbázisban, de ez az erőforrások nagy részét nem használja az alacsony aktivitású időszakokban.

Egy készletben ezek a használaton kívüli DTU-k több adatbázis között lehetnek megosztva, így a szükséges DTU-k száma és az összköltség is csökken.

Az előző példa mentén továbbhaladva tegyük fel, hogy további adatbázisokkal rendelkezünk, amelyeket DB1-hez hasonló felhasználási minták jellemeznek. Az alábbi két ábrán négy adatbázis és 20 adatbázis kihasználtsága ugyanarra a grafikonra van rétegezve, hogy bemutassa a DTU-alapú vásárlási modell használatával történő használatuk nem átfedő jellegét:

   ![négy adatbázis egy készletbe illő kihasználtsági mintával](./media/sql-database-elastic-pool/four-databases.png)

   ![húsz adatbázis egy készletbe illő kihasználtsági mintával](./media/sql-database-elastic-pool/twenty-databases.png)

A DTU-k mind a 20 adatbázisra vonatkozó összesített kihasználtságát a fekete vonal jelzi az előző ábrán. Ez alapján a teljes DTU-használat soha nem lépi túl a 100 DTU értéket, és az időtartam során a 20 adatbázis 100 eDTU-t használhat közösen. Ez a DIT-ek 20-szeres csökkenését és 13-szeres árcsökkenést eredményez az egyes adatbázisok S3 számítási méretekben való elhelyezéséhez képest.

Ez a példa az alábbi okokból ideális:

- Nagy különbségek vannak az adatbázisok átlagos és kiugró mértékű kihasználtsága között.
- Az egyes adatbázisok kiugró mértékű kihasználtsága különböző időpontokban jelentkezik.
- Az eDTU-k több adatbázis között vannak megosztva.

A készletre vonatkozó költség az eDTU-készlet függvénye. A készlethez tartozó eDTU-k egységára egy önálló adatbázis DTU-egységárának másfélszerese, azonban **a készlethez tartozó eDTU-kat sok adatbázis használhatja, így kevesebb eDTU-ra van szükség**. Ezek a díjszabásban és eDTU-megosztásban jelentkező különbségek adják a készletekkel elérhető megtakarítás alapját.

Az adatbázisszámmal és az adatbázis-kihasználtsítással kapcsolatos alábbi ökölszabályok biztosítják, hogy a készlet alacsonyabb költségeket biztosítson az egyes adatbázisok számítási méreteinek használatával összehasonlítva.

### <a name="minimum-number-of-databases"></a>Adatbázisok minimális száma

Ha az egyes adatbázisok összes erőforrás-mennyisége több mint 1,5-szerese a készlethez szükséges erőforrásoknak, akkor egy rugalmas készlet költséghatékonyabb.

***Példa A DTU-alapú vásárlási modellre***<br>
Legalább két S3-adatbázisra vagy legalább 15 S0-adatbázisra van szükség ahhoz, hogy egy 100 eDTU-készlet költséghatékonyabb legyen, mint az egyes adatbázisok számítási méreteinek használata.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Egyidejűleg kiugró kihasználtságú adatbázisok maximális száma

Az erőforrások megosztásával a készletben lévő adatbázisok közül nem minden használhatja egyszerre az erőforrásokat az egyes adatbázisok számára rendelkezésre álló határig. Minél kevesebb adatbázis érhető el egyidejűleg, annál alacsonyabb a készlet erőforrásai, és annál költséghatékonyabb lesz a készlet. Általában legfeljebb 2/3 (vagy 67%) a készletben lévő adatbázisok nak egyidejűleg az erőforrás-korlátjukig kell tartaniuk.

***Példa A DTU-alapú vásárlási modellre***

Ha csökkenteni szeretnénk három S3-adatbázis költségét egy 200 eDTU-s készletben, akkor a háromból egyszerre legfeljebb kettő működhet kiugró kihasználtsággal. Ha ebből a három S3-adatbázisból több mint kettő működik egyszerre kiugró kihasználtsággal, akkor a készletnek több mint 200 eDTU-t kellene tartalmaznia. Ha a készlet et több mint 200 eDTE-re méretezi át, több S3-adatbázist kell hozzáadni a készlethez, hogy a költségek alacsonyabbak maradjanak, mint az egyes adatbázisok számítási mérete.

Vegye figyelembe, hogy ez a példa nem veszi figyelembe a készletben lévő más adatbázisok kihasználtságát. Ha egy adott időpontban minden adatbázis használatban van valamilyen szinten, akkor az adatbázisok kevesebb mint kétharmad része (vagy 67%-a) működhet egyszerre kiugró kihasználtsággal.

### <a name="resource-utilization-per-database"></a>Erőforrás-kihasználtság adatbázisonként

Az adatbázisok kiugró és átlagos kihasználtsága közötti lényeges különbség a hosszú, alacsony kihasználtságú és a rövid magas kihasználtságú időszakokban mutatkozik meg. Ilyen felhasználási minta esetén ideális az erőforrások adatbázisok közötti megosztása. Az adatbázis készletben való használatát akkor érdemes megfontolni, ha a kiugró mértékű kihasználtsága hozzávetőlegesen másfélszer nagyobb az átlagos kihasználtságánál.

**DTU-alapú vásárlási modell példa:** Egy S3-adatbázis, amely 100 DTU-ra ér és átlagosan 67 DTU-t használ, jó jelölt az eDTU-k megosztására egy készletben. Ha pedig egy 20 DTU-s kiugró kihasználtsággal működő S1-adatbázis átlagosan legfeljebb 13 DTU-t használ, akkor jó jelöltnek számít egy készlethez.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Hogyan választhatom ki a megfelelő medenceméretet?

A készlet legjobb mérete a készlet összes adatbázisához szükséges összesített erőforrásoktól függ. Ez magában foglalja a következők meghatározását:

- A készletösszes adatbázisa által használt maximális erőforrások (maximális DIT-ek vagy maximális virtuális magok a választott erőforrás-kezelési modelltől függően).
- A készletben szereplő összes adatbázis által használt maximális tárterület (bájtban).

Az egyes erőforrásmodellekhez elérhető szolgáltatási szinteket a [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) ben vagy a [virtuálismag-alapú vásárlási modellben](sql-database-service-tiers-vcore.md)lehet megtekinteni.

Ha nincs lehetősége eszközök használatára, az alábbi részletes útmutatóval megbecsülheti, hogy a készlet költséghatékonyabb-e az önálló adatbázisok használatánál:

1. Becsülje meg a készlethez szükséges eDVO-kat vagy virtuális magokat az alábbiak szerint:

   DTU-alapú beszerzési modell esetén: MAX(<*A DbX* *átlagos DTU-kihasználtság* ának száma db->,<br>  
   <*A kiugró kihasználtsággal egyszerre működő adatbázisok száma* X *Az egyes adatbázisok kiugró DTU-használata*)

   Virtuálismag-alapú vásárlási modell esetén: MAX(<*A DBs* X *átlagos virtuálismag-kihasználtságának száma DB->,*<br>  
   <*Egyidejűleg tetőző DB-k száma* X *csúcs virtuálismag-kihasználtság db-onként*)

2. A készlethez szükséges tárterület méretének becsléséhez adja össze a készlet egyes adatbázisaihoz szükséges bájtok számát. Ezután határozza meg a szükséges tárhelyet biztosító eDTU-készlet méretét.
3. A DTU-alapú vásárlási modell, hogy a nagyobb eDTU becslések lépés 1 és 2 lépés. A virtuálismag-alapú vásárlási modell, vegye ki a virtuális mag becslést az 1.
4. Tekintse meg az [SQL Database díjszabási lapját,](https://azure.microsoft.com/pricing/details/sql-database/) és keresse meg a 3.
5. Hasonlítsa össze az 5.

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Más SQL Database-szolgáltatások használata rugalmas készletekkel

### <a name="elastic-jobs-and-elastic-pools"></a>Rugalmas feladatok és rugalmas medencék

A készletek használata leegyszerűsíti a felügyeleti feladatokat, mivel a szkriptek **[rugalmas feladatokban](elastic-jobs-overview.md)** futtathatók. A rugalmas feladatok használatával kiküszöbölhető a nagy számú adatbázis kezelésével járó monotonitás.

További információk a több adatbázissal dolgozó további adatbázis-eszközökről: [Horizontális felskálázás az Azure SQL Database-ben](sql-database-elastic-scale-introduction.md).

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>A rugalmas készletben lévő adatbázisok üzletmenet-folytonossági lehetőségei

A rugalmas készletbe helyezett adatbázisok általánosságban ugyanazokat [az üzletmenet-folytonossági funkciókat](sql-database-business-continuity.md) támogatják, amelyek az önálló adatbázisokhoz is elérhetők.

- **Adott időpontnak megfelelő helyreállítás**

  Az időponthoz való időpont visszaállítása automatikus adatbázis-biztonsági mentést használ a készletadatbázis helyreállításához egy adott időpontig. Lásd: [Időponthoz kötött visszaállítás](sql-database-recovery-using-backups.md#point-in-time-restore)

- **Geo-visszaállítás**

  Geo-visszaállítás biztosítja az alapértelmezett helyreállítási lehetőséget, ha egy adatbázis nem érhető el, mert egy incidens a régióban, ahol az adatbázis található. Lásd: [Azure SQL-adatbázis visszaállítása vagy feladatátvétel másodlagosra](sql-database-disaster-recovery.md)

- **Aktív georeplikáció**

  A geo-visszaállításnál agresszívebb helyreállítási követelményekkel rendelkező alkalmazások esetében konfigurálja [az aktív georeplikációt](sql-database-active-geo-replication.md) vagy [az automatikus feladatátvételi csoportot.](sql-database-auto-failover-group.md)

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Új SQL-adatbázis rugalmas készlet létrehozása az Azure Portal használatával

Két módon hozhat létre egy rugalmas készletet az Azure Portalon.

1. Lépjen az [Azure Portalra](https://portal.azure.com) egy rugalmas készlet létrehozásához. Keresse meg és válassza az **Azure SQL**lehetőséget.
2. Válassza a **+Hozzáadás** lehetőséget az **SQL-telepítés kiválasztása lehetőséglap** megnyitásához. A rugalmas készletekkel kapcsolatos további információkat az **Adatbázisok** csempén a **Részletek megjelenítése** lehetőséget választva tekintheti meg.
3. Az **Adatbázisok** csempén válassza a **Rugalmas készlet lehetőséget** az **Erőforrástípus** legördülő menüben, majd a **Létrehozás**lehetőséget:

   ![Rugalmas készlet létrehozása](./media/sql-database-elastic-pool/create-elastic-pool.png)


1. Vagy létrehozhat egy rugalmas készletet egy meglévő Azure SQL-kiszolgálóra való navigálással, és a **+ Új készlet** elemre kattintva közvetlenül az adott kiszolgálóra.

> [!NOTE]
> A kiszolgálón több készletet is létrehozhat, de különböző kiszolgálókadatbázisait nem veheti fel ugyanabba a készletbe.

A készlet szolgáltatási szintje határozza meg a készletben lévő rugalmas funkciók at, és az egyes adatbázisok számára elérhető erőforrások maximális mennyiségét. További információt a [DTU modell](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes)rugalmas készleteinek erőforráskorlátai című témakörben talál. A rugalmas készletek virtuálismag-alapú erőforráskorlátokról a [virtuálismag-alapú erőforráskorlátok – rugalmas készletek című témakörben található.](sql-database-vcore-resource-limits-elastic-pools.md)

A készlet erőforrásainak és díjszabásának konfigurálásához kattintson a **Készlet konfigurálása**gombra. Ezután válasszon ki egy szolgáltatási réteget, adjon adatbázisokat a készlethez, és konfigurálja a készlet és az adatbázisok erőforráskorlátait.

Miután befejezte a készlet konfigurálását, kattintson az "Alkalmaz" gombra, nevezze el a készletet, majd kattintson az "OK" gombra a készlet létrehozásához.

## <a name="monitor-an-elastic-pool-and-its-databases"></a>Rugalmas készlet és adatbázisainak figyelése

Az Azure Portalon figyelheti egy rugalmas készlet és az adatbázisok a készleten belül kihasználtságát. A rugalmas készlet módosításait is módosíthatja, és az összes módosítást egyidejűleg elküldheti. Ezek közé tartozik az adatbázisok hozzáadása vagy eltávolítása, a rugalmas készlet beállításainak módosítása vagy az adatbázis-beállítások módosítása.

A rugalmas készlet figyelésének megkezdéséhez keressen meg és nyisson meg egy rugalmas készletet a portálon. Először megjelenik egy képernyő, amely áttekintést nyújt a rugalmas készlet állapotáról. Az érintett műveletek közé tartoznak az alábbiak:

- Figyelési diagramok a rugalmas készlet erőforrás-használatát megjelenítő diagramok
- Legutóbbi riasztások és javaslatok, ha rendelkezésre állnak, a rugalmas készlet

A következő ábra egy példa rugalmas készlet:

![Készlet nézet](./media/sql-database-elastic-pool-manage-portal/basic.png)

Ha további információra van szüksége a készletről, kattintson az áttekintésben elérhető információk bármelyikére. Az **Erőforrás-kihasználtsági** diagramra kattintva az Azure Monitoring nézetben testreszabhatja a diagramon látható mutatókat és időablakot. Az elérhető értesítésekre kattintva egy panelre lép, amely megjeleníti az adott riasztás vagy ajánlás teljes részleteit.

Ha a készleten belüli adatbázisokat szeretné figyelni, a bal oldali erőforrásmenü **Figyelés** szakaszában az **Adatbázis-erőforrás-kihasználtság** elemre kattinthat.

![Adatbázis-erőforrás kihasználtsága lap](./media/sql-database-elastic-pool-manage-portal/db-utilization.png)

### <a name="to-customize-the-chart-display"></a>A diagram megjelenítésének testreszabása

A diagram és a metrikaoldal szerkesztésével más metrikákat is megjeleníthet, például a CPU százalékos arányát, az adatok I/százalékát és a naplói i/o-százalékos értéket.

A **Diagram szerkesztése** képernyőn kiválaszthat egy rögzített időtartományt, vagy az **egyéni** gombra kattintva kijelölheti az elmúlt két hét 24 órás ablakait, majd kiválaszthatja a figyelni kívánt erőforrásokat.

### <a name="to-select-databases-to-monitor"></a>A figyelni kívánt adatbázisok kiválasztása

Alapértelmezés szerint az **adatbázis-erőforrás-kihasználtság** panelen lévő diagram a DTU vagy a CPU szerint (a szolgáltatási szinttől függően) az első 5 adatbázist jeleníti meg. A diagram adatbázisait úgy kapcsolhatja be, hogy a bal oldali jelölőnégyzetek segítségével kijelöli az adatbázisokat a diagram alatti listából, és törli a jelet a kijelölésből.

További metrikákat is kiválaszthat, amelyeket egymás mellett tekinthet meg ebben az adatbázistáblában, hogy teljesebb képet kapjon az adatbázisok teljesítményéről.

További információt az [SQL Database-riasztások létrehozása az Azure Portalon című témakörben talál.](sql-database-insights-alerts-portal.md)

## <a name="customer-case-studies"></a>Ügyféleset-tanulmányok

- [SnelStart](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-snelstart/)

  A SnelStart rugalmas készleteket használt az Azure SQL Database segítségével, hogy gyorsan bővítse üzleti szolgáltatásait havi 1000 új Azure SQL-adatbázissal.

- [Umbraco](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-umbraco/)

  Az Umbraco rugalmas készleteket használ az Azure SQL Database használatával, hogy gyorsan kicsapata és méretezése érdekében a felhőben lévő bérlők ezrei számára nyújtjon ki és méretezhet szolgáltatásokat.

- [Daxko/CSI](https://customers.microsoft.com/story/726277-csi-daxko-partner-professional-service-azure)    

   A Daxko/CSI rugalmas készleteket használ az Azure SQL Database használatával a fejlesztési ciklus felgyorsítása, valamint az ügyfélszolgálat és a teljesítmény javítása érdekében.   

## <a name="next-steps"></a>További lépések

- Az árképzési információkért [lásd: Rugalmas készlet árképzése.](https://azure.microsoft.com/pricing/details/sql-database/elastic)
- Rugalmas készletek méretezése, lásd: [Rugalmas készletek méretezése](sql-database-elastic-pool-scale.md) és [egy rugalmas készlet méretezése - mintakód](scripts/sql-database-monitor-and-scale-pool-powershell.md)
- Videó megtekintéséhez tekintse meg a [Microsoft Virtual Academy videótanfolyamot az Azure SQL Database rugalmas képességeiről](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
- A rugalmas készleteket használó SaaS-alkalmazások szerkezeti kialakításainak alaposabb megismeréséhez olvassa el a [Tervminták több-bérlős SaaS-alkalmazásokhoz Azure SQL Database esetén](sql-database-design-patterns-multi-tenancy-saas-applications.md) című részt.
- A rugalmas készleteket használó SaaS-oktatóanyagról [a Bevezetés a Wingtip SaaS alkalmazás bemutatása című témakörben](sql-database-wtp-overview.md)található.
- Ha többet szeretne tudni a sok adatbázissal rendelkező rugalmas készletekerőforrás-kezeléséről, olvassa el [az Erőforrás-kezelés sűrű rugalmas készletekben (Erőforráskezelés sűrű rugalmas készletekben) témakört.](sql-database-elastic-pool-resource-management.md)
