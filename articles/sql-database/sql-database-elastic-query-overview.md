---
title: A rugalmas lekérdezések áttekintése
description: A rugalmas lekérdezés lehetővé teszi egy több adatbázisra kiterjedő Transact-SQL-lekérdezés futtatását.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 12/05/2019
ms.openlocfilehash: 827fab0661a58bfa7d28452960ea6df64d18bf84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873743"
---
# <a name="azure-sql-database-elastic-query-overview-preview"></a>Az Azure SQL Database rugalmas lekérdezésáttekintése (előzetes verzió)

A rugalmas lekérdezési funkció (előzetes verzióban) lehetővé teszi egy Transact-SQL-lekérdezés futtatását, amely több adatbázisra terjed ki az Azure SQL Database-ben. Lehetővé teszi, hogy adatbázisközi lekérdezéseket hajtson végre a távoli táblák eléréséhez, valamint a Microsoft és a külső eszközök (Excel, Power BI, Tableau stb.) összekapcsolásához a több adatbázissal rendelkező adatrétegek közötti lekérdezéshez. Ezzel a funkcióval a lekérdezéseket nagy adatrétegekre skálázhatja az SQL Database-ben, és megjelenítheti az eredményeket az üzletiintelligencia-jelentésekben.

## <a name="why-use-elastic-queries"></a>Miért érdemes rugalmas lekérdezéseket használni

### <a name="azure-sql-database"></a>Azure SQL Database

Lekérdezés az Azure SQL-adatbázisokban teljesen a T-SQL-ben. Ez lehetővé teszi a távoli adatbázisok írásvédett lekérdezését, és lehetővé teszi a jelenlegi helyszíni SQL Server-ügyfelek számára az alkalmazások három- és négyrészes nevek vagy csatolt kiszolgáló SQL DB-re történő áttelepítését.

### <a name="available-on-standard-tier"></a>Elérhető a standard szinten

Rugalmas lekérdezés a standard és a prémium szintű szolgáltatási szinteken is támogatott. Tekintse meg az alábbi, az alacsonyabb szolgáltatási szintek teljesítménykorlátozásaira vonatkozó előzetes korlátozások című szakaszt.

### <a name="push-parameters-to-remote-databases"></a>Paraméterek leküldése távoli adatbázisokba

Rugalmas lekérdezések most már leküldéses SQL-paramétereket a távoli adatbázisok végrehajtásra.

### <a name="stored-procedure-execution"></a>Tárolt eljárás végrehajtása

Távoli tárolt eljáráshívások vagy távoli függvények végrehajtása [a sp\_execute \_remote](https://msdn.microsoft.com/library/mt703714)használatával.

### <a name="flexibility"></a>Rugalmasság

A rugalmas lekérdezéssel rendelkező külső táblák más sémával vagy táblanévvel rendelkező távoli táblákra hivatkozhatnak.

## <a name="elastic-query-scenarios"></a>Rugalmas lekérdezési forgatókönyvek

A cél az olyan esetek lekérdezésének megkönnyítése, amelyekben több adatbázis egyetlen általános eredményhez járul hozzá a sorokhoz. A lekérdezést a felhasználó vagy az alkalmazás közvetlenül, vagy közvetve is összeállhat az adatbázishoz kapcsolódó eszközökön keresztül. Ez különösen akkor hasznos, ha jelentéseket hoz létre, kereskedelmi üzletiintelligencia- vagy adatintegrációs eszközöket használ, vagy olyan alkalmazásokat, amelyeket nem lehet módosítani. Egy rugalmas lekérdezéssel több adatbázist is lekérdezhet a jól ismert SQL Server-kapcsolati környezethasználatával olyan eszközökben, mint az Excel, a Power BI, a Tableau vagy a Cognos.
A rugalmas lekérdezés lehetővé teszi az adatbázisok teljes gyűjteményének egyszerű elérését az SQL Server Management Studio vagy a Visual Studio által kiadott lekérdezéseken keresztül, és megkönnyíti az adatbázisközi lekérdezést az Entitáskeretrendszerből vagy más ORM-környezetekből. 1. ábra egy olyan forgatókönyvet mutat be, amelyben egy meglévő felhőalkalmazás (amely a [rugalmas adatbázis-ügyfélkönyvtárat](sql-database-elastic-database-client-library.md)használja) egy kibővített adatrétegre épül, és egy rugalmas lekérdezést használ az adatbázisközi jelentésekhez.

**1. ábra** Horizontális lekérdezés a kicsinyített adatrétegen

![Horizontális lekérdezés a kicsinyített adatrétegen][1]

A rugalmas lekérdezés ekként forgatókönyveit a következő topológiák jellemzik:

* **Vertikális particionálás – Adatbázisközi lekérdezések** (Topológia 1): Az adatok függőlegesen particionálva vannak az adatréteg több adatbázisa között. Általában különböző táblakészletek találhatók különböző adatbázisokban. Ez azt jelenti, hogy a séma különböző adatbázisokban eltérő. Például a készlet minden táblája egy adatbázisban van, míg az összes könyveléssel kapcsolatos tábla egy második adatbázisban van. Az ezzel a topológiával használt gyakori használati esetekhez több adatbázisban is le kell kérdezni vagy össze kell állítani a jelentéseket a táblák között.
* **Horizontális particionálás – horizontális skálázás** (Topológia 2): Az adatok horizontálisan particionálva vannak elosztva a sorok egy kicsinyített adatréteg en. Ezzel a módszerrel a séma azonos az összes részt vevő adatbázisok. Ezt a megközelítést "szilánkolásnak" is nevezik. A szilánkolás (1) a rugalmas adatbázis-eszközök kódtára vagy (2) önnagyításhasználatával hajtható végre és kezelhető. A rugalmas lekérdezés ek lekérdezése vagy fordítása jelentések több szegmensek között. Szilánkok általában adatbázisok egy rugalmas készleten belül. A rugalmas lekérdezés hatékony módja a rugalmas készlet összes adatbázisának egyszerre történő lekérdezéséhez, feltéve, hogy az adatbázisok megosztják a közös sémát.

> [!NOTE]
> Rugalmas lekérdezés működik a legjobban a jelentési forgatókönyvek, ahol a legtöbb feldolgozás (szűrés, összesítés) lehet végrehajtani a külső forrásoldalon. Nem alkalmas ETL műveletekhez, ahol nagy mennyiségű adat átvitele történik távoli adatbázis(ok)ról. A nagy jelentési számítási feladatok vagy adattárház forgatókönyvek összetettebb lekérdezések, fontolja meg [az Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics)használatát is.
>  

## <a name="vertical-partitioning---cross-database-queries"></a>Vertikális particionálás – adatbázisközi lekérdezések

A kódolás megkezdéséhez [olvassa el az Adatbázisközi lekérdezés (vertikális particionálás) első lépéseit.](sql-database-elastic-query-getting-started-vertical.md)

A rugalmas lekérdezés segítségével az SQL-adatbázisban található adatok at más SQL-adatbázisok számára is elérhetővé teheti. Ez lehetővé teszi, hogy az egyik adatbázis lekérdezései bármely más távoli SQL-adatbázis tábláira hivatkozjanak. Az első lépés egy külső adatforrás definiálása minden távoli adatbázishoz. A külső adatforrás abban a helyi adatbázisban van definiálva, amelyből a távoli adatbázisban található táblákhoz szeretne hozzáférni. A távoli adatbázisban nincs szükség módosításokra. A tipikus függőleges particionálási forgatókönyvek, ahol a különböző adatbázisok különböző sémák, rugalmas lekérdezések használható gyakori használati esetek, például a referenciaadatokhoz való hozzáférés és az adatbázisközi lekérdezés.

> [!IMPORTANT]
> Rendelkeznie kell alter minden külső adatforrás engedélyt. Ez az engedély az ALTER DATABASE engedély része. Alter Bármely külső adatforrás-engedély szükséges az alapul szolgáló adatforrásra való hivatkozáshoz.
>

**Referenciaadatok**: A topológia referencia-adatkezeléshez használatos. Az alábbi ábrán két táblázat (T1 és T2) tárolható egy erre a célra létrehozott adatbázisban. Rugalmas lekérdezés használatával most már más adatbázisokból is hozzáférhet a T1 és T2 táblákhoz, ahogy az ábrán látható. 1. topológiát használjon, ha a referenciatáblák kis méretűek, vagy a referenciatáblába eső távoli lekérdezések szelektív predikátumokkal rendelkeznek.

**2. ábra** Függőleges particionálás – Rugalmas lekérdezés használata hivatkozási adatok lekérdezéséhez

![Függőleges particionálás – Rugalmas lekérdezés használata hivatkozási adatok lekérdezéséhez][3]

**Adatbázisközi lekérdezés:** A rugalmas lekérdezések olyan használati eseteket tesznek lehetővé, amelyek több SQL-adatbázis lekérdezését igénylik. ábra négy különböző adatbázist mutat be: CRM, Inventory, HR és Products. Az egyik adatbázisban végrehajtott lekérdezéseknek is hozzá kell férnie az egyik vagy az összes többi adatbázishoz. Egy rugalmas lekérdezés használatával konfigurálhatja az adatbázist az esethez néhány egyszerű DDL-utasítás futtatásával mind a négy adatbázison. Az egyszeri konfiguráció után a távoli táblához való hozzáférés olyan egyszerű, mint a Helyi táblára való hivatkozás a T-SQL-lekérdezésekből vagy az üzletiintelligencia-eszközökből. Ez a megközelítés ajánlott, ha a távoli lekérdezések nem adnak vissza nagy eredményeket.

**3. ábra** Függőleges particionálás – Rugalmas lekérdezés használata különböző adatbázisok lekérdezéséhez

![Függőleges particionálás – Rugalmas lekérdezés használata különböző adatbázisok lekérdezéséhez][4]

A következő lépések rugalmas adatbázis-lekérdezéseket konfigurálnak olyan vertikális particionálási forgatókönyvekhez, amelyek hozzáférést igényelnek egy távoli SQL-adatbázisokban található táblához ugyanazzal a sémával:

* [MASTER KEY létrehozása](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
* [ADATBÁZIS-hatókör hitelesítő adatok hitelesítő adatainak létrehozása](https://msdn.microsoft.com/library/mt270260.aspx)
* **RDBMS** típusú [KÜLSŐ ADATFORRÁS létrehozása/LEDOBÁSa](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource
* [KÜLSŐ TÁBLA létrehozása/ledobása](https://msdn.microsoft.com/library/dn935021.aspx) mytable

A DDL utasítások futtatása után a távoli "mytable" táblát úgy érheti el, mintha egy helyi tábla lenne. Az Azure SQL Database automatikusan megnyitja a kapcsolatot a távoli adatbázissal, feldolgozza a kérelmet a távoli adatbázisban, és visszaadja az eredményeket.

## <a name="horizontal-partitioning---sharding"></a>Vízszintes particionálás - horizontális

Rugalmas lekérdezés használatával jelentési feladatok végrehajtása egy horizontálisan particionált, azaz horizontálisan particionált, adatréteg hez egy [rugalmas adatbázis-szegmens leképezésaz](sql-database-elastic-scale-shard-map-management.md) adatréteg adatbázisainak ábrázolásához. Ebben a forgatókönyvben általában csak egyetlen szegmensleképezés használatos, és egy dedikált, rugalmas lekérdezési képességekkel (fő csomóponttal) rendelkező adatbázis szolgál a lekérdezések jelentésezési pontjaként. Csak ez a dedikált adatbázis szükséges hozzáférést a shard térképhez. 4. ábra bemutatja ezt a topológia és a konfiguráció a rugalmas lekérdezési adatbázis és a shard térkép. Az adatrétegben lévő adatbázisok bármely Azure SQL Database-verzióval vagy -kiadással lehetnek. A rugalmas adatbázis-ügyfélkódtárról és a szegmensleképezések létrehozásáról további információt a [Shard térképkezelés című](sql-database-elastic-scale-shard-map-management.md)témakörben talál.

**4.** Horizontális particionálás – rugalmas lekérdezés használata a szilánkos adatrétegek jelentéséhez

![Horizontális particionálás – rugalmas lekérdezés használata a szilánkos adatrétegek jelentéséhez][5]

> [!NOTE]
> Rugalmas lekérdezési adatbázis (fő csomópont) lehet külön adatbázis, vagy lehet ugyanaz az adatbázis, amely a szegmens leképezést üzemelteti.
> Bármilyen konfigurációt is választ, győződjön meg arról, hogy a szolgáltatási szint és az adatbázis számítási mérete elég magas ahhoz, hogy kezelje a bejelentkezési/lekérdezési kérelmek várható mennyiségét.

A következő lépések rugalmas adatbázis-lekérdezéseket konfigurálnak olyan horizontális particionálási forgatókönyvekhez, amelyek több távoli SQL-adatbázisban található táblák készletéhez való hozzáférést igényelnek:

* [MASTER KEY létrehozása](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql) mymasterkey
* [ADATBÁZIS-hatókör hitelesítő adatok hitelesítő adatainak létrehozása](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql)
* Hozzon létre egy [shard térképet,](sql-database-elastic-scale-shard-map-management.md) amely az adatréteget a rugalmas adatbázis-ügyfélkódtár használatával.
* [KÜLSŐ ADATFORRÁS LÉTREHOZÁSA/ELDOBÁSA](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql) **SHARD_MAP_MANAGER**
* [KÜLSŐ TÁBLA létrehozása/ledobása](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql) mytable

Miután elvégezte ezeket a lépéseket, elérheti a vízszintesen particionált tábla "mytable", mintha egy helyi tábla. Az Azure SQL Database automatikusan megnyit több párhuzamos kapcsolatot a távoli adatbázisokhoz, ahol a táblák fizikailag tárolódnak, feldolgozza a távoli adatbázisokon lévő kérelmeket, és visszaadja az eredményeket.
A horizontális particionálási forgatókönyvhöz szükséges lépésekről a [horizontális particionálás rugalmas lekérdezésében](sql-database-elastic-query-horizontal-partitioning.md)talál további információt.

A kódolás megkezdéséhez olvassa el a [horizontális particionálás (horizontális felosztás) rugalmas lekérdezésének első lépéseit.](sql-database-elastic-query-getting-started.md)

> [!IMPORTANT]
> A rugalmas lekérdezés sikeres végrehajtása az adatbázisok nagy készletén nagymértékben függ az egyes adatbázisok rendelkezésre állásától a lekérdezés végrehajtása során. Ha az adatbázisok egyike nem érhető el, a teljes lekérdezés sikertelen lesz. Ha azt tervezi, hogy egyszerre több száz vagy több ezer adatbázist szeretne lekérdezni, győződjön meg arról, hogy az ügyfélalkalmazás újrapróbálkozási logikával rendelkezik, vagy fontolja meg [a rugalmas adatbázis-feladatok](https://docs.microsoft.com/azure/sql-database/sql-database-job-automation-overview#elastic-database-jobs-preview) (előzetes verzió) és az adatbázisok kisebb részhalmazainak lekérdezését, az egyes lekérdezések eredményeit egyetlen célba összesítve.

## <a name="t-sql-querying"></a>T-SQL lekérdezés

Miután definiálta a külső adatforrásokat és a külső táblákat, normál SQL Server kapcsolati karakterláncokkal csatlakozhat azokhoz az adatbázisokhoz, amelyeken a külső táblákat definiálta. Ezután a kapcsolaton futtathatja a T-SQL-utasításokat a külső táblákon az alábbiakban ismertetett korlátozásokkal. A T-SQL-lekérdezésekre vonatkozó további információkat és példákat a [horizontális particionálásés](sql-database-elastic-query-horizontal-partitioning.md) [függőleges particionálás](sql-database-elastic-query-vertical-partitioning.md)dokumentációs témaköreiben talál.

## <a name="connectivity-for-tools"></a>Eszközök csatlakoztatása

A hagyományos SQL Server kapcsolati karakterláncok segítségével összekapcsolhatja az alkalmazásokat és az üzletiintelligencia-eszközöket vagy az adatintegrációs eszközöket külső táblákkal tartalmazó adatbázisokhoz. Győződjön meg arról, hogy az SQL Server támogatja az eszköz adatforrásaként. A csatlakozás után tekintse meg a rugalmas lekérdezési adatbázist és az adatbázis külső tábláit, ugyanúgy, mint bármely más SQL Server-adatbázist, amelyhez az eszközzel csatlakozik.

> [!IMPORTANT]
> Az Azure Active Directory rugalmas lekérdezésekkel történő hitelesítése jelenleg nem támogatott.

## <a name="cost"></a>Költségek

Rugalmas lekérdezés szerepel az Azure SQL Database-adatbázisok költségét. Vegye figyelembe, hogy azok a topológiák, ahol a távoli adatbázisok a rugalmas lekérdezési végponttól eltérő adatközpontban találhatók, támogatottak, de a távoli adatbázisokból származó adatforgalom érti a díjakat rendszeresen [az Azure-díjak.](https://azure.microsoft.com/pricing/details/data-transfers/)

## <a name="preview-limitations"></a>Előnézeti korlátozások

* Az első rugalmas lekérdezés futtatása akár néhány percet is igénybe vehet a standard szolgáltatási szinten. Ez az idő szükséges a rugalmas lekérdezési funkció betöltéséhez; a betöltési teljesítmény a magasabb szolgáltatási szintek és számítási méretek miatt javul.
* Az SSMS-ből vagy az SSDT-ből származó külső adatforrások vagy külső táblák parancsfájlok használata még nem támogatott.
* Az SQL DB importálása/exportálása még nem támogatja a külső adatforrásokat és a külső táblákat. Ha az Importálás/exportálás t szeretné használni, az exportálás előtt dobja ki ezeket az objektumokat, majd az importálás után hozza létre újra őket.
* Rugalmas lekérdezés jelenleg csak támogatja az írásvédett hozzáférést a külső táblákhoz. A t-SQL teljes funkcionalitását azonban használhatja azon az adatbázisban, ahol a külső tábla definiálva van. Ez hasznos lehet például az ideiglenes eredmények megőrzéséhez például a SELECT <column_list> INTO <local_table> használatával, vagy a rugalmas lekérdezési adatbázisban tárolt eljárások meghatározásához, amelyek külső táblákra hivatkoznak.
* Az nvarchar(max) kivételével a LOB-típusokat (beleértve a térbeli típusokat is) a külső tábladefiníciók nem támogatják. Kerülő megoldásként létrehozhat egy nézetet a távoli adatbázisban, amely a LOB-típust nvarchar(max. értékre) veti, a külső táblát az alaptábla helyett a nézet fölé határozza meg, majd visszavetheti a lekérdezések eredeti LOB-típusába.
* Az eredményhalmazban lévő nvarchar(max) adattípus oszlopai letiltják az Elastic Query implementációjában használt speciális kötegelési technikát, és befolyásolhatják a lekérdezés teljesítményét nagyságrenddel, vagy akár két nagyságrenddel nem kanonikus használati esetekben, amikor nagy mennyiségű nem összesített adatok átvitele lekérdezés eredményeként történik.
* A külső táblákoszlop-statisztikái jelenleg nem támogatottak. A táblastatisztikák támogatottak, de manuálisan kell létrehozni őket.
* A rugalmas lekérdezés csak az Azure SQL Database-rel működik. Nem használhatja a helyszíni SQL Server vagy az SQL Server lekérdezésére egy virtuális gép.

## <a name="feedback"></a>Visszajelzés

Az alábbiakban, az MSDN fórumokon vagy a Stack Overflow-n megoszthatja velünk a rugalmas lekérdezésekkel kapcsolatos tapasztalataival kapcsolatos visszajelzéseket. Érdeklődünk mindenféle visszajelzés a szolgáltatásról (hibák, durva élek, funkció hiányosságok).

## <a name="next-steps"></a>További lépések

* Függőleges particionálási oktatóanyag: [Első lépések adatbázisközi lekérdezéssel (függőleges particionálás)](sql-database-elastic-query-getting-started-vertical.md).
* A vertikálisan particionált adatok szintaxisát és mintalekérdezéseit a [Vertikálisan particionált adatok lekérdezése) témakörben találja.](sql-database-elastic-query-vertical-partitioning.md)
* Horizontális particionálási (horizontális) oktatóanyag: [A rugalmas lekérdezés horizontális particionálás (horizontális sáfálás) című témakörben található.](sql-database-elastic-query-getting-started.md)
* A vízszintesen particionált adatok szintaxisát és mintalekérdezéseit [lásd: Horizontálisan particionált adatok lekérdezése)](sql-database-elastic-query-horizontal-partitioning.md)
* [Lásd:\_ \_sp távoli végrehajtása](https://msdn.microsoft.com/library/mt703714) egy tárolt eljárás, amely végrehajtja a Transact-SQL utasításegyetlen távoli Azure SQL-adatbázis vagy egy horizontális particionálási séma szegmensként szolgáló adatbázisok készlete.

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png
[3]: ./media/sql-database-elastic-query-overview/vertpartrrefdata.png
[4]: ./media/sql-database-elastic-query-overview/verticalpartitioning.png
[5]: ./media/sql-database-elastic-query-overview/horizontalpartitioning.png

<!--anchors-->
