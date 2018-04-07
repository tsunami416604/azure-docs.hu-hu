---
title: Az Azure SQL Database méretezési out |} Microsoft Docs
description: A szolgáltatott szoftverként (SaaS) fejlesztők szoftverként egyszerűen létrehozhat rugalmas, méretezhető adatbázisok a felhőben, ezek az eszközök használatával
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 4944c1c017dbb59b7880a73bce7d0a9b0d972b3f
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="scaling-out-with-azure-sql-database"></a>Scaling out with Azure SQL Database (Horizontális felskálázás az Azure SQL Database segítségével)
Azure SQL Database-adatbázisok könnyen lehet horizontálisan a **Elastic Database** eszközök. Az eszközök és a szolgáltatások lehetővé teszik, hogy az adatbázis-erőforrások használata **Azure SQL Database** megoldások tranzakciós munkaterhelések, és különösen a szoftver egy szolgáltatott szoftverként (SaaS) alkalmazásokként létrehozásához. A rugalmas adatbázis-szolgáltatások álló a:

* [A rugalmas adatbázis ügyféloldali kódtár](sql-database-elastic-database-client-library.md): az ügyféloldali kódtár olyan szolgáltatás, amely lehetővé teszi a szilánkos adatbázisok karbantartását.  Lásd: [Ismerkedés a rugalmas adatbáziseszközöket](sql-database-elastic-scale-get-started.md).
* [A rugalmas adatbázis vegyes egyesítéses eszköz](sql-database-elastic-scale-overview-split-and-merge.md): szilánkos adatbázisok között mozgatja az adatokat. Ez akkor hasznos, az adatok áthelyezése egy több-bérlős-adatbázisból egy egyetlen-adatbázis (vagy fordítva). Lásd: [rugalmas adatbázis vegyes egyesítéses eszköz oktatóanyag](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
* [Rugalmas adatbázis-feladatok](sql-database-elastic-jobs-overview.md) (előzetes verzió): Azure SQL-adatbázisok nagyszámú feladatok segítségével. Egyszerűbben végezhetők el a felügyeleti műveletek, például a sémamódosítások, a hitelesítő adatok kezelése, a hivatkozás frissítéseket, a teljesítményadat-gyűjtés vagy a bérlő (ügyfél) telemetriai adatgyűjtési feladatok segítségével.
* [A rugalmas adatbázis-lekérdezés](sql-database-elastic-query-overview.md) (előzetes verzió): lehetővé teszi több adatbázist is Transact-SQL-lekérdezés futtatható. Ez lehetővé teszi, hogy a kapcsolat jelentéskészítési eszközök, például az Excel, a Power BI, a Tableau, stb.
* [Rugalmas tranzakciók](sql-database-elastic-transactions-overview.md): Ez a funkció lehetővé teszi az Azure SQL Database adatbázisok kiterjedő tranzakciók futtatását. Rugalmas adatbázis-tranzakció érhetők el a .NET-alkalmazásokban ADO .NET használatával, és a megszokott programozási élmény segítségével integrálja a [System.Transaction osztályok](https://msdn.microsoft.com/library/system.transactions.aspx).

A következő ábrán látható egy architektúra, amely tartalmazza a **rugalmas adatbázis-szolgáltatások** viszonyítva adatbázisok gyűjteménye.

A kép színeit az adatbázis sémák határoz meg. Ugyanazzal a színnel adatbázisok ugyanazon séma megosztani.

1. Egy **Azure SQL-adatbázisok** Azure-ban horizontális architektúrát működtetnek.
2. A **Elastic Database ügyféloldali kódtárának** shard kezelésére szolgál.
3. Az adatbázisok egy részhalmazát engedélyeznie kell egy **rugalmas készlet**. (Lásd: [Mi az, hogy a tárolókészlet?](sql-database-elastic-pool.md)).
4. Egy **rugalmas feladat** ütemezett vagy alkalmi T-SQL parancsfájlt futtat minden adatbázisokhoz.
5. A **vegyes egyesítéses eszköz** adatok áthelyezése egy shard másik szolgál.
6. A **rugalmas adatbázis-lekérdezés** lehetővé teszi, hogy az összes adatbázis shard készletében is.
7. **Rugalmas tranzakciók** teszik tranzakciók, amelyek több több adatbázis futtatását. 

![Rugalmasadatbázis-eszközök][1]

## <a name="why-use-the-tools"></a>Az eszközök miért érdemes használni?
Egyszerű, a virtuális gépek és a blob-tároló elérése érdekében a rugalmasság és a felhőalapú alkalmazások számára is méretezhető lett - egyszerűen hozzáadása vagy kivonás egységek, vagy növelje a power. Azonban ez az állapot-nyilvántartó adatok feldolgozása a relációs adatbázisok kihívást maradt. Kihívást kiderült, a következő használati helyzetekben:

* A számítási feladatok relációs adatbázis része kapacitása zsugorítását, és inkább.
* Az adatok – például egy foglalt end-ügyfél (bérlő) egy adott részének érintő esetleg felmerülő elérési pontokhoz való kezelése.

Hagyományosan helyzetekben, például ezek eddig a nagyobb méretű adatbázis-kiszolgálók támogatásához az alkalmazás által az említett. Azonban ez a beállítás korlátozza a felhőben, ahol minden feldolgozás történik, az előre definiált hagyományos hardvereken. Ehelyett terjesztésére adatokat, és több azonos strukturált adatbázis közötti feldolgozása (a kibővített minta "horizontális" néven ismert) köszönhetően a hagyományos méretezett megközelítések költség-és a rugalmasság.

## <a name="horizontal-and-vertical-scaling"></a>Vízszintes és függőleges skálázás
Az alábbi ábrán a vízszintes és függőleges dimenziók a méretezés, amely az alapvető módon, a rugalmas adatbázisok is méretezhető.

![Vízszintes és függőleges kibővítési][2]

Horizontális skálázás hivatkozik hozzáadásával vagy eltávolításával adatbázisok ahhoz, hogy módosítsa a kapacitás vagy általános teljesítményét. Ez is feltüntettük "skálázás". Horizontális, amelyben adatok particionálása keresztül azonos strukturált adatbázisok gyűjteménye közös módja a horizontális skálázás végrehajtásához.  

Függőleges skálázás bővítése vagy csökkentése az egyes adatbázisok teljesítményszintjét hivatkozik – ezt is nevezik "vertikális felskálázásával."

A legtöbb felhőméretű adatbázis-alkalmazások ezen két stratégiák együtt használja. Például a szoftver a szolgáltatásalkalmazás előfordulhat, hogy segítségével a függőleges skálázás és horizontális skálázás új végfelhasználóhoz kiépítéséhez minden end felhasználói adatbázis növekedhet és csökkenhet az erőforrásokat, a munkaterhelés szerint igény szerint.

* Horizontális skálázás kezeli a [Elastic Database ügyféloldali kódtárának](sql-database-elastic-database-client-library.md).
* Függőleges skálázás úgy hajthatja végre, Azure PowerShell-parancsmagok használatával módosíthatja a szolgáltatásszintet, vagy úgy, hogy adatbázisok rugalmas készlethez.

## <a name="sharding"></a>Sharding
*Horizontális* technika, nagy mennyiségű azonos strukturált adatok szét egy független adatbázisok számát. Általában különösen szoftver létrehozása a végfelhasználók vagy a vállalatok számára a szolgáltatás (SAAS) ajánlatok felhő fejlesztőkkel. Ezen end ügyfelek gyakran "bérlő" nevezik. Horizontális szükséges bármely számos oka lehet:  

* Adatok teljes mérete túl nagy belül egy önálló adatbázis
* A tranzakció átviteli képessége – a teljes munkaterhelés meghaladja az egy önálló adatbázis képességeit
* Bérlők szükség lehet egymástól, fizikai elkülönítése, így külön adatbázisok van szükség az egyes bérlők számára
* Adatbázis különböző szakaszaiban kell lennie, a megfelelőségi, a teljesítmény vagy a geopolitikai okok különböző földrajzi területeken.

Más esetekben, például az adatfeldolgozást az elosztott eszközökről származó adatok horizontális segítségével töltse ki a adatbázisok készleteit tartalmazó ideiglenesen vannak rendezve. Például egy másik adatbázist is számára dedikált, minden nap vagy hét. Ebben az esetben a horizontális kulcs (a szilánkos tábla összes sora szerepel) dátumot jelölő egész szám lehet, és az alkalmazást a szóban forgó tartományát adatbázisok részhalmaz kell továbbítani, lekérdezések dátumtartomány adatainak lekérése során.

Horizontális működik optimálisan, ha az alkalmazás minden tranzakció korlátozható egy horizontális skálázási kulcs egyetlen értéket. Amely biztosítja, hogy minden tranzakció helyi adott adatbázishoz.

## <a name="multi-tenant-and-single-tenant"></a>Több-bérlős és a bérlői egyetlen
Egyes alkalmazások használata a legegyszerűbb módszere az önálló adatbázist hoz létre az egyes bérlők számára. Ez a **egybérlős horizontális mintát** elkülönítési, biztonsági mentés/visszaállítás lehetősége és a bérlő részletességű skálázás erőforrás biztosít. Egyetlen bérlői horizontális az egyes adatbázisok vagy társítva egy adott bérlő azonosító értékét (felhasználói kulcs értékét), de kulcs nem minden esetben kell az adatokat mozgatná szerepel. Az alkalmazás felelőssége, hogy egyes kérelmek átirányítása a megfelelő adatbázis - és az ügyféloldali kódtár egyszerűbbé teheti a.

![Több-bérlős és egybérlős][4]

Más forgatókönyvek csomag több bérlő együtt az adatbázisok, nem pedig az önálló adatbázisok elszigetelése őket. Ez az egy tipikus **több-bérlős horizontális mintát** - és azt a tényt, hogy egy alkalmazás kezeli-e nagy mennyiségű kis bérlők vezethetik. A több-bérlős horizontális a sorokat a táblák összes tervezték, hogy a kulcs, amely azonosítja a bérlő azonosítója vagy horizontális kulcs. Ebben az esetben az alkalmazás réteg felelős a bérlőkérést útválasztási a megfelelő adatbázishoz, és ez a rugalmas adatbázis ügyféloldali kódtár támogatja. Emellett a sorszintű biztonság használható-e a szűrő mely sorai, minden bérlői hozzáférhessenek - további információkért lásd: [több-bérlős alkalmazásokhoz a rugalmas adatbáziseszközöket és sorszintű biztonság](sql-database-elastic-tools-multi-tenant-row-level-security.md). Adatbázisok között terjesztése lehet szükség a több-bérlős horizontális mintával, és ez megkönnyíti a rugalmas adatbázis vegyes egyesítéses eszköz. A rugalmas készleteket használó SaaS-alkalmazások szerkezeti kialakításainak alaposabb megismeréséhez olvassa el a [Tervminták több-bérlős SaaS-alkalmazásokhoz Azure SQL Database esetén](sql-database-design-patterns-multi-tenancy-saas-applications.md) című részt.

### <a name="move-data-from-multiple-to-single-tenancy-databases"></a>Adatok áthelyezése több single-bérlőhöz adatbázisokhoz
Egy SaaS-alkalmazás létrehozása, esetén jellemző, a lehetséges ügyfelek felajánlott a szoftver próbaverzióját. Ebben az esetben is költséghatékony, hogy egy több-bérlős adatbázisban az adatok. Azonban az ügyfél egy potenciális válásakor single-bérlő adatbázis jobb, mivel jobb teljesítményt nyújt. Ha a felhasználói adatok hozna létre, a próbaidőszak során, használja a [vegyes egyesítéses eszköz](sql-database-elastic-scale-overview-split-and-merge.md) a több-bérlős az adatok áthelyezése az új single-bérlő adatbázis.

## <a name="next-steps"></a>További lépések
Azt mutatja be, az ügyféloldali kódtár mintaalkalmazást, lásd: [Ismerkedés a rugalmas adatbáziseszközöket](sql-database-elastic-scale-get-started.md).

További információ meglévő adatbázisok használata az eszközök átalakításáról: [telepítse át a meglévő adatbázisok horizontális](sql-database-elastic-convert-to-use-elastic-tools.md).

A rugalmas készlet mintaadatokról, olvassa el [rugalmas készletek ára és teljesítménye szempontok](sql-database-elastic-pool.md), vagy hozzon létre egy új készletet a [rugalmas készletek](sql-database-elastic-pool-manage-portal.md).  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-introduction/tools.png
[2]:./media/sql-database-elastic-scale-introduction/h_versus_vert.png
[3]:./media/sql-database-elastic-scale-introduction/overview.png
[4]:./media/sql-database-elastic-scale-introduction/single_v_multi_tenant.png

