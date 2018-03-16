---
title: "Az Azure SQL Database rugalmas lekérdezési áttekintése |} Microsoft Docs"
description: "Rugalmas lekérdezési lehetővé teszi több adatbázist is Transact-SQL-lekérdezés futtatható."
services: sql-database
manager: craigg
author: MladjoA
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/27/2016
ms.author: mlandzic
ms.openlocfilehash: 76a8332b99639bacee994824b69624d7fd457a7f
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="azure-sql-database-elastic-query-overview-preview"></a>Az Azure SQL Database rugalmas lekérdezési áttekintése (előzetes verzió)
A rugalmas lekérdezés (az előzetes verzió) funkciójával is több adatbázis az Azure SQL Database Transact-SQL-lekérdezés futtatható. Lehetővé teszi a távoli táblákat elérő és a csatlakozás a Microsoft és harmadik féltől származó eszközök (az Excel, Power BI, Tableau, stb.) az adatok rétegek több adatbázisból átfogó lekérdezése közötti adatbázis-lekérdezések végrehajtásához. Ezzel a szolgáltatással horizontális felskálázás lekérdezések nagy adat szintet az SQL-adatbázis és az üzleti intelligenciával jelentések eredményeinek képi megjelenítése.


## <a name="why-use-elastic-queries"></a>Rugalmas lekérdezések miért érdemes használni?

**Azure SQL Database**

A lekérdezés teljesen a T-SQL Azure SQL-adatbázisok között. Ez lehetővé teszi a távoli adatbázis lekérdezése csak olvasható. Ez lehetővé teszi a jelenlegi helyszíni SQL Server ügyfelek három és négy part nevek vagy csatolt kiszolgáló az SQL-Adatbázist használó alkalmazások áttelepítéséhez.

**Standard csomagban érhető el**

Rugalmas lekérdezés használata támogatott a szabványos teljesítmény-rétegen a prémium szintű teljesítményszintet mellett. Lásd az alábbi előzetes verzió korlátozásai alacsonyabb teljesítményt rétegek teljesítményének korlátozásai.

**Távoli adatbázis leküldése**

Rugalmas lekérdezések most tolható SQL-paraméterek a távoli adatbázisokhoz végrehajtásra.

**A tárolt eljárás végrehajtása**

Távoli tárolt eljárás hívások vagy a távoli függvények használatával hajtható végre [sp\_hajtható végre \_távoli](https://msdn.microsoft.com/library/mt703714).

**Rugalmasság**

Rugalmas lekérdezéssel külső táblák most jelentheti a távoli táblák különböző séma vagy tábla neve.

## <a name="elastic-query-scenarios"></a>Rugalmas lekérdezési forgatókönyvek

A cél, hogy amennyiben több adatbázis hozzájárul az egyetlen teljes eredmény sorok lekérdező forgatókönyvek megkönnyítése. A lekérdezés vagy a felhasználó vagy alkalmazás közvetlenül vagy közvetve útján az adatbázis csatlakozó eszközök állíthatók össze. Ez különösen fontos jelentések, kereskedelmi BI vagy adatok-integrációs eszközök használatával létrehozásakor – vagy bármilyen alkalmazás, és nem módosítható. Rugalmas lekérdezéshez lekérheti az eszközök, például az Excel, a Power BI, a Tableau vagy a Cognos a megszokott SQL Server kapcsolódási élményt több adatbázis között.
Egy rugalmas lekérdezés lehetővé teszi egy teljes gyűjteményhez az adatbázisok SQL Server Management Studio vagy Visual Studio által kibocsátott lekérdezések használatával egyszerűen hozzáférhetnek, és megkönnyíti a kereszt-adatbázis lekérdezése az Entity Framework vagy más ORM környezetekben. 1. ábra mutatja egy olyan forgatókönyvet, ahol egy meglévő felhőbeli alkalmazás (használó a [elastic database ügyféloldali kódtárának](sql-database-elastic-database-client-library.md)) réteg a kiterjesztett adatok épít, és egy rugalmas lekérdezési adatbázisok közötti jelentéskészítéshez használt.

**1. ábra** kiterjesztett adatszinten használt rugalmas lekérdezés

![A kiterjesztett adatszinten rugalmas lekérdezés][1]

Az alábbi topológiák jellemző rugalmas lekérdezés forgatókönyvet:

* **A vertikális particionálás - adatbázisok közötti lekérdezések** (1. topológia): az adatok particionálása függőleges egy adatrétegbeli adatbázisok számú között. A táblák más-más részhalmazához általában a különböző adatbázisokhoz elhelyezve. Ez azt jelenti, hogy a séma nem egyezik, a különböző adatbázisokhoz. Például a készlet összes tábla esetén egy adatbázis míg nyilvántartási kapcsolatos összes táblázatot egy második adatbázison. Ez a topológia gyakori alkalmazási esetekben szükséges átfogó lekérdezése vagy jelentések fordítási adatbázisok táblái között.
* **Vízszintes particionálására - horizontális** (topológia 2): adatok particionálása vízszintesen szét a sorok méretezett kimenő adatok réteg. Ezt a módszert használja a séma megegyezik a programban részt vevő összes adatbázisra. Ez a megközelítés "horizontális" néven is ismert. Horizontális hajtható végre, és felügyelt használata (1) a rugalmas adatbázis eszközöket, könyvtárakat vagy (2) a önkiszolgáló-horizontális skálázási. Egy rugalmas lekérdezés segítségével lekérdezése vagy fordítsa le a jelentések számos szilánkok között.

> [!NOTE]
> Rugalmas lekérdezési működik a legjobban az alkalmi jelentéskészítési forgatókönyvekhez, ahol a feldolgozás része az adatréteg hajtható végre. Jelentéskészítési munkaterhelést vagy adatraktározási forgatókönyvekben összetettebb lekérdezések esetében is érdemes lehet [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
>  

## <a name="vertical-partitioning---cross-database-queries"></a>A vertikális particionálás - közötti adatbázis-lekérdezések

A kódolási megkezdéséhez tekintse meg [első lépések (a vertikális particionálás) közötti adatbázis-lekérdezés](sql-database-elastic-query-getting-started-vertical.md).

Egy rugalmas lekérdezést, hogy más SQL-adatbázisok számára elérhető SQL-adatbázisban található adatok használható. Ez lehetővé teszi, hogy a lekérdezések bármely távoli SQL adatbázis tábláit hivatkozik egy adatbázisból. Az első lépés célja az egyes távoli adatbázisok külső adatforrás meghatározása. A külső adatforrást, amelyből el kívánja a távoli adatbázis lévő táblák eléréséhez a helyi adatbázisban van definiálva. Nem szükségesek a távoli adatbázishoz. A tipikus függőleges particionálási forgatókönyveket, amelyekben a különböző adatbázisokhoz rendelkeznek különböző sémákat rugalmas lekérdezések segítségével gyakori alkalmazási esetekben, például a referencia-adatokhoz való hozzáférés megvalósításához és adatbázisok közötti lekérdezése.

> [!IMPORTANT]
> Az ALTER ANY külső ADATFORRÁS engedéllyel kell rendelkeznie. Ez az engedély megtalálható az ALTER DATABASE engedéllyel. Az ALTER ANY külső ADATFORRÁS engedélyekre van szükség az alapul szolgáló adatforrásban hivatkozik.
>

**Referenciaadatok**: A topológia hivatkozás adatok felügyeletére használható. Az alábbi ábrán két tábla (T1 és T2) referenciaadatokkal egy dedikált adatbázis tárolja. Rugalmas lekérdezéssel, érhetők el a T1 és T2 táblák távolról más adatbázisból származó az ábrán látható módon. Ha referencia táblákat kis vagy távoli referencia táblába lekérdezések 1 topológiájának használata szelektív predikátumok rendelkezik.

**2. ábra** vertikális particionálás - rugalmas lekérdezés a lekérdezés referenciaadatok használatával

![Vertikális particionálás - rugalmas lekérdezés a lekérdezés referenciaadatok használatával][3]

**Adatbázisok közötti lekérdezése**: rugalmas lekérdezések lehetővé teszik a használati esetek igénylő számos SQL-adatbázisok közötti lekérdezése. 3. ábrán látható négy különböző adatbázist: CRM-hez, a szoftverleltár, a HR és a termékek. Egy adatbázis végzett lekérdezések kell egyet vagy mind a más adatbázisok eléréséhez. Rugalmas lekérdezéssel, konfigurálhatja az adatbázis ebben az esetben néhány egyszerű DDL-utasításokban fut a négy adatbázisok mindegyike esetében. Ez egyszeri konfigurálás után egy távoli táblájához való hozzáférés más dolga, mint a T-SQL-lekérdezések, illetve az Üzletiintelligencia-eszközök egy helyi táblára hivatkozik. Ezt a módszert akkor ajánlott, ha a távoli lekérdezéseknél nem eredményeket nagy.

**3. ábra** vertikális particionálás - különböző adatbázisok közötti rugalmas lekérdezés a lekérdezés használata

![Vertikális particionálás - különböző adatbázisok közötti rugalmas lekérdezés a lekérdezés használata][4]

Az alábbi lépéseket a függőleges particionálási forgatókönyvek, amelyek az ugyanazon sémával távoli SQL-adatbázisok található táblázat hozzáférést igényelnek a rugalmas adatbázis-lekérdezéseinek konfigurálása:

* [Hozzon létre FŐKULCS](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
* [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
* [KÜLSŐ ADATFORRÁS létrehozása/DROP](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource típusú **RDBMS**
* [A külső tábla létrehozása/DROP](https://msdn.microsoft.com/library/dn935021.aspx) táblanév

Miután a DDL-utasításokban, a "mytable" távoli tábla, mintha egy helyi táblára érheti el. Az Azure SQL Database automatikusan megnyitja a kapcsolatot a távoli adatbázissal, feldolgozza a kérést a távoli adatbázishoz, és az eredményeket ad vissza.

## <a name="horizontal-partitioning---sharding"></a>Vízszintes particionálás - horizontális
A Rugalmas lekérdezési vízszintesen, azaz particionálva, a szilánkos keresztül jelentéskészítési feladatok elvégzéséhez adatszinten szükség van egy [rugalmas adatbázis shard térkép](sql-database-elastic-scale-shard-map-management.md) képviseli az adatréteg adatbázisait. Általában csak egyetlen shard térképre használatban van ebben a forgatókönyvben, és rugalmas lekérdezési lehetőségeket (átjárócsomópont) dedikált adatbázis lekérdezések jelentéskészítéshez belépési pontként szolgál. Csak a dedikált adatbázis a shard térkép hozzáférésre van szüksége. 4. ábra szemlélteti, ez a topológia és a Rugalmas lekérdezési adatbázis és a shard hozzárendelése és konfigurációja. Az adatbázisok az adatréteg lehet bármely Azure SQL Database verzióját vagy kiadását. A rugalmas adatbázis ügyféloldali kódtár és shard maps létrehozásával kapcsolatos további információkért lásd: [Shard térkép felügyeleti](sql-database-elastic-scale-shard-map-management.md).

**4. ábra** vízszintes particionálás - rugalmas lekérdezéssel felett horizontálisan skálázott adatok rétegek jelentéskészítéshez

![Vízszintes particionálás - rugalmas lekérdezéssel felett horizontálisan skálázott adatok rétegek jelentéskészítéshez][5]

> [!NOTE]
> A rugalmas adatbázis lekérdezése (átjárócsomópont) lehet külön adatbázis, vagy ugyanazt az adatbázist, amelyen a shard leképezés. Bármilyen konfiguráció választja, győződjön meg arról, hogy az adatbázis szolgáltatás- és rétegű elég nagy a bejelentkezési/lekérdezésekre vonatkozó kérelmek várható mennyisége kezelésére.

Az alábbi lépéseket a vízszintes particionálási forgatókönyvek tábla hozzáférést igénylő található rugalmas adatbázis-lekérdezéseinek (általában) több távoli SQL-adatbázisok konfigurálása:

* [Hozzon létre FŐKULCS](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
* [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
* Hozzon létre egy [shard térkép](sql-database-elastic-scale-shard-map-management.md) képviselő az adatréteg a rugalmas adatbázis ügyféloldali kódtár segítségével.   
* [KÜLSŐ ADATFORRÁS létrehozása/DROP](https://msdn.microsoft.com/library/dn935022.aspx) típusú mydatasource **SHARD_MAP_MANAGER**
* [A külső tábla létrehozása/DROP](https://msdn.microsoft.com/library/dn935021.aspx) táblanév

Miután elvégezte ezeket a lépéseket, érheti el a "mytable" vízszintes particionált tábla, mintha egy helyi táblára. Az Azure SQL Database automatikusan megnyílik több párhuzamos kapcsolatokat a táblák fizikailag tároló a távoli adatbázisokhoz, feldolgozza a kérelmeket a távoli adatbázisokhoz, és az eredményeket ad vissza.
További információ a a vízszintes particionálási forgatókönyv találhatók a szükséges lépéseket [vízszintes particionálására rugalmas lekérdezési](sql-database-elastic-query-horizontal-partitioning.md).

A kódolási megkezdéséhez tekintse meg [Ismerkedés a vízszintes particionálására (horizontális) rugalmas lekérdezési](sql-database-elastic-query-getting-started.md).

## <a name="t-sql-querying"></a>T-SQL-lekérdezés
Miután meghatározta a külső adatforrások és a külső táblák, rendszeres SQL Server kapcsolati karakterláncok segítségével csatlakozzon az adatbázishoz, ahol definiálva a a külső táblákra. Ezt követően futtathatja T-SQL-utasítások a külső táblákon végrehajtott a kapcsolat az alábbiakban leírt korlátozásokkal. További információt és példákat a T-SQL-lekérdezések megtalálhatja a dokumentáció témaköröket [vízszintes particionálás](sql-database-elastic-query-horizontal-partitioning.md) és [vertikális particionálás](sql-database-elastic-query-vertical-partitioning.md).

## <a name="connectivity-for-tools"></a>Eszközök kapcsolattal
Rendszeres SQL Server kapcsolati karakterláncok használhatja az alkalmazásokat és BI vagy adatok integrációs eszközök adatbázisok esetén, amelyek külső táblákon való kapcsolódáshoz. Győződjön meg arról, hogy az SQL Server támogatja-e az eszköz adatforrásként. Miután csatlakozott, tekintse meg a lekérdezés rugalmas adatbázis és a külső táblákhoz az adatbázis ugyanúgy, mint bármely más SQL Server adatbázis, amely az eszköz csatlakozhat kellene tennie.

> [!IMPORTANT]
> Hitelesítés az Azure Active Directoryval rugalmas lekérdezési jelenleg nem támogatott.
> 
> 

## <a name="cost"></a>Költségek
Rugalmas lekérdezési szerepel, az Azure SQL Database adatbázisok költségét. Vegye figyelembe, hogy a távoli adatbázisok esetén egy másik adatközpont, mint a Rugalmas lekérdezési végpont topológiák támogatottak, de távoli adatbázisokból adatforgalommal regular van szó, [Azure díjszabás](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="preview-limitations"></a>Előzetes verzió korlátozásai
* Az első rugalmas lekérdezés futtatása is eltarthat néhány percig, amíg a szabványos teljesítmény-rétegen. Ez a Rugalmas lekérdezési funkcionalitás; betöltéséhez szükséges ideje teljesítmény betöltése növeli a magasabb teljesítmény rétegekkel.
* A külső adatforrások vagy a külső táblákhoz az SSMS vagy az SSDT Scripting még nem támogatott.
* Importálási/exportálási az SQL DB egyelőre nem támogatják a külső adatforrások és a külső táblákra. Importálási/exportálási használni kell, ha dobható el, ezek az objektumok exportálása előtt, és ezután hozza létre őket importálása után.
* Rugalmas lekérdezés jelenleg csak támogatja a külső táblákra csak olvasási hozzáféréssel. Azonban használhatja teljes T-SQL funkciókat az adatbázis ahol a külső tábla meg van adva. Ez akkor lehet hasznos, pl. ideiglenes eredményeket, pl. maradnak, válassza ki a < column_list > a < local_table >, illetve tárolt eljárások adja meg a lekérdezés rugalmas adatbázis, amely tekintse meg a külső táblákra.
* Típus: nvarchar(max), kivéve a LOB-típusok nem támogatottak a külső tábla definíciókat. Megoldás hozhat létre a távoli adatbázis, amely a LOB típusú, típus: nvarchar(max) adat kerül egy nézet, a külső tábla megadása helyett az alaptábla nézet keresztül és majd típussá be az eredeti LOB-típust a lekérdezésekben.
* A külső táblákon végrehajtott oszlop statisztikai adatainak jelenleg nem támogatottak. Táblák statisztika támogatottak, de manuálisan kell létrehozni.

## <a name="feedback"></a>Visszajelzés
Ossza meg a felhasználói élmény kapcsolatos visszajelzéseket rugalmas lekérdezési betartásának vizsgálatára Livefyre az alábbi, az MSDN fórumain vagy Stackoverflow. Folyamatban, a szolgáltatás (hibák, nyers szélén, a szolgáltatás hézagok) visszajelzést különböző érdekelt.

## <a name="next-steps"></a>További lépések

* Függőleges particionálási oktatóanyagért lásd a [első lépések (a vertikális particionálás) közötti adatbázis-lekérdezés](sql-database-elastic-query-getting-started-vertical.md).
* A szintaxis és a minta lekérdezések függőleges particionált adatok, lásd: [adatok lekérdezése függőleges particionálva)](sql-database-elastic-query-vertical-partitioning.md)
* Vízszintes particionálás (horizontális) oktatóanyagért lásd a [Ismerkedés a vízszintes particionálására (horizontális) rugalmas lekérdezési](sql-database-elastic-query-getting-started.md).
* A szintaxis és a minta lekérdezések vízszintesen particionált adatok, lásd: [adatok vízszintesen lekérdezése particionálva)](sql-database-elastic-query-horizontal-partitioning.md)
* Lásd: [sp\_hajtható végre \_távoli](https://msdn.microsoft.com/library/mt703714) tárolt eljárás, amely végrehajtja a Transact-SQL-utasítás egy egyetlen távoli Azure SQL Database vagy az adatbázisok egy vízszintes particionálási sémát a szilánkok szolgál.

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png
[3]: ./media/sql-database-elastic-query-overview/vertpartrrefdata.png
[4]: ./media/sql-database-elastic-query-overview/verticalpartitioning.png
[5]: ./media/sql-database-elastic-query-overview/horizontalpartitioning.png

<!--anchors-->
