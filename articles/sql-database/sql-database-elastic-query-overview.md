---
title: Az Azure SQL Database rugalmas lekérdezés – áttekintés |} A Microsoft Docs
description: Rugalmas lekérdezés lehetővé teszi a több adatbázisra kiterjedő Transact-SQL-lekérdezés futtatásához.
services: sql-database
manager: craigg
author: MladjoA
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: mlandzic
ms.openlocfilehash: a57e354651255e2d3ff723e978222d1a5a9fa002
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45733346"
---
# <a name="azure-sql-database-elastic-query-overview-preview"></a>Az Azure SQL Database rugalmas lekérdezésének áttekintése (előzetes verzió)

A rugalmas lekérdezés funkciójának (előzetes verzió) lehetővé teszi, hogy egy Azure SQL Database-ben több adatbázisra kiterjedő Transact-SQL-lekérdezés futtatásához. Lehetővé teszi a távoli táblákat elérő és a csatlakozás a Microsoft és külső eszközöket (az Excel, a Power BI, Tableau stb.) a lekérdezés több adat szintet több adatbázis adatbázisközi lekérdezések végrehajtásához. Ez a szolgáltatás nagy mennyiségű adat szint az SQL Database-lekérdezések horizontális, és az eredményeket az üzleti intelligenciára épülő (BI) jelentések megjelenítése.

## <a name="why-use-elastic-queries"></a>Miért érdemes használni a rugalmas lekérdezések

### <a name="azure-sql-database"></a>Azure SQL Database

T-SQL teljes egészében az Azure SQL-adatbázisok lekérdezésére. Ez lehetővé teszi a távoli adatbázis csak olvasható lekérdezését, és az aktuális a helyszíni SQL Server ügyfelek három és négy part nevét vagy az SQL DB-hez csatolt kiszolgáló használatával alkalmazásokat telepíthet át lehetőséget biztosít.

### <a name="available-on-standard-tier"></a>A standard szinten elérhető

Rugalmas lekérdezés mind a Standard és prémium szolgáltatási szinteken támogatott. Lásd az alábbi előzetes verzió korlátozásai teljesítmény korlátozásai alacsonyabb szolgáltatási szinteken.

### <a name="push-parameters-to-remote-databases"></a>Küldési paraméterek a távoli adatbázisokhoz

Rugalmas lekérdezések most leküldheti SQL-paraméterek végrehajtási távoli adatbázisról.

### <a name="stored-procedure-execution"></a>Tárolt eljárás végrehajtása

Hajtsa végre a távoli tárolt eljárás hívások vagy távoli függvények használatával [sp\_végrehajtása \_távoli](https://msdn.microsoft.com/library/mt703714).

### <a name="flexibility"></a>Rugalmasság

Rugalmas lekérdezés rendelkező külső táblák hivatkozhat egy másik séma vagy a tábla neve a távoli táblákat.

## <a name="elastic-query-scenarios"></a>Rugalmas lekérdezés forgatókönyveket

A cél, hogy lekérdezését, ahol több adatbázis közreműködéshez be egy teljes eredmény sorok forgatókönyvek megkönnyítése. A lekérdezés vagy a felhasználónak vagy alkalmazásnak közvetlenül vagy közvetve az adatbázishoz csatlakoztatott eszközök segítségével állíthatók össze. Ez különösen hasznos a kereskedelmi BI vagy a data-integrációs eszközök vagy bármilyen alkalmazás, és nem módosítható jelentések létrehozása során. Az egy rugalmas lekérdezés lekérdezheti a használatával a jól ismert SQL Server kapcsolódási élményt az eszközökkel, például az Excel, a Power BI, Tableau vagy Cognos több adatbázis között.
Az a rugalmas lekérdezés lehetővé teszi az adatbázisok SQL Server Management Studio vagy Visual Studio által kiadott lekérdezések keresztül egy teljes gyűjteményt is egyszerű hozzáférést, és megkönnyíti a adatbázisközi lekérdezések az Entity Framework vagy más ORM környezetben. 1. ábra bemutatja egy olyan forgatókönyvet, ahol egy meglévő felhőalapú alkalmazás (melyik használja a [elastic database-ügyfélkódtár](sql-database-elastic-database-client-library.md)) réteg egy kiterjesztett adatok épít, és az a rugalmas lekérdezés adatbázisközi jelentéskészítés használja.

**1. ábra** horizontálisan felskálázott adatok szintjén használt rugalmas lekérdezés

![Horizontálisan felskálázott adatok szintjén használt rugalmas lekérdezés][1]

Rugalmas lekérdezés forgatókönyvet a következő topológiákat jellemző:

* **A vertikális particionálás - adatbázisközi lekérdezések** (1. topológia): az adatok függőlegesen particionált adatok szintű adatbázisok számú között. Táblák más-más részhalmazához általában különböző adatbázisok találhatók. Amely azt jelenti, hogy a séma különböző különböző adatbázisokon. Például a készlet összes tábla van egy adatbázis Bár minden nyilvántartási kapcsolódó tábla egy második adatbázishoz. Ez a topológia a gyakori alkalmazási helyzetek szükséges egyik lekérdezésére, vagy fordítsa le a jelentések számos adatbázisokban lévő táblák között.
* **Horizontális particionálás – bérlőadatok horizontális skálázása** (topológia % 2): adatok horizontális particionálása sorok szét a bővítő adatok szint. Ezzel a módszerrel a séma megegyezik a programban részt vevő összes adatbázis. Ez a megközelítés "horizontális skálázási" is nevezik. A horizontális skálázás elvégezhető, és (1) az elastic database használatával felügyelt eszközöket, kódtárak és (2) a önkiszolgáló-horizontális particionálás. Az a rugalmas lekérdezés kérdezheti le vagy több horizontális partícióra kiterjedő fordítsa le a jelentések segítségével.

> [!NOTE]
> Rugalmas lekérdezés a leginkább jelentéskészítési lehetőségeket, ahol a feldolgozás (szűrés, összesítési) többsége a külső adatforrás-oldali hajtható végre. Nem alkalmas az ETL-műveletek ahol nagy mennyiségű adat átvitele a távoli adatbázis (oka). Jelentéskészítési terhelés vagy az adatraktározási forgatókönyvekben az összetett lekérdezések esetén is érdemes [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
>  

## <a name="vertical-partitioning---cross-database-queries"></a>A vertikális particionálás - adatbázisközi lekérdezések

Kódolási lépésekért lásd: [adatbázisközi lekérdezések (vertikális partíciók) – első lépések](sql-database-elastic-query-getting-started-vertical.md).

Az a rugalmas lekérdezés segítségével más SQL-adatbázisok számára elérhető SQL-adatbázisban található adatokat. Ez lehetővé teszi a lekérdezések egy adatbázis lehet hivatkozni a táblázatok minden olyan távoli SQL database-ben. Az első lépéseként határozzon meg egy külső adatforrást minden egyes távoli adatbázishoz. A külső adatforrás, amelyből a távoli adatbázis található táblák hozzáférést szeretne a helyi adatbázisban van meghatározva. Módosítások nem szükségesek a távoli adatbázis. A tipikus függőleges particionálási alkalmazási ahol különböző adatbázisok különböző sémákkal rendelkeznek rugalmas lekérdezések gyakori alkalmazási helyzetek, például a referencia-adatokhoz való hozzáférés megvalósításához és a lekérdezés adatbázisközi használható.

> [!IMPORTANT]
> Az ALTER ANY EXTERNAL DATA SOURCE engedéllyel kell rendelkeznie. Ez az engedély megtalálható az ALTER DATABASE engedéllyel. Az ALTER ANY EXTERNAL DATA SOURCE engedélyekre van szükség, tekintse meg az alapul szolgáló adatforrás.
>

**Referenciaadatok**: A topológia referencia adatok felügyeletére használható. Az alábbi ábrán két tábla (a T1 és T2) referenciaadatokkal őrzi meg a dedikált adatbázis. Az a rugalmas lekérdezés használatával, most már elérheti a T1 és T2 táblák távolról más adatbázisból származó az ábrán látható módon. Használat topológia 1, ha a referencia táblák, kis- és távoli lekérdezés referencia táblába szelektív predikátumok rendelkezik.

**2. ábra** vertikális particionálás – rugalmas lekérdezés a lekérdezés Referenciaadatokkal

![Vertikális particionálás – rugalmas lekérdezés a lekérdezés Referenciaadatokkal][3]

**Adatbázisközi lekérdezés**: rugalmas lekérdezések engedélyezése használati esetek igénylő, számos SQL-adatbázisok lekérdezése. 3. ábrán látható négy különböző adatbázisok: CRM, a szoftverleltár, a HR és a termékek. Egy adatbázis végrehajtott lekérdezéseket is egyikét vagy mindegyikét az egyéb adatbázisok hozzá kell férniük. Az a rugalmas lekérdezés használatával, konfigurálhatja az adatbázis ebben az esetben az egyes négy adatbázis néhány egyszerű DDL-utasítások futtatásával. Ez egyszeri konfigurálás után egyszerű: a T-SQL-lekérdezések, illetve az Üzletiintelligencia-eszközökkel egy helyi táblára hivatkozó távoli tábla a hozzáférést. Ez a megközelítés akkor ajánlott, ha a távoli lekérdezés nem adott vissza nagy eredményeket.

**3. ábra** vertikális particionálás – rugalmas lekérdezés a lekérdezés használatával különböző adatbázisok között

![Vertikális particionálás – rugalmas lekérdezés a lekérdezés használatával különböző adatbázisok között][4]

Az alábbi lépésekkel konfigurálhatóak az elastic database-lekérdezések függőleges particionálási, ugyanazzal a sémával rendelkező távoli SQL-adatbázis található egy táblát a hozzáférést igénylő forgatókönyvek esetén:

* [Hozzon létre FŐKULCS](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
* [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
* [KÜLSŐ ADATFORRÁS létrehozása/DROP](https://msdn.microsoft.com/library/dn935022.aspx) típusú mydatasource **RDBMS**
* [KÜLSŐ tábla létrehozása/DROP](https://msdn.microsoft.com/library/dn935021.aspx) mytable

A DDL-utasítások futtatása, után hozzáférhet a távoli tábla "mytable", mintha egy helyi táblára. Az Azure SQL Database automatikusan megnyit egy kapcsolatot a távoli adatbázis feldolgozza a kérést a távoli adatbázis és az eredményeket adja vissza.

## <a name="horizontal-partitioning---sharding"></a>Horizontális particionálás – bérlőadatok horizontális skálázása

Rugalmas lekérdezés használatával jelentéskészítési feladatok elvégzéséhez a horizontálisan skálázott keresztül, azt jelenti, vízszintesen particionált, adatrétegbeli van szükség egy [rugalmas horizontális skálázási térképet](sql-database-elastic-scale-shard-map-management.md) , amelyek az adatréteg adatbázisait. Általában csak egyetlen szegmenstérkép használatban van ebben a forgatókönyvben, és rugalmas lekérdezési lehetőségeket a (átjárócsomópont) dedikált adatbázis lekérdezések jelentéskészítési belépési pontként szolgál. Csak a dedikált adatbázis a szegmenstérkép hozzá kell férnie. 4. ábra szemlélteti ezt a topológiát és annak konfigurációját, a rugalmas lekérdezés adatbázis és a szegmens térképet. Az adatbázisok az adatszinten lévő bármely Azure SQL Database verzióra vagy kiadásra lehet. Az elastic database ügyfélkódtár és szegmenstérképet létrehozásával kapcsolatos további információkért lásd: [Szilánkleképezés-kezelés](sql-database-elastic-scale-shard-map-management.md).

**4. ábra** horizontális particionálás – jelentéskészítés felett horizontálisan skálázott adatok szinten a rugalmas lekérdezés használatával

![Horizontális particionálás – jelentéskészítés felett horizontálisan skálázott adatok szinten a rugalmas lekérdezés használatával][5]

> [!NOTE]
> Rugalmas adatbázis lekérdezése (átjárócsomópont) lehet különálló adatbázis, vagy ugyanazt az adatbázist, amelyen a szegmenstérkép lehet.
> Válassza ki, győződjön meg arról, hogy a szolgáltatási szint és az adott adatbázis méretétől számítási bármilyen konfigurációs értéke elég nagy ahhoz, a bejelentkezési és a lekérdezésekre vonatkozó kérelmek várható mennyisége kezelésére.

Az alábbi lépéseket a rugalmas adatbázis-lekérdezések horizontális particionálási forgatókönyvek esetén (általában) több távoli SQL-adatbázis található táblázatok halmazát hozzáférést igénylő konfigurálása:

* [Hozzon létre FŐKULCS](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
* [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
* Hozzon létre egy [szegmenstérkép](sql-database-elastic-scale-shard-map-management.md) jelölő az adatszint az elastic database ügyfélkódtár használatával.
* [KÜLSŐ ADATFORRÁS létrehozása/DROP](https://msdn.microsoft.com/library/dn935022.aspx) típusú mydatasource **SHARD_MAP_MANAGER**
* [KÜLSŐ tábla létrehozása/DROP](https://msdn.microsoft.com/library/dn935021.aspx) mytable

Miután elvégezte ezeket a lépéseket, érheti el a horizontálisan particionált tábla "mytable", mintha egy helyi táblára. Az Azure SQL Database automatikusan megnyílik a távoli adatbázis a táblák fizikailag tároló több párhuzamos kapcsolatot, feldolgozza a kérelmeket a távoli adatbázis, és az eredményeket adja vissza.
További információk a tekintheti meg a horizontális particionálási forgatókönyvhöz szükséges lépésekről [rugalmas lekérdezés horizontális particionálási](sql-database-elastic-query-horizontal-partitioning.md).

Kódolási lépésekért lásd: [rugalmas lekérdezés horizontális particionálást (sharding) – első lépések](sql-database-elastic-query-getting-started.md).

## <a name="t-sql-querying"></a>T-SQL-lekérdezés

Miután meghatározta a külső adatforrásokhoz, és a külső táblák, használhatja a szokásos SQL Server-kapcsolati karakterláncok a adatbázisaihoz való kapcsolódásra, amelyben definiálta a külső táblák. Ezt követően futtathatja T-SQL-utasítások a külső táblákon végrehajtott a kapcsolat az alábbi korlátozásokkal. A dokumentáció témakörök további információt és példákat a T-SQL-lekérdezések találhatja [horizontális particionálást](sql-database-elastic-query-horizontal-partitioning.md) és [a vertikális particionálás](sql-database-elastic-query-vertical-partitioning.md).

## <a name="connectivity-for-tools"></a>Az eszközök kapcsolat

Használhatja a szokásos SQL Server-kapcsolati karakterláncok adatbázisaihoz való kapcsolódásra az alkalmazások és a BI-t vagy a data-integrációs eszközök, amelyek külső táblák. Győződjön meg arról, hogy az SQL Server támogatott-e az eszköz adatforrásként. Ha csatlakoztatva van, tekintse meg a rugalmas lekérdezés adatbázis és a külső táblák az adatbázisban, mint bármely más SQL Server adatbázis, amely az eszközzel kapcsolódhat kellene tennie.

> [!IMPORTANT]
> Hitelesítés Azure Active Directory az elastic Database-lekérdezések jelenleg nem támogatott.

## <a name="cost"></a>Költségek

Rugalmas lekérdezés az Azure SQL Database-adatbázisok költségeinek tartalmazza. Vegye figyelembe, hogy a távoli adatbázis esetén, mint a rugalmas lekérdezés végpontot egy másik adatközpontban topológiák támogatottak, de távoli adatbázisból származó kimenő adatforgalom díját rendszeresen [Azure díjszabása](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="preview-limitations"></a>Előzetes verzió korlátozásai

* Az első rugalmas lekérdezés futtatása akár is igénybe vehet néhány percet a Standard szolgáltatási rétegben található. Most szükség, a rugalmas lekérdezés funkció; betöltése a magasabb szintű szolgáltatási szintek és a számítási méret javítja a teljesítmény betöltése.
* Külső adatforrások vagy külső táblák az ssms-ben vagy az SSDT Scripting még nem támogatott.
* Az SQL DB Import/Export még nem támogatja a külső és a külső táblák. Ha szeretné használni az Import/Export, dobja el ezek az objektumok exportálása előtt, és hozza létre újra őket az importálás után.
* Rugalmas lekérdezés jelenleg csak támogatja a külső táblák csak olvasási hozzáféréssel. Azonban használhatja teljes T-SQL-funkciók az adatbázison, a külső tábla van definiálva. Ez lehet hasznos, például használja, például ideiglenes eredmények megőrzése KIVÁLASZTANI < column_list > és < local_table >, vagy a külső táblákra hivatkozó rugalmas lekérdezés adatbázisban tárolt eljárások meghatározásához.
* Kivéve az nvarchar(max) LOB-típusok nem támogatottak a külső tábla-definíciókban. Áthidaló megoldásként létrehozhat egy nézet a távoli adatbázis, amely a LOB-típusok nvarchar(max) helyezhetők kerül, a külső tábla megadása helyett az alaptábla a nézetben és majd alakítania azt az eredeti LOB típusú be újra a lekérdezést.
* Eredmény set tiltsa le az nvarchar(max) adattípusú oszlopokkal speciális kötegelés technics rugalmas lekérdezés végrehajtása során használt, és hatással lehet ez ezerszer tartozó lekérdezés teljesítményére, vagy még két nagyságrenddel a nem kanonikus használati eseteket, ahol nagy mennyiségű nem összesíthető adatátvitel lekérdezés eredményeként.
* A külső táblákon végrehajtott oszlopstatisztika jelenleg nem támogatott. Táblastatisztika támogatottak, de manuálisan kell létrehozni.

## <a name="feedback"></a>Visszajelzés

Visszajelzés küldése az elastic Database-lekérdezések az alábbi velünk tapasztalatait, az MSDN-fórumokhoz vagy a StackOverflow-n. Jelenleg a szolgáltatás (hibák, hozzávetőleges élek, a szolgáltatás hézagok) visszajelzést különféle iránt.

## <a name="next-steps"></a>További lépések

* Függőleges particionálási oktatóanyagért lásd: [adatbázisközi lekérdezések (vertikális partíciók) – első lépések](sql-database-elastic-query-getting-started-vertical.md).
* Függőlegesen particionált adatok szintaxis és a minta lekérdezéseket, lásd: [lekérdezése függőlegesen particionált adatok)](sql-database-elastic-query-vertical-partitioning.md)
* Horizontális particionálást (sharding) foglalkozó oktatóanyagért lásd: [rugalmas lekérdezés horizontális particionálást (sharding) – első lépések](sql-database-elastic-query-getting-started.md).
* A szintaxist és a minta lekérdezésekhez vízszintesen particionált adatok, lásd: [lekérdezése vízszintesen particionált adatok)](sql-database-elastic-query-horizontal-partitioning.md)
* Lásd: [sp\_végrehajtása \_távoli](https://msdn.microsoft.com/library/mt703714) egy tárolt eljárás, amely végrehajtja a Transact-SQL-utasítás egy távoli Azure SQL Database vagy adatbázisok horizontális particionálási séma kidolgozásához szegmensek szolgáló készletét.

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png
[3]: ./media/sql-database-elastic-query-overview/vertpartrrefdata.png
[4]: ./media/sql-database-elastic-query-overview/verticalpartitioning.png
[5]: ./media/sql-database-elastic-query-overview/horizontalpartitioning.png

<!--anchors-->
