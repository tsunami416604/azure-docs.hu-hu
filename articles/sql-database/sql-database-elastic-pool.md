---
title: Több SQL-adatbázis kezelése rugalmas készletekkel – Azure | Microsoft Docs
description: Több száz és ezer SQL-adatbázist kezelhet és méretezheti rugalmas készletek használatával. Egy díj az erőforrások számára, amelyekhez szükség esetén terjeszthető.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: ninarn, carlrab
manager: craigg
ms.date: 02/28/2019
ms.openlocfilehash: 775e1abe1f3d6412171b8ff5427fd905e37480b5
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489693"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-azure-sql-databases"></a>A rugalmas készletek segítségével több Azure SQL Database-adatbázist kezelhet és méretezheti

Az SQL Database rugalmas készletei egyszerű, költséghatékony megoldást kínálnak egyszerre több olyan adatbázis kezelésére és méretezésére, amelyek felhasználási igénye nagy mértékben és kiszámíthatatlanul változik. A rugalmas készletben lévő adatbázisok egyetlen Azure SQL Database-kiszolgálón találhatók, és meghatározott áron oszthatják meg az erőforrásokat. Az Azure SQL Database rugalmas készleteivel az SaaS-fejlesztők az előre meghatározott költségvetésen belül maradva optimalizálhatják az adatbáziscsoportok ár-teljesítmény arányát, és rugalmas teljesítményt biztosíthatnak az egyes adatbázisokhoz.

## <a name="what-are-sql-elastic-pools"></a>Mik a rugalmas SQL-készletek

A SaaS-fejlesztők több adatbázisból álló nagyméretű adatrétegekre építenek alkalmazásokat. Gyakori alkalmazásminta az önálló adatbázis biztosítása minden egyes ügyfél számára. A különböző ügyfelek felhasználási mintája nagymértékben és kiszámíthatatlan módon változik, ezért nehéz megjósolni az adatbázis minden egyes felhasználójának erőforrásigényét. Hagyományosan két lehetőség közül választhat:

- Többek között kiépíthető erőforrások a csúcsérték-használat és a fizetések alapján, vagy
- A kiépítés a költségek megtakarítása érdekében, a teljesítmény és az ügyfelek elégedettségének rovására a csúcsok során.

A rugalmas készletek a probléma megoldásához biztosítják, hogy az adatbázisok megkapják a szükséges teljesítménybeli erőforrásokat. Egy egyszerű erőforrás-lefoglalási mechanizmust biztosítanak, kiszámítható költségekkel. A rugalmas készleteket használó SaaS-alkalmazások szerkezeti kialakításainak alaposabb megismeréséhez olvassa el a [Tervminták több-bérlős SaaS-alkalmazásokhoz Azure SQL Database esetén](sql-database-design-patterns-multi-tenancy-saas-applications.md) című részt.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

> [!IMPORTANT]
> Rugalmas készletekhez nem tartozik adatbázis-díj. Minden órában, a legmagasabb eDTU vagy virtuális mag, a használattól függetlenül, illetve azt, hogy a készlet egy óránál kevesebb ideig volt-e aktív.

A rugalmas készletek lehetővé teszik, hogy a fejlesztő erőforrásokat vásároljon egy több adatbázis által megosztott készlet számára, hogy az egyes adatbázisok előre nem látható használati időszakait is kielégítse. A készlethez a [DTU-alapú vásárlási modellen](sql-database-service-tiers-dtu.md) vagy a [virtuális mag-alapú vásárlási modellen](sql-database-service-tiers-vcore.md)alapuló erőforrásokat is konfigurálhat. A készletre vonatkozó erőforrás-követelményt az adatbázisainak összesített kihasználtsága határozza meg. A készlet számára elérhető erőforrások mennyiségét a fejlesztői költségvetés vezérli. A fejlesztő egyszerűen hozzáadja az adatbázisokat a készlethez, beállítja az adatbázisok minimális és maximális erőforrásait (a minimális és a maximális DTU, illetve a minimális vagy maximális virtuális mag, attól függően, hogy milyen újrabeszerzési modellt választ), majd beállítja a készlet erőforrásait a következők alapján: költségvetés. A készletek segítségével a fejlesztő zökkenőmentesen és fokozatosan növelheti szolgáltatásának teljesítményét a korlátozott erőforrásokkal bíró startupok szintjéről az érett vállalkozások szintjére.

A készleten belül az önálló adatbázisok az automatikus méretezés rugalmasságával rendelkeznek. A nagy terhelés alatt az adatbázisok több erőforrást is felhasználhatnak az igények kielégítése érdekében. A világos terhelésű adatbázisok kevesebbet használnak, és a terhelés nélküli adatbázisok nem használnak fel erőforrásokat. Az erőforrásoknak az egyes adatbázisok helyett a teljes készlet számára hozzáférhetővé tétele jelentősen leegyszerűsíti a felügyeleti feladatokat. Emellett kiszámítható költségvetést is biztosít a készlethez. Az adatbázis leállása nélküli meglévő készletekhez további erőforrások is hozzáadhatók, azzal a különbséggel, hogy az adatbázisokat át kell helyezni az új eDTU-foglalás további számítási erőforrásainak biztosításához. Hasonlóképpen, ha már nincs szükség további erőforrásokra, a meglévő készletből bármikor eltávolítható. Ezenfelül a készlethez adatbázisok adhatók hozzá vagy vonhatók ki belőle. Ha egy adatbázis kiszámítható módon nem használja ki az erőforrásokat, helyezze át az adatbázist.

> [!NOTE]
> Az adatbázisok rugalmas készletbe vagy onnan történő áthelyezésekor nincs leállás, kivéve a művelet végén lévő rövid időtartamot (másodpercben) az adatbázis-kapcsolatok eldobásakor.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>Mikor érdemes figyelembe vennie SQL Database rugalmas készletet

A készleteket kifejezetten a nagy számú, speciális felhasználási mintákkal rendelkező adatbázisokhoz tervezték. Az egyes adatbázisok mintáit átlagosan alacsony, és viszonylag rendszertelen időközönkénti hirtelen megugró kihasználtság jellemzi.

Minél több adatbázist tud hozzáadni egy készlethez, annál többet takaríthat meg. Az alkalmazás használati mintáitól függően már akár két S3-adatbázissal is megtakarítást érhet el.

A következő szakaszokból megtudhatja, hogyan mérje fel, hogy előnyös-e, ha egy adott adatbázis-gyűjtemény egy készlethez tartozik. A példákban Standard készletek szerepelnek, de ugyanezen elvek alkalmazhatók az Alapszintű és a Premium készletekre is.

### <a name="assessing-database-utilization-patterns"></a>Az adatbázis felhasználási mintáinak felmérése

Az alábbi ábra egy olyan adatbázist mutat be, amely sok időt tölt tétlenül, miközben időszakosan hirtelen megugró kihasználtság jellemzi. Az ilyen kihasználtsági minta esetén használhatók készletek:

   ![egy készletbe illő önálló adatbázis](./media/sql-database-elastic-pool/one-database.png)

Az ábrázolt ötperces periódus során a DB1 adatbázis maximális DTU-használata 90, de az összesített átlagos használati érték öt DTU alatt van. Ezen munkaterhelés egyetlen adatbázisban való futtatásához S3 számítási méret szükséges, azonban ez a művelet az alacsony tevékenységű időszakok során nem használt erőforrások többségét elhagyja.

Egy készletben ezek a használaton kívüli DTU-k több adatbázis között lehetnek megosztva, így a szükséges DTU-k száma és az összköltség is csökken.

Az előző példa mentén továbbhaladva tegyük fel, hogy további adatbázisokkal rendelkezünk, amelyeket DB1-hez hasonló felhasználási minták jellemeznek. Az alábbi két ábrán a négy adatbázis és 20 adatbázis kihasználtsága ugyanarra a gráfra van felhasználva, hogy a DTU-alapú vásárlási modell használatával ábrázolja a kihasználtsága nem átfedéses jellegét az idő múlásával:

   ![négy adatbázis egy készletbe illő kihasználtsági mintával](./media/sql-database-elastic-pool/four-databases.png)

   ![húsz adatbázis egy készletbe illő kihasználtsági mintával](./media/sql-database-elastic-pool/twenty-databases.png)

A DTU-k mind a 20 adatbázisra vonatkozó összesített kihasználtságát a fekete vonal jelzi az előző ábrán. Ez alapján a teljes DTU-használat soha nem lépi túl a 100 DTU értéket, és az időtartam során a 20 adatbázis 100 eDTU-t használhat közösen. Ez a DTU 20x-csökkenést eredményez, amely az egyes adatbázisok S3 számítási méretekben történő elhelyezésével összehasonlítható a 13x.

Ez a példa az alábbi okokból ideális:

- Nagy különbségek vannak az adatbázisok átlagos és kiugró mértékű kihasználtsága között.
- Az egyes adatbázisok kiugró mértékű kihasználtsága különböző időpontokban jelentkezik.
- Az eDTU-k több adatbázis között vannak megosztva.

A készletre vonatkozó költség az eDTU-készlet függvénye. A készlethez tartozó eDTU-k egységára egy önálló adatbázis DTU-egységárának másfélszerese, azonban **a készlethez tartozó eDTU-kat sok adatbázis használhatja, így kevesebb eDTU-ra van szükség**. Ezek a díjszabásban és eDTU-megosztásban jelentkező különbségek adják a készletekkel elérhető megtakarítás alapját.

A következő, az adatbázis-használattal és az adatbázis-kihasználtsággal kapcsolatos szabályok biztosítják, hogy egy készlet csökkentett költségeket biztosítson, mint az önálló adatbázisok számítási méreteinek használata.

### <a name="minimum-number-of-databases"></a>Adatbázisok minimális száma

Ha az önálló adatbázisok erőforrásainak összesített mennyisége több mint 1,5 x a készlethez szükséges erőforrások száma, akkor a rugalmas készlet költséghatékonyabb.

***Példa a DTU-alapú vásárlási modellre***<br>
Legalább két S3-adatbázis vagy legalább 15 S0-adatbázis szükséges ahhoz, hogy egy 100 eDTU-készlet költséghatékonyabb legyen, mint az önálló adatbázisok számítási méreteinek használata.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Egyidejűleg kiugró kihasználtságú adatbázisok maximális száma

Az erőforrások megosztásával a készletben lévő összes adatbázis nem tud egyidejűleg erőforrásokat használni az önálló adatbázisok számára elérhető korlátig. Minél kevesebb adatbázis van, amely egyidejűleg meghaladja a maximális értéket, annál kisebb a készlet erőforrásai, és a készlet költséghatékonyabb lesz. Általánosságban nem több mint 2/3 (vagy 67%) a készletben lévő adatbázisoknak egyidejűleg kell megjelenniük a maximális erőforrás-korlátot.

***Példa a DTU-alapú vásárlási modellre***

Ha csökkenteni szeretnénk három S3-adatbázis költségét egy 200 eDTU-s készletben, akkor a háromból egyszerre legfeljebb kettő működhet kiugró kihasználtsággal. Ha ebből a három S3-adatbázisból több mint kettő működik egyszerre kiugró kihasználtsággal, akkor a készletnek több mint 200 eDTU-t kellene tartalmaznia. Ha a készlet átméretezése több mint 200 Edtu, a készlethez több S3-adatbázist kell hozzáadni, hogy a költségek kevesebbek legyenek, mint az önálló adatbázisok számítási méretei.

Ne feledje, hogy ebben a példában nem vesszük számításba a készlet egyéb adatbázisainak kihasználását. Ha egy adott időpontban minden adatbázis használatban van valamilyen szinten, akkor az adatbázisok kevesebb mint kétharmad része (vagy 67%-a) működhet egyszerre kiugró kihasználtsággal.

### <a name="resource-utilization-per-database"></a>Erőforrás-használat/adatbázis

Az adatbázisok kiugró és átlagos kihasználtsága közötti lényeges különbség a hosszú, alacsony kihasználtságú és a rövid magas kihasználtságú időszakokban mutatkozik meg. Ilyen felhasználási minta esetén ideális az erőforrások adatbázisok közötti megosztása. Az adatbázis készletben való használatát akkor érdemes megfontolni, ha a kiugró mértékű kihasználtsága hozzávetőlegesen másfélszer nagyobb az átlagos kihasználtságánál.

**Példa a DTU-alapú vásárlási modellre**: Ha egy 100 DTU-s kiugró kihasználtsággal működő S3-adatbázis átlagosan legfeljebb 67 DTU-t használ, akkor jó jelöltnek számít egy eDTU-kat közösen használó készlethez. Ha pedig egy 20 DTU-s kiugró kihasználtsággal működő S1-adatbázis átlagosan legfeljebb 13 DTU-t használ, akkor jó jelöltnek számít egy készlethez.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Hogyan a megfelelő készlet méretének kiválasztása

A készletek legjobb mérete a készletben lévő összes adatbázishoz szükséges összesített erőforrásoktól függ. Ez a következők meghatározását foglalja magában:

- A készletben lévő összes adatbázis által felhasznált erőforrások maximális száma (a maximális DTU vagy a maximális virtuális mag a választott újrabeszerzési modelltől függően).
- A készletben szereplő összes adatbázis által használt maximális tárterület (bájtban).

Az egyes erőforrás-modellekhez elérhető szolgáltatási rétegekért tekintse meg a [DTU-alapú vásárlási modellt](sql-database-service-tiers-dtu.md) vagy a [virtuális mag-alapú vásárlási modellt](sql-database-service-tiers-vcore.md).

Ha nincs lehetősége eszközök használatára, az alábbi részletes útmutatóval megbecsülheti, hogy a készlet költséghatékonyabb-e az önálló adatbázisok használatánál:

1. Becsülje meg a készlethez szükséges Edtu vagy virtuális mag a következőképpen:

   DTU-alapú vásárlási modell esetén: MAX(<*Az adatbázisok teljes száma* X *Az egyes adatbázisok átlagos DTU-használata*>,<br>  
   <*A kiugró kihasználtsággal egyszerre működő adatbázisok száma* X *Az egyes adatbázisok kiugró DTU-használata*)

   Virtuális mag-alapú vásárlási modell esetén: MAX (<*adatbázisok* *átlagos virtuális mag*-kihasználtsága (db >)<br>  
   <*Egyidejű csúcsérték-kiugró adatbázisok száma* X *csúcsérték-virtuális mag kihasználtsága/db*)

2. A készlethez szükséges tárterület méretének becsléséhez adja össze a készlet egyes adatbázisaihoz szükséges bájtok számát. Ezután határozza meg a szükséges tárhelyet biztosító eDTU-készlet méretét.
3. A DTU-alapú vásárlási modell esetében az 1. és a 2. lépésben szereplő eDTU-becslések nagyobb mennyiségét kell megbecsülni. A virtuális mag-alapú vásárlási modell esetében az 1. lépés alapján végezze el a virtuális mag becslését.
4. Tekintse meg a [SQL Database díjszabási oldalát](https://azure.microsoft.com/pricing/details/sql-database/) , és keresse meg a 3. lépésben megbecsülhető legkisebb méretű készletet.
5. Hasonlítsa össze az 5. lépésben szereplő készlet árát az önálló adatbázisok megfelelő számítási méreteinek árával.

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Más SQL Database funkciók használata rugalmas készletekkel

### <a name="elastic-jobs-and-elastic-pools"></a>Rugalmas feladatok és rugalmas készletek

A készletek használata leegyszerűsíti a felügyeleti feladatokat, mivel a szkriptek **[rugalmas feladatokban](elastic-jobs-overview.md)** futtathatók. A rugalmas feladatok használatával kiküszöbölhető a nagy számú adatbázis kezelésével járó monotonitás.

További információk a több adatbázissal dolgozó további adatbázis-eszközökről: [Horizontális felskálázás az Azure SQL Database-ben](sql-database-elastic-scale-introduction.md).

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Rugalmas készletben lévő adatbázisok üzleti folytonossági lehetőségei

A rugalmas készletbe helyezett adatbázisok általánosságban ugyanazokat [az üzletmenet-folytonossági funkciókat](sql-database-business-continuity.md) támogatják, amelyek az önálló adatbázisokhoz is elérhetők.

- **Időponthoz való visszaállítás**

  Az időponthoz való visszaállítás az adatbázis automatikus biztonsági mentését használja a készletben lévő adatbázisok egy adott időpontra történő helyreállításához. Lásd: [Időponthoz kötött visszaállítás](sql-database-recovery-using-backups.md#point-in-time-restore)

- **Geo-restore**

  A Geo-visszaállítás az alapértelmezett helyreállítási beállítást adja meg, ha egy adatbázis nem érhető el, mert az adatbázist futtató régióban egy incidens található. Lásd: [Az Azure SQL-adatbázis visszaállítása vagy feladatátvétel a másodlagos kiszolgálóra](sql-database-disaster-recovery.md)

- **Aktív georeplikáció**

  Az agresszív helyreállítási követelményekkel rendelkező alkalmazások esetében, mint a Geo-visszaállítás, konfigurálhatja az [aktív földrajzi replikálást](sql-database-active-geo-replication.md) vagy egy [automatikus feladatátvételi csoportot](sql-database-auto-failover-group.md).

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Új SQL Database rugalmas készlet létrehozása a Azure Portal használatával

Kétféle módon hozhat létre rugalmas készletet a Azure Portal.

1. Rugalmas készletet úgy hozhat létre, ha az **SQL rugalmas készletet** keresi a **piactéren** , vagy a **+ Hozzáadás** lehetőségre kattint az SQL rugalmas készletek Tallózás paneljén. Egy új vagy egy meglévő kiszolgálót is meg tud adni a készlet üzembe helyezési munkafolyamatán keresztül.
2. Vagy létrehozhat egy rugalmas készletet úgy, hogy egy meglévő SQL Server-kiszolgálóra navigál, és a **készlet létrehozása** lehetőségre kattint, hogy közvetlenül a kiszolgálóra hozzon létre egy készletet. Az egyetlen különbség, hogy kihagyja azt a lépést, ahol a kiszolgálót a készlet üzembe helyezési munkafolyamata során meg kell adnia.

> [!NOTE]
> Egy kiszolgálón több készlet is létrehozható, de nem adhat hozzá különböző kiszolgálókon lévő adatbázisokat ugyanahhoz a készlethez.

A készlet szolgáltatási szintje határozza meg a készletben lévő rugalmas készletek elérhető funkcióit, valamint az egyes adatbázisok számára elérhető erőforrások maximális mennyiségét. Részletekért lásd: rugalmas készletek erőforrás-korlátai a [DTU](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes)-modellben. A rugalmas készletek virtuális mag-alapú erőforrás-korlátaival kapcsolatban lásd: [virtuális mag-alapú erőforrás-korlátok – rugalmas készletek](sql-database-vcore-resource-limits-elastic-pools.md).

A készlet erőforrásainak és díjszabásának konfigurálásához kattintson a **készlet konfigurálása**elemre. Ezután válasszon ki egy szolgáltatási szintet, adja hozzá az adatbázisokat a készlethez, és konfigurálja az erőforrás-korlátokat a készlethez és az adatbázisaihoz.

Ha befejezte a készlet konfigurálását, kattintson az Alkalmaz gombra, nevezze el a készletet, majd kattintson az OK gombra a készlet létrehozásához.

## <a name="monitor-an-elastic-pool-and-its-databases"></a>Rugalmas készlet és adatbázisainak figyelése

A Azure Portal egy rugalmas készlet és a készleten belüli adatbázisok kihasználtságának figyelésére van lehetőség. A rugalmas készlet módosításait is elvégezheti, és egyszerre is elküldheti az összes módosítást. Ezek a változások közé tartoznak az adatbázisok hozzáadása vagy eltávolítása, a rugalmas készlet beállításainak módosítása vagy az adatbázis beállításainak módosítása.

A rugalmas készlet figyelésének megkezdéséhez keresse meg és nyissa meg a portálon egy rugalmas készletet. Ekkor megjelenik egy képernyő, amely áttekintést nyújt a rugalmas készlet állapotáról. Az érintett műveletek közé tartoznak az alábbiak:

- A rugalmas készlet erőforrásainak használatát bemutató diagramok figyelése
- A rugalmas készletre vonatkozó legutóbbi riasztások és javaslatok, ha vannak ilyenek

Az alábbi ábrán egy példaként szolgáló rugalmas készlet látható:

![Készlet nézet](./media/sql-database-elastic-pool-manage-portal/basic.png)

Ha további információra van szüksége a készletről, kattintson a jelen áttekintés bármely elérhető információja lehetőségre. Az erőforrás- **kihasználtsági** diagramra kattintva megtekintheti az Azure figyelési nézetét, ahol testreszabhatja a diagramon látható mérőszámokat és időablakokat. Az elérhető értesítések bármelyikére kattintva megnyílik egy panel, amely a riasztás vagy javaslat részletes adatait jeleníti meg.

Ha a készletben lévő adatbázisokat szeretné figyelni, kattintson a bal oldali erőforrás menü **figyelés** szakaszában található **adatbázis-Erőforrás kihasználtsága** lehetőségre.

![Adatbázis-Erőforrás kihasználtsága lap](./media/sql-database-elastic-pool-manage-portal/db-utilization.png)

### <a name="to-customize-the-chart-display"></a>A diagram megjelenítésének testreszabása

A diagram és a metrika oldalon szerkesztheti az egyéb mérőszámokat, például a CPU-százalékot, az adatio-százalékos arányt, valamint a napló i/o-százalékos arányát.

A **diagram szerkesztése** űrlapon kiválaszthat egy rögzített időtartományt, vagy az **Egyéni** lehetőségre kattintva kiválaszthatja a 24 órás időszakot az elmúlt két hétben, majd kiválaszthatja a figyelni kívánt erőforrásokat.

### <a name="to-select-databases-to-monitor"></a>A figyelni kívánt adatbázisok kiválasztása

Alapértelmezés szerint az **adatbázis-Erőforrás kihasználtsága** panelen az első 5 adatbázis jelenik meg a DTU vagy a CPU alapján (a szolgáltatási szintjétől függően). A diagramon lévő adatbázisokat úgy állíthatja be, hogy kijelöli és törli az adatbázisok kijelölését a diagram alatti listából a bal oldali jelölőnégyzetek segítségével.

További mérőszámokat is kiválaszthat az adatbázis-táblázat egymás melletti megtekintéséhez, így átfogóbb képet kaphat az adatbázis teljesítményéről.

További információ: [SQL Database riasztások létrehozása Azure Portalokban](sql-database-insights-alerts-portal.md).

## <a name="customer-case-studies"></a>Ügyféleset-tanulmányok

- [SnelStart](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-snelstart/)

  A SnelStart rugalmas készleteket használt Azure SQL Databaseekkel, hogy havonta gyorsan bővítse üzleti szolgáltatásait, 1 000 új Azure SQL Database-adatbázist.

- [Umbraco](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-umbraco/)

  A árnyék rugalmas készleteket használ a Azure SQL Database segítségével, amelyekkel gyorsan kiépítheti és méretezheti a szolgáltatásokat több ezer bérlő számára a felhőben.

- [Daxko/CSI](https://customers.microsoft.com/story/csi-used-azure-to-accelerate-its-development-cycle-and-to-enhance-its-customer-services)

  A Daxko/CSI rugalmas készleteket használ Azure SQL Database a fejlesztési ciklus felgyorsításához, valamint az ügyfelek szolgáltatásainak és teljesítményének növeléséhez.

## <a name="next-steps"></a>További lépések

- Rugalmas készletek méretezése: [rugalmas készletek skálázása](sql-database-elastic-pool-scale.md) és [rugalmas készlet méretezése – mintakód](scripts/sql-database-monitor-and-scale-pool-powershell.md)
- A videókkal kapcsolatban lásd: [a Microsoft Virtual Academy videós kurzusa Azure SQL Database rugalmas képességek](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
- A rugalmas készleteket használó SaaS-alkalmazások szerkezeti kialakításainak alaposabb megismeréséhez olvassa el a [Tervminták több-bérlős SaaS-alkalmazásokhoz Azure SQL Database esetén](sql-database-design-patterns-multi-tenancy-saas-applications.md) című részt.
- A rugalmas készleteket használó SaaS-oktatóanyagért tekintse [meg a Wingtip SaaS-alkalmazás bemutatása](sql-database-wtp-overview.md)című témakört.
