---
title: Több, a rugalmas készletek – Azure SQL-adatbázisok kezelése |} A Microsoft Docs
description: Kezelése és méretezése több SQL-adatbázis – több száz és több ezer - rugalmas készletek használatával. Egyetlen díj oszthat meg, ahol szükséges erőforrások.
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
ms.date: 01/25/2019
ms.openlocfilehash: 1e7ec07c29f742202b17e94d96d88b0dfb223100
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55464347"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-azure-sql-databases"></a>Rugalmas készletek kezelése és a több Azure SQL-adatbázisok horizontális Súgó

Az SQL Database rugalmas készletei egyszerű, költséghatékony megoldást kínálnak egyszerre több olyan adatbázis kezelésére és méretezésére, amelyek felhasználási igénye nagy mértékben és kiszámíthatatlanul változik. A rugalmas készletben található adatbázisok egy Azure SQL Database-kiszolgálón, és beállítása a set áron erőforrások száma. Az Azure SQL Database rugalmas készleteivel az SaaS-fejlesztők az előre meghatározott költségvetésen belül maradva optimalizálhatják az adatbáziscsoportok ár-teljesítmény arányát, és rugalmas teljesítményt biztosíthatnak az egyes adatbázisokhoz.

## <a name="what-are-sql-elastic-pools"></a>Mik a rugalmas SQL-készletek

A SaaS-fejlesztők több adatbázisból álló nagyméretű adatrétegekre építenek alkalmazásokat. Gyakori alkalmazásminta az önálló adatbázis biztosítása minden egyes ügyfél számára. A különböző ügyfelek felhasználási mintája nagymértékben és kiszámíthatatlan módon változik, ezért nehéz megjósolni az adatbázis minden egyes felhasználójának erőforrásigényét. Hagyományosan két módja volt:

- Túltervezés az erőforrásokat a kihasználtsági csúcs- és keresztül használatalapú, vagy
- Túlfizetés csökkenthetők a költségek, teljesítmény és az Ügyfélelégedettség rovására.

Rugalmas készletek megoldhatja a problémát azáltal, hogy az adatbázisok beolvasása a teljesítmény-erőforrásokat, amikor szükségük van rá szükségük. Egy egyszerű erőforrás-lefoglalási mechanizmust biztosítanak, kiszámítható költségekkel. A rugalmas készleteket használó SaaS-alkalmazások szerkezeti kialakításainak alaposabb megismeréséhez olvassa el a [Tervminták több-bérlős SaaS-alkalmazásokhoz Azure SQL Database esetén](sql-database-design-patterns-multi-tenancy-saas-applications.md) című részt.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

Rugalmas készletek köszönhetően a fejlesztő által az egyes adatbázisok használati időszakok kiszámíthatatlan befogadásához több adatbázis által közösen használt készlethez tartozó erőforrások megvásárlására. A készlet alapján vagy konfigurálhat erőforrásokat a [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) vagy a [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md). Egy készlet erőforrás-követelmény-adatbázisa a összesített kihasználtsága határozza meg. A fejlesztői költségvetés határozza meg a készlet számára elérhető erőforrások mennyisége. A fejlesztő egyszerűen adatbázisokat ad a készlethez, beállítja az adatbázisok erőforrások minimális és maximális (vagy minimális és maximális dtu-k vagy minimális vagy maximális virtuális magok a választott modell resourcing függően), és ezután beállítja az erőforrás-készlet alapján azok Költségvetés. A készletek segítségével a fejlesztő zökkenőmentesen és fokozatosan növelheti szolgáltatásának teljesítményét a korlátozott erőforrásokkal bíró startupok szintjéről az érett vállalkozások szintjére.

A készleten belül az önálló adatbázisok az automatikus méretezés rugalmasságával rendelkeznek. Nagy terhelés alatt az adatbázisok használhatnak további erőforrásokat az igényeknek. A kisebb terhelésű adatbázisok kevesebbet, és terhelés alatt nem álló adatbázisoknak nincs erőforrás. Az erőforrásoknak az egyes adatbázisok helyett a teljes készlet számára hozzáférhetővé tétele jelentősen leegyszerűsíti a felügyeleti feladatokat. Emellett a készlet kiszámítható költségekkel rendelkezik. További erőforrások lehet hozzáadni egy meglévő készlet leállnának, azzal a különbséggel, hogy az adatbázisok áthelyezése a adja meg a további számítási erőforrásokat az új eDTU-foglalás szükség lehet. Hasonlóképpen ha már nincs szükség további erőforrásokat, távolíthatja el a létező készletből bármikor időben. Ezenfelül a készlethez adatbázisok adhatók hozzá vagy vonhatók ki belőle. Ha egy adatbázis kiszámítható módon nem használja ki az erőforrásokat, helyezze át az adatbázist.

> [!NOTE]
> Adatbázisok áthelyezése vagy egy rugalmas készlet abból, ha nem jár egy rövid ideig (másodperc) sorrendje lesz a művelet végén kivéve ha az adatbázis-kapcsolatok megszakadnak.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>Mikor érdemes egy SQL Database rugalmas készlet

A készleteket kifejezetten a nagy számú, speciális felhasználási mintákkal rendelkező adatbázisokhoz tervezték. Az egyes adatbázisok mintáit átlagosan alacsony, és viszonylag rendszertelen időközönkénti hirtelen megugró kihasználtság jellemzi.

Minél több adatbázist tud hozzáadni egy készlethez, annál többet takaríthat meg. Az alkalmazás használati mintáitól függően már akár két S3-adatbázissal is megtakarítást érhet el.

A következő szakaszokból megtudhatja, hogyan mérje fel, hogy előnyös-e, ha egy adott adatbázis-gyűjtemény egy készlethez tartozik. A példákban Standard készletek szerepelnek, de ugyanezen elvek alkalmazhatók az Alapszintű és a Premium készletekre is.

### <a name="assessing-database-utilization-patterns"></a>Az adatbázis felhasználási mintáinak felmérése

Az alábbi ábra egy olyan adatbázist mutat be, amely sok időt tölt tétlenül, miközben időszakosan hirtelen megugró kihasználtság jellemzi. Az ilyen kihasználtsági minta esetén használhatók készletek:

   ![egy készletbe illő önálló adatbázis](./media/sql-database-elastic-pool/one-database.png)

Az ábrázolt ötperces periódus során a DB1 adatbázis maximális DTU-használata 90, de az összesített átlagos használati érték öt DTU alatt van. Az S3 szintű számítási méret ilyen számítási feladatok futtatásához önálló adatbázis szükséges, de a kihasználatlan marad az erőforrások legnagyobb része kihasználatlan alacsony tevékenységű időszakok során.

Egy készletben ezek a használaton kívüli DTU-k több adatbázis között lehetnek megosztva, így a szükséges DTU-k száma és az összköltség is csökken.

Az előző példa mentén továbbhaladva tegyük fel, hogy további adatbázisokkal rendelkezünk, amelyeket DB1-hez hasonló felhasználási minták jellemeznek. A következő két ábrán láthatja az alábbi a négy adatbázis és a 20 adatbázis kihasználtsági ugyanazon a grafikonon mutatja be a DTU-alapú vásárlási modell használatával időben egymást nem átfedő jellege szintekre épülő:

   ![négy adatbázis egy készletbe illő kihasználtsági mintával](./media/sql-database-elastic-pool/four-databases.png)

   ![húsz adatbázis egy készletbe illő kihasználtsági mintával](./media/sql-database-elastic-pool/twenty-databases.png)

A DTU-k mind a 20 adatbázisra vonatkozó összesített kihasználtságát a fekete vonal jelzi az előző ábrán. Ez alapján a teljes DTU-használat soha nem lépi túl a 100 DTU értéket, és az időtartam során a 20 adatbázis 100 eDTU-t használhat közösen. Ennek eredményeképpen a dtu-k a 20 x csökkenését, és a egy 13 x képest, mintha minden egyes adatbázis S3 díjcsökkentésnek számítási méretek az önálló adatbázisok számára.

Ez a példa az alábbi okokból ideális:

- Nagy különbségek vannak az adatbázisok átlagos és kiugró mértékű kihasználtsága között.
- Az egyes adatbázisok kiugró mértékű kihasználtsága különböző időpontokban jelentkezik.
- Az eDTU-k több adatbázis között vannak megosztva.

A készletre vonatkozó költség az eDTU-készlet függvénye. A készlethez tartozó eDTU-k egységára egy önálló adatbázis DTU-egységárának másfélszerese, azonban **a készlethez tartozó eDTU-kat sok adatbázis használhatja, így kevesebb eDTU-ra van szükség**. Ezek a díjszabásban és eDTU-megosztásban jelentkező különbségek adják a készletekkel elérhető megtakarítás alapját.

A következő szabályok költségcsökkenést eredményezzen az adatbázisok és az adatbázis-kihasználtsági kapcsolódó segítségével biztosíthatja, hogy a készlet számítási méretek használatával az önálló adatbázisok képest.

### <a name="minimum-number-of-databases"></a>Adatbázisok minimális száma

Ha az önálló adatbázisok számára az erőforrások összesített mennyisége nem több, mint a készlet számára szükséges erőforrások x 1.5 költséghatékonyabb a rugalmas készlet használata.

***DTU-alapú vásárlási modell példa***<br>
Legalább két S3-adatbázis vagy legalább 15 S0-adatbázis 100 edtu-s készlet költséghatékonyabban, mint az önálló adatbázisok számítási méretek használata szükséges.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Egyidejűleg kiugró kihasználtságú adatbázisok maximális száma

Az erőforrások megosztása a készlet nem minden adatbázis egyszerre használhatja a korlátig rendelkezésre álló erőforrások az önálló adatbázisok számára. Minél kevesebb adatbázis működik egyszerre kiugró kihasználtsággal, annál alacsonyabbra az adatbáziskészlet erőforrásainak akkor állítható be, és annál költséghatékonyabbá válik a készlet. Általánosságban a készletben található adatbázisok legfeljebb 2/3 (vagy 67 %-át) működhet egyszerre a maximális erőforrások maximális számát.

***DTU-alapú vásárlási modell példa***

Ha csökkenteni szeretnénk három S3-adatbázis költségét egy 200 eDTU-s készletben, akkor a háromból egyszerre legfeljebb kettő működhet kiugró kihasználtsággal. Ha ebből a három S3-adatbázisból több mint kettő működik egyszerre kiugró kihasználtsággal, akkor a készletnek több mint 200 eDTU-t kellene tartalmaznia. Ha a készletet 200 Edtu-nál nagyobbra, több S3-adatbázist kellene adni a készlethez, hogy a költség alacsonyabb, mint az önálló adatbázisok méretű számítási.

Ne feledje, hogy ebben a példában nem vesszük számításba a készlet egyéb adatbázisainak kihasználását. Ha egy adott időpontban minden adatbázis használatban van valamilyen szinten, akkor az adatbázisok kevesebb mint kétharmad része (vagy 67%-a) működhet egyszerre kiugró kihasználtsággal.

### <a name="resource-utilization-per-database"></a>Erőforrás-használat adatbázisonként

Az adatbázisok kiugró és átlagos kihasználtsága közötti lényeges különbség a hosszú, alacsony kihasználtságú és a rövid magas kihasználtságú időszakokban mutatkozik meg. Ilyen felhasználási minta esetén ideális az erőforrások adatbázisok közötti megosztása. Az adatbázis készletben való használatát akkor érdemes megfontolni, ha a kiugró mértékű kihasználtsága hozzávetőlegesen másfélszer nagyobb az átlagos kihasználtságánál.

**DTU-alapú vásárlási modell példa**: Ha egy 100 DTU-s kiugró kihasználtsággal működő S3-adatbázis átlagosan legfeljebb 67 DTU-t használ, akkor jó jelöltnek számít egy eDTU-kat közösen használó készlethez. Ha pedig egy 20 DTU-s kiugró kihasználtsággal működő S1-adatbázis átlagosan legfeljebb 13 DTU-t használ, akkor jó jelöltnek számít egy készlethez.

## <a name="how-do-i-choose-the-correct-pool-size"></a>A megfelelő készletméret kiválasztása

A készlet optimális mérete attól függ, hogy a szükséges a készletben található adatbázisok összesített erőforrásokat. Ez magában foglalja, amely meghatározza, hogy a következő:

- (Maximális dtu-k vagy a maximális virtuális magot kapnak a választott modell resourcing függően) a készletben található összes adatbázis által használt erőforrások maximális száma.
- A készletben szereplő összes adatbázis által használt maximális tárterület (bájtban).

A szolgáltatásszintek érhető el minden egyes erőforrás-modellje, lásd: a [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) vagy a [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md).

Ha nincs lehetősége eszközök használatára, az alábbi részletes útmutatóval megbecsülheti, hogy a készlet költséghatékonyabb-e az önálló adatbázisok használatánál:

1. Megbecsülheti az edtu-k vagy a virtuális magok a készlet a következő szükséges:

   A DTU-alapú vásárlási modell: MAX(<*Az adatbázisok teljes száma* X *Az egyes adatbázisok átlagos DTU-használata*>,<br>  
   <*A kiugró kihasználtsággal egyszerre működő adatbázisok száma* X *Az egyes adatbázisok kiugró DTU-használata*)

   A Virtuálismag-alapú vásárlási modell: MAX (<*adatbázisok száma* X *átlagos virtuális mag használata*>,<br>  
   <*Több egyidejűleg kiugró kihasználtságú adatbázisok* X *csúcs-virtuális mag használata*)

2. A készlethez szükséges tárterület méretének becsléséhez adja össze a készlet egyes adatbázisaihoz szükséges bájtok számát. Ezután határozza meg a szükséges tárhelyet biztosító eDTU-készlet méretét.
3. A DTU-alapú vásárlási modell igénybe meghatározott eDTU-becslések közül a nagyobb 1. lépést és a 2. lépés. A Virtuálismag-alapú vásárlási modell figyelembe venni a virtuális mag becslés az 1. lépésben.
4. Tekintse meg a [SQL Database díjszabási oldalát](https://azure.microsoft.com/pricing/details/sql-database/) , és keresse meg a legkisebb készletméretet, amely nagyobb, mint 3. lépésben megbecsült.
5. Hasonlítsa össze a készlet árát az 5. lépés árát az önálló adatbázisok számára a megfelelő számítási méretek használatával.

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Rugalmas készletek egyéb SQL adatbázis-szolgáltatások használata

### <a name="elastic-jobs-and-elastic-pools"></a>Rugalmas feladatok és a rugalmas készletek

A készletek használata leegyszerűsíti a felügyeleti feladatokat, mivel a szkriptek **[rugalmas feladatokban](sql-database-elastic-jobs-overview.md)** futtathatók. A rugalmas feladatok használatával kiküszöbölhető a nagy számú adatbázis kezelésével járó monotonitás. Kezdetnek tekintse át [a rugalmas feladatokkal kapcsolatos első lépéseket ismertető](sql-database-elastic-jobs-getting-started.md) témakört.

További információk a több adatbázissal dolgozó további adatbázis-eszközökről: [Horizontális felskálázás az Azure SQL Database-ben](sql-database-elastic-scale-introduction.md).

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>A rugalmas készletben található adatbázisok üzletmenet-folytonossági funkciókat

A készletezett adatbázisok általánosságban ugyanazokat [az üzletmenet-folytonossági funkciókat](sql-database-business-continuity.md) támogatják, amelyek az önálló adatbázisokhoz is elérhetők.

- **Pont – történő visszaállítás**

  Időponthoz visszaállítási időben egy készlethez egy adott időpontra adatbázis helyreállítása az adatbázis automatikus biztonsági másolatokat használ. Lásd: [Időponthoz kötött visszaállítás](sql-database-recovery-using-backups.md#point-in-time-restore)

- **Geo-restore**

  GEO-visszaállítás az alapértelmezett helyreállítási lehetőséget biztosít, amikor a miatt incidens a régióban, az adatbázist üzemeltető adatbázis nem érhető el. Lásd: [Az Azure SQL Database visszaállítása vagy feladatátvétel a másodlagos kiszolgálóra](sql-database-disaster-recovery.md)

- **Aktív georeplikáció**

  A geo-visszaállítás kínálnak agresszívabb helyreállítási követelményekkel rendelkező alkalmazások esetében konfigurálhatja [aktív georeplikáció](sql-database-active-geo-replication.md) vagy egy [automatikus feladatátvételi csoport](sql-database-auto-failover-group.md).

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Az Azure portal használatával új SQL Database rugalmas készlet létrehozása

Az Azure Portalon is létrehozhat a rugalmas készlet két módja van.

1. Rugalmas készlet létrehozásához keressen **SQL rugalmas készlet** a a **Marketplace-en** vagy **+ Hozzáadás** a rugalmas SQL-készletek böngészőpanelen. Ön adja meg egy új vagy meglévő kiszolgáló alábbi készletkiépítő munkafolyamat révén képes.
2. Vagy létrehozhat egy rugalmas készlet és gombra kattintva meglévő SQL server **készlet létrehozása** -készlet létrehozása közvetlenül a kiszolgálón. Az egyetlen különbség az, a lépés kihagyása adhatja meg a kiszolgáló a készletet érintő kiépítési munkafolyamat során.

> [!NOTE]
> Egy kiszolgálón több készletet is létrehozhat, de egy készlethez különböző kiszolgálókról származó adatbázisok nem adhat.

A készlet szolgáltatásszintje meghatározza, hogy a készlet, valamint az egyes adatbázisok számára elérhető erőforrások maximális mennyisége a elastics elérhető funkciók. További információkért lásd: a rugalmas készletek erőforráskorlátok a [DTU modell](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes). Virtuálismag-alapú erőforráskorlátok a rugalmas készletek, lásd: [Virtuálismag-alapú erőforráskorlátok – rugalmas készletek](sql-database-vcore-resource-limits-elastic-pools.md).

Konfigurálja az erőforrásokat, és kattintson a készlet díjszabás **készlet beállítása**. Válassza ki valamelyik szolgáltatási rétegben, a készlethez adatbázisok hozzáadása és konfigurálása a erőforráskorlátok a készlet és az adatbázisokhoz.

Befejezése után a készlet konfigurálása, kattintson az "Alkalmaz", a készlet nevét, és kattintson az "OK" gombra a készlet létrehozásához.

## <a name="monitor-an-elastic-pool-and-its-databases"></a>Rugalmas készlet és a hozzá tartozó adatbázisok figyelése

Az Azure Portalon követheti nyomon a rugalmas készlet és az adott készletben lévő adatbázisok felhasználását. Módosítások egy készletének a rugalmas készlet részeként győződjön meg arról, és az összes változtatás mentése egyszerre is. Ezek a változások a következők hozzáadása vagy eltávolítása az adatbázisok, a rugalmas készlet beállításainak módosítása vagy az adatbázis-beállítások módosítása.

A rugalmas készlet figyelése, keresse meg és nyissa meg a rugalmas készlet a portálon. Először megjelenik egy képernyő, amely áttekintést nyújt a rugalmas készlet állapotát. Az érintett műveletek közé tartoznak az alábbiak:

- Diagramok erőforrások használatát a rugalmas készlet figyelése
- Legutóbbi riasztások és javaslatok, ha elérhető, a rugalmas készlet

A következő ábrán látható egy példa a rugalmas készlet:

![Készlet megtekintése](./media/sql-database-elastic-pool-manage-portal/basic.png)

Ha azt szeretné, hogy további információ a készlet is kattinthat bármelyik ebben a cikkben áttekintjük a rendelkezésre álló információt. Kattintson a a **erőforrás-használat** diagram léphet az Azure figyelési nézet szabhatók testre a metrikák és idő ablakban látható a diagramon. Elérhető értesítések kattintva léphet egy riasztás vagy javaslat részletei panel.

Ha szeretné figyelni a készleten belüli adatbázisok, kattintson a **adatbázis-erőforrás-használat** a a **figyelés** szakaszában az erőforrás a bal oldali menüben.

![Adatbázis erőforrás-felhasználási oldalon](./media/sql-database-elastic-pool-manage-portal/db-utilization.png)

### <a name="to-customize-the-chart-display"></a>Diagram megjelenítéséhez

Módosíthatja a diagram és a metrika lap más mérőszámokat, például a CPU, adat IO százalékos aránya, és napló i/o (%) használt megjelenítéséhez.

Az a **diagram szerkesztése** képernyőn kiválaszthatja fix időpontot tartományt, vagy kattintson a **egyéni** bármely 24 órás időszakon belül az elmúlt két hétben, majd válassza ki és az erőforrások a figyelésére való.

### <a name="to-select-databases-to-monitor"></a>Válassza ki az adatbázisok figyelése

A diagram a alapértelmezés szerint a **Adabáziserőforrás-felhasználás** panelen jelennek meg az első 5 adatbázisok dtu-k vagy a CPU (attól függően, a szolgáltatási szintben). Jelölje ki, és a listából a diagram a bal oldalon a jelölőnégyzetek segítségével alatt adatbázisok unselecting mentése ezen a diagramon az adatbázisok válthat.

Válassza ki a nézet egymás mellett az adatbázis-tábla az adatbázisok teljesítményének teljes képet kaphat a további metrikák is.

További információkért lásd: [SQL Database-riasztások létrehozása az Azure Portalon](sql-database-insights-alerts-portal.md).

## <a name="next-steps"></a>További lépések

- Tekintse meg a rugalmas készletek vertikális felskálázásához [skálázás rugalmas készletek](sql-database-elastic-pool.md) és [méretezhető rugalmas készlet – mintakód](scripts/sql-database-monitor-and-scale-pool-powershell.md)
- Egy videót [Microsoft Virtual Academy videotanfolyam az Azure SQL Database rugalmas funkcióiról](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
- A rugalmas készleteket használó SaaS-alkalmazások szerkezeti kialakításainak alaposabb megismeréséhez olvassa el a [Tervminták több-bérlős SaaS-alkalmazásokhoz Azure SQL Database esetén](sql-database-design-patterns-multi-tenancy-saas-applications.md) című részt.
- Rugalmas készleteket használó SaaS oktatóanyagért lásd: [– a Wingtip SaaS-alkalmazás bevezetés](sql-database-wtp-overview.md).
