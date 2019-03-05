---
title: Az Azure SQL Database többmodelles képességei |} A Microsoft Docs
description: Az Azure SQL Database lehetővé teszi, hogy több adatmodellt ugyanabban az adatbázisban.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
manager: craigg
ms.date: 12/17/2018
ms.openlocfilehash: 69ca51776a61b43768ce7cb1565451c4f118de6e
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57316523"
---
# <a name="multi-model-capabilities-of-azure-sql-database"></a>Az Azure SQL Database többmodelles képességek

Többmodelles adatbázisok engedélyezése tárolhatja, és az adatok jelennek meg több adatformátumok a célnyelven például a relációs adatok, diagramok, JSON vagy XML-dokumentumok, kulcs-érték párok, stb.

Az Azure SQL Database használata a relációs modell, amely biztosítja a legjobb teljesítmény érdekében a legtöbb esetben a különböző általános célú alkalmazások célja. Azure SQL Database viszont nem relációs adatok csak korlátozott. Az Azure SQL Database számos különböző nem relációs formátumban, szorosan integrált a relációs modell használatát teszi lehetővé. Az Azure SQL a következő többmodelles szolgáltatásokat biztosítja:
- [Graph-funkciók](#graph-features) lehetővé teszi az adatok csomópontok és élek tartozik, és a graph fokozott standard Transact-SQL-lekérdezések használata `MATCH` operátor a grafikon adatainak lekérdezéséhez.
- [JSON-funkcióit](#json-features) lehetővé teszi a JSON-dokumentumok put táblákban, alakíthat át adatokat relációs, JSON-dokumentumokat, és ez fordítva is igaz. A JSON-dokumentumok elemzéséhez függvényekkel fokozott standard Transact-SQL nyelvet használja, és nem fürtözött indexek, az oszlopcentrikus indexek vagy memóriaoptimalizált táblák segítségével optimalizálható a lekérdezéseket.
- [Térbeli funkciók](#spatial-features) lehetővé teszi a geometriai, földrajzi és adatok tárolására, a térbeli indexekkel indexelés és térbeli lekérdezéseket használó adatok lekéréséhez.
- [XML-funkciók](#xml-features) engedélyezése tárolhatja, és az adatbázis adatainak XML index és natív XQuery/XPath műveletek használható az XML-adataiban. Az Azure SQL database beépített XML lekérdezési motor, amely XML-adatok feldolgozására van speciális.
- [Kulcs-érték párok](#key-value-pairs) nem explicit módon támogatottak különleges funkciókat, mivel a kulcs-érték Párizs két oszlop táblákként natív módon modellezhető.

  > [!Note]
  > Segítségével JSON-útvonalának kifejezését, XQuery/XPath kifejezés, térbeli funkciók és graph-lekérdezési kifejezések ugyanabban a Transact-SQL-lekérdezésben hozzáférni az adatbázisban tárolt adatokhoz. Emellett bármely eszköz vagy a programozási nyelv, amely hajthat végre lekérdezést Transact-SQL, használhatja a lekérdezési felületet többmodelles adatok eléréséhez. Ez a többmodelles adatbázisok képest például a fő különbség az [Azure Cosmos DB](/azure/cosmos-db/) speciális API-t biztosít különböző adatmodellek számára.

A következő szakaszokban megismerheti a Azures SQL Database legfontosabb többmodelles képességeit.

## <a name="graph-features"></a>Gráffunkciók

Az Azure SQL Database adatbázisban több-a-többhöz kapcsolatok modellezésére graph adatbázis-képességeket kínál. Gráf gyűjteménye csomópontok (vagy csúcspontok) és élek (vagy kapcsolatok). A csomópont egy entitás (például egy személy vagy szervezet) és a egy edge jelenti (a példában, a kedvelések vagy az ismerősök felvételének engedélyezése) csatlakozó a két csomópont közötti kapcsolatot. Az alábbiakban néhány olyan funkciókat egy gráfadatbázist egyedi:
- Élek és kapcsolatok keresési módjához első osztályú entitások egy Gráfadatbázist és is attribútumok vagy tulajdonságok társított őket.
- Egyetlen edge rugalmasan csatlakozhatnak egy Gráfadatbázist több csomópontján.
- Is express egyszerűen mintamegfeleltetés és lekérdezések Többugrásos navigáció.
- Tranzitív megszüntetésre és polimorf lekérdezések könnyedén is express.

A graph-kapcsolatok graph-lekérdezési képességek integrálva vannak a Transact-SQL és fogadni, az alapvető adatbázis-kezelő rendszer, az SQL Server használatának előnyeit.
[Gráffeldolgozási](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) van az alapvető SQL Server adatbázismotor-funkció, így található további információ a Graph feldolgozási hiba.

### <a name="when-to-use-a-graph-capability"></a>Mikor érdemes használni egy graph képesség

Nincs semmi gráfadatbázis érheti el, amelyek nem tudják elérni a relációs adatbázis. Azonban egy gráfadatbázist megkönnyítheti bizonyos lekérdezéseket Express. A választhat közülük a döntést a következő tényezőket:

- Ahol egy csomópont lehet több szülőkkel, így nem használható a HierarchyId hierarchikus adatok modellezése
- Modell van az alkalmazás összetett több-a-többhöz kapcsolatok; rendelkezik módon alakul ki az alkalmazást, új kapcsolatokat a rendszer hozzáadja.
- Összekapcsolt adatok és kapcsolatok elemezni kell.

## <a name="json-features"></a>JSON-funkciókkal

Az Azure SQL Database lehetővé teszi a elemzése és kérdezhet le adatokat a JavaScript Object Notation jelölt [(JSON)](http://www.json.org/) formázhatja és exportálni a relációs adatok JSON-szövegben.

JSON-ja egy modern webes és mobilalkalmazások az adatcsere használt népszerű adatok formátuma. JSON félig strukturált adatok tárolására, naplófájlokban vagy hasonló NoSQL-adatbázisok esetében is alkalmazható [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Számos REST webes szolgáltatás visszaadott eredmények formázott JSON-szöveget, vagy fogadja el az adatok JSON formátumú. A legtöbb Azure-szolgáltatások például [Azure Search](https://azure.microsoft.com/services/search/), [Azure Storage](https://azure.microsoft.com/services/storage/), és [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) adja vissza, vagy JSON felhasználása REST-végpontokat.

Az Azure SQL Database lehetővé teszi a JSON-adatok egyszerűen dolgozhat, és az adatbázis integrálása a modern szolgáltatások. Az Azure SQL Database a JSON-adatok használata a következő funkciókat biztosítja:

![JSON-függvények](./media/sql-database-json-features/image_1.png)

Ha JSON-szöveg, JSON-ból adatokat nyerhet ki, vagy győződjön meg arról, hogy a beépített funkciókkal megfelelően formázott JSON [JSON_VALUE](https://msdn.microsoft.com/library/dn921898.aspx), [JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx), és [ISJSON](https://msdn.microsoft.com/library/dn921896.aspx). A [JSON_MODIFY](https://msdn.microsoft.com/library/dn921892.aspx) funkció lehetővé teszi, hogy frissítse a JSON-szövegben lévő értéket. További speciális lekérdezés és elemzés céljából, [OPENJSON](https://msdn.microsoft.com/library/dn921885.aspx) függvény JSON-objektumok tömbje ismeretekké a sorkészletet. Bármely SQL-lekérdezés a visszaadott eredménykészlet hajtható végre. Végül pedig egy [FOR JSON](https://msdn.microsoft.com/library/dn921882.aspx) záradék, amely lehetővé teszi a JSON-szövegként a relációs táblákban tárolt adatok formázása.

További információkért lásd: [használata azure SQL Database-ben JSON-adatok](sql-database-json-features.md).
[JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) van core SQL Server adatbázismotor-funkció, így ott a JSON-szolgáltatással kapcsolatos további információ található.

### <a name="when-to-use-a-json-capability"></a>Mikor érdemes használni egy olyan JSON-funkció

A dokumentum modellek helyett a relációs modell különleges esetekben használhatók:
- Nagy-normalizálási séma nem használata jelentős előnyöket, mert az összes mezőt az objektumok egyszerre eléréséhez, vagy soha nem frissíti az objektumok normalizált részét. A normalizált modell azonban bonyolítják a táblákat, amelyek kell csatlakoztatni az adatok beolvasásához nagy száma miatt a lekérdezéseket.
- Dolgozunk az alkalmazásokat, hogy natív módon használható JSON-dokumentumok kommunikációs és adatmodelleket, és nem kívánja bevezetni a további rétegek, amelyek alakítja át az adatokat relációs, JSON, és ez fordítva is igaz.
- Egyszerűsítse az adatmodellben megszüntetéséhez normalizálása gyermek táblák vagy entitás értékű objektum minták kell.
- Kell betölteni vagy exportálhat adatokat anélkül, hogy néhány további eszköz, amely elemzi az adatokat JSON formátumban tárolja.

## <a name="spatial-features"></a>Térbeli funkciók

Térbeli adatok fizikai helyének és geometriai objektum alakját adatait jelöli. Ezek az objektumok pontok helykérelmei vagy összetett objektumok, például az országok, utak vagy Lake lehet.

Az Azure SQL Database támogatja a térbeli adatok kétféle - a geometria adattípust, és a földrajzi adatok típusa.
- A geometry típusú Euclidean (egyszerű) koordináta-rendszerére tárolt adatokat jelöli.
- A földrajzi hely típusa egy ciklikus-earth koordináta-rendszerére tárolt adatokat jelöli.

Térbeli objektumok, például az Azure SQL database-ben használható számos van [pont](https://docs.microsoft.com/sql/relational-databases/spatial/point), [LineString](https://docs.microsoft.com/sql/relational-databases/spatial/linestring), [sokszög](https://docs.microsoft.com/sql/relational-databases/spatial/polygon)stb.

Az Azure SQL Database emellett speciális [a térbeli indexek](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-indexes-overview) , amely használható a térbeli lekérdezések teljesítményének javítása érdekében.

[Térbeli támogatási](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) van core SQL Server adatbázismotor-funkció, így megtalálhatja a térbeli szolgáltatással kapcsolatos további információk.

## <a name="xml-features"></a>XML-funkciók

Az SQL Server hatékony platformot biztosít, részben strukturált adatok kezelésére gazdag alkalmazások fejlesztéséhez. Támogatja az XML integrálva van az SQL Server összes összetevőt, és a következőket tartalmazza:

- Az xml adattípust. XML-értékeket egy xml adattípusú oszlop XML-sémák gyűjteménye megfelelően írta be, vagy balra típus nélküli natív módon is tárolhatók. Az XML-oszlop indexelésére használhatja.
- Az XQuery-lekérdezést és változókat az xml-típusú oszlopokban tárolt XML-adatok megadásának lehetőségét. XQuery funkciókat is használható adatmodellek, amelyek használatával az adatbázis eléréséhez a Transact-SQL lekérdezések.
- XML-dokumentumok szereplő összes elem automatikus indexelése [elsődleges XML-index](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#primary-xml-index) , vagy adja meg a pontos elérési utakat kell indexelni használatával [másodlagos XML-index](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#secondary-xml-indexes).
- Az OPENROWSET, amely lehetővé teszi, hogy az XML-adatok tömeges betöltése.
- Relációs adatok XML-formátumba alakítsa át.

[XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server) van core SQL Server adatbázismotor-funkció, így hiba az XML-szolgáltatással kapcsolatos további információ található.

### <a name="when-to-use-an-xml-capability"></a>Mikor érdemes használni egy XML-képesség

A dokumentum modellek helyett a relációs modell különleges esetekben használhatók:
- Nagy-normalizálási séma nem használata jelentős előnyöket, mert az összes mezőt az objektumok egyszerre eléréséhez, vagy soha nem frissíti az objektumok normalizált részét. A normalizált modell azonban bonyolítják a táblákat, amelyek kell csatlakoztatni az adatok beolvasásához nagy száma miatt a lekérdezéseket.
- Dolgozunk az alkalmazásokat, hogy natív módon használható XML-dokumentumok kommunikáció vagy adatmodelleket, és nem kívánja bevezetni a további rétege, amely átalakítja a relációs adatok XML-fájlba, és ez fordítva is igaz.
- Egyszerűsítse az adatmodellben megszüntetéséhez normalizálása gyermek táblák vagy entitás értékű objektum minták kell.
- Kell betölteni vagy exportálhat adatokat anélkül, hogy néhány további eszköz, amely elemzi az adatokat az XML formátumban tárolja.

## <a name="key-value-pairs"></a>Kulcs-érték párok

Az Azure SQL Database speciális típusokat vagy struktúrák, amelyek támogatják a kulcs-érték párokat, kulcs-érték struktúrák natív módon jelölt standard relációs tábláival, mivel nem rendelkezik:

```sql
CREATE TABLE Collection (
  Id int identity primary key,
  Data nvarchar(max)
)
```

Ez a kulcs-érték struktúra korlátozások nélkül saját igényei szerint testre szabhatja. Tegyük fel, az érték lehet XML-dokumentumot `nvarchar(max)` típusa, ha az érték JSON-dokumentumok, helyezheti `CHECK` korlátozás, amely a JSON-tartalmak érvényességét ellenőrzi. Helyezzen egy kulcsot a további oszlopok értékeinek tetszőleges számú, adja hozzá a számított oszlopok és egyszerűbbé tétele és az adatelérés optimalizálása indexekkel, adja meg a tábla memóriaoptimalizált/csak a sémára vonatkozó táblaként való beolvasása jobb teljesítményt, és így tovább.

Lásd: [hogyan BWin használja In-Memory OLTP páratlan teljesítmény és méretezhetőség eléréséhez](https://blogs.msdn.microsoft.com/sqlcat/20../../how-bwin-is-using-sql-server-2016-in-memory-oltp-to-achieve-unprecedented-performance-and-scale/) saját ASP.NET-gyorsítótárazás megoldás, amely 1.200.000 elért kötegeli az másodpercenként példaként hogyan relációs modellt is hatékonyan használható kulcs-érték pár megoldás a gyakorlatban.

## <a name="next-steps"></a>További lépések
Többmodelles képességek az Azure SQL Database-adatbázisok is rendelkezésre állnak az SQL Server adatbázismotor alapszolgáltatások, amely az Azure SQL Database és az SQL Server között vannak megosztva. Ezek a funkciók kapcsolatos további részletekért látogasson el a relációs SQL database dokumentációs oldalát érintő:

* [Graph-feldolgozás](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview)
* [JSON-adatok](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server)
* [Térbeli támogatása](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [XML-adatok](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server)
