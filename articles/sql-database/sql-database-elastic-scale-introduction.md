---
title: Méretezés
description: A szoftverszolgáltatás (SaaS) fejlesztői ezeken az eszközökön könnyedén létrehozhatnak rugalmas, méretezhető adatbázisokat a felhőben
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 44c6147402cbe05e62c091863cb0bd4f9235bfab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061646"
---
# <a name="scaling-out-with-azure-sql-database"></a>Scaling out with Azure SQL Database (Horizontális felskálázás az Azure SQL Database segítségével)
Az Azure SQL-adatbázisok at a **rugalmas** adatbázis-eszközökkel egyszerűen kiskálázhatja. Ezek az eszközök és funkciók lehetővé teszik az **Azure SQL Database** adatbázis-erőforrásainak használatát a tranzakciós számítási feladatok, és különösen a Szoftver szolgáltatásként (SaaS) alkalmazások hozásához. A rugalmas adatbázis-funkciók a következőkből állnak:

* [Rugalmas adatbázis-ügyfélkódtár:](sql-database-elastic-database-client-library.md)Az ügyféltár olyan szolgáltatás, amely lehetővé teszi a szilánkos adatbázisok létrehozását és karbantartását.  Lásd: [Első lépések a rugalmas adatbázis-eszközökkel.](sql-database-elastic-scale-get-started.md)
* [Rugalmas adatbázis-felosztásos egyesítési eszköz:](sql-database-elastic-scale-overview-split-and-merge.md)adatokat mozgat a szilánkos adatbázisok között. Ez az eszköz akkor hasznos, ha az adatok áthelyezése egy több-bérlős adatbázis egy egy-bérlős adatbázis (vagy fordítva). Lásd: [Rugalmas adatbázis Split-Merge eszköz bemutató](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
* [Rugalmas adatbázis-feladatok:](elastic-jobs-overview.md)Feladatok használatával nagy számú Azure SQL-adatbázisok kezelésére. Egyszerűen hajtsa végre a felügyeleti műveleteket, például a sémamódosításokat, a hitelesítő adatok kezelését, a referencia-adatfrissítéseket, a teljesítményadatok gyűjtését vagy a bérlői (ügyfél) telemetriai adatok gyűjtését a feladatok használatával.
* [Rugalmas adatbázis-lekérdezés](sql-database-elastic-query-overview.md) (előzetes verzió): Lehetővé teszi, hogy több adatbázisra kiterjedő Transact-SQL-lekérdezést futtasson. Ez lehetővé teszi a kapcsolatot a jelentéskészítő eszközökkel, például az Excellel, a Power BI-val, a Tableau-val stb.
* [Rugalmas tranzakciók:](sql-database-elastic-transactions-overview.md)Ez a funkció lehetővé teszi, hogy az Azure SQL Database több adatbázisára kiterjedő tranzakciók futtatását. Rugalmas adatbázis-tranzakciók érhetők el a .NET alkalmazások számára az ADO .NET használatával, és integrálhatók a jól ismert programozási környezetbe a [System.Transaction osztályok](https://msdn.microsoft.com/library/system.transactions.aspx)használatával.

A következő ábra egy architektúrát mutat be, amely tartalmazza a **rugalmas adatbázis-funkciókat** az adatbázisok gyűjteményéhez kapcsolódóan.

Ebben a grafikában az adatbázis színei sémákat jelölnek. Az azonos színű adatbázisok ugyanazt a sémát osztják meg.

1. Az **Azure SQL-adatbázisok** készlete az Azure-ban van tárolva a szilánkos architektúra használatával.
2. A **rugalmas adatbázis-ügyfélkódtár** egy szegmenskészlet kezelésére szolgál.
3. Az adatbázisok egy részhalmaza **egy rugalmas készletbe**kerül. (Lásd: [Mi az a medence?](sql-database-elastic-pool.md)).
4. Egy **rugalmas adatbázis-feladat** ütemezett vagy ad hoc T-SQL-parancsfájlokat futtat az összes adatbázison.
5. A **felosztásos egyesítési eszköz** segítségével az adatok áthelyezése az egyik szegmensből a másikba.
6. A **rugalmas adatbázis-lekérdezés** lehetővé teszi, hogy írjon egy lekérdezést, amely a szegmenskészlet összes adatbázisára kiterjed.
7. **Rugalmas tranzakciók** lehetővé teszik, hogy több adatbázisra kiterjedő tranzakciók futtatásához. 

![Rugalmasadatbázis-eszközök][1]

## <a name="why-use-the-tools"></a>Miért használja az eszközöket?
A felhőalapú alkalmazások rugalmasságának és méretezésének elérése egyszerű volt a virtuális gépek és a blobstorage számára – egyszerűen adjon hozzá vagy vonjon ki egységeket, vagy növelje az energiát. De továbbra is kihívást jelent a relációs adatbázisok ban történő, állapotalapú adatfeldolgozás számára. Az alábbi forgatókönyvekben jelentkeztek kihívások:

* A munkaterhelés relációs adatbázis-részének növekvő és csökkenő kapacitása.
* Az adatok egy adott részhalmazát – például egy foglalt végfelhasználót (bérlőt) érintő hotspotok kezelése.

Hagyományosan az ilyen forgatókönyveket az alkalmazás támogatásához nagyobb méretű adatbázis-kiszolgálókba való befektetéssel orvosoltuk. Ez a lehetőség azonban korlátozott a felhőben, ahol az összes feldolgozás előre meghatározott áruhardveren történik. Ehelyett az adatok és a feldolgozás terjesztése számos azonos strukturált adatbázisok (a horizontális felskálázási minta néven ismert horizontális skálázás) alternatívát nyújt a hagyományos horizontális felskálázási megközelítések mind a költség és a rugalmasság szempontjából.

## <a name="horizontal-and-vertical-scaling"></a>Vízszintes és függőleges méretezés
Az alábbi ábrán a skálázás vízszintes és függőleges méretei láthatók, amelyek a rugalmas adatbázisok méretezhető alapvető módjai.

![Vízszintes és függőleges horizontális felskálázás][2]

A horizontális skálázás adatbázisok hozzáadását vagy eltávolítását jelöli a kapacitás vagy az általános teljesítmény , más néven "horizontális felskálázás" módosítása érdekében. Horizontális skálázás, amelyben az adatok azonos strukturált adatbázisok gyűjteménye között van particionálva, a horizontális skálázás megvalósításának gyakori módja.  

A vertikális skálázás az egyes adatbázisok, más néven "vertikális felskálázás" számítási méretének növelését vagy csökkentését nevezi.

A legtöbb felhőszintű adatbázis-alkalmazás e két stratégia kombinációját használja. Például egy szoftver szolgáltatásként alkalmazásként használhat horizontális skálázás t új végfelhasználók és vertikális skálázás, hogy minden végfelhasználó i database-t, hogy növekszik vagy csökken az erőforrásokat, ha szükséges a számítási feladatok.

* A horizontális skálázás kezelése a [Rugalmas adatbázis ügyfélkódtár](sql-database-elastic-database-client-library.md)használatával történik.
* A függőleges skálázás azure PowerShell-parancsmagokkal történik a szolgáltatási szint módosításához, vagy adatbázisok rugalmas készletbe helyezésével.

## <a name="sharding"></a>Sharding
A *horizontális méretezéssel* nagy mennyiségű azonos struktúrájú adatot oszthat el több független adatbázis között. Különösen népszerű a szoftverszolgáltatásként (SAAS) kínáló szoftvereket létrehozó felhőfejlesztők számára a végfelhasználók vagy vállalkozások számára. Ezeket a végfelhasználókat gyakran "bérlőknek" nevezik. A szilánkolásra számos okból lehet szükség:  

* Az adatok teljes mennyisége túl nagy ahhoz, hogy elférjen az egyes adatbázisok korlátai között
* A teljes munkaterhelés tranzakciós átviteli igénye meghaladja az egyes adatbázisok képességeit
* A bérlők fizikai elkülönítést igényelhetnek egymástól, ezért minden bérlőhöz külön adatbázisokra van szükség
* Előfordulhat, hogy az adatbázis különböző részeinek különböző földrajzi területeken kell tartózkodniuk megfelelőségi, teljesítménybeli vagy geopolitikai okokból.

Más esetekben, például az elosztott eszközökről származó adatok betöltése, a szilánkolás segítségével időbelileg rendezett adatbázisok készletét töltheti ki. Például egy külön adatbázis is fellehet őket különítve minden nap vagy hét számára. Ebben az esetben a skálázási kulcs lehet egy egész szám, amely a dátumot jelöli (a szilánkos táblák összes sorában jelen van), és a dátumtartomány információinak lekérdezését az alkalmazásnak a kérdéses tartományt lefedő adatbázisok részhalmazához kell irányítania.

A szilánkolás akkor működik a legjobban, ha egy alkalmazás minden tranzakciója egy szilánkos kulcs egyetlen értékére korlátozható. Ez biztosítja, hogy minden tranzakció helyi egy adott adatbázisban.

## <a name="multi-tenant-and-single-tenant"></a>Több-bérlős és egybérlős
Egyes alkalmazások a legegyszerűbb megközelítést használja egy külön adatbázis létrehozása minden bérlő számára. Ez a megközelítés az **egyetlen bérlő horizontális skálázási minta,** amely elkülönítést, biztonsági mentési és visszaállítási képességet és erőforrás-skálázást biztosít a bérlő részletességével. Az egybérlős skálázás, minden adatbázis egy adott bérlőazonosító érték (vagy ügyfélkulcs-érték), de a kulcs nem mindig jelen van az adatok ban is. Az alkalmazás felelőssége, hogy minden kérelmet a megfelelő adatbázisba irányítson , és az ügyfélkódtár egyszerűsítheti ezt.

![Egy-bérlő és több-bérlős][4]

Más forgatókönyvek több bérlőt csomagolnak össze adatbázisokba, ahelyett, hogy külön adatbázisokba külön-külön elkülönítenék őket. Ez a minta egy tipikus **több-bérlős skálázási minta** , és lehet, hogy az a tény, hogy egy alkalmazás kezeli a kis bérlők nagy számban. Több-bérlős szilánkok, az adatbázis-táblák sorai mindegyike úgy van kialakítva, hogy a bérlőazonosítót vagy a skálázási kulcsot azonosító kulcs hordozása. Ismét az alkalmazásszint felelős a bérlő imszámára a megfelelő adatbázisba történő útválasztásáért, és ezt a rugalmas adatbázis-ügyfélkódtár is támogathatja. Ezenkívül a sorszintű biztonság segítségével szűrhető, hogy az egyes bérlők mely sorokhoz férhetnek hozzá – a részletekért lásd: [Több-bérlős alkalmazások rugalmas adatbázis-eszközökkel és sorszintű biztonsággal.](sql-database-elastic-tools-multi-tenant-row-level-security.md) Az adatok adatbázisok közötti újraelosztása szükség lehet a több-bérlős skálázási minta, és megkönnyíti a rugalmas adatbázis felosztás-egyesítési eszköz. A rugalmas készleteket használó SaaS-alkalmazások szerkezeti kialakításainak alaposabb megismeréséhez olvassa el a [Tervminták több-bérlős SaaS-alkalmazásokhoz Azure SQL Database esetén](sql-database-design-patterns-multi-tenancy-saas-applications.md) című részt.

### <a name="move-data-from-multiple-to-single-tenancy-databases"></a>Adatok áthelyezése több-egybérlős adatbázisokból
SaaS-alkalmazások létrehozásakor jellemző, hogy a leendő ügyfelek számára a szoftver próbaverzióját kínálják. Ebben az esetben költséghatékony egy több-bérlős adatbázis használata az adatokhoz. Azonban ha egy potenciális vevő ügyfélné válik, az egybérlős adatbázis jobb, mivel jobb teljesítményt nyújt. Ha az ügyfél a próbaidőszak alatt hozott létre adatokat, a [felosztásos egyesítési eszközzel](sql-database-elastic-scale-overview-split-and-merge.md) helyezze át az adatokat a több-bérlős adatbázisból az új egybérlős adatbázisba.

## <a name="next-steps"></a>További lépések
Az ügyféltárat bejelző mintaalkalmazásról az [Arugalmas adatbázis-eszközök – Első lépések a rugalmas adatbázis-eszközökkel című témakörben](sql-database-elastic-scale-get-started.md)található.

Ha a meglévő adatbázisokat az eszközök használatára szeretné konvertálni, olvassa el a [Meglévő adatbázisok áttelepítése horizontális felskálázáshoz](sql-database-elastic-convert-to-use-elastic-tools.md)című témakört.

A rugalmas készlet sajátosságainak megtekintéséhez olvassa el a [rugalmas készlet ár- és teljesítményszempontjai](sql-database-elastic-pool.md)című témakört, vagy hozzon létre egy [új, rugalmas készletekkel rendelkező készletet.](sql-database-elastic-pool-manage-portal.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-introduction/tools.png
[2]:./media/sql-database-elastic-scale-introduction/h_versus_vert.png
[3]:./media/sql-database-elastic-scale-introduction/overview.png
[4]:./media/sql-database-elastic-scale-introduction/single_v_multi_tenant.png

