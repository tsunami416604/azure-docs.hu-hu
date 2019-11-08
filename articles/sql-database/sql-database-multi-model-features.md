---
title: Többmodelles képességek
description: Azure SQL Database lehetővé teszi több adatmodell használatát ugyanabban az adatbázisban.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/17/2018
ms.openlocfilehash: 2e8519fa8d96b7fe016b9da4ba84ce481a57d94e
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73802810"
---
# <a name="multi-model-capabilities-of-azure-sql-database"></a>Azure SQL Database több modellből álló képességei

A többmodelles adatbázisok lehetővé teszik több adatformátumban (például a kapcsolatok, a diagramok, a JSON/XML-dokumentumok, a kulcs-érték párok stb.) történő tárolását és használatát.

## <a name="when-to-use-multi-model-capabilities"></a>Mikor kell használni a többmodelles funkciókat?

A Azure SQL Database úgy lett kialakítva, hogy működjön együtt a legjobb teljesítményt nyújtó, a legtöbb esetben a különböző általános célú alkalmazások esetében. A Azure SQL Database azonban nem kizárólag a kapcsolódó adatmennyiségre korlátozódik. A Azure SQL Database lehetővé teszi, hogy a kapcsolati modellbe szorosan integrált, különböző, nem rokon formátumokat használjon.
A következő esetekben érdemes megfontolni a Azure SQL Database többmodelles funkcióinak használatát:
- Olyan információkkal vagy struktúrákkal rendelkezik, amelyek jobban illeszkednek a NoSQL-modellekhez, és nem szeretne külön NoSQL-adatbázist használni.
- Az adatai többsége megfelelő a rokon modellekhez, és az adatai egyes részeit NoSQL stílusban kell modellezni.
- Széles körű Transact-SQL nyelvet szeretne használni a NoSQL-és adatelemzési lekérdezések lekérdezéséhez és elemzéséhez, valamint az SQL nyelvet használó különféle eszközök és alkalmazások integrálásához.
- Olyan adatbázis-funkciókat kíván alkalmazni, mint [a memórián belüli technológiák](sql-database-in-memory.md) a NoSQL-adatstruktúrák elemzésének vagy feldolgozásának javítása érdekében, [tranzakciós replikálás](sql-database-managed-instance-transactional-replication.md) vagy [olvasható replikák](sql-database-read-scale-out.md) használatával készítsen másolatot az adatairól a másik helyet és a kiszervezést végez néhány analitikus számítási feladatot az elsődleges adatbázisból.

## <a name="overview"></a>Áttekintés

Az Azure SQL a következő többmodelles funkciókat biztosítja:
- A [Graph-funkciók](#graph-features) lehetővé teszik, hogy az adatait csomópontok és élek szerint képviseljék, és szabványos Transact-SQL-lekérdezéseket használjon, amelyek a Graph `MATCH` operátorral bővülnek a gráf adatai lekérdezéséhez.
- A [JSON-funkciók](#json-features) lehetővé teszik a JSON-dokumentumok táblázatokba való behelyezését, a kapcsolódó adattípusok a JSON-dokumentumokra való átalakítását és fordítva. A normál Transact-SQL nyelvet a JSON-függvények használatával a dokumentumok elemzéséhez használhatja, a nem fürtözött indexeket, a oszlopcentrikus indexeket és a memóriára optimalizált táblákat pedig a lekérdezések optimalizálására használhatja.
- A [térbeli funkciók](#spatial-features) lehetővé teszik a földrajzi és a geometriai adattárolást, a térbeli indexekkel való indexelést, valamint a térbeli lekérdezések használatával történő lekérését.
- Az [XML-funkciók](#xml-features) lehetővé teszik az XML-adatok tárolását és indexelését az adatbázisban, és natív XQuery/XPath műveleteket használhatnak az XML-adatokkal való munkavégzéshez. Az Azure SQL Database speciális beépített XML-lekérdezési motorral rendelkezik, amely XML-adatfeldolgozást dolgoz fel.
- A [kulcs-érték párok](#key-value-pairs) nincsenek kifejezetten támogatva speciális funkciókként, mivel a kulcs-érték párok natív módon, kétoszlopos táblákként vannak modellezve.

  > [!Note]
  > Ugyanazzal a Transact-SQL-lekérdezéssel a JSON Path kifejezés, a XQuery/XPath kifejezések, a térbeli függvények és a Graph-lekérdezés kifejezéseket használhatja az adatbázisban tárolt adatok eléréséhez. Emellett a Transact-SQL-lekérdezéseket végrehajtó bármely eszköz vagy programozási nyelv is használhatja a lekérdezési felületet a többmodelles adattípusokhoz való hozzáféréshez. Ez a különbség a többmodelles adatbázisok, például a különböző adatmodellek speciális API-ját biztosító [Azure Cosmos db](/azure/cosmos-db/) összehasonlítva.

A következő szakaszokban megismerheti az Azure-SQL Database legfontosabb többmodelles funkcióit.

## <a name="graph-features"></a>Gráffunkciók

A Azure SQL Database gráf-adatbázis-képességeket kínál a több-a-többhöz relációk modellezéséhez az adatbázisban. A gráfok csomópontok (vagy csúcspontok) és élek (vagy kapcsolatok) gyűjteményei. A csomópontok egy entitást (például egy személyt vagy szervezetet) jelölnek, és az Edge az általa összekapcsolt két csomópont (például a likes vagy a barátok) közötti kapcsolatot jelöli. Íme néhány olyan funkció, amely egyedi gráf-adatbázist tesz elérhetővé:
- Az élek vagy a kapcsolatok az első osztályú entitások egy gráf-adatbázisban, és rendelkezhetnek hozzájuk társított attribútumokkal vagy tulajdonságokkal.
- Egyetlen Edge rugalmasan tud csatlakoztatni több csomópontot egy gráf-adatbázisban.
- Egyszerűen kipróbálhatja a minta egyeztetését és a több ugrásos navigációs lekérdezéseket.
- A tranzitív lezáró és a polimorf lekérdezéseket egyszerűen kipróbálhatja.

A Graph-kapcsolatok és a Graph lekérdezési képességek integrálva vannak a Transact-SQL szolgáltatásba, és a SQL Server használatának előnyeit kapják meg az alapvető adatbázis-kezelő rendszerként.
A [Graph Processing](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) a Core SQL Server adatbázismotor-szolgáltatás, így további információk is megtalálhatók a gráf feldolgozásáról.

### <a name="when-to-use-a-graph-capability"></a>Mikor használjon gráf-képességet

A Graph-adatbázis nem érhető el, mert nem érhető el a viszonyítási adatbázis használatával. A Graph-adatbázisok azonban egyszerűbbé tehetik bizonyos lekérdezések kifejtését. A másik lehetőség közül választhat a következő tényezők alapján:

- Olyan hierarchikus adat modellezése, amelyben egy csomópont több szülővel rendelkezhet, így a HierarchyId nem használható
- A modellben az alkalmazás komplex több-a-többhöz kapcsolatot tartalmaz; az alkalmazás fejlődése során új kapcsolatok lesznek hozzáadva.
- Össze kell elemezni a kapcsolattal összekapcsolt adatkapcsolatokat és kapcsolatokat.

## <a name="json-features"></a>JSON-funkciók

Azure SQL Database lehetővé teszi a JavaScript Object Notation [(JSON)](https://www.json.org/) formátumban ábrázolt adatelemzést és lekérdezéseket, és a rokoni adatait JSON-szövegként exportálja.

A JSON a modern webes és mobil alkalmazások adatcseréjéhez használt népszerű adatformátum. A JSON-t a részben strukturált, a naplófájlokban vagy NoSQL-adatbázisokban (például [Azure Cosmos db](https://azure.microsoft.com/services/cosmos-db/)) tárolt fájlok tárolására is használják. Számos REST-webszolgáltatásnak az eredményeket JSON-szövegként kell megadnia, vagy JSON-ként formázott adatokhoz kell fogadnia. A legtöbb Azure-szolgáltatás, például az [azure Cognitive Search](https://azure.microsoft.com/services/search/), az [Azure Storage](https://azure.microsoft.com/services/storage/)és a [Azure Cosmos db](https://azure.microsoft.com/services/cosmos-db/) olyan Rest-végpontokkal rendelkezik, amelyek JSON-t adnak vissza vagy használnak fel.

Azure SQL Database lehetővé teszi, hogy könnyen működjön a JSON-adataival, és integrálja az adatbázist a modern szolgáltatásokkal. A Azure SQL Database a következő funkciókat biztosítja a JSON-adatok használatához:

![JSON-függvények](./media/sql-database-json-features/image_1.png)

Ha JSON-szöveggel rendelkezik, kinyerheti az adatait a JSON-ból, vagy ellenőrizheti, hogy a JSON formátuma megfelelően formázott-e a beépített függvények [JSON_VALUE](https://msdn.microsoft.com/library/dn921898.aspx), [JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx)és [ISJSON](https://msdn.microsoft.com/library/dn921896.aspx)használatával. A [JSON_MODIFY](https://msdn.microsoft.com/library/dn921892.aspx) függvény lehetővé teszi az érték JSON-szövegen belüli frissítését. A speciális lekérdezés és elemzés érdekében a [openjson utasítással](https://msdn.microsoft.com/library/dn921885.aspx) függvény JSON-objektumok tömbjét is át tudja alakítani sorok halmazára. Bármely SQL-lekérdezés végrehajtható a visszaadott eredményhalmaz alapján. Végezetül van egy [for JSON](https://msdn.microsoft.com/library/dn921882.aspx) záradék, amely lehetővé teszi, hogy a rokon táblákban tárolt adatait JSON-szövegként formázza.

További információkért lásd: [JSON-adatok használata az Azure-ban SQL Database](sql-database-json-features.md).
A [JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) a Core SQL Server adatbázismotor szolgáltatás, így további információ található a JSON szolgáltatásról.

### <a name="when-to-use-a-json-capability"></a>Mikor használjon JSON-képességet

A kapcsolódó modellek helyett a dokumentumok modelljei is használhatók a különböző helyzetekben:
- A séma nagy mértékű normalizálása nem jelent jelentős előnyöket, mert egyszerre fér hozzá az összes mezőhöz, vagy ha soha nem frissíti az objektumok normalizált részeit. A normalizált modell azonban növeli a lekérdezések összetettségét, mert a nagy számú tábla, amelyhez az adatgyűjtéshez csatlakoznia kell.
- A JSON-dokumentumokat natív módon használó alkalmazásokkal dolgozik a kommunikációs vagy adatmodellekben, és nem szeretne olyan további réteget bevezetni, amely átalakítja a kapcsolati adatforgalmat a JSON-ba, és fordítva.
- Egyszerűsíteni kell az adatmodellt a gyermektábla vagy az entitás-érték mintázatok normalizálása révén.
- JSON formátumban tárolt adatterhelést kell betöltenia vagy exportálnia anélkül, hogy további eszközt kellene elemezni.

## <a name="spatial-features"></a>Térbeli funkciók

A térbeli adatok a geometriai objektumok fizikai helyével és alakával kapcsolatos információkat jelölik. Ezek az objektumok lehetnek pontok vagy összetettebb objektumok, például országok/régiók, utak vagy tavak.

Azure SQL Database két térbeli adattípust támogat: a geometria adattípust és a földrajz adattípust.
- A geometriai típus egy euklideszi (Flat) koordináta-rendszerbeli adathalmazt jelöl.
- A földrajzi típus a kör alakú koordináta-rendszerbeli adathalmazokat jelöli.

Több térbeli objektum is használható az Azure SQL Database-ben, például [pont](https://docs.microsoft.com/sql/relational-databases/spatial/point), [LineString](https://docs.microsoft.com/sql/relational-databases/spatial/linestring), [sokszög](https://docs.microsoft.com/sql/relational-databases/spatial/polygon)stb.

A Azure SQL Database olyan speciális [térbeli indexeket](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-indexes-overview) is biztosít, amelyek a térbeli lekérdezések teljesítményének javítására használhatók.

A [térbeli támogatás](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) a Core SQL Server adatbázismotor-szolgáltatás, így további információ található a térbeli szolgáltatásról.

## <a name="xml-features"></a>XML-funkciók

A SQL Server hatékony platformot biztosít a félig strukturált adatkezeléshez használható sokoldalú alkalmazások fejlesztéséhez. Az XML-támogatás integrálva van a SQL Server összes összetevőjébe, és a következőket tartalmazza:

- Az XML-adattípus. Az XML-értékek natív módon tárolhatók egy XML-adattípusú oszlopban, amely XML-sémák gyűjteménye szerint írható, vagy a típus nem hagyható el. Az XML-oszlop indexelése megtehető.
- Az XML-típus oszlopaiban és változójában tárolt XML-adatmennyiség XQuery-lekérdezésének megadására van lehetőség. A XQuery funkciói olyan Transact-SQL-lekérdezésekben használhatók, amelyek az adatbázisban használt adatmodellekhez férnek hozzá.
- Az XML-dokumentumok összes elemét automatikusan indexeli az [elsődleges XML-index](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#primary-xml-index) használatával, vagy megadhatja a [másodlagos XML-index](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#secondary-xml-indexes)használatával indexelni kívánt elérési utakat.
- OPENROWSET, amely lehetővé teszi az XML-adatmennyiségek tömeges betöltését.
- A viszonyítási adattartalom átalakítása XML-formátumba.

Az [XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server) a Core SQL Server adatbázismotor szolgáltatás, így további információk is megtalálhatók az XML-szolgáltatásról.

### <a name="when-to-use-an-xml-capability"></a>Mikor kell XML-képességet használni

A kapcsolódó modellek helyett a dokumentumok modelljei is használhatók a különböző helyzetekben:
- A séma nagy mértékű normalizálása nem jelent jelentős előnyöket, mert egyszerre fér hozzá az összes mezőhöz, vagy ha soha nem frissíti az objektumok normalizált részeit. A normalizált modell azonban növeli a lekérdezések összetettségét, mert a nagy számú tábla, amelyhez az adatgyűjtéshez csatlakoznia kell.
- Az XML-dokumentumokat natív módon használó alkalmazásokkal folytatott kommunikációt és adatmodelleket használ, és nem szeretne olyan további réteget bevezetni, amely átalakítja a kapcsolati adattípusokat az XML-be, és fordítva.
- Egyszerűsíteni kell az adatmodellt a gyermektábla vagy az entitás-érték mintázatok normalizálása révén.
- XML formátumban tárolt adatgyűjtést vagy-exportálást kell végeznie az adatelemzést elvégező további eszköz nélkül.

## <a name="key-value-pairs"></a>Kulcs-érték párok

Azure SQL Database nem rendelkezik olyan specializált típusokkal vagy struktúrákkal, amelyek támogatják a kulcs-érték párokat, mivel a kulcs-érték struktúrák natív módon, szabványos táblázatként jeleníthetők meg:

```sql
CREATE TABLE Collection (
  Id int identity primary key,
  Data nvarchar(max)
)
```

Ezt a kulcs-érték struktúrát úgy szabhatja testre, hogy korlátozás nélkül illeszkedjen az igényeihez. Az érték például lehet XML-dokumentum `nvarchar(max)` típus helyett, ha az érték JSON-dokumentum, `CHECK` korlátozást is beállíthat, amely ellenőrzi a JSON-tartalom érvényességét. A további oszlopok egy kulcsával kapcsolatos tetszőleges számú értéket adhat hozzá, a számított oszlopok és indexek hozzáadásával egyszerűsítheti és optimalizálhatja az adatelérést, valamint a tábla memóriához/optimalizált séma-táblaként való definiálásával jobb teljesítményt érhet el.

Megtudhatja, [hogyan használja a BWin a memóriában tárolt OLTP, hogy példátlan teljesítményt és méretezést](https://blogs.msdn.microsoft.com/sqlcat/20../../how-bwin-is-using-sql-server-2016-in-memory-oltp-to-achieve-unprecedented-performance-and-scale/) biztosítson a ASP.net gyorsítótárazási megoldás számára, amely másodpercenként 1.200.000-kötegeket ért el, például hogyan lehet a viszonyítási modellt hatékonyan használni kulcs-érték párok megoldásként eljárás.

## <a name="next-steps"></a>További lépések
Az Azure SQL Database-ben a többmodelles funkciók a Azure SQL Database és a SQL Server között megosztva SQL Server adatbázismotor alapszintű funkciói. Ha többet szeretne megtudni ezekről a funkciókról, látogasson el az SQL-kapcsolati adatbázis dokumentációs oldalaira:

* [Gráf feldolgozása](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview)
* [JSON-adatok](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server)
* [Térbeli támogatás](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [XML-adatfájlok](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server)
