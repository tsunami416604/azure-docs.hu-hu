---
title: Azure SQL Database rugalmas lekérdezés áttekintése
description: A rugalmas lekérdezés lehetővé teszi a több adatbázisra kiterjedő Transact-SQL-lekérdezés futtatását.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 07/01/2019
ms.openlocfilehash: 08c191742425c448618db255491c709130df33a1
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690367"
---
# <a name="azure-sql-database-elastic-query-overview-preview"></a>Azure SQL Database rugalmas lekérdezés áttekintése (előzetes verzió)

A rugalmas lekérdezés funkció (előzetes verzió) lehetővé teszi, hogy olyan Transact-SQL-lekérdezést futtasson, amely Azure SQL Database több adatbázisra is kiterjed. Lehetővé teszi a távoli táblák elérését, valamint a Microsoft és harmadik féltől származó eszközök (Excel, Power BI, tabló stb.) összekapcsolását a több adatbázissal rendelkező adatrétegek lekérdezéséhez. Ezzel a funkcióval kibővítheti a lekérdezéseket a SQL Database nagyméretű adatszintjeire, és megjelenítheti az eredményeket az üzleti intelligencia-(BI-) jelentésekben.

## <a name="why-use-elastic-queries"></a>Miért érdemes rugalmas lekérdezéseket használni

### <a name="azure-sql-database"></a>Azure SQL Database

Teljes egészében az Azure SQL-adatbázisok lekérdezése a T-SQL-ben. Ez lehetővé teszi a távoli adatbázisok írásvédett lekérdezését, és lehetőséget nyújt a jelenlegi helyszíni SQL Server ügyfelek számára az alkalmazások áttelepítésére három és négy részből álló névvel vagy csatolt kiszolgálóról az SQL-ADATBÁZISba.

### <a name="available-on-standard-tier"></a>Elérhető a standard szinten

A rugalmas lekérdezés a standard és a prémium szintű szolgáltatási szinten is támogatott. Az alacsonyabb szintű szolgáltatási szintek teljesítményére vonatkozó korlátozásokkal kapcsolatban lásd az előzetes verzió korlátozásait ismertető szakaszt.

### <a name="push-parameters-to-remote-databases"></a>Paraméterek leküldése a távoli adatbázisokra

A rugalmas lekérdezések mostantól leküldhetik az SQL-paramétereket a távoli adatbázisoknak végrehajtás céljából.

### <a name="stored-procedure-execution"></a>Tárolt eljárás végrehajtása

Távoli tárolt eljárási hívások vagy távoli függvények végrehajtása az [sp\_\_távoli végrehajtásával](https://msdn.microsoft.com/library/mt703714).

### <a name="flexibility"></a>Rugalmasság

A rugalmas lekérdezéssel rendelkező külső táblák más sémával vagy táblázattal rendelkező távoli táblákra is vonatkozhatnak.

## <a name="elastic-query-scenarios"></a>Rugalmas lekérdezési forgatókönyvek

A cél az olyan forgatókönyvek lekérdezése, amelyekben egyszerre több adatbázis is hozzájárul a sorok egyetlen átfogó eredményhez való bevezetéséhez. A lekérdezés vagy a felhasználó vagy az alkalmazás közvetlenül, vagy közvetve az adatbázishoz csatlakozó eszközökön keresztül végezhető el. Ez különösen akkor hasznos, ha jelentéseket hoz létre a kereskedelmi BI-vagy adatintegrációs eszközökkel, vagy bármely olyan alkalmazást, amely nem módosítható. A rugalmas lekérdezéssel több adatbázisra is lekérdezheti az ismerős SQL Server kapcsolódási élményt az olyan eszközökön, mint az Excel, a Power BI, a tabló vagy a Cognos.
A rugalmas lekérdezés lehetővé teszi, hogy a SQL Server Management Studio vagy a Visual Studio által kiadott lekérdezések révén egyszerű hozzáférést biztosítson az adatbázisok teljes gyűjteményéhez, és megkönnyíti a Entity Framework vagy más ORM-környezetek adatbázisok közötti lekérdezését. Az 1. ábra egy olyan forgatókönyvet mutat be, amelyben egy meglévő Felhőbeli alkalmazás (amely a [rugalmas adatbázis-ügyfél függvénytárát](sql-database-elastic-database-client-library.md)használja) egy kibővített adatrétegre épül, és egy rugalmas lekérdezést használ az adatbázisok közötti jelentéskészítéshez.

**1. ábra** Kibővített adatrétegen használt rugalmas lekérdezés

![Kibővített adatrétegen használt rugalmas lekérdezés][1]

A rugalmas lekérdezés felhasználói forgatókönyveit a következő topológiák jellemzik:

* **Vertikális particionálás – adatbázis-** összevont lekérdezések (1. topológia): az adatrétegben lévő adatbázisok között vertikálisan particionálja az adatmennyiséget. A táblák különböző készletei általában különböző adatbázisokban találhatók. Ez azt jelenti, hogy a séma eltér a különböző adatbázisokon. Például a leltárhoz tartozó összes tábla egy adatbázison található, míg az összes nyilvántartással kapcsolatos tábla egy második adatbázisban található. Az ehhez a topológiához tartozó gyakori használati esetekben egy-egy lekérdezésre van szükség a különböző adatbázisok tábláiban, illetve a jelentések fordításához.
* **Vízszintes particionálás – horizontális** felskálázás (2. topológia): az adatmennyiséget vízszintesen particionálja a rendszer a sorok elosztására egy horizontálisan elosztott adatrétegben. Ezzel a módszerrel a séma megegyezik az összes részt vevő adatbázison. Ezt a megközelítést "horizontális felskálázásnak" is nevezzük. A horizontális skálázás végezhető el és kezelhető (1) a rugalmas adatbázis-eszközök kódtárai vagy (2) saját horizontálisan. Rugalmas lekérdezéssel több szegmens között lehet lekérdezni vagy lefordítani a jelentéseket.

> [!NOTE]
> A rugalmas lekérdezés olyan jelentéskészítési forgatókönyvekhez használható, amelyekben a feldolgozás (szűrés, Összesítés) a külső forrás oldalán végezhető el. Az ETL-műveletek nem alkalmasak arra, hogy a rendszer nagy mennyiségű adatátvitelt továbbítson a távoli adatbázis (ok) ból. A nehéz jelentéskészítési feladatokhoz vagy az összetettebb lekérdezésekkel rendelkező adatraktározási forgatókönyvekhez érdemes [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/)is használni.
>  

## <a name="vertical-partitioning---cross-database-queries"></a>Vertikális particionálás – adatbázisok közötti lekérdezések

A kódolás megkezdéséhez lásd: [Bevezetés az adatbázisok közötti lekérdezéssel (vertikális particionálás)](sql-database-elastic-query-getting-started-vertical.md).

A rugalmas lekérdezéssel a más SQL-adatbázisok számára elérhető SQL-adatbázisokban található adatforrások is használhatók. Ez lehetővé teszi, hogy az egyik adatbázis lekérdezései bármely más távoli SQL-adatbázisban lévő táblákra hivatkozzon. Az első lépés egy külső adatforrás definiálása minden távoli adatbázishoz. A külső adatforrás a helyi adatbázisban van definiálva, amelyről hozzáférést szeretne adni a távoli adatbázisban található táblákhoz. Nincs szükség módosításra a távoli adatbázison. A tipikus vertikális particionálási forgatókönyvek esetében, ahol a különböző adatbázisok különböző sémákkal rendelkeznek, a rugalmas lekérdezések használhatók olyan gyakori használati esetek megvalósításához, mint például a hivatkozási adatokhoz való hozzáférés és az adatbázisok közötti lekérdezés.

> [!IMPORTANT]
> Rendelkeznie kell a külső adatforrásra vonatkozó engedélyekkel. Ez az engedély az ALTER DATABASE engedély részét képezi. Az alapul szolgáló adatforrásra való hivatkozáshoz minden külső ADATFORRÁS engedélyére van szükség.
>

**Hivatkozási érték**: a topológia a hivatkozási adatkezeléshez használatos. Az alábbi ábrán két táblázat (T1 és T2) található, és a rendszer egy dedikált adatbázisban tárolja a hivatkozási adatot. A rugalmas lekérdezés használatával mostantól a T1 és a T2 táblákat távolról is elérheti más adatbázisokból, ahogy az ábrán is látható. 1\. topológia használata, ha a hivatkozási táblázatok kis-vagy távoli lekérdezések a hivatkozási táblázatba vannak szelektív predikátumok.

**2. ábra** Vertikális particionálás – a rugalmas lekérdezés használatával kérdezi le a hivatkozási adataikat

![Vertikális particionálás – a rugalmas lekérdezés használatával kérdezi le a hivatkozási adataikat][3]

**Adatbázisok közötti lekérdezés**: a rugalmas lekérdezések olyan használati eseteket tesznek lehetővé, amelyek több SQL-adatbázis lekérdezését igénylik. A 3. ábra négy különböző adatbázist mutat be: CRM, leltár, HR és termékek. Az egyik adatbázisban végrehajtott lekérdezéseknek hozzá kell férniük egy vagy az összes többi adatbázishoz is. A rugalmas lekérdezés használatával az adatbázist úgy is konfigurálhatja, hogy a négy adatbázison néhány egyszerű DDL-utasítást futtat. Ezen egyszeri konfiguráció után a távoli táblához való hozzáférés olyan egyszerű, mintha a T-SQL-lekérdezésekből vagy a BI-eszközökből helyi táblára hivatkozik. Ez a módszer akkor javasolt, ha a távoli lekérdezések nem adnak vissza nagy eredményeket.

**3. ábra** Vertikális particionálás – rugalmas lekérdezés használata különböző adatbázisok lekérdezéséhez

![Vertikális particionálás – rugalmas lekérdezés használata különböző adatbázisok lekérdezéséhez][4]

A következő lépésekkel rugalmas adatbázis-lekérdezéseket konfigurálhat olyan vertikális particionálási forgatókönyvekhez, amelyek egy, a távoli SQL-adatbázisokban található táblához való hozzáférést igényelnek ugyanazzal a sémával:

* [Főkulcs Mymasterkey létrehozása](https://msdn.microsoft.com/library/ms174382.aspx)
* [Adatbázis-hatókörű hitelesítő adatok Mycredential létrehozása](https://msdn.microsoft.com/library/mt270260.aspx)
* **RDBMS** típusú [külső adatforrás létrehozása/eldobása](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource
* [Külső tábla Sajáttábla létrehozása/ELdobása](https://msdn.microsoft.com/library/dn935021.aspx)

A DDL-utasítások futtatása után a "sajáttábla" távoli táblát úgy érheti el, mintha egy helyi tábla lenne. Azure SQL Database automatikusan megnyit egy kapcsolódást a távoli adatbázishoz, feldolgozza a kérést a távoli adatbázison, és visszaadja az eredményeket.

## <a name="horizontal-partitioning---sharding"></a>Vízszintes particionálás – horizontális felskálázás

Ha rugalmas lekérdezést használ a jelentéskészítési feladatok elvégzésére egy lépcsőzetesen, azaz vízszintesen particionált, az adatréteghez [rugalmas adatbázis](sql-database-elastic-scale-shard-map-management.md) -szegmenst kell rendelni, amely az adatréteg adatbázisainak ábrázolására szolgál. Ebben a forgatókönyvben jellemzően csak egyetlen szegmenses leképezést használunk, és a rugalmas lekérdezési képességekkel rendelkező dedikált adatbázisok (fő csomópont) a jelentéskészítési lekérdezések belépési pontként szolgálnak. Csak erre a dedikált adatbázisra van szükség a szegmenses Térkép eléréséhez. A 4. ábra ezt a topológiát és a hozzá tartozó konfigurációt mutatja be a rugalmas lekérdezési adatbázissal és a szegmenses leképezéssel. Az adatszinten lévő adatbázisok bármilyen Azure SQL Database verziója vagy kiadása lehetséges. További információ a rugalmas adatbázis-ügyfél könyvtáráról és a szegmenses térképek létrehozásáról: a szegmenses [Térkép kezelése](sql-database-elastic-scale-shard-map-management.md).

**4. ábra** Horizontális particionálás – a rugalmas lekérdezés használata többszintű adatrétegek jelentéskészítéséhez

![Horizontális particionálás – a rugalmas lekérdezés használata többszintű adatrétegek jelentéskészítéséhez][5]

> [!NOTE]
> A rugalmas lekérdezési adatbázis (fő csomópont) lehet különálló adatbázis, vagy lehet ugyanaz az adatbázis, amely a szegmens térképet tárolja.
> Bármilyen konfigurációt választhat, győződjön meg arról, hogy a szolgáltatási szint és az adatbázis számítási mérete elég magas ahhoz, hogy kezelni tudja a bejelentkezési/lekérdezési kérelmek várt mennyiségét.

A következő lépésekkel rugalmas adatbázis-lekérdezéseket konfigurálhat olyan horizontális particionálási forgatókönyvekhez, amelyek hozzáférést igényelnek a (jellemzően) több távoli SQL-adatbázishoz tartozó táblák készletéhez:

* [Főkulcs Mymasterkey létrehozása](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql)
* [Adatbázis-hatókörű hitelesítő adatok Mycredential létrehozása](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql)
* Hozzon létre egy szegmenses [térképet](sql-database-elastic-scale-shard-map-management.md) , amely az adatréteget jelképezi a rugalmas adatbázis ügyféloldali kódtár használatával.
* **SHARD_MAP_MANAGER** típusú [külső adatforrás létrehozása/eldobása](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql) mydatasource
* [Külső tábla Sajáttábla létrehozása/ELdobása](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql)

Miután végrehajtotta ezeket a lépéseket, elérheti a "sajáttábla" horizontálisan particionált táblát, mintha egy helyi tábla lenne. Azure SQL Database automatikusan több párhuzamos kapcsolatot nyit meg azokkal a távoli adatbázisokkal, ahol a táblák fizikailag vannak tárolva, feldolgozza a kéréseket a távoli adatbázisokon, és visszaadja az eredményeket.
A horizontális particionálási forgatókönyvhöz szükséges lépésekkel kapcsolatos további információkért tekintse meg a [rugalmas lekérdezés a horizontális particionáláshoz](sql-database-elastic-query-horizontal-partitioning.md)című témakört.

A kódolás megkezdéséhez tekintse meg a [rugalmas lekérdezés a horizontális particionáláshoz](sql-database-elastic-query-getting-started.md)való használatáról szóló témakört.

## <a name="t-sql-querying"></a>T-SQL-lekérdezés

A külső adatforrások és a külső táblák meghatározása után rendszeres SQL Server kapcsolati karakterláncokat használhat a külső táblákat definiáló adatbázisokhoz való kapcsolódáshoz. Ezután futtathat T-SQL-utasításokat a külső táblákon ezen a kapcsolaton az alább ismertetett korlátozásokkal. További információkat és példákat a T-SQL-lekérdezésekről a [horizontális particionálással](sql-database-elastic-query-horizontal-partitioning.md) és a [vertikális particionálással](sql-database-elastic-query-vertical-partitioning.md)kapcsolatos dokumentációs témakörökben talál.

## <a name="connectivity-for-tools"></a>Eszközökhöz való kapcsolódás

Az alkalmazások és a BI-vagy adatintegrációs eszközök összekapcsolásához használhat normál SQL Server kapcsolati karakterláncokat külső táblákkal rendelkező adatbázisokhoz. Győződjön meg arról, hogy a SQL Server támogatott adatforrásként az eszköz számára. A csatlakozás után tekintse meg a rugalmas lekérdezési adatbázist és az adatbázisban található külső táblákat úgy, mint bármely más SQL Server-adatbázist, amelyhez az eszközzel csatlakozik.

> [!IMPORTANT]
> A rugalmas lekérdezésekkel Azure Active Directory használatával történő hitelesítés jelenleg nem támogatott.

## <a name="cost"></a>Költségek

A rugalmas lekérdezés a Azure SQL Database adatbázisok díjait tartalmazza. Vegye figyelembe, hogy azok a topológiák, amelyekben a távoli adatbázisok egy másik adatközpontban találhatók, mint a rugalmas lekérdezési végpont, de a távoli adatbázisokból kimenő adatokra rendszeresen az [Azure díjszabása](https://azure.microsoft.com/pricing/details/data-transfers/)vonatkozik.

## <a name="preview-limitations"></a>Előzetes verzió korlátozásai

* Az első rugalmas lekérdezés futtatása akár néhány percet is igénybe vehet a standard szintű szolgáltatási szinten. Ez az idő szükséges a rugalmas lekérdezési funkció betöltéséhez; a teljesítmény betöltése nagyobb a szolgáltatási szintek és a számítási méretek tekintetében.
* A külső adatforrások vagy a SSMS vagy SSDT külső tábláinak parancsfájlkezelése még nem támogatott.
* Az SQL-adatbázis importálása/exportálása még nem támogatja a külső adatforrásokat és a külső táblákat. Ha importálás/exportálást kell használnia, dobja el ezeket az objektumokat az exportálás előtt, majd hozza létre újra az importálás után.
* A rugalmas lekérdezés jelenleg csak olvasási hozzáférést biztosít a külső táblákhoz. Használhatja azonban a teljes T-SQL-funkciót azon az adatbázison, ahol a külső tábla definiálva van. Ez hasznos lehet, például az ideiglenes eredmények megtartása a használatával, például kiválaszthatja < column_list > a < local_table >ba, vagy megadhatja a tárolt eljárásokat a külső táblákra hivatkozó rugalmas lekérdezési adatbázison.
* A nvarchar (max) kivételével a LOB-típusok (beleértve a térbeli típusokat is) nem támogatottak a külső táblák definíciójában. Megkerülő megoldásként létrehozhat egy nézetet a távoli adatbázison, amely a LOB-típust a nvarchar (max), az alaptábla helyett a nézetben definiálja a külső táblát, majd visszahelyezi azt az eredeti LOB-típusba a lekérdezésekben.
* Az eredményhalmaz nvarchar (max) adattípusának oszlopai letiltják a rugalmas lekérdezési implementációban használt speciális kötegelt feldolgozást, és befolyásolhatják a lekérdezés teljesítményét a nem kanonikus használati esetekben, vagy akár két nagyságrenddel is, ha nagy mennyiségű a nem összesített adatokat a rendszer a lekérdezés eredményeképpen továbbítja.
* A külső táblákon lévő oszlopok statisztikái jelenleg nem támogatottak. A tábla statisztikái támogatottak, de manuálisan kell létrehozni.
* A rugalmas lekérdezés csak Azure SQL Databaseekkel működik. Nem használhatja a helyszíni SQL Server lekérdezésére, vagy egy virtuális gépen SQL Server.

## <a name="feedback"></a>Visszajelzés

Ossza meg velünk a tapasztalatait rugalmas lekérdezésekkel az alábbi MSDN-fórumokon, illetve a Stack Overflowon. A szolgáltatással kapcsolatos visszajelzések (hibák, durva élek, szolgáltatások hiányosságainak) iránt érdeklődünk.

## <a name="next-steps"></a>További lépések

* A vertikális particionálással kapcsolatos oktatóanyagért lásd: [Bevezetés az adatbázisok közötti lekérdezéssel (vertikális particionálás)](sql-database-elastic-query-getting-started-vertical.md).
* A függőlegesen particionált információk szintaxisát és mintáit lásd: [függőlegesen particionált adatlekérdezés](sql-database-elastic-query-vertical-partitioning.md)
* A horizontális particionálással (skálázással) kapcsolatos oktatóanyagért lásd: az [első lépések a rugalmas lekérdezéssel a horizontális particionáláshoz](sql-database-elastic-query-getting-started.md).
* A horizontálisan particionált információk szintaxisát és mintáit lásd: [vízszintesen particionált adatlekérdezés](sql-database-elastic-query-horizontal-partitioning.md)
* Lásd: [sp\_\_távoli végrehajtása](https://msdn.microsoft.com/library/mt703714) egy tárolt eljáráshoz, amely Transact-SQL-utasítást hajt végre egyetlen távoli Azure SQL Database vagy egy horizontális particionálási sémában szegmensként szolgáló adatbázis-készletet.

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png
[3]: ./media/sql-database-elastic-query-overview/vertpartrrefdata.png
[4]: ./media/sql-database-elastic-query-overview/verticalpartitioning.png
[5]: ./media/sql-database-elastic-query-overview/horizontalpartitioning.png

<!--anchors-->
