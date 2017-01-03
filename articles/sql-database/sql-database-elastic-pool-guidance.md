---
title: "Mikor ajánlott a rugalmas készletek használata?"
description: "A rugalmas készlet olyan elérhető erőforrások gyűjteménye, amelyet rugalmas adatbázisok egy csoportja közösen használ. Ez a dokumentum útmutatást nyújt az adatbázisok egy csoportja által használt rugalmas készletek megfelelőségének felméréséhez."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 3d3941d5-276c-4fd2-9cc1-9fe8b1e4c96c
ms.service: sql-database
ms.custom: multiple databases
ms.devlang: NA
ms.date: 08/08/2016
ms.author: sstein
ms.workload: data-management
ms.topic: get-started-article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: 591272fa55d4e3989c95323f3ef65c4508e2822f


---
# <a name="when-should-an-elastic-pool-be-used"></a>Mikor ajánlott a rugalmas készletek használata?
Az adatbázis használati mintái, illetve a rugalmas készlet és az önálló adatbázisok közötti díjszabásbeli különbségek alapján mérje fel, hogy a rugalmas készlet használata költséghatékony megoldás-e. A meglévő SQL Database-adatbázisokhoz szükséges készlet aktuális méretének meghatározásához további útmutatás érhető el.  

* A készletek áttekintését lásd: [Rugalmas készletek az SQL Database-ben](sql-database-elastic-pool.md).

> [!NOTE]
> A rugalmas készletek minden Azure-régióban általánosan elérhetők, kivéve Nyugat-Indiát, ahol a szolgáltatás jelenleg előzetes verzióként érhető el.  A rugalmas készletek ebben a régióban a lehető leghamarabb általánosan elérhetővé válnak.
> 
> 

## <a name="elastic-pools"></a>rugalmas készletek
A SaaS-fejlesztők több adatbázisból álló nagyméretű adatrétegekre építenek alkalmazásokat. Gyakori alkalmazásminta az önálló adatbázis biztosítása minden egyes ügyfél számára. A különböző ügyfelek felhasználási mintája nagymértékben és kiszámíthatatlan módon változik, ezért nehéz megjósolni az adatbázis minden egyes felhasználójának erőforrásigényét. Így előfordulhat, hogy a fejlesztő a kedvező kapacitás biztosítása érdekében a szükségesnél több erőforrást helyez üzembe. A fejlesztő esetleg a kiadások csökkentése mellett is dönthet, amely esetben viszont fennáll a gyenge teljesítmény esélye. A rugalmas készleteket használó SaaS-alkalmazások szerkezeti kialakításainak alaposabb megismeréséhez olvassa el a [Tervminták több-bérlős SaaS-alkalmazásokhoz Azure SQL Database esetén](sql-database-design-patterns-multi-tenancy-saas-applications.md) című részt.

Az Azure SQL Database rugalmas készleteivel az SaaS-fejlesztők az előre meghatározott költségvetésen belül maradva optimalizálhatják az adatbáziscsoportok ár-teljesítmény arányát, és rugalmas teljesítményt biztosíthatnak az egyes adatbázisokhoz. A készleteknek köszönhetően a fejlesztő rugalmas Database Transaction Unitokat (eDTU) vásárolhat a több adatbázis által közösen használt készlethez, hogy kiszolgálhassa az önálló adatbázisok kiszámíthatatlan felhasználási periódusait. Egy készlet eDTU-igényét az egyes adatbázisok összesített kihasználtsága határozza meg. A készlet által elérhető eDTU-k mennyiségét a fejlesztői költségvetés határozza meg. A készletek révén a fejlesztők könnyedén dönthetnek a költségvetés és a teljesítmény kölcsönhatásáról a készlet szempontjából. A fejlesztő egyszerűen adatbázisokat ad a készlethez, megadja az adatbázisok által elérhető eDTU-k minimális és maximális számát, majd a költségvetés alapján beállítja a készlethez tartozó eDTU-k számát. A készletek segítségével a fejlesztő zökkenőmentesen és fokozatosan növelheti szolgáltatásának teljesítményét a korlátozott erőforrásokkal bíró startupok szintjéről az érett vállalkozások szintjére.  

## <a name="when-to-consider-a-pool"></a>Mikor érdemes megfontolni a készlet használatát
A készleteket kifejezetten a nagy számú, speciális felhasználási mintákkal rendelkező adatbázisokhoz tervezték. Az egyes adatbázisok mintáit átlagosan alacsony, és viszonylag rendszertelen időközönkénti hirtelen megugró kihasználtság jellemzi.

Minél több adatbázist tud hozzáadni egy készlethez, annál többet takaríthat meg. Az alkalmazás használati mintáitól függően már akár két S3-adatbázissal is megtakarítást érhet el.  

A következő szakaszokból megtudhatja, hogyan mérje fel, hogy előnyös-e, ha egy adott adatbázis-gyűjtemény egy készlethez tartozik. A példákban Standard készletek szerepelnek, de ugyanezen elvek alkalmazhatók az Alapszintű és a Premium készletekre is.

### <a name="assessing-database-utilization-patterns"></a>Az adatbázis felhasználási mintáinak felmérése
Az alábbi ábra egy olyan adatbázist mutat be, amely sok időt tölt tétlenül, miközben időszakosan hirtelen megugró kihasználtság jellemzi. Az ilyen kihasználtsági minta esetén a készletek jól használhatók:

   ![egy készletbe illő önálló adatbázis](./media/sql-database-elastic-pool-guidance/one-database.png)

A fent ábrázolt ötperces periódus során a DB1 adatbázis maximális DTU-használata 90, de az összesített átlagos használati érték öt DTU alatt van. Ilyen számítási feladatok futtatásához önálló adatbázis esetén S3-as teljesítményszintre van szükség, amellyel azonban az alacsony tevékenységű időszakok során az erőforrások legnagyobb része kihasználatlan marad.

Egy készletben ezek a használaton kívüli DTU-k több adatbázis között lehetnek megosztva, így az összes szükséges DTU száma és az összköltség is csökken.

Az előző példa mentén továbbhaladva tegyük fel, hogy további adatbázisokkal rendelkezünk, amelyeket DB1-hez hasonló felhasználási minták jellemeznek. A következő két ábrán a négy és a 20 adatbázis kihasználtsági adatait ugyanazon a grafikonon jelenítettük meg, így látható, hogy az adatbázisok kihasználtsága időben nem fedi egymást:

   ![négy adatbázis egy készletbe illő kihasználtsági mintával](./media/sql-database-elastic-pool-guidance/four-databases.png)

   ![húsz adatbázis egy készletbe illő kihasználtsági mintával](./media/sql-database-elastic-pool-guidance/twenty-databases.png)

A DTU-k mind a 20 adatbázisra vonatkozó összesített kihasználtságát a fekete vonal jelzi a fenti ábrán. Ez alapján a teljes DTU-használat soha nem lépi túl a 100 DTU értéket, és az időtartam során a 20 adatbázis 100 eDTU-t használhat közösen. Ennek eredményeképpen a DTU-k száma huszadára, a költség pedig tizenharmadára csökken ahhoz képest, mintha minden egyes adatbázis S3 teljesítményszintű önálló adatbázisként működne.

Ez a példa az alábbi okokból ideális:

* Nagy különbségek vannak az adatbázisok átlagos és kiugró mértékű kihasználtsága között.  
* Az egyes adatbázisok kiugró mértékű kihasználtsága különböző időpontokban jelentkezik.
* Az eDTU-k sok adatbázis között vannak megosztva.

A készletre vonatkozó költség az eDTU-készlet függvénye. A készlethez tartozó eDTU-k egységára egy önálló adatbázis DTU-egységárának másfélszerese, azonban **a készlethez tartozó eDTU-kat sok adatbázis használhatja, így a legtöbb esetben kevesebb eDTU-ra van szükség**. Ezek a díjszabásban és eDTU-megosztásban jelentkező különbségek adják a készletekkel elérhető megtakarítás alapját.  

Az adatbázisok számára és kihasználtságára vonatkozó alábbi általános szabályokkal biztosíthatja, hogy a készlet költségcsökkenést eredményezzen az önálló adatbázisok és teljesítményszintek használatához képest.

### <a name="minimum-number-of-databases"></a>Adatbázisok minimális száma
Ha az önálló adatbázisok teljesítményszintjeihez tartozó DTU-k száma több mint másfélszerese a készlethez szükséges eDTU-k összegének, akkor a költséghatékonyabb megoldás a rugalmas készlet használata. Az elérhető méreteket lásd: [Rugalmas készletek és rugalmas adatbázisok eDTU- és tárterületi korlátozásai](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases).

***Példa***<br>
Legalább két S3-adatbázis vagy legalább 15 S0-adatbázis szükséges ahhoz, hogy egy 100 eDTU-s készlet költséghatékonyabban működjön, mint ha teljesítményszinteket és önálló adatbázisokat használna.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Egyidejűleg kiugró kihasználtságú adatbázisok maximális száma
Az eDTU-k megosztásával a készlethez tartozó adatbázisok nem használhatják ki mind egyszerre az önálló adatbázisok és teljesítményszintek használata esetén elérhető maximális eDTU-kapacitást. Minél kevesebb adatbázis működik egyszerre kiugró kihasználtsággal, annál alacsonyabbra állítható be a készlet eDTU-inak száma, és annál költséghatékonyabbá válik a készlet. Általánosságban a készletben szereplő adatbázisok legfeljebb 2/3 része (67%-a) működhet egyszerre a maximális eDTU-számmal.

***Példa***<br>
Ha csökkenteni szeretnénk három S3-adatbázis költségét egy 200 eDTU-s készletben, akkor a háromból egyszerre legfeljebb kettő működhet kiugró kihasználtsággal.  Ha ebből a három S3-adatbázisból több mint kettő működik egyszerre kiugró kihasználtsággal, akkor a készletnek több mint 200 eDTU-t kellene tartalmaznia.  Ha pedig a készletet 200 eDTU-nál nagyobbra növeljük, akkor több S3-adatbázist kellene hozzáadnunk a készlethez, hogy a költség alacsonyabb legyen, mint ha teljesítményszinteket és önálló adatbázisokat használnánk.  

Ne feledje, hogy ebben a példában nem vesszük számításba a készlet egyéb adatbázisainak kihasználását. Ha egy adott időpontban minden adatbázis használatban van valamilyen szinten, akkor az adatbázisok kevesebb mint kétharmad része (vagy 67%-a) működhet egyszerre kiugró kihasználtsággal.

### <a name="dtu-utilization-per-database"></a>DTU-használat adatbázisonként
Az adatbázisok kiugró és átlagos kihasználtsága közötti lényeges különbség a hosszú, alacsony kihasználtságú és a rövid magas kihasználtságú időszakokban mutatkozik meg. Ilyen felhasználási minta esetén ideális az erőforrások adatbázisok közötti megosztása. Az adatbázis készletben való használatát akkor érdemes megfontolni, ha a kiugró mértékű kihasználtsága hozzávetőlegesen másfélszer nagyobb az átlagos kihasználtságánál.

***Példa***<br>
Ha egy 100 DTU-s kiugró kihasználtsággal működő S3-adatbázis átlagosan legfeljebb 67 DTU-t használ, akkor jó jelöltnek számít egy eDTU-kat közösen használó készlethez.  Ha pedig egy 20 DTU-s kiugró kihasználtsággal működő S1-adatbázis átlagosan legfeljebb 13 DTU-t használ, akkor jó jelöltnek számít egy készlethez.

## <a name="sizing-an-elastic-pool"></a>Rugalmas készlet méretezése
A készlet optimális mérete a benne szereplő adatbázisokhoz szükséges eDTU-k és tárolási erőforrások mennyiségétől függ. Ehhez meg kell állapítani, hogy az alábbiak közül melyik értéke a nagyobb:

* A készletben szereplő összes adatbázis által használt DTU-k maximális száma.
* A készletben szereplő összes adatbázis által használt maximális tárterület (bájtban).

Az elérhető méreteket lásd: [Rugalmas készletek és rugalmas adatbázisok eDTU- és tárterületi korlátozásai](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases).

Az SQL Database automatikusan kiértékeli az SQL Database-kiszolgálók adatbázisainak erőforrás-használati előzményeit, és felajánlja a megfelelő készletkonfigurációt az Azure Portalon. Az ajánlások mellett egy beépített funkciót is használhat, amely megbecsüli a kiszolgáló egyedi adatbáziscsoportjainak eDTU-használatát. Ez alapján lehetőségelemzést végezhet adatbázisok interaktív hozzáadásával és eltávolításával, majd az erőforrás-használati elemzés és a méretezési tanácsok megtekintésével a módosítások véglegesítése előtt. Útmutatás: [Rugalmas készlet felügyelete, kezelése és méretezése](sql-database-elastic-pool-manage-portal.md).

A 12-es verziónál korábbi kiszolgálókon alkalmi méretezési becsléseket lehetővé tevő, rugalmasabb erőforrás-használati értékelések, valamint a különböző kiszolgálókon található adatbázisok méretezési becslései: [Powershell-szkript a rugalmas készletben való használatra alkalmas adatbázisok azonosításához](sql-database-elastic-pool-database-assessment-powershell.md).

| Képesség | Portal-felület | PowerShell-szkript |
|:--- |:--- |:--- |
| Részletesség |15 másodperc |15 másodperc |
| Figyelembe veszi a készlet, valamint a teljesítményszintek és önálló adatbázisok használata közötti díjszabásbeli különbségeket |Igen |Nem |
| Az elemzett adatbázisok listája testre szabható |Igen |Igen |
| Az elemzésben használt időszak testre szabható |Nem |Igen |
| A különböző kiszolgálókon elemzett adatbázisok listája testre szabható |Nem |Igen |
| A 11-es verziójú kiszolgálókon elemzett adatbázisok listája testre szabható |Nem |Igen |

Ha nincs lehetősége eszközök használatára, az alábbi részletes útmutatóval megbecsülheti, hogy a készlet költséghatékonyabb-e az önálló adatbázisok használatánál:

1. A készlethez szükséges eDTU-k számát a következőképpen becsülje meg:
   
   MAX(<*Az adatbázisok teljes száma* X *Az egyes adatbázisok átlagos DTU-használata*>,<br>
   <*A kiugró kihasználtsággal egyszerre működő adatbázisok száma* X *Az egyes adatbázisok kiugró DTU-használata*)
2. A készlethez szükséges tárterület méretének becsléséhez adja össze a készlet egyes adatbázisaihoz szükséges bájtok számát.  Ezután határozza meg a szükséges tárhelyet biztosító eDTU-készlet méretét.  További információ a készlet az eDTU-készlet mérete alapján meghatározott tárterületi korlátozásairól: [Rugalmas készletek és rugalmas adatbázisok eDTU- és tárterületi korlátozásai](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases).
3. Vegye az 1. és a 2. lépésben meghatározott eDTU-becslések közül a nagyobbat.
4. Látogassa meg az [SQL Database díjszabási oldalát](https://azure.microsoft.com/pricing/details/sql-database/) és keresse meg a legkisebb eDTU-val rendelkező készletméretet, amely nagyobb a 3. lépésben megbecsült értéknél.
5. Hasonlítsa össze az 5. lépésben szereplő készlet árát az önálló adatbázisok megfelelő teljesítményszintjeinek árával.

## <a name="summary"></a>Összefoglalás
Nem minden önálló adatbázis optimális a készletben való használatra. A legjobb jelöltek azok az adatbázisok, amelyek használati mintázatát alacsony átlagos kihasználtság és viszonylag rendszertelen kiugró kihasználtság jellemzi. Az alkalmazáshasználati minták dinamikusak, így az ebben a cikkben tárgyalt információkkal és eszközökkel felmérheti, hogy a készlet használata jó döntés-e az adatbázisok egy része vagy egésze szempontjából. Ez a cikk kiindulási pontot annak eldöntéséhez, hogy célszerű-e a rugalmas készlet használata. Ne feledje, hogy folyamatosan figyelnie kell az erőforrások használati előzményeit, és folyamatosan újra kell értékelnie az adatbázisok teljesítményszintjeit. Vegye figyelembe, hogy az adatbázisok könnyedén elhelyezhetők a rugalmas készletekben, illetve eltávolíthatók onnan, ha pedig rendkívül sok adatbázissal rendelkezik, akkor több különböző méretű készletben is eloszthatja őket.

## <a name="next-steps"></a>Következő lépések
* [Rugalmas készlet létrehozása](sql-database-elastic-pool-create-portal.md)
* [Rugalmas készlet figyelése, kezelése és méretezése](sql-database-elastic-pool-manage-portal.md)
* [Az SQL Database beállításai és teljesítménye: mi érhető el az egyes szolgáltatásszinteken](sql-database-service-tiers.md)
* [Powershell-szkript a rugalmas készletben való használatra alkalmas adatbázisok azonosításához](sql-database-elastic-pool-database-assessment-powershell.md)




<!--HONumber=Dec16_HO2-->


