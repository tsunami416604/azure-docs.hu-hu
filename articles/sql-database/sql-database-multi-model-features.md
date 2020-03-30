---
title: Többmodelles képességek
description: Az Azure SQL Database lehetővé teszi, hogy több adatmodellt is működjön ugyanabban az adatbázisban.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73802810"
---
# <a name="multi-model-capabilities-of-azure-sql-database"></a>Az Azure SQL Database többmodelles képességei

A többmodelles adatbázisok lehetővé teszik a több adatformátumban, például relációs adatokban, grafikonokban, JSON/XML-dokumentumokban, kulcsérték-párokban stb.

## <a name="when-to-use-multi-model-capabilities"></a>Mikor kell használni a többmodelles képességeket?

Az Azure SQL Database úgy van kialakítva, hogy a relációs modellel működjön együtt, amely a legtöbb esetben a legjobb teljesítményt nyújtja a különböző általános célú alkalmazások esetében. Azonban az Azure SQL Database nem korlátozódik a relációs adatok csak. Az Azure SQL Database lehetővé teszi, hogy a relációs modellbe szorosan integrált, nem relációs formátumok széles skáláját használja.
Az Azure SQL Database többmodelles képességeit a következő esetekben érdemes használnia:
- Van néhány információ vagy struktúrák, amelyek jobban illeszkednek a NoSQL modellek, és nem szeretné használni külön NoSQL adatbázis.
- Az adatok többsége alkalmas relációs modell, és az adatok egyes részeit NoSQL stílusban kell modellezni.
- A rich Transact-SQL nyelvet a relációs és a NoSQL-adatok lekérdezéséhez és elemzéséhez, valamint az SQL-nyelvet használó különféle eszközökkel és alkalmazásokkal való integráláshoz szeretné felhasználni.
- Olyan adatbázis-funkciókat szeretne alkalmazni, mint például [a memórián belüli technológiák,](sql-database-in-memory.md) amelyek javítják az analitikus vagy a NoSQL adatstruktúrák feldolgozásának teljesítményét, [tranzakciós replikációt](sql-database-managed-instance-transactional-replication.md) vagy [olvasható replikákat](sql-database-read-scale-out.md) használva másolatot készítaz adatokról a másik helyen, és kiszervezhet néhány analitikus számítási feladatot az elsődleges adatbázisból.

## <a name="overview"></a>Áttekintés

Az Azure SQL a következő többmodelles szolgáltatásokat nyújtja:
- [A Graph-szolgáltatások](#graph-features) lehetővé teszik, hogy az adatokat csomópontok és élek halmazaként képviselje, és a diagramadatok lekérdezéséhez a gráfoperátorral `MATCH` bővített szabványos Transact-SQL-lekérdezéseket használja.
- [A JSON-funkciók](#json-features) lehetővé teszik, hogy JSON-dokumentumokat helyezzen a táblákba, a relációs adatokat JSON-dokumentumokká alakítsa át, és fordítva. A szabványos Transact-SQL nyelvet a JSON-függvényekkel bővített dokumentumok elemzéséhez használhatja, és nem fürtözött indexeket, oszlopcentrikus indexeket vagy memóriaoptimalizált táblákat használhat a lekérdezések optimalizálásához.
- [A térbeli jellemzők](#spatial-features) lehetővé teszik a földrajzi és geometriai adatok tárolását, a térbeli indexek használatával történő indexelését, valamint az adatok térbeli lekérdezések használatával történő beolvasását.
- [Az XML-szolgáltatások](#xml-features) lehetővé teszik az XML-adatok adatbázisban való tárolását és indexelését, valamint a natív XQuery/XPath-műveletek használatát az XML-adatokkal való munkához. Az Azure SQL-adatbázis speciális beépített XML-lekérdezési motorral rendelkezik, amely xml-adatokat dolgoz fel.
- [A kulcs-érték párok](#key-value-pairs) nem kifejezetten támogatottak speciális szolgáltatásokként, mivel a kulcs-érték párok natív módon kétoszlopos táblákként modellezhetők.

  > [!Note]
  > A JSON Path kifejezés, az XQuery/XPath-kifejezések, a térbeli függvények és a graph-query kifejezések ugyanabban a Transact-SQL lekérdezésben az adatbázisban tárolt adatok eléréséhez használhatók. Továbbá, minden olyan eszköz vagy programozási nyelv, amely képes végrehajtani transact-SQL lekérdezések, is használhatja, hogy a lekérdezési felület eléréséhez többmodelles adatokat. Ez a legfontosabb különbség a többmodelles adatbázisokhoz, például az [Azure Cosmos DB-hez](/azure/cosmos-db/) képest, amely speciális API-t biztosít a különböző adatmodellekhez.

A következő szakaszokban megismerheti az Azures SQL Database legfontosabb többmodelles képességeit.

## <a name="graph-features"></a>Gráffunkciók

Az Azure SQL Database graph adatbázis-képességeket kínál a több-a-többhöz kapcsolatok modellezéséhez az adatbázisban. A grafikon csomópontok (vagy csúcspontok) és élek (vagy kapcsolatok) gyűjteménye. A csomópont egy entitást (például egy személyt vagy szervezetet) jelöl, a perem pedig az általa összekötött két csomópont (például kedvelések vagy barátok) közötti kapcsolatot. Íme néhány olyan szolgáltatás, amely egyedivé teszi a gráfadatbázist:
- Az élek vagy kapcsolatok a Graph-adatbázis első osztályú entitásai, és attribútumokkal vagy tulajdonságokkal rendelkezhetnek.
- Egyetlen él rugalmasan csatlakoztathat több csomópontot egy gráf-adatbázisban.
- A mintaegyezést és a többugrásos navigációs lekérdezéseket egyszerűen kifejezheti.
- Könnyedén kifejezheti a tranzitív lezárást és a polimorf lekérdezéseket.

A gráfkapcsolatok és a gráflekérdezési képességek integrálva vannak a Transact-SQL-be, és az SQL Server alapszintű adatbázis-kezelő rendszerként való használatának előnyeit élvezik.
[A gráffeldolgozás](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) az SQL Server Database Engine alapvető szolgáltatása, így további információkat találhat a Graph feldolgozásról.

### <a name="when-to-use-a-graph-capability"></a>Mikor kell használni a grafikon képességét?

Nincs olyan, amit egy gráfadatbázis elérhetne, ami relációs adatbázissal nem érhető el. A gráfadatbázisok azonban megkönnyíthetik bizonyos lekérdezések kifejezését. Az Ön döntése, hogy válasszon egyet a másikkal szemben, a következő tényezőkön alapulhat:

- Modell hierarchikus adatok, ahol egy csomópontnak több szülője lehet, így a HierarchyId nem használható
- Modell az alkalmazás összetett több-a-többhöz kapcsolatok; az alkalmazás fejlődésével új kapcsolatok kerülnek hozzáadásra.
- Az összekapcsolt adatokat és kapcsolatokat kell elemeznie.

## <a name="json-features"></a>JSON-funkciók

Az Azure SQL Database lehetővé teszi a JavaScript-objektumjelölés [(JSON)](https://www.json.org/) formátumban ábrázolt adatok elemzésével és lekérdezésével, és a relációs adatok JSON-szövegként történő exportálását.

A JSON egy népszerű adatformátum, amelyet a modern webes és mobil alkalmazásokban történő adatcserére használnak. A JSON a félig strukturált adatok naplófájlokban vagy NoSQL-adatbázisokban, például az [Azure Cosmos DB-ben](https://azure.microsoft.com/services/cosmos-db/)való tárolására is használható. Számos REST webszolgáltatás JSON-szövegként formázott eredményeket ad vissza, vagy JSON formátumú adatokat fogad el. A legtöbb Azure-szolgáltatások, például [az Azure Cognitive Search,](https://azure.microsoft.com/services/search/) [az Azure Storage](https://azure.microsoft.com/services/storage/)és az [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) REST-végpontok, amelyek visszaadják vagy használják json.

Az Azure SQL Database lehetővé teszi a JSON-adatokkal való egyszerű munkát, és az adatbázis modern szolgáltatásokkal való integrálását. Az Azure SQL Database a következő funkciókat biztosítja a JSON-adatokkal való munkához:

![JSON függvények](./media/sql-database-json-features/image_1.png)

Ha JSON-szöveget használ, kinyerheti az adatokat a JSON-ból, vagy ellenőrizheti, hogy a JSON megfelelően van-e formázva a [beépített függvények használatával, JSON_VALUE](https://msdn.microsoft.com/library/dn921898.aspx), [JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx)és [ISJSON](https://msdn.microsoft.com/library/dn921896.aspx). A [JSON_MODIFY](https://msdn.microsoft.com/library/dn921892.aspx) függvény lehetővé teszi a JSON-szövegen belüli érték frissítését. A fejlettebb lekérdezés és elemzés, [OPENJSON](https://msdn.microsoft.com/library/dn921885.aspx) függvény képes átalakítani egy tömb JSON objektumok egy sor. Bármely SQL-lekérdezés végrehajtható a visszaadott eredményhalmazon. Végül van egy [FOR JSON](https://msdn.microsoft.com/library/dn921882.aspx) záradék, amely lehetővé teszi a relációs táblázatokban tárolt adatok JSON szövegként való formázását.

További információ: [JSON-adatok az azure SQL Database-ben.](sql-database-json-features.md)
[A JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) az SQL Server Database Engine alapvető szolgáltatása, így további információkat találhat a JSON szolgáltatásról.

### <a name="when-to-use-a-json-capability"></a>Mikor kell JSON-képességet használni?

A dokumentummodellek a relációs modellek helyett használhatók bizonyos esetekben:
- A séma magas normalizálása nem jár jelentős előnyökkel, mert egyszerre fér hozzá az objektumok összes mezőjéhez, vagy soha nem frissíti az objektumok normalizált részeit. A normalizált modell azonban növeli a lekérdezések összetettségét az adatok lekérdezik beszerezni szükséges táblák nagy száma miatt.
- A JSON-dokumentumokat natív módon használó alkalmazásokkal kommunikációs vagy adatmodellek, és nem szeretne további rétegeket bevezetni, amelyek a relációs adatokat JSON-ra alakítják át, és fordítva.
- Egyszerűsítenie kell az adatmodellt a gyermektáblák vagy az Entitás-objektum-érték minták normalizálásának csökkentésével.
- JSON formátumban tárolt adatokat kell betöltenie vagy exportálnia az adatokat elemző további eszköz nélkül.

## <a name="spatial-features"></a>Térbeli jellemzők

A térbeli adatok a geometriai objektumok fizikai helyével és alakjával kapcsolatos információkat jelölik. Ezek az objektumok lehetnek ponthelyek vagy összetettebb objektumok, például országok/régiók, utak vagy tavak.

Az Azure SQL Database két térbeli adattípust támogat – a geometriai adattípust és a földrajzi adattípust.
- A geometriatípus egy euklideszi (lapos) koordináta-rendszerben lévő adatokat jelöli.
- A földrajzi típus egy földkörüli koordináta-rendszerben lévő adatokat jelöli.

Számos térbeli objektum használható az Azure SQL-adatbázisban, például [a Point](https://docs.microsoft.com/sql/relational-databases/spatial/point), [LineString](https://docs.microsoft.com/sql/relational-databases/spatial/linestring), [Polygon](https://docs.microsoft.com/sql/relational-databases/spatial/polygon)stb.

Az Azure SQL Database speciális [térbeli indexeket](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-indexes-overview) is biztosít, amelyek a térbeli lekérdezések teljesítményének javítására használhatók.

[A térbeli támogatás](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) az SQL Server Database Engine alapvető szolgáltatása, így további információkat találhat az ottani térbeli szolgáltatásról.

## <a name="xml-features"></a>XML-szolgáltatások

Az SQL Server hatékony platformot biztosít a félig strukturált adatkezelés gazdag alkalmazásainak fejlesztéséhez. Az XML támogatása az SQL Server összes összetevőjébe integrálódik, és a következőket tartalmazza:

- Az XML-adattípus. Az XML-értékek natív módon tárolhatók egy XML-adattípus-oszlopban, amely XML-sémák gyűjteménye szerint írható be, vagy nem írható be. Az XML-oszlop indexelhető.
- XQuery-lekérdezés xml típusú oszlopokban és változókban tárolt XML-adatokkal való megadásának lehetősége. Az XQuery funkciói bármely Transact-SQL lekérdezésben használhatók, amely az adatbázisban használt bármely adatmodellhez hozzáfér.
- Az XML-dokumentumok összes elemének automatikus indexelése [elsődleges XML-index](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#primary-xml-index) használatával, vagy adja meg a [másodlagos XML-index](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#secondary-xml-indexes)használatával indexelendő pontos elérési utakat.
- OPENROWSET, amely lehetővé teszi az XML-adatok tömeges betöltését.
- Relációs adatok átalakítása XML formátumba.

[Az XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server) az SQL Server Database Engine alapvető szolgáltatása, így további információkat találhat az XML szolgáltatásról.

### <a name="when-to-use-an-xml-capability"></a>Mikor kell XML-képességet használni?

A dokumentummodellek a relációs modellek helyett használhatók bizonyos esetekben:
- A séma magas normalizálása nem jár jelentős előnyökkel, mert egyszerre fér hozzá az objektumok összes mezőjéhez, vagy soha nem frissíti az objektumok normalizált részeit. A normalizált modell azonban növeli a lekérdezések összetettségét az adatok lekérdezik beszerezni szükséges táblák nagy száma miatt.
- Az XML-dokumentumokat natív módon használó alkalmazásokkal kommunikációs vagy adatmodellek, és nem szeretne olyan további rétegeket bevezetni, amelyek a relációs adatokat XML-vé alakítják át, és fordítva.
- Egyszerűsítenie kell az adatmodellt a gyermektáblák vagy az Entitás-objektum-érték minták normalizálásának csökkentésével.
- Az XML formátumban tárolt adatokat az adatokat elemző további eszköz nélkül kell betöltenie vagy exportálnia.

## <a name="key-value-pairs"></a>Kulcs-érték párok

Az Azure SQL Database nem rendelkezik olyan speciális típusokkal vagy struktúrákkal, amelyek támogatják a kulcs-érték párokat, mivel a kulcs-érték struktúrák natív módon képviselhetők szabványos relációs táblákként:

```sql
CREATE TABLE Collection (
  Id int identity primary key,
  Data nvarchar(max)
)
```

Ezt a kulcs-érték struktúrát igény szerint, korlátok nélkül testreszabhatja. Például az érték lehet XML-dokumentum `nvarchar(max)` típus helyett, ha az érték JSON-dokumentum, a JSON-tartalom érvényességét ellenőrző megkötést helyezhet el. `CHECK` A további oszlopokban tetszőleges számú, egy kulcshoz kapcsolódó értéket helyezhet el, számított oszlopokat és indexeket adhat hozzá az adatelérés egyszerűsítéséhez és optimalizálásához, a táblát csak memória/optimalizált sématáblaként definiálhatja a jobb teljesítmény érdekében stb.

Tekintse meg, [hogy a BWin hogyan használja a memórián belüli OLTP-t, hogy soha nem látott teljesítményt és skálát érjen el](https://blogs.msdn.microsoft.com/sqlcat/20../../how-bwin-is-using-sql-server-2016-in-memory-oltp-to-achieve-unprecedented-performance-and-scale/) a ASP.NET gyorsítótárazási megoldásához, amely másodpercenként 1.200.000 köteget ért el, példaként arra, hogy a relációs modell hogyan használható hatékonyan kulcsérték-pár megoldásként a gyakorlatban.

## <a name="next-steps"></a>További lépések
Az Azure SQL-adatbázisok többmodelles képességei az SQL Server Database Engine alapvető szolgáltatásai is, amelyek az Azure SQL Database és az SQL Server között vannak megosztva. Ezekről a szolgáltatásokról az SQL Relációs adatbázis dokumentációs lapjain tájékozódhat:

* [Grafikonfeldolgozás](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview)
* [JSON-adatok](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server)
* [Térbeli támogatás](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [XML-adatok](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server)
