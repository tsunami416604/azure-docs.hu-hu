---
title: Több, a rugalmas készletek-Azure SQL-adatbázisok kezelése |} Microsoft Docs
description: Kezelése, és több SQL adatbázis - méretezési több száz és több ezer - rugalmas készleteket használó. Egy ár terjesztheti, amennyiben szükséges erőforrások.
keywords: több adatbázisból, adatbázis-erőforrások, adatbázis-teljesítmény
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.date: 06/20/2018
ms.author: ninarn
ms.topic: conceptual
ms.openlocfilehash: 5ef32b231a77906a6840ad3550e81b631ddc0c13
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309654"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-azure-sql-databases"></a>Rugalmas készletek kezelése, és több Azure SQL-adatbázisok méretezése

SQL Database rugalmas készletek, amely egyszerű és költséghatékony megoldást a kezelése és a méretezés több adatbázisok esetén, amelyek különböző és előre nem látható használati iránti igények kielégítése érdekében. Az adatbázisok rugalmas készlethez egy Azure SQL Database-kiszolgálón, és beállítása a készlet áron erőforrások száma. Az Azure SQL Database rugalmas készleteivel az SaaS-fejlesztők az előre meghatározott költségvetésen belül maradva optimalizálhatják az adatbáziscsoportok ár-teljesítmény arányát, és rugalmas teljesítményt biztosíthatnak az egyes adatbázisokhoz.

## <a name="what-are-sql-elastic-pools"></a>Mik azok a rugalmas SQL-készletek?

A SaaS-fejlesztők több adatbázisból álló nagyméretű adatrétegekre építenek alkalmazásokat. Gyakori alkalmazásminta az önálló adatbázis biztosítása minden egyes ügyfél számára. A különböző ügyfelek felhasználási mintája nagymértékben és kiszámíthatatlan módon változik, ezért nehéz megjósolni az adatbázis minden egyes felhasználójának erőforrásigényét. Hagyományosan kellett két lehetőség közül választhat:

- Túlzott kiépíteni az erőforrásokat alapján használati csúcsot és keresztül fizetési, vagy
- Hiány rendelkezés költség, csökkenti a teljesítményt és az ügyfelek elégedettségének során csúcsait mentéséhez.

Rugalmas készletek biztosításával, hogy az adatbázisok beolvasása a teljesítmény erőforrásokat, amikor szükség van szükségük a probléma megoldásához. Egy egyszerű erőforrás-lefoglalási mechanizmust biztosítanak, kiszámítható költségekkel. A rugalmas készleteket használó SaaS-alkalmazások szerkezeti kialakításainak alaposabb megismeréséhez olvassa el a [Tervminták több-bérlős SaaS-alkalmazásokhoz Azure SQL Database esetén](sql-database-design-patterns-multi-tenancy-saas-applications.md) című részt.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

Rugalmas készletek engedélyezése a fejlesztői készlet használati előre nem látható időszakok olyan egyéni adatbázis több adatbázis oszt erőforrások vásárlásához. A készlet-alapú vagy az erőforrások konfigurálhatja a [alapjául szolgáló vásárlási modell DTU-alapú](sql-database-service-tiers-dtu.md) vagy a [vCore-alapú alapjául szolgáló vásárlási modell (előzetes verzió)](sql-database-service-tiers-vcore.md). A készlet erőforrás követelmény adatbázisa összesített kihasználtsági határozza meg. A tárolókészlet számára elérhető erőforrások mennyisége a fejlesztői költségvetés vezérli. A fejlesztői egyszerűen adatbázisokat ad hozzá a címkészletet, beállítja a minimális és maximális erőforrások az adatbázisok (minimális és maximális dtu-inak vagy minimális vagy maximális vCores attól függően, hogy a választott resourcing modell), majd beállítja a készlet alapján az erőforrások és a keret. A készletek segítségével a fejlesztő zökkenőmentesen és fokozatosan növelheti szolgáltatásának teljesítményét a korlátozott erőforrásokkal bíró startupok szintjéről az érett vállalkozások szintjére.

A készleten belül az önálló adatbázisok az automatikus méretezés rugalmasságával rendelkeznek. A túlterheltség adatbázis is több erőforrást igény kielégítéséhez. Könnyű terhelések tartozó adatbázisok használnak kisebb, és nem tartozó adatbázisok nem erőforrást. Az erőforrásoknak az egyes adatbázisok helyett a teljes készlet számára hozzáférhetővé tétele jelentősen leegyszerűsíti a felügyeleti feladatokat. Plus akkor a készlet előre jelezhető költségvetést. További források azzal a különbséggel, hogy az adatbázisok áthelyezése a adja meg a további számítási erőforrásokat az új eDTU-foglalás esetleg egy meglévő készletbe adatbázis állásidő nélkül lehet hozzáadni. Hasonlóképpen ha már nincs szükség további erőforrásokat azok távolíthatók el egy meglévő készlet bármikor időben. Ezenfelül a készlethez adatbázisok adhatók hozzá vagy vonhatók ki belőle. Ha egy adatbázis kiszámítható módon nem használja ki az erőforrásokat, helyezze át az adatbázist.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>Mikor érdemes egy SQL Database rugalmas készlet?

A készleteket kifejezetten a nagy számú, speciális felhasználási mintákkal rendelkező adatbázisokhoz tervezték. Az egyes adatbázisok mintáit átlagosan alacsony, és viszonylag rendszertelen időközönkénti hirtelen megugró kihasználtság jellemzi.

Minél több adatbázist tud hozzáadni egy készlethez, annál többet takaríthat meg. Az alkalmazás használati mintáitól függően már akár két S3-adatbázissal is megtakarítást érhet el.

A következő szakaszokból megtudhatja, hogyan mérje fel, hogy előnyös-e, ha egy adott adatbázis-gyűjtemény egy készlethez tartozik. A példákban Standard készletek szerepelnek, de ugyanezen elvek alkalmazhatók az Alapszintű és a Premium készletekre is.

### <a name="assessing-database-utilization-patterns"></a>Az adatbázis felhasználási mintáinak felmérése

Az alábbi ábra egy olyan adatbázist mutat be, amely sok időt tölt tétlenül, miközben időszakosan hirtelen megugró kihasználtság jellemzi. Az ilyen kihasználtsági minta esetén használhatók készletek:

   ![egy készletbe illő önálló adatbázis](./media/sql-database-elastic-pool/one-database.png)

Az ábrázolt ötperces periódus során a DB1 adatbázis maximális DTU-használata 90, de az összesített átlagos használati érték öt DTU alatt van. Ilyen számítási feladatok futtatásához önálló adatbázis esetén S3-as teljesítményszintre van szükség, amellyel azonban az alacsony tevékenységű időszakok során az erőforrások legnagyobb része kihasználatlan marad.

Egy készletben ezek a használaton kívüli DTU-k több adatbázis között lehetnek megosztva, így a szükséges DTU-k száma és az összköltség is csökken.

Az előző példa mentén továbbhaladva tegyük fel, hogy további adatbázisokkal rendelkezünk, amelyeket DB1-hez hasonló felhasználási minták jellemeznek. Az alábbi következő két ábrán a négy és 20 adatbázisait használata az, hogy bemutassa a DTU-alapú alapjául szolgáló vásárlási modell használatával időbeli hasznosítását mozaikként, átfedés nélkül jellege ugyanazon diagramhoz rétegben helyezkedik:

   ![négy adatbázis egy készletbe illő kihasználtsági mintával](./media/sql-database-elastic-pool/four-databases.png)

   ![húsz adatbázis egy készletbe illő kihasználtsági mintával](./media/sql-database-elastic-pool/twenty-databases.png)

A DTU-k mind a 20 adatbázisra vonatkozó összesített kihasználtságát a fekete vonal jelzi az előző ábrán. Ez alapján a teljes DTU-használat soha nem lépi túl a 100 DTU értéket, és az időtartam során a 20 adatbázis 100 eDTU-t használhat közösen. Ennek eredményeképpen a DTU-k száma huszadára, a költség pedig tizenharmadára csökken ahhoz képest, mintha minden egyes adatbázis S3 teljesítményszintű önálló adatbázisként működne.

Ez a példa az alábbi okokból ideális:

* Nagy különbségek vannak az adatbázisok átlagos és kiugró mértékű kihasználtsága között.
* Az egyes adatbázisok kiugró mértékű kihasználtsága különböző időpontokban jelentkezik.
* Az eDTU-k több adatbázis között vannak megosztva.

A készletre vonatkozó költség az eDTU-készlet függvénye. A készlethez tartozó eDTU-k egységára egy önálló adatbázis DTU-egységárának másfélszerese, azonban **a készlethez tartozó eDTU-kat sok adatbázis használhatja, így kevesebb eDTU-ra van szükség**. Ezek a díjszabásban és eDTU-megosztásban jelentkező különbségek adják a készletekkel elérhető megtakarítás alapját.

Az adatbázisok számára és kihasználtságára vonatkozó alábbi általános szabályokkal biztosíthatja, hogy a készlet költségcsökkenést eredményezzen az önálló adatbázisok és teljesítményszintek használatához képest.

### <a name="minimum-number-of-databases"></a>Adatbázisok minimális száma

Az önálló adatbázisok erőforrások összesített mennyisége több, mint a készlet szükséges erőforrásokat x 1,5, majd egy rugalmas készlet akkor költséghatékonyabb.

***DTU-alapú vásárlási modell példa***<br>
Legalább két S3-adatbázis vagy legalább 15 S0-adatbázis szükséges ahhoz, hogy egy 100 eDTU-s készlet költséghatékonyabban működjön, mint ha teljesítményszinteket és önálló adatbázisokat használna.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Egyidejűleg kiugró kihasználtságú adatbázisok maximális száma

Az erőforrások megosztása nem minden adatbázis készletben egyidejűleg használhatják legfeljebb a rendelkezésre álló erőforrások az önálló adatbázisok. A kevesebb adatbázis, amely egyidejűleg csúcsidőre, minél kisebb a készlet erőforrások állítható be, és annál költséghatékony a készlet válik. Általában a készletben lévő adatbázisok legfeljebb 2/3 (vagy 67 %) kell egyidejűleg maximális az erőforrások határértéket.

***DTU-alapú vásárlási modell példa***<br>
Ha csökkenteni szeretnénk három S3-adatbázis költségét egy 200 eDTU-s készletben, akkor a háromból egyszerre legfeljebb kettő működhet kiugró kihasználtsággal. Ha ebből a három S3-adatbázisból több mint kettő működik egyszerre kiugró kihasználtsággal, akkor a készletnek több mint 200 eDTU-t kellene tartalmaznia. Ha a készletet 200 eDTU-nál nagyobbra növeljük, akkor több S3-adatbázist kellene hozzáadnunk a készlethez, hogy a költség alacsonyabb legyen, mint ha teljesítményszinteket és önálló adatbázisokat használnánk.

Ne feledje, hogy ebben a példában nem vesszük számításba a készlet egyéb adatbázisainak kihasználását. Ha egy adott időpontban minden adatbázis használatban van valamilyen szinten, akkor az adatbázisok kevesebb mint kétharmad része (vagy 67%-a) működhet egyszerre kiugró kihasználtsággal.

### <a name="resource-utilization-per-database"></a>Adatbázisonként erőforrás-használat
Az adatbázisok kiugró és átlagos kihasználtsága közötti lényeges különbség a hosszú, alacsony kihasználtságú és a rövid magas kihasználtságú időszakokban mutatkozik meg. Ilyen felhasználási minta esetén ideális az erőforrások adatbázisok közötti megosztása. Az adatbázis készletben való használatát akkor érdemes megfontolni, ha a kiugró mértékű kihasználtsága hozzávetőlegesen másfélszer nagyobb az átlagos kihasználtságánál.

***DTU-alapú vásárlási modell példa***<br>
Ha egy 100 DTU-s kiugró kihasználtsággal működő S3-adatbázis átlagosan legfeljebb 67 DTU-t használ, akkor jó jelöltnek számít egy eDTU-kat közösen használó készlethez. Ha pedig egy 20 DTU-s kiugró kihasználtsággal működő S1-adatbázis átlagosan legfeljebb 13 DTU-t használ, akkor jó jelöltnek számít egy készlethez.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Hogyan válassza ki a megfelelő mérete?

A készlet méretének attól függ, hogy az összesítő a készletben található összes adatbázis szükséges erőforrásokat. Ez magában foglalja a meghatározásakor a következők:

* Összes adatbázis a készletben (attól függően, hogy a választott modell resourcing maximális vCores vagy maximális dtu-k) által használt erőforrások maximális száma.
* A készletben szereplő összes adatbázis által használt maximális tárterület (bájtban).

Minden erőforrás-modellje elérhető szolgáltatás rétegei, tekintse meg a [alapjául szolgáló vásárlási modell DTU-alapú](sql-database-service-tiers-dtu.md) vagy a [vCore-alapú alapjául szolgáló vásárlási modell (előzetes verzió)](sql-database-service-tiers-vcore.md).

Az SQL Database automatikusan kiértékeli az SQL Database-kiszolgálók adatbázisainak erőforrás-használati előzményeit, és felajánlja a megfelelő készletkonfigurációt az Azure Portalon. Az ajánlások mellett egy beépített funkciót is használhat, amely megbecsüli a kiszolgáló egyedi adatbáziscsoportjainak eDTU-használatát. Ez alapján lehetőségelemzést végezhet adatbázisok interaktív hozzáadásával és eltávolításával, majd az erőforrás-használati elemzés és a méretezési tanácsok megtekintésével a módosítások véglegesítése előtt. Útmutatás: [Rugalmas készlet felügyelete, kezelése és méretezése](#monitor-an-elastic-pool-and-its-databases).

Ha nincs lehetősége eszközök használatára, az alábbi részletes útmutatóval megbecsülheti, hogy a készlet költséghatékonyabb-e az önálló adatbázisok használatánál:

1. Becsült edtu-k vagy vCores a készlet a következő szükséges:

   A DTU-alapú alapjául szolgáló vásárlási modell: MAX (<*adatbázisok száma* X *DTU-felhasználásban / adatbázis átlagos*>,<br>
   <*A kiugró kihasználtsággal egyszerre működő adatbázisok száma* X *Az egyes adatbázisok kiugró DTU-használata*)

   Az alapjául szolgáló vásárlási modell vCore-alapú (előzetes verzió): MAX (<*adatbázisok száma* X *átlagos vCore használata / adatbázis*>,<br>
   <*Egyidejűleg peaking száma adatbázisok* X *lehessen vCore-felhasználás csúcsidőszakát / adatbázis*)

2. A készlethez szükséges tárterület méretének becsléséhez adja össze a készlet egyes adatbázisaihoz szükséges bájtok számát. Ezután határozza meg a szükséges tárhelyet biztosító eDTU-készlet méretét.
3. A DTU-alapú vásárlási modell igénybe a nagyobb az edtu-k becsült 1. lépés és a 2. lépés. Ahhoz, hogy a vCore-alapú alapjául szolgáló vásárlási modell (előzetes verzió) végrehajtani a vCore becslés 1. lépés.
4. Tekintse meg a [árképzést ismertető oldalra SQL-adatbázis](https://azure.microsoft.com/pricing/details/sql-database/) és a legkisebb készlet, amely méretezés keresés érték nagyobb, mint a becslés a 3. lépés.
5. Hasonlítsa össze az 5. lépésben szereplő készlet árát az önálló adatbázisok megfelelő teljesítményszintjeinek árával.

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Rugalmas készletek más SQL-adatbázis szolgáltatások használata

### <a name="elastic-jobs-and-elastic-pools"></a>Rugalmas feladat és a rugalmas készletek

A készletek használata leegyszerűsíti a felügyeleti feladatokat, mivel a szkriptek **[rugalmas feladatokban](sql-database-elastic-jobs-overview.md)** futtathatók. A rugalmas feladatok használatával kiküszöbölhető a nagy számú adatbázis kezelésével járó monotonitás. Kezdetnek tekintse át [a rugalmas feladatokkal kapcsolatos első lépéseket ismertető](sql-database-elastic-jobs-getting-started.md) témakört.

További információk a több adatbázissal dolgozó további adatbázis-eszközökről: [Horizontális felskálázás az Azure SQL Database-ben](sql-database-elastic-scale-introduction.md).

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Az adatbázisok rugalmas készlethez üzletmenet-folytonossági funkciókat
A készletezett adatbázisok általánosságban ugyanazokat [az üzletmenet-folytonossági funkciókat](sql-database-business-continuity.md) támogatják, amelyek az önálló adatbázisokhoz is elérhetők.

- **Pont időponthoz kötött visszaállítás**: időpontban visszaállítási adatbázis automatikus biztonsági mentés használja egy adott tárolókészlet-adatbázis helyreállítása időben. Lásd: [Időponthoz kötött visszaállítás](sql-database-recovery-using-backups.md#point-in-time-restore)

- **Georedundáns helyreállítás**: Georedundáns helyreállítás alapértelmezett helyreállítási lehetőséget biztosít, ha egy adatbázis nem érhető el a régióban, ahol az adatbázis egy incidens miatt. Lásd: [Az Azure SQL Database visszaállítása vagy feladatátvétel a másodlagos kiszolgálóra](sql-database-disaster-recovery.md)

- **Aktív georeplikáció**: olyan alkalmazások, mint georedundáns helyreállítás kínálhat szigorúbb helyreállítási követelményekkel rendelkező, konfigurálja [aktív georeplikáció](sql-database-geo-replication-overview.md).

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Az Azure portál használatával, új SQL Database rugalmas készlet létrehozása

Az Azure portálon is létrehozhat egy rugalmas készlet két módja van.
1. Létrehozhat egy rugalmas készlet keresve **SQL rugalmas készlet** a a **piactér** vagy kattint **+ Hozzáadás** panelen keresse meg az SQL rugalmas készletek. Tudunk adjon meg egy új vagy meglévő kiszolgáló ezzel a készlettel munkafolyamat kiépítés keresztül.
2. Lépjen a meglévő SQL server csomópontra, majd kattintson egy rugalmas készletet is létrehozhat vagy **készlet létrehozásához** készlet létrehozása közvetlenül az adott kiszolgálóra. Az egyetlen különbség a lépés kihagyásához a kiszolgáló megadhatja a kiépítés munkafolyamat készlet során.

> [!NOTE]
> Egy kiszolgálón több készletet is létrehozhat, de egy készlethez különböző kiszolgálókról származó adatbázisok nem adhat.

A készlet szolgáltatási szintjétől funkciók érhetők el a készletet, és az egyes adatbázisok számára elérhető erőforrások maximális száma elastics határozza meg. További információkért lásd: a rugalmas készletek erőforrás korlátairól a [DTU modell](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-performance-levels). A rugalmas vCore alapuló erőforrás-korlátok, lásd: [vCore-alapú erőforrás korlátok - rugalmas készletek](sql-database-vcore-resource-limits-elastic-pools.md).

Konfigurálja az erőforrásokat, és kattintson a készlet árképzési **készlet beállítása**. Válassza ki a szolgáltatási rétegben, adatbázisok hozzáadása a készlethez, és az erőforrás-határértékeken a készlet és az adatbázisok konfigurálása.

A készlet konfigurálása befejezése után kattintson az "Alkalmaz", a készlet nevét, és kattintson az "OK" gombra a készlet létrehozásához.

## <a name="monitor-an-elastic-pool-and-its-databases"></a>Rugalmas készletek és az adatbázisok figyelése

Az Azure portálon megfigyelheti a rugalmas készletek és a készlethez tartozó adatbázis-felhasználását. Módosítások készlete teheti a rugalmas készlethez és egyszerre az összes változtatás is. Ezen változtatások közé tartozik a Hozzáadás, adatbázisok, a rugalmas készlet beállításainak módosítása, vagy nem módosíthatja az adatbázis-beállításokat.

A rugalmas készlet figyelése, keresse meg, és nyissa meg a rugalmas készletekben a portálon. Először, amely áttekintést nyújt a rugalmas készlet állapotát képernyő jelenik meg. Az érintett műveletek közé tartoznak az alábbiak:

* A rugalmas készlet erőforrások felhasználásának ábrázoló diagramok figyelése
* Legutóbbi riasztások és javaslatok, ha elérhető, a rugalmas készlet

A következő ábrán látható egy példa a rugalmas készlet:

![Készlet megtekintése](./media/sql-database-elastic-pool-manage-portal/basic.png)

Ha azt szeretné, hogy további információt a készlet meg ez az Áttekintés a rendelkezésre álló adatait. Kattintson a **erőforrás-használat** diagram léphet vissza az Azure-figyelési nézet ahol testre szabhatja a metrikák és idő ablakban, a diagramon látható. Kattintson a bármely elérhető értesítések átirányítja egy panel, amely a riasztás vagy javaslat teljes részleteit jeleníti meg.

Ha szeretné a készletben lévő adatbázisok figyelése, rákattinthat a **adatbázis-erőforrás-használat** a a **figyelés** a erőforrás menü a bal oldali részén.

![Adatbázis Erőforrás kihasználtsága lap](./media/sql-database-elastic-pool-manage-portal/db-utilization.png)

### <a name="to-customize-the-chart-display"></a>A diagram megjelenítéséhez

A diagram és más metrikákkal, például a Processzor százalékos, adat IO százalékos és napló IO százalékos használt megjelenítendő metrika lapon szerkesztheti.

Az a **diagram szerkesztése lehetőséget** űrlap, választhat csak egyféle időpont között, vagy kattintson a **egyéni** jelölje ki az elmúlt két hétben semmilyen 24 órás időszak, és válassza ki az erőforrásokat a figyelheti.

### <a name="to-select-databases-to-monitor"></a>Jelölje be az adatbázisok figyelése

Alapértelmezés szerint a diagram a **adatbázis erőforrás-használat** panelen jelennek meg az első 5 adatbázisok DTU vagy Processzor (attól függően, hogy a szolgáltatási réteg). Jelölje ki, az alábbi listán a diagram a négyzet jelölését, a bal oldali keresztül adatbázisok unselecting ezen a diagramon az adatbázisok válthat.

Válassza ki a nézetbe egymás mellett a adatbázistábla ki az adatbázisok teljesítményének teljesebb megjelenítési további metrikák is.

További információkért lásd: [SQL-adatbázis figyelmeztetések létrehozása az Azure-portálon](sql-database-insights-alerts-portal.md).

## <a name="next-steps"></a>További lépések

- Rugalmas készletek méretezési, lásd: [rugalmas készletek skálázás](sql-database-elastic-pool.md) és [méretezhető rugalmas készletek - példakód](scripts/sql-database-monitor-and-scale-pool-powershell.md)
* A videót: [a Microsoft Virtual Academy videó működés során az Azure SQL Database rugalmas képességek](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
* A rugalmas készleteket használó SaaS-alkalmazások szerkezeti kialakításainak alaposabb megismeréséhez olvassa el a [Tervminták több-bérlős SaaS-alkalmazásokhoz Azure SQL Database esetén](sql-database-design-patterns-multi-tenancy-saas-applications.md) című részt.
* A rugalmas készleteket használó SaaS-oktatóanyag, lásd: [bemutatása a Wingtip SaaS-alkalmazáshoz](sql-database-wtp-overview.md).
