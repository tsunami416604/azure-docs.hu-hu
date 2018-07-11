---
title: Az Azure Cosmos DB – gyakori kérdések |} A Microsoft Docs
description: Válaszok az Azure Cosmos DB egy globálisan elosztott, többmodelles adatbázis-szolgáltatás – gyakori kérdések. Ismerje meg a kapacitás, a teljesítményszintek és a méretezés.
keywords: Adatbázissal kapcsolatos kérdések, gyakori kérdések, documentdb, azure, Microsoft azure
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: sngun
ms.openlocfilehash: 30ebe4f990dc65e53c34673f0948d3aa2240385c
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37859700"
---
# <a name="azure-cosmos-db-faq"></a>Az Azure Cosmos DB – gyakori kérdések
## <a name="azure-cosmos-db-fundamentals"></a>Az Azure Cosmos DB – alapok
### <a name="what-is-azure-cosmos-db"></a>Mi az Azure Cosmos DB?
Az Azure Cosmos DB egy globálisan replikált, többmodelles adatbázis-szolgáltatás, amely sémamentes adatok széleskörű lekérdezését, konfigurálható és megbízható teljesítményt nyújt, és gyors fejlesztést tesz lehetővé a. Ez a összes gazdafájlon keresztül, egy felügyelt platform, amely használatával a power és elérni a Microsoft Azure. 

Azure Cosmos DB a webes, mobil-, játék-, a megoldást és IoT-alkalmazásokat, ha kiszámítható teljesítményt, magas rendelkezésre állású, rövid késést, és a egy séma nélküli adatmodell kapcsolatos követelményeket. Séma rugalmasságát, és gazdag indexelésnek kínál, és több dokumentumos tranzakciótámogatást JavaScript-integrációval ellátott tartalmazza. 

További adatbázissal kapcsolatos kérdések, válaszok, és üzembe helyezése és ezzel a szolgáltatással kapcsolatos utasításokat lásd: a [Azure Cosmos DB-dokumentációs oldalát](https://docs.microsoft.com/azure/cosmos-db/).

### <a name="what-happened-to-the-documentdb-api"></a>Mi történt a DocumentDB API-hoz?

Az Azure Cosmos DB DocumentDB API-hoz vagy az SQL (DocumentDB) API most már az Azure Cosmos DB SQL API néven ismert. A DocumentDB API-val készített alkalmazások futtatását bármin változtatni nincs szükségünk. A funkció változatlan marad.

Ha a DocumentDB API-fiók, mielőtt, most már az SQL API-fiók, a számlázás változatlan. 

### <a name="what-happened-to-azure-documentdb-as-a-service"></a>Mi történt az Azure DocumentDB szolgáltatás?

Az Azure DocumentDB szolgáltatással most már az Azure Cosmos DB szolgáltatás részét képezi, és akkor jelentkezik, az SQL API formájában. Az Azure documentdb készített alkalmazások ellen az Azure Cosmos DB SQL API módosítások nélkül fog futni. Emellett az Azure Cosmos DB támogatja a Graph API-t, a Table API, a MongoDB API-t és a Cassandra API (előzetes verzió).

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Melyek a tipikus használati esetek, az Azure Cosmos DB?
Azure Cosmos DB a megfelelő választás az olyan új webes, mobil-, játék-, és fontos, ahol automatikus méretezés, a kiszámítható teljesítmény, a gyors ezredmásodperces válaszidők sorrendje, illetve a épülő lekérdezés képessége sémamentes adatokra IoT-alkalmazásokat. Az Azure Cosmos DB adatmodelljeinek gyors fejlesztési lehetőségeket és az alkalmazás folyamatos ismétlését. Felhasználó által létrehozott tartalom és adatokat kezelő alkalmazások [Azure Cosmos DB gyakori alkalmazási helyzetei](use-cases.md). 

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Hogyan kínál a Azure Cosmos DB kiszámítható teljesítményt?
A [kérelemegység](request-units.md) (RU) az Azure Cosmos DB-ben a teljesítmény mértékegysége. Egy 1 – RU átviteli sebesség az átviteli sebesség egy 1 KB-os dokumentum Get felel meg. Olvasási, írási, SQL-lekérdezések és a tárolt eljárás-végrehajtás, beleértve az Azure Cosmos DB-ben minden művelet értéke determinisztikus RU a művelet végrehajtásához szükséges teljesítmény alapján. Szem előtt tartva CPU, IO, és a memória és azok minden adatátviteli teljesítmény, helyett alkalmazásteljesítményre RU egyetlen mérték.

Egyes Azure Cosmos DB-tárolók a kiosztott átviteli sebesség szempontjából másodpercenkénti kérelemegység tartható fenn. Bármilyen méret esetén az alkalmazások benchmark egyes kérelmeket Kérelemegység-értékük, és kezelje az összes kérelem összes kérelemegységének tároló üzembe helyezése. Vertikális felskálázás is, vagy a tároló átviteli sebességet az alkalmazás változásával igényeinek megfelelően méretezhető. További információ a kérelemegységekről és a meghatározásához a tároló alkalmazástípusokról [átviteli sebesség becslése](request-units.md#estimating-throughput-needs) , és próbálkozzon a [átviteli Számológép](https://www.documentdb.com/capacityplanner). Az előfizetési időszak *tároló* itt hivatkozik egy SQL API-gyűjtemény, a Graph API-grafikon, a MongoDB API-gyűjtemény és a Table API-tábla hivatkozik. 

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Hogyan támogatja az Azure Cosmos DB különböző adatmodelleket, például a kulcs/érték, Oszlopalapú, a dokumentum és a graph?

Kulcs/érték (tábla), Oszlopalapú, dokumentum- és a modellek is natívan támogatja az ARS (atomokból, -rekordok és -sorozatok) miatt a tervezési, hogy az Azure Cosmos DB graph adatváltozásainak épül. Atom, a rekordokat és a feladatütemezések egyszerűen leképezve és előre jelzett különböző adatmodelleket. Az API-k, a modellek egy részhalmaza számára elérhető már zajlik (SQL, MongoDB, tábla és Graph API-k) és mások további adatmodellek jellemző elérhető lesz a jövőben.

Az Azure Cosmos DB egy séma sémafüggetlen indexelés összetevő, amely képes automatikusan indexeli az azt betöltő anélkül, hogy bármiféle séma vagy másodlagos indexek a fejlesztőtől származó összes adatot tartalmaz. A motor logikai index elrendezések (fordított, Oszlopalapú, fa), amelyek a tárolási elrendezés az index és a Lekérdezésfeldolgozás alrendszerek szétválaszthatók készletét támaszkodik. A cosmos DB lehetővé teszi az átviteli protokollokat és API-k támogatása kibővíthető módon és kéréseivé átalakítani azokat hatékonyan a core data model (1) és a logikai index elrendezések (2) így egyedileg képes, a natív módon több adatmodellt is tartalmaz.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Az Azure Cosmos DB a HIPAA szabványnak megfelelő?
Igen, az Azure Cosmos DB a HIPAA-szabványnak megfelelő. A HIPAA az egyéni és beazonosítható egészségügyi adatok használatára, nyilvánosságra hozatalára és védelmére vonatkozó követelményeket állapítja meg. További információkért lásd: [Microsoft Adatvédelmi központ](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Mik az Azure Cosmos DB tárolási korlátai?
Egy tárolót az Azure Cosmos DB tárolható adatok teljes mennyisége nincs korlátozva van.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Mik az Azure Cosmos DB átviteli sebességének korlátai?
Átviteli sebesség egy tárolót az Azure Cosmos DB által támogatott teljes mennyisége nincs korlátozva van. A fő cél, hogy a munkaterhelés nagyjából egyenletes elosztása megfelelően nagy számú partíciós kulcsok között.

### <a name="are-direct-and-gateway-connectivity-modes-encrypted-"></a>Közvetlen és az átjáró kapcsolódási módjai titkosított? 
Igen mindkét mód mindig teljes mértékben titkosított. 

### <a name="how-much-does-azure-cosmos-db-cost"></a>Azure Cosmos DB mennyibe?
Részletekért tekintse meg a [díjszabásáról az Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) lapot. Az Azure Cosmos DB-használati díjak üzembe helyezett tárolók, a tárolók is online órák száma számát határozza meg, és az egyes tárolók a kiosztott átviteli sebesség. Az előfizetési időszak *tárolók* itt az SQL API-gyűjtemény, a Graph API graph, a MongoDB API-gyűjtemény és a Table API-táblák hivatkozik. 

### <a name="is-a-free-account-available"></a>Van egy ingyenes fiókot?
Igen, akkor regisztráljon egy időben korlátozott fiók ingyenesen, kötelezettségvállalás nélkül. Ha szeretne regisztrálni, keresse fel a [próbálja ki ingyenesen az Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) vagy további információ: a [próbálja ki az Azure Cosmos DB – gyakori kérdések](#try-cosmos-db).

Ha most ismerkedik az Azure-ba, akkor regisztráljon egy [ingyenes Azure-fiók](https://azure.microsoft.com/free/), 30 napig révén, és, és próbálja ki az Azure-szolgáltatásokhoz. A Visual Studio-előfizetéssel rendelkezik, ha jogosult is [ingyenes Azure-kreditek](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) bármely Azure-szolgáltatások használatára. 

Is használhatja a [Azure Cosmos DB Emulatort](local-emulator.md) fejlesztéséhez és teszteléséhez az alkalmazás helyi ingyenes, az Azure-előfizetés létrehozása nélkül. Amikor már elégedett az alkalmazás működésével az Azure Cosmos DB Emulatorban, átválthat az Azure Cosmos DB-fiók használatára a felhőben.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Hogyan kaphatok további segítséget az Azure Cosmos DB?

Technikai kérdés, az alábbi két kérdés egyik közzététele, és válaszoljon a fórumok:
* [MSDN-fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-cosmosdb). A stack Overflow a legjobb a programozási kérdéseit. Ellenőrizze, hogy a kérdés [témához illeszkedő](https://stackoverflow.com/help/on-topic) és [adja meg a legtöbb adatot lehet, így a kérdést, egyértelmű és megválaszolható](https://stackoverflow.com/help/how-to-ask). 

Javasolhat új funkciókat, hozzon létre egy új kérelmet [Uservoice](https://feedback.azure.com/forums/263030-azure-cosmos-db).

Ha a fiókjával van probléma, nyújtson be [támogatási kérelmet](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) az Azure Portalon.

További kérdések küldheti el a csapatának [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com); Ez azonban nem a technikai támogatási címe. 

<a id="try-cosmos-db"></a>
## <a name="try-azure-cosmos-db-subscriptions"></a>Próbálja ki az Azure Cosmos DB-előfizetések

Most már élvezheti egy időben korlátozott Azure Cosmos DB nyújtotta előfizetés, díjfizetés és elköteleződés ingyenes nélkül. Próbálja ki az Azure Cosmos DB-előfizetéshez tartozó regisztráció, lépjen a [próbálja ki ingyenesen az Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/). Ebben az előfizetésben nem azonos az a [Azure ingyenes próbaverziója](https://azure.microsoft.com/free/), és használható mellett az Azure ingyenes próbaverziójára, vagy egy Azure-előfizetéssel. 

Próbálja meg az Azure Cosmos DB előfizetések jelennek meg az Azure portal mellett más előfizetésekkel társított felhasználói azonosítóját. 

A következő feltételek lesznek érvényesek, próbálja ki az Azure Cosmos DB-előfizetések:

* Egy tároló SQL, a Gremlin (Graph API-val) és a tábla fiókok előfizetésenként.
* MongoDB-fiókok előfizetésenként legfeljebb 3 gyűjteményeket.
* 10 GB tárolókapacitással.
* Globális replikálás érhető el a következő [Azure-régiók](https://azure.microsoft.com/regions/): USA középső RÉGIÓJA, Észak-Európa és Délkelet-Ázsia
* 5 ezer Kérelemegység/s maximális átviteli sebesség.
* Előfizetések 24 óra után lejárnak, és legfeljebb 48 óra teljes kiterjeszthető.
* Próbálja ki az Azure Cosmos DB-fiókok; nem hozható létre Azure-támogatási jegyek azonban támogatást is magukban a meglévő támogatási csomagokról-előfizetőknek. 

## <a name="set-up-azure-cosmos-db"></a>Állítsa be az Azure Cosmos DB-hez
### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Hogyan tudok regisztrálni az Azure Cosmos DB?
Az Azure Cosmos DB az Azure Portalon érhető el. Először regisztráljon egy Azure-előfizetést. Miután bejelentkezett a, is hozzáadhat egy SQL API-t, Graph API-t, Table API, a MongoDB API vagy Cassandra API-fiókot az Azure-előfizetéshez.

### <a name="what-is-a-master-key"></a>Mi a főkulcs?
A főkulcs egy biztonsági jogkivonat, amellyel egy fiók összes erőforrása elérhető. A főkulccsal rendelkező egyének olvasási és írási hozzáférés az adatbázis-fiókban lévő összes erőforrást. Körültekintően járjon el főkulcsok terjesztésekor. Az elsődleges és másodlagos főkulcsok érhetők el a **kulcsok** paneljén a [az Azure portal][azure-portal]. Kulcsokkal kapcsolatos további információkért lásd: [megtekintése, másolása és újragenerálása tárelérési kulcsok](manage-account.md#keys).

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>Mik azok a régiók, amelyek a PreferredLocations értékre lehet beállítani? 
A preferredlocations listában érték beállítható bármely Azure-régiót, amelyben Cosmos DB érhető el. Az elérhető régiók listáját lásd: [Azure-régiók](https://azure.microsoft.com/regions/).

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Van-e, hogy ügyelnie, ha az adatok eloszthatók a Azure-adatközpontok keresztül a világon? 
Az Azure Cosmos DB az összes Azure-régióban a megadott megtalálható a [Azure-régiók](https://azure.microsoft.com/regions/) lapot. Mivel a core-szolgáltatás, minden új adatközpont rendelkezik egy Azure Cosmos DB jelenlétét. 

Állíthat be egy régiót, ne feledje, hogy az Azure Cosmos DB betartja és a government szuverén felhőkben. Azt jelenti Ha létrehoz egy fiókot a egy [szuverén régió](https://azure.microsoft.com/global-infrastructure/), nem lehet replikálni, amely kívül [szuverén régió](https://azure.microsoft.com/global-infrastructure/). Hasonlóképpen egy külső partner szuverén helyekre történő replikáció nem engedélyezhető. 

### <a name="is-it-possible-to-switch-from-container-level-throughput-provisioning-to-database-level-throughput-provisioning-or-vice-versa"></a>Van lehetőség az átállás a tároló szint átviteli adatbázis-szintű teljesítmény kiépítés folyamatban? Vagy fordítva

Tároló és az adatbázis átviteli szintű üzembe helyezésének egymástól különálló ajánlatok, és mindkét közötti váltáskor szükséges áttelepítés forrás adatait a célhelyre. Ami azt jelenti, hogy hozzon létre egy új adatbázist vagy egy új gyűjteményt, és ezután telepítse át az adatok segítségével kell [tömeges végrehajtó könyvtár](bulk-executor-overview.md) vagy [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).


## <a name="develop-against-the-sql-api"></a>Fejlesztés az SQL API-n

### <a name="how-do-i-start-developing-against-the-sql-api"></a>Hogyan kezdhetem meg az SQL API fejlesztését?
Először, jelentkezzen Azure-előfizetésre. Regisztrációt követően az Azure-előfizetéssel, hozzáadhat egy SQL API-tárolót az Azure-előfizetéshez. Az Azure Cosmos DB-fiók hozzáadására vonatkozó utasításokért lásd: [hozzon létre egy Azure Cosmos DB-adatbázisfiók](create-sql-api-dotnet.md#create-account). 

[SDK-k](sql-api-sdk-dotnet.md) érhetők el .NET, Python, Node.js, JavaScript és Java. A fejlesztők is használhatja a [RESTful HTTP API-k](/rest/api/cosmos-db/) kommunikál a különböző platformok és nyelvek az Azure Cosmos DB erőforrásokat.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Hozzáférhet a bizonyos előre elkészített mintát üzembe?
Az SQL API-minták [.NET](sql-api-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](sql-api-nodejs-samples.md), és [Python](sql-api-python-samples.md) SDK a Githubon érhetők el.


### <a name="does-the-sql-api-database-support-schema-free-data"></a>Az SQL API-adatbázis támogatja a sémamentes adatokat?
Igen, az SQL API lehetővé teszi, hogy sémadefiníciók vagy mutatók nélkül tetszőleges JSON-dokumentumokat tárolhat alkalmazásokat. Adatok azonnal lekérdezhetők a Azure Cosmos DB SQL-lekérdezési felületén keresztül.  

### <a name="does-the-sql-api-support-acid-transactions"></a>Az SQL API támogatja az ACID-tranzakciókat?
Igen, az SQL API támogatja a JavaScript-tárolt eljárásokkal és eseményindítókkal kifejezett dokumentumok közötti tranzakciókat. Tranzakciók hatóköre a tárolók belül egyetlen partícióra és ACID szemantikákkal, "mindent vagy semmit," végrehajtani más párhuzamosan kódtól vagy felhasználói kérelmektől elkülönítve. Ha a kivételek jelentkeznek a JavaScript alkalmazáskód kiszolgálóoldali végrehajtási, a teljes tranzakció vissza lesz állítva. Tranzakciókkal kapcsolatos további információkért lásd: [adatbázis-program tranzakciókat](programming.md#database-program-transactions).

### <a name="what-is-a-container"></a>Mi az a tároló?
Egy tároló olyan dokumentumokat és a kapcsolódó JavaScript alkalmazáslogikát. Egy tároló-e egy számlázható entitás, ahol a [költség](performance-levels.md) az átviteli sebesség határozza meg, és a storage használt. A tárolók is kiterjedhet, egy vagy több partíció vagy a kiszolgálók és gyakorlatilag korlátlan mennyiségű tárterület vagy átviteli sebesség kezelésére méretezhetők. 

* SQL- és MongoDB API-fiókok esetében egy tároló képez le egy gyűjteményt. 
* Cassandra- és Table API-fiókok esetében a tároló képez le egy táblát. 
* Gremlin API-fiókok esetében egy tároló képez le egy grafikont. 

Tárolók is rendelkezésre állnak az Azure Cosmos DB számlázási egységei. Minden tároló számlázása óránként, a kiosztott átviteli sebesség alapján és a felhasznált tárterület. További információkért lásd: [Azure Cosmos DB szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/cosmos-db/). 

### <a name="how-do-i-create-a-database"></a>Hogyan hozható létre adatbázis?
Az adatbázist is létrehozhat a [az Azure portal](https://portal.azure.com)leírtak szerint [gyűjtemény hozzáadása](create-sql-api-dotnet.md#create-collection), egy, a [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md), vagy a [REST API-k](/rest/api/cosmos-db/). 

### <a name="how-do-i-set-up-users-and-permissions"></a>Hogyan állíthatom be a felhasználókat és engedélyeket?
Egyikének használatával hozhat létre felhasználókat és engedélyeket a [Cosmos DB API SDK-kkal](sql-api-sdk-dotnet.md) vagy a [REST API-k](/rest/api/cosmos-db/).  

### <a name="does-the-sql-api-support-sql"></a>Támogatja az SQL API-t-SQL?
Az SQL API-fiókok által támogatott SQL lekérdező nyelve az SQL Server által támogatott lekérdezési funkcionalitás továbbfejlesztett alkészlete. Az Azure Cosmos DB SQL-lekérdezési nyelvet biztosít a részletes hierarchikus és relációs operátorokat és bővíthetőséget a JavaScript-alapú, felhasználó által definiált függvények (UDF-EK). A JSON-szintaxis lehetővé teszi, hogy a JSON-dokumentumok modellezési olyan címkézett csomópontokhoz, amelyek az Azure Cosmos DB automatikus indexelési technikák és az Azure Cosmos DB SQL lekérdezési dialektusa egyaránt által használt. SQL-szintaxis használatával kapcsolatos információkért lásd: a [SQL-lekérdezés] [ query] cikk.

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>Az SQL API támogatja az SQL összesítő függvények?
Az SQL API támogatja a közel valós idejű aggregációs aggregátumfüggvények keresztül bármilyen méretben `COUNT`, `MIN`, `MAX`, `AVG`, és `SUM` az SQL-szintaxis használatával. További információkért lásd: [összesítő függvény](sql-api-sql-query.md#Aggregates).

### <a name="how-does-the-sql-api-provide-concurrency"></a>Hogyan nyújt az SQL API az egyidejűséget?
Az SQL API támogatja a HTTP entitáscímkék vagy ETag-EK keresztül hozzáférések optimista vezérlését (OCC). Minden SQL API-erőforrás rendelkezik egy ETag, és minden alkalommal, amikor a dokumentum frissül az ETag címke értéke a kiszolgálón. Az ETag fejrészt és a jelenlegi érték szerepel parancsválasz-üzeneteket. Döntse el, hogy egy erőforrás frissíteni kell a kiszolgáló ETag használható az If-Match fejlécet. If-Match értéke alapján kell ellenőrizni az ETag-érték. Ha az ETag-érték megfelel a kiszolgáló ETag-érték, az erőforrás frissítése. Ha az ETag címke már nem aktuális, a kiszolgáló elutasítja a műveletet egy "HTTP 412 sikertelen előfeltétel" válaszkóddal. Az ügyfél majd újból lekéri az erőforrás beszerezni az erőforrás jelenlegi ETag-érték. Emellett ETag segítségével az If-None-Match fejléc határozza meg, hogy szükség van-e az erőforrás ismét beolvassa.

Használjon optimista egyidejűséget, a .NET-ben, használja a [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) osztály. A .NET-minta: [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) a DocumentManagement minta a Githubon.

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>Hogyan végezhetők tranzakciók az SQL API-ban?
Az SQL API támogatja a nyelvintegrált tranzakciókat JavaScript-tárolt eljárásokkal és eseményindítókkal keresztül. Az összes művelet pillanatkép-elkülönítés lesznek végrehajtva. Ha egy egypartíciós gyűjtemény, a végrehajtás a gyűjtemény hatókörét. Ha a gyűjtemény particionált, a végrehajtás a gyűjteményen belül azonos partíciós kulccsal értékkel dokumentumok hatókörét. A tranzakció kezdetekor pillanatkép készül a dokumentumverziókról (ETag-ek), és csak akkor lesznek véglegesítve, ha a parancsfájl sikeres. Ha a JavaScript hibát jelez, a tranzakció vissza lesz állítva. További információkért lásd: [kiszolgálóoldali JavaScript programozás az Azure Cosmos DB](programming.md).

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Hogyan lehet I tömeges beszúrási dokumentumok az Cosmos DB-be?
Akkor is tömeges beszúrási dokumentumokat az Azure Cosmos DB-be a következő módszerek valamelyikével:

* A tömeges végrehajtó eszközt, a leírtak szerint [Using tömeges végrehajtó .NET-kódtár](bulk-executor-dot-net.md) és [tömeges végrehajtó Java-kódtár használata](bulk-executor-java.md)
* Az adatáttelepítés eszközzel leírtak szerint [az Azure Cosmos DB adatbázis-áttelepítési eszköz](import-data.md).
* Tárolt eljárások, leírtak szerint [kiszolgálóoldali JavaScript programozás az Azure Cosmos DB](programming.md).

### <a name="i-have-setup-my-container-to-use-lazy-indexing-i-see-that-my-queries-do-not-return-expected-results"></a>A telepítő használata a Lusta indexelő tárolóm van, látom, hogy a lekérdezés nem adott vissza várt eredményt. 
Az indexelő szakaszban leírtak ezt a viselkedést a lusta indexelési eredményezhet. Mindig használjon egységes indexelő az összes alkalmazáshoz. 


### <a name="does-the-sql-api-support-resource-link-caching"></a>Nem, az SQL API támogatási erőforrás-hivatkozások gyorsítótárazását?
Igen, mivel az Azure Cosmos DB egy RESTful szolgáltatás, erőforrás-hivatkozások nem módosíthatók, és ezáltal gyorsítótárazhatók. Az SQL API-ügyfelek adja meg egy "If-None-Match" fejlécet bármilyen erőforrás-szerű dokumentum vagy gyűjtemény olvasása és frissítse a helyi példányok, miután a kiszolgálóverzió módosult.

### <a name="is-a-local-instance-of-sql-api-available"></a>Az SQL API helyi példány érhető el?
Igen. A [Azure Cosmos DB Emulatort](local-emulator.md) egy élethű emulációs a Cosmos DB szolgáltatást biztosít. Támogatja a Funkciók, amelyek azonos Azure Cosmos DB-hez, például létrehozása és lekérdezése a JSON-dokumentumok, kiépítés és a gyűjtemények méretezéséről és végrehajtása tárolt eljárásokkal és eseményindítókkal. Fejlesztés és tesztelik az alkalmazásokat az Azure Cosmos DB Emulator használatával, és telepítse őket a az Azure globális méretű, módosítsa a kapcsolati végpont az Azure Cosmos DB egyetlen konfigurációjának.

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>Miért vannak kerekítve, ha a portálon az adatkezelőben megtekintett dokumentum mennyi ideig lebegőpontos értékeket. 
Ez a korlátozás a JavaScript. JavaScript kétszeres pontosságú lebegőpontos számformázás használja, mint a megadott IEEE 754 és biztonságosan jelenthet – közötti szám (253 - 1) és a 253-1 (azaz a 9007199254740991) csak.

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>Ha engedélyezettek a engedélyek az objektum hierarchiában?

A descendants (például dokumentumok, a mellékletek) és a tároló szintű engedélyek ResourceTokens használatával történő létrehozásának engedélyezett. Ez azt jelenti, hogy hozzon létre egy engedéllyel, amikor az adatbázis próbálkozik, vagy a fiók szintjén jelenleg nem engedélyezett.


## <a name="develop-against-the-api-for-mongodb"></a>Fejlesztés az API a mongodb-hez
### <a name="what-is-the-azure-cosmos-db-api-for-mongodb"></a>Mi az az Azure Cosmos DB API a mongodb-hez?
Az Azure Cosmos DB API a mongodb-hez, amely olyan kompatibilitási, amely lehetővé teszi az alkalmazások egyszerűen és könnyen kommunikálni az Azure Cosmos DB natív adatbázismotor meglévő, a Közösség által támogatott Apache MongoDB API-k és az illesztőprogramok. A fejlesztők mostantól a meglévő MongoDB eszköz láncok és képességek alkalmazások összeállítását, amelyek az Azure Cosmos DB előnyeit. A fejlesztők kihasználhatják az Azure Cosmos DB, az egyedi képességeivel, többek között az automatikus indexelést, a biztonsági mentési karbantartási, pénzügyi felelősséggel vállalt szolgáltatásiszint-szerződések (SLA), és így tovább.

### <a name="how-do-i-connect-to-my-api-for-mongodb-database"></a>Hogyan létesíthetek kapcsolatot a saját API a MongoDB-adatbázis?
A leggyorsabb módja az Azure Cosmos DB API a mongodb-hez, hogy a fő keresztül való csatlakozáshoz a [az Azure portal](https://portal.azure.com). Jelentkezzen be a fiókjába, és a bal oldali navigációs menüben kattintson **gyors üzembe helyezés**. Gyors üzembe helyezési ajánlott módja a megszerezni a kódrészleteket az adatbázishoz való csatlakozáshoz. 

Az Azure Cosmos DB szigorú biztonsági követelmények és előírások érvénybe lépteti. Az Azure Cosmos DB-fiókokhoz hitelesítés és biztonságos SSL-kommunikációra van szükség, ezért ügyeljen arra, hogy TLSv1.2.

További információkért lásd: [az API a MongoDB-adatbázishoz való kapcsolódás](connect-mongodb-account.md).

### <a name="are-there-additional-error-codes-for-an-api-for-mongodb-database"></a>Vannak további hibakódok adatbázis MongoDB API-k?
MongoDB gyakori hibakódok mellett a MongoDB API-val rendelkezik a saját adott hibakódok:


| Hiba               | Kód  | Leírás  | Megoldás  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Felhasznált kérelemegységek teljes száma túllépte a gyűjteményhez kiosztott kérelemegység díjaival, és megfelelően szabályozva lett. | Az átviteli sebességet rendelve egy tárolót vagy tárolók egy készletét az Azure portal vagy az újrapróbálkozás újra megfontolni. |
| ExceededMemoryLimit | 16501 | Több-bérlős szolgáltatás a művelet túllépte az ügyfél memória mekkora. | Csökkentse a szigorúbb lekérdezési feltételek használatával művelet hatókörének, vagy forduljon az ügyfélszolgálathoz a a [az Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). <br><br>Példa:  *&nbsp; &nbsp; &nbsp; &nbsp;db.getCollection('users').aggregate ([<br> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;{$match: {név: "Andy"}}, <br> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;{$sort: {kor: -1} }<br>&nbsp;&nbsp;&nbsp;&nbsp;])*) |

## <a name="develop-with-the-table-api"></a>Fejlesztés a Table API-val

### <a name="how-can-i-use-the-table-api-offering"></a>Hogyan használhatom a Table API-ajánlat? 
Az Azure Cosmos DB Table API érhető el a [az Azure portal][azure-portal]. Először, jelentkezzen Azure-előfizetésre. Miután bejelentkezett a, az egy Azure Cosmos DB Table API-fiók hozzáadása az Azure-előfizetéshez, és adja hozzá a táblák a fiókjához. 

A támogatott nyelvek és a kapcsolódó használatának első lépései a találja a [bemutatása az Azure Cosmos DB Table API](table-introduction.md).

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>Szükségem van egy új SDK-t a Table API-val? 
Nem, a meglévő storage SDK-k továbbra is működnie kell. Ajánlott azonban, hogy mindig kap a legújabb SDK-k és a legtöbb esetben a legjobb támogatás kiváló teljesítmény. Az elérhető nyelvek listáját lásd a [bemutatása az Azure Cosmos DB Table API](table-introduction.md).

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Ha nem Table API az Azure Table storage működése azonos?
Van néhány viselkedésbeli különbségeket, hogy az Azure Table storage-ból érkező táblázatok létrehozása az Azure Cosmos DB Table API az kívánó felhasználóknak érdemes figyelembe:

* Az Azure Cosmos DB Table API-tartalékkapacitást modellt használ a garantált teljesítmény biztosítása érdekében, de ez azt jelenti, hogy egy havidíja fedezi a kapacitást, amint az a tábla jön létre, akkor is, ha a kapacitás nincs használatban. Az Azure Table storage egy csak azért fizet, ténylegesen használt kapacitás. Ez segít annak magyarázata, hogy miért Table API elérhetővé teheti egy 10 ms olvasási és 15 ms írási SLA 99 százalékon, az Azure Table storage egy 10 második SLA-t biztosít. De ennek következtében a Table API-táblákkal, minden kéréshez, a költség pénzt azokat bármely, a szolgáltatási kéréseinek kezeléséhez rendelkezésre álló kapacitás biztosítása nélkül is üres táblák által kínált Azure Cosmos DB.
* A Table API által visszaadott lekérdezési eredmények nem jelennek meg partíció sorkulcs sorrendjének, azok az Azure Table storage-ban.
* Sorkulcsok csak legfeljebb 255 bájt lehet
* Kötegek tartalmazhat legfeljebb 2 MB
* A CORS jelenleg nem támogatott
* Az Azure Table storage-beli táblázatnevek nem kis-és nagybetűket, de azok az Azure Cosmos DB Table API
* Egyes Azure Cosmos DB belső formátumok kódolási információk, például bináris mezőket, jelenleg nem annyira hatékony, például előfordulhat, hogy az egyik. Ezért az adatok mérete váratlan korlátozások léphetnek föl. Például jelenleg egyik nem használható a teljes 1 Meg egy tábla entitás bináris adatok tárolására, mert a kódolás növeli az adatok mérete.
* Entitás tulajdonság neve "Id" jelenleg nem támogatott
* TableQuery TakeCount nem korlátozódik az 1000

A REST API tekintetében számos végpontok és lekérdezési lehetőségeket, amelyek nem támogatottak az Azure Cosmos DB Table API:
| REST-metódus | REST-végpont/lekérdezési beállítás | Dokumentum URL-címek | Magyarázat |
| ------------| ------------- | ---------- | ----------- |
| GET, PUT | /? restype =service@comp= tulajdonságai| [Állítsa be a Table Service tulajdonságok](https://docs.microsoft.com/rest/api/storageservices/set-table-service-properties) és [Table Service tulajdonságainak lekérése](https://docs.microsoft.com/rest/api/storageservices/get-table-service-properties) | Ez a végpont segítségével állítsa be a CORS-szabályok, storage analytics konfigurációs és naplózási beállítások. CORS jelenleg nem támogatott, és az analytics és a naplózás az Azure Cosmos DB, mint az Azure Storage-táblák másképp kezeli |
| BEÁLLÍTÁSOK | / < table-resource-name > | [Üzem előtti repülési CORS tábla kérelem](https://docs.microsoft.com/rest/api/storageservices/preflight-table-request) | Ez része, amely az Azure Cosmos DB jelenleg nem támogatja a CORS. |
| GET | /? restype =service@comp= stats | [Táblastatisztika szolgáltatás beolvasása](https://docs.microsoft.com/rest/api/storageservices/get-table-service-stats) | Milyen gyorsan adat áll replikálás alatt, elsődleges és másodlagos adatbázisok közötti információkat tartalmaz. A replikáció része írások, ez nem szükséges Cosmos DB-ben. |
| GET, PUT | /mytable? comp = acl | [ACL-tábla](https://docs.microsoft.com/rest/api/storageservices/get-table-acl) és [tábla ACL beállítása](https://docs.microsoft.com/rest/api/storageservices/set-table-acl) | Ez lekérdezi és beállítja a tárolt hozzáférési szabályzatok kezelése közös hozzáférésű Jogosultságkódok (SAS) használt. SAS támogatják, állítsa be, és másképp kezeli. |

Emellett az Azure Cosmos DB Table API támogatja a JSON-formátumban, nem ATOM csak.

Bár az Azure Cosmos DB támogatja a közös hozzáférésű Jogosultságkódok (SAS) bizonyos szabályzatok, ezt nem támogatja, kifejezetten azok kapcsolódó felügyeleti műveleteket, mint a jogot arra, hogy új táblákat hozhat létre.

A .NET SDK-hoz készült különösen vannak osztályok és módszerek, amely az Azure Cosmos DB jelenleg nem támogatja.

| Osztály | A metódus nem támogatott |
|-------|-------- |
| CloudTableClient | \*ServiceProperties* |
|                  | \*ServiceStats* |
| CloudTable | SetPermissions* |
|            | GetPermissions * |
| TableServiceContext | * (Ez az osztály ténylegesen elavult) |
| TableServiceEntity | " " |
| TableServiceExtensions | " " |
| TableServiceQuery | " " |

Ha bármelyik a különbségeket a projekt probléma, forduljon [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com) és tudassa velünk.

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Hogyan küldhetek visszajelzést az SDK-t vagy a hibák?
Az alábbi módokon is ossza meg velünk véleményét:

* [Uservoice](https://feedback.azure.com/forums/599062-azure-cosmos-db-table-api)
* [MSDN-fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-cosmosdb). A stack Overflow a legjobb a programozási kérdéseit. Ellenőrizze, hogy a kérdés [témához illeszkedő](https://stackoverflow.com/help/on-topic) és [adja meg a legtöbb adatot lehet, így a kérdést, egyértelmű és megválaszolható](https://stackoverflow.com/help/how-to-ask).

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Mi az a kapcsolati karakterláncot, amely a Table API csatlakozni kell?
A kapcsolati karakterláncot a következő:
```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```
A kapcsolati karakterlánc kaphat az Azure Portal kapcsolati karakterlánc oldaláról. 

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Hogyan bírálhatom felül az kérésbeállításokat for a Table API .NET SDK-ban konfigurációs beállításait?
Konfigurációs beállításaival kapcsolatos további információkért lásd: [Azure Cosmos DB-képességek](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities). Egyes beállítások Túlterheléseivel metódus és más kezeli, az app.config az appSettings szakaszt az ügyfélalkalmazásban található keresztül.

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>Vannak-e módosítások a már meglévő Azure Table storage SDK-kat használó ügyfelek számára?
Nincs. Nem változtak meglévő vagy új, a meglévő Azure Table storage SDK-kat használó ügyfelek számára. 

### <a name="how-do-i-view-table-data-that-is-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Hogyan tekinthetem meg az Azure Cosmos DB Table API-val használható tárolt táblaadatok? 
Az Azure portal segítségével keresse meg az adatokat. A Table API-kód vagy az eszközök a következő választ említett is használhatja. 

### <a name="which-tools-work-with-the-table-api"></a>Mely eszközök működnek a Table API-val? 
Használhatja a [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

Eszközök is igénybe vehet egy kapcsolati karakterláncot a megadott formátumban korábban az új Table API is támogatja. A megadott tábla eszközök listáját az [Azure Storage-ügyféleszközök](../storage/common/storage-explorers.md) lapot. 

### <a name="is-the-concurrency-on-operations-controlled"></a>Az egyidejűség az ellenőrzött műveletek?
Igen, az optimista egyidejűség biztosítunk az ETag mechanizmus segítségével. 

### <a name="is-the-odata-query-model-supported-for-entities"></a>Az entitások támogatott OData lekérdezési modellje? 
Igen, a Table API támogatja az OData-lekérdezés és a LINQ-lekérdezésekre. 

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>Csatlakozhatok az Azure Table Storage és Azure Cosmos DB Table API egymás mellett ugyanabban az alkalmazásban? 
Igen, csatlakoztathatja a CloudTableClient, mindegyik a saját URI-n keresztül a kapcsolati karakterlánc mutató két külön példányának létrehozásával.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>Hogyan migrálhatok ezt az ajánlatot egy meglévő Azure Table storage alkalmazást?
[Az AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) és a [Azure Cosmos DB adatáttelepítési eszköz](import-data.md) azok egyaránt támogatott.

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Hogyan történik a tároló mérete, ha például a indítható el, ez a szolgáltatás készen bővítése *n* adatok és a saját adatok GB-os idővel növekszik 1 TB-os? 
Az Azure Cosmos DB célja a horizontális skálázás segítségével korlátlan tárolási megoldás biztosítása. A szolgáltatás figyelheti és hatékonyan növelheti a tárhely. 

### <a name="how-do-i-monitor-the-table-api-offering"></a>Hogyan figyelhetem a Table API-ajánlat?
A Table API használható **metrikák** panelen a kérelmek és a storage használatának monitorozása. 

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Hogyan számítja ki a az átviteli sebességet, követelhetem?
A kapacitás estimator a műveletek által megkövetelt TableThroughput kiszámításához használható. További információkért lásd: [becslés Kérelemegységek és az adattárolásra](https://www.documentdb.com/capacityplanner). Általában JSON-ként az entitást képviselik, és adja meg a számokat a műveletek. 

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>Használható a Table API SDK helyi emulátorral?
Jelenleg nem.

### <a name="can-my-existing-application-work-with-the-table-api"></a>Meglévő alkalmazás dolgozhat a Table API-t? 
Igen, az azonos API támogatja.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-do-not-want-to-use-the-table-api-features"></a>Van szükségem az áttelepítést a meglévő Azure Table storage alkalmazásaimat az SDK-t, ha nem szeretnék a Table API-funkciók?
Nem, hozzon létre, és használja a meglévő Azure Table storage eszközök bármiféle megszakítás nélkül. Azonban ha nem használja a Table API-t, akkor nem kihasználhatják az Automatikus indexelés, a további konzisztencia lehetőséget vagy a globális terjesztés. 

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-multiple-regions-of-azure"></a>Hogyan adhatok hozzá az adatok replikálása a Table API az Azure-területekre?
Az Azure Cosmos DB Portallal [globális replikációs beállítások](tutorial-global-distribution-sql-api.md#portal) régiók, amelyek alkalmasak az alkalmazás hozzáadásához. A globálisan elosztott alkalmazások fejlesztése, hozzá kell az alkalmazás a PreferredLocation adatokkal állítsa be a helyi régiónak, amelyek biztosítják az alacsony olvasási késés. 

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Hogyan módosíthatom a Table API-ban a fiók az elsődleges írási régiót?
Az Azure Cosmos DB globális replikációt portál panel használatával hozzáadta a régiót, és ezután átadja a feladatokat a szükséges régió. Útmutatásért lásd: [fejlesztés a többrégiós Azure Cosmos DB-fiókok](regional-failover.md). 

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Hogyan konfigurálhatom az alacsony késés érdekében saját előnyben részesített olvasási régiók I adataimat terjesztésekor? 
Annak érdekében, olvassa el a helyi helyről, használja a PreferredLocation kulcsot az app.config fájlban. Meglévő alkalmazások a Table API hibát jelez, ha LocationMode van beállítva. Távolítsa el a adott kódot, mert a tábla API szerzi be ezt az információt az app.config fájlból. További információkért lásd: [Azure Cosmos DB-képességek](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities).

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>Hogyan kell konzisztenciaszintek a Table API a gondolja? 
Az Azure Cosmos DB jól indoklással és hátrányokkal konzisztencia, a rendelkezésre állás és a késést biztosít. Az Azure Cosmos DB öt konzisztenciaszintet kínál a Table API-fejlesztők számára, így válassza ki a megfelelő konzisztenciájú modellt, a tábla szintjén, és győződjön meg arról, az egyes kérelmek, az adatok lekérdezése közben. Amikor egy ügyfél csatlakozik, azt a konzisztenciaszint adhatja meg. Módosíthatja a szintet Túlterheléseivel consistencyLevel argumentuma keresztül. 

A Table API-t biztosít, közel valós idejű olvassa be a "saját írási műveletek"olvasása kötött-frissesség konzisztencia alapértelmezés szerint. További információkért lásd: [konzisztenciaszintek](consistency-levels.md). 

Alapértelmezés szerint az Azure Table storage biztosítja a konzisztenciát egy adott régión belül és a végleges konzisztencia a másodlagos helyeken. 

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>Kínál az Azure Cosmos DB Table API az Azure Table storage-nál több konzisztenciaszintek?
Igen, kihasználhatják az Azure Cosmos DB elosztott jellege kapcsolatos információkért lásd: [konzisztenciaszintek](consistency-levels.md). A konzisztenciaszintek előírt garanciákat, mert a magabiztosan használhatja őket. További információkért lásd: [Azure Cosmos DB-képességek](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities).

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Ha engedélyezve van a globális terjesztés, mennyi ideig tart, replikálja az adatokat?
Az Azure Cosmos DB az adatokat a helyi régióban tartósan véglegesítések, és leküldéses értesítések az adatok közvetlenül más régiókhoz csupán néhány ezredmásodperc. A fájlreplikációs szolgáltatás csak az üzenetváltási időt (RTT) az adatközpontok függ. Azure Cosmos DB globális terjesztési képességét kapcsolatos további információkért lásd: [Azure Cosmos DB: egy globálisan elosztott adatbázis-szolgáltatás az Azure-ban](distribute-data-globally.md).

### <a name="can-the-read-request-consistency-level-be-changed"></a>Módosíthatja az olvasási kérést konzisztenciaszint?
Az Azure Cosmos DB a konzisztenciaszint beállíthatja a tároló szintjén (a leírásban). A .NET SDK használatával módosíthatja a szintet TableConsistencyLevel kulcsot az app.config fájlban az érték megadásával. A lehetséges értékek a következők: erős, PBS, munkamenet, konzisztens előtag és végleges. További információkért lásd: [adatok aprólékosan beállítható konzisztenciaszintek az Azure Cosmos DB](consistency-levels.md). A kulcs lényege, hogy nem állítható be a kérelem konzisztencia szint több, mint a beállítást a táblára vonatkozóan:. Például a konzisztenciaszint a táblához nem beállítása végleges, és a kérés konzisztenciaszint erős. 

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>Hogyan a Table API kezeli feladatátvételi Ha leáll egy régióban? 
A Table API a az Azure Cosmos DB globálisan elosztott platformot használ. Győződjön meg arról, hogy az alkalmazás működését adatközpont-leállás, engedélyezni kell legalább egy további régióban, a fiók az Azure Cosmos DB portál [fejlesztés a többrégiós Azure Cosmos DB-fiókok](regional-failover.md). A portál használatával beállíthatja az a régió prioritásának [fejlesztés a többrégiós Azure Cosmos DB-fiókok](regional-failover.md). 

A fiók, és szabályozhatja, ahol azt átveheti azáltal, hogy a feladatátvétel prioritási tetszőleges számú régiót adhat hozzá. Természetesen az adatbázis használatához meg kell adnia egy alkalmazást is túl. Ha így tesz, az ügyfelek nem tapasztalható üzemszünet. A [legújabb .NET ügyféloldali SDK-val](table-sdk-dotnet.md) automatikus helykezelési van, de a más SDK-k nem. Észlelését, azt a régiót, amelyben nem működik, és automatikusan átadja a feladatokat az új régióban.

### <a name="is-the-table-api-enabled-for-backups"></a>A Table API engedélyezve van a biztonsági mentéseket?
Igen, a Table API-t használja a biztonsági másolatokat az Azure Cosmos DB platform. Biztonsági mentés automatikusan történik. További információkért lásd: [Online biztonsági mentés és visszaállítás az Azure Cosmos DB](online-backup-and-restore.md).

 
### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>A Table API index egy entitás összes attribútumának alapértelmezés szerint?
Igen, egy entitás összes attribútumának indexelt alapértelmezés szerint. További információkért lásd: [Azure Cosmos DB: indexelési szabályzataihoz](indexing-policies.md). 

### <a name="does-this-mean-i-do-not-have-to-create-multiple-indexes-to-satisfy-the-queries"></a>Ez azt jelenti, nem kell lekérdezéseket kielégítéséhez több indexet hoz létre does? 
Igen, az Azure Cosmos DB Table API biztosít az összes attribútum bármely séma definíció nélküli automatikus indexelés. Ezt az automatizálást a fejlesztők számára, hogy az alkalmazás helyett az index létrehozásának és felügyeletének fókusz szabadít fel. További információkért lásd: [Azure Cosmos DB: indexelési szabályzataihoz](indexing-policies.md).

### <a name="can-i-change-the-indexing-policy"></a>Módosíthatja az indexelési házirendet?
Igen, módosíthatja az indexelési házirendet azáltal, hogy az index definícióját. További információkért lásd: [Azure Cosmos DB-képességek](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities). Megfelelően kódolása és a beállítások karaktert kell. 

a nem – .NET SDK-k az indexelési házirendet csak állítható be a portálon, a **adatkezelő**, keresse meg az adott táblában szeretné módosítani, és folytassa a a **méretezés és beállítások**-> indexelési házirend a kívánt módosítást, majd **mentése**.

A .NET SDK-ból, így el lehet az app.config fájlban:
```
{
  "indexingMode": "consistent",
  "automatic": true,
  "includedPaths": [
    {
      "path": "/somepath",
      "indexes": [
        {
          "kind": "Range",
          "dataType": "Number",
          "precision": -1
        },
        {
          "kind": "Range",
          "dataType": "String",
          "precision": -1
        } 
      ]
    }
  ],
  "excludedPaths": 
[
 {
      "path": "/anotherpath"
 }
]
}
```

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>Az Azure Cosmos DB platform úgy tűnik, hogy rengeteg funkciók, például a rendezést, az összesítések, hierarchia és egyéb funkciókat. Ön hozzáadni a ezeket a képességeket a Table API-hoz? 
A tábla API ugyanazokat a lekérdezési funkciókat, mint az Azure Table storage biztosít. Az Azure Cosmos DB a rendezést, az összesítéseket, a földrajzi lekérdezéseket, a hierarchiát és számos különféle beépített funkciót is támogat. A tábla API egy jövőbeli szolgáltatásfrissítésben további funkciókat biztosít. További információkért lásd: [SQL-lekérdezések](sql-api-sql-query.md).
 
### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>Mikor kell módosítani a tábla API TableThroughput?
TableThroughput akkor kell megváltoztatnia, ha érvényes a következő feltételek valamelyike:
* Egy kinyerési, átalakítási és betöltési (ETL) az adatok hajt végre, vagy rövid időn belül nagy mennyiségű adatot feltölteni kívánt. 
* További átviteli sebesség a tárolóból vagy egy készletből, a tárolók, a háttéralkalmazás van szüksége. Például láthatja, hogy a használt átviteli sebesség több, mint a kiosztott átviteli sebesség, és Ön első szabályozott. További információkért lásd: [teljesítmény beállítása az Azure Cosmos DB-tárolók](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>Vertikális felskálázás vagy leskálázás átviteli sebességének Table API táblázat? 
Igen, az Azure Cosmos DB-portál scale panel használatával méretezheti az átviteli sebességet. További információkért lásd: [teljesítmény beállítása](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Az alapértelmezett TableThroughput állítsa be az újonnan létrehozott táblák?
Igen, ha nem bírálja felül a TableThroughput app.config keresztül, és ne használjon egy előre létrehozott tárolót az Azure Cosmos DB, a szolgáltatás létrehoz egy táblát az átviteli sebesség 400.
 
### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Van-e bármilyen módosítás a meglévő ügyfelei számára az Azure Table storage szolgáltatás díjszabása?
Nincs. Nem történik változás ár meglévő Azure Table storage-ügyfelek számára. 

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Hogyan számítják az ár a Table API-hoz? 
A díj attól függ, hogy a lefoglalt TableThroughput. 

### <a name="how-do-i-handle-any-rate-limiting-on-the-tables-in-table-api-offering"></a>Hogyan kezelhetem a bármely sebességkorlátozást, a táblák a Table API-ajánlat? 
Ha a kérések aránya meghaladja a kiosztott átviteli sebesség a mögöttes tároló kapacitása vagy egy tárolók, hibaüzenetet kap, és az SDK-t a hívás újrapróbálkozik az újrapróbálkozási szabályzat alkalmazásával.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Miért kell választania a PartitionKey és rowkey tulajdonságok esetén kihasználásához az Azure Cosmos DB Table API elérhető átviteli sebesség?
Ha nem ad meg az app.config fájlban, vagy a portálon keresztül egy Azure Cosmos DB egy alapértelmezett átviteli sebességet a tároló állítja be. 

Az Azure Cosmos DB garanciákat nyújt a teljesítmény és a késés, a felső korlátja a műveletet. Ez garantálja a motor kényszerítheti a cégirányítási műveletek a bérlő esetén lehetséges. TableThroughput beállítás biztosítja, hogy kap a garantált átviteli sebességgel és késéssel, mert a platform fenntartja a kapacitást, és garantálja a működési sikeres. 

Az átviteli sebesség meghatározása révén rugalmasan módosíthatja, hogy az alkalmazás a szezonalitás kihasználhatják, az átviteli sebesség igényeinek és költségeit.

### <a name="azure-table-storage-has-been-very-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-have-not-performed-a-single-transaction-or-stored-anything-can-you-please-explain"></a>Az Azure Table storage nagyon alacsony költségű számomra, lett, mert csak szeretné tárolni az adatokat, és csak ritkán lekérdezést kell fizetni. Az Azure Cosmos DB Table API-ajánlat úgy tűnik, hogy lehet díjszabási me annak ellenére, hogy e végre egy tranzakció vagy nem tárolt semmit. Adjon melyek?

Az Azure Cosmos DB egy globálisan elosztott, SLA-alapú rendszer rendelkezésre állását, a késés és az átviteli sebesség garanciákkal tervezték. Fenntartott átviteli sebesség az Azure Cosmos DB, amikor biztosított, ellentétben más rendszerek az átviteli sebességet. Az Azure Cosmos DB további képességeket biztosít, amelyek ügyfelek kért, például a másodlagos indexek és globális disztribúciót.  

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Soha nem jelenik meg a teljes kvóta"értesítés (azt jelzi, hogy egy partíción teljes) Ha szeretnék betölteni az adatokat az Azure Table storage. A Table API-val Ez az üzenet jelenik meg. Ez kínál me korlátozásával, és módosíthatja a meglévő alkalmazás me kényszerítése?

Az Azure Cosmos DB amely korlátlan számú skálázási, a késés, átviteli sebesség, rendelkezésre állás és konzisztencia garanciákkal biztosító SLA-alapú rendszer. Garantált prémium szintű teljesítmény biztosítása érdekében győződjön meg arról, hogy az adatok mérete és az index kezelhető és méretezhető. A 10 GB-os korlátot, az entitások vagy partíciókulcson elemek száma, hogy győződjön meg arról, hogy remek keresési és a lekérdezési teljesítmény kínálunk. Győződjön meg arról, hogy az alkalmazás jól, akár az Azure Storage, skálázható, akkor javasoljuk, hogy Ön *nem* az egy partícióban kapcsolatos összes információ tárolása és lekérdezése, gyakori elérésű partíciót létrehozni. 

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>Ezért PartitionKey és rowkey tulajdonságok esetén azok továbbra is szükséges a Table API-val? 
Igen. Mivel a Table API felületének az Azure Table storage SDK-t a hasonló, a partíciókulcs biztosít egy hatékony módszer az adatok elosztására. A sorkulcs pedig a partíción belül egyedi. A sorkulcs megtalálhatónak kell lennie, és nem lehet null értékű, mint a standard szintű SDK-t. RowKey hossza 255 bájt, és a PartitionKey legfeljebb 1 KB-os. 

### <a name="what-are-the-error-messages-for-the-table-api"></a>Mik azok a hibaüzeneteket, a Table API-hoz?
Az Azure Table storage és az Azure Cosmos DB Table API az azonos SDK-kat használja, így a hibák leggyakrabban azonos lesz.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Miért do I leszabályozza jelenik meg létrehozása táblák rengeteg egymás után a Table API-ban?
Az Azure Cosmos DB amely késés, átviteli sebesség, rendelkezésre állás és konzisztencia megvalósulásának biztosító SLA-alapú rendszer. Mivel a szolgáltatás egy üzembe helyezett rendszert, ezek a követelmények biztosításához erőforrások fenntartja magának. A táblák létrehozásának gyors aránya észlelt és szabályozott. Azt javasoljuk, hogy, tekintse meg a táblák létrehozásának sebessége alacsonyabb, kevesebb mint 5 percenként. Ne feledje, hogy a tábla API-t egy üzembe helyezett rendszer. A pillanatban, annak üzembe helyezésekor, elkezdi akkor kell fizetnie. 

## <a name="develop-against-the-graph-api"></a>Fejlesztés a Graph API ellen
### <a name="how-can-i-apply-the-functionality-of-graph-api-to-azure-cosmos-db"></a>Hogyan használhatom a Graph API funkcióit az Azure Cosmos DB?
Használhatja egy bővítménykódtár a alkalmazni a Graph API funkcióit. A könyvtár neve a Microsoft Azure-diagramok, és elérhető a NuGet. 

### <a name="it-looks-like-you-support-the-gremlin-graph-traversal-language-do-you-plan-to-add-more-forms-of-query"></a>Úgy tűnik, a Gremlin graph bejárási nyelv támogatott. Tervezi a lekérdezés további űrlapokat vehet fel?
Igen, bővítését tervezzük más mechanizmusok lekérdezés a jövőben. 

### <a name="how-can-i-use-the-new-graph-api-offering"></a>Hogyan használható az új Graph API-ajánlat? 
Első lépésként hajtsa végre a [Graph API](../cosmos-db/create-graph-dotnet.md) rövid útmutatóban.

<a id="cassandra"></a> 
## <a name="develop-with-the-apache-cassandra-api-preview"></a>Fejlesztés az Apache Cassandra API (előzetes verzió)

### <a name="what-is-the-protocol-version-supported-in-the-private-preview-is-there-a-plan-to-support-other-protocols"></a>Mi az a private preview verzióban támogatott protokoll verziója? Egyéb protokollok támogatása csomag van?
Az Azure Cosmos DB Apache Cassandra API ma CQL 4-es verziója támogatja. Ha más protokollt támogatásával kapcsolatos visszajelzést szeretne küldeni, ossza meg velünk keresztül [uservoice-visszajelzések](https://feedback.azure.com/forums/263030-azure-cosmos-db) , vagy küldjön egy e-mailek [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com). 

### <a name="why-is-choosing-a-throughput-for-a-table-a-requirement"></a>Miért megválasztása a tábla egy átviteli követelmény?
Az Azure Cosmos DB alapértelmezett átviteli sebességet beállítja a tárolóhoz rendeltek, ahol létrehozhatja az a tábla - portálon vagy a CQL. Az Azure Cosmos DB garanciákat nyújt a teljesítmény és a késés, a felső korlátja a műveletet. Ez garantálja a motor kényszerítheti a cégirányítási műveletek a bérlő esetén lehetséges. Beállítás átviteli biztosítja, hogy kap a garantált átviteli sebességgel és késéssel, mert a platform fenntartja a kapacitást, és garantálja a művelet sikeres. Rugalmasan módosíthatja az átviteli sebességet, hogy az alkalmazás a szezonalitás előnyös és költségeit.

Az átviteli sebesség koncepció ismertetése a [Azure Cosmos DB-ben a kérelemegység](request-units.md) cikk. Egy táblát az átviteli sebesség egyenlően a mögöttes fizikai partíciók között.  

### <a name="what-is-the-default-rus-of-table-when-created-through-cql-what-if-i-need-to-change-it"></a>Mi az az alapértelmezett RU/s tábla létrehozásakor CQL keresztül? Mi történik, ha szükséges, módosítsa?
Az Azure Cosmos DB kérelemegység / másodperc (RU/s) használja, amelyek biztosítják az átviteli sebesség a pénznem. CQL keresztül létrehozott táblák rendelkezik 400 RU. Módosíthatja a Kérelemegység a portálról. 

CQL
```
CREATE TABLE keyspaceName.tablename (user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=1200
```

.NET
```csharp
int provisionedThroughput = 400;
var simpleStatement = new SimpleStatement($"CREATE TABLE {keyspaceName}.{tableName} (user_id int PRIMARY KEY, lastname text)");
var outgoingPayload = new Dictionary<string, byte[]>();
outgoingPayload["cosmosdb_provisioned_throughput"] = Encoding.UTF8.GetBytes(provisionedThroughput.ToString());
simpleStatement.SetOutgoingPayload(outgoingPayload); 
``` 

### <a name="what-happens-when-throughput-is-exceeded"></a>Mi történik a átviteli sebesség túllépésekor? 
Az Azure Cosmos DB garanciákat nyújt a teljesítmény és a késés, a felső korlátja a műveletet. Ez garantálja a motor kényszerítheti a cégirányítási műveletek a bérlő esetén lehetséges. Ez akkor lehetséges, az átviteli sebességet, amely biztosítja, hogy kap a garantált átviteli sebességgel és késéssel, mert a platform fenntartja a kapacitást, és garantálja a művelet sikeres beállítás alapján. Ha túllépi a kapacitást túlterhelt jellegű hibaüzenetet kap üzenetet a rendszer túllépte a kapacitást. 0x1001 túlterhelt: nem lehet feldolgozni a kérelmet, mert a "Túl sok kérelmet adott". Ebben a pillanatban elengedhetetlen, hogy milyen műveleteket, és a mennyiségi okozza a problémát. Felhasznált kapacitása meghaladja a kiosztott kapacitást a portálon a metrikákkal kapcsolatos ötlet kérheti le. Ezután győződjön meg arról, szinte felhasznált kapacitás egyaránt összes alapul szolgáló partíciók között. Ha egy partíció által felhasznált átviteli a legtöbb, rendelkezik torzulása számítási feladat. 

Metrikák érhetők el, amelyek bemutatják, átviteli sebesség használatáról órában, napban, és a egy hét nap, több partícióra kiterjedő vagy Összesítés. További információkért lásd: [megfigyelési és a hibakeresést az Azure Cosmos DB metrikákkal](use-metrics.md).

Diagnosztikai naplók mutatjuk be a [diagnosztikai naplózás az Azure Cosmos DB](logging.md) cikk.

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>Az elsődleges kulcs térkép az Azure Cosmos DB a partíciós kulcs fogalma nem?
Igen, a partíciókulcs megfelelő helyen helyezi el az entitás szolgál. Az Azure Cosmos DB szolgál, hogy egy fizikai partíciónak jobb logikai partíció található. A particionálási fogalma jól ismertetése a [particionálási és horizontális az Azure Cosmos DB](partition-data.md) cikk. Az alapvető hajtsa végre a megfelelő nyelvet a lenti listában itt látható, hogy a logikai partíció nem haladhatja meg a 10 GB-os korlát még ma. 

### <a name="what-happens-when-i-get-a-quota-full-notification-indicating-that-a-partition-is-full"></a>Mi történik, ha a teljes kvóta jelenik meg:"értesítés arról, hogy a partíció nem teljes?
Az Azure Cosmos DB egy rendszer SLA-alapú, amely korlátlan számú skálázási késés, átviteli sebesség, rendelkezésre állás és konzisztencia vonatkozó garanciákat biztosít. Cassandra API lehetővé teszi a korlátlan tárolási adatok túl. A korlátlan tárolási fő fogalmat, a particionálás használata adatok vízszintes horizontális felskálázás alapul. A particionálási fogalma jól ismertetése a [particionálási és horizontális az Azure Cosmos DB](partition-data.md) cikk.

Az entitások vagy meg kell felelnie logikai partíciónként elemek számának 10 GB-os korlát. Győződjön meg arról, hogy az alkalmazás jól skálázható, javasoljuk, hogy Ön *nem* az egy partícióban kapcsolatos összes információ tárolása és lekérdezése, gyakori elérésű partíciót létrehozni. Ez a hiba csak származnak, ha az adatokat, akkor torzítja – Ez akkor nagy mennyiségű adat az egy partíciókulcsot – azaz a is 10 GB-nál. A storage-portál használata az adatok eloszlása találja. Ez a hiba kijavításának módja recrete, a tábla, válassza ki a részletes elsődleges (partíciókulcs), amely lehetővé teszi az adatok jobb elosztása.

### <a name="is-it-possible-to-use-cassandra-api-as-key-value-store-with-millions-or-billions-of-individual-partition-keys"></a>A Cassandra API-kulcs-érték tárolóként használható a több millió vagy egyéni partíciókulcsok milliárd?
Az Azure Cosmos DB korlátlan adatot tárolhat a storage horizontális felskálázásával. Ez az az átviteli sebesség független. Tárolására és beolvasására, kulcs/érték megadásával a megfelelő elsődleges/partíciókulcs Igen Cassandra API csak mindig használhatja. Ezek a egyedi kulcsok saját logikai partíció beolvasása, és problémamentesen fizikai partíciók felett található. 

### <a name="is-it-possible-to-create-multiple-tables-with-apache-cassandra-api-of-azure-cosmos-db"></a>Az lehetőség több tábla létrehozása az Apache Cassandra API-t az Azure Cosmos DB?
Igen, Kréta lehetőség több tábla Apache Cassandra API-val. Ezeket a táblákat és tárolásért egység számít. 

### <a name="is-it-possible-to-create-multiple-tables-in-succession"></a>Az egymás után több tábla létrehozására?
Az Azure Cosmos DB rendszer erőforrás szabályozott adatokat és a vezérlési sík tevékenységekhez. Tárolók, például gyűjtemények, a táblák olyan futásidejű entitások, amelyek a megadott átviteli kapacitás kiépítése. Egymás után gyorsan tárolók létrehozása nem várt tevékenységet és szabályozott. Ha teszteket, amelyek közvetlen/táblák létrehozása azonnal - próbálja címterét őket.

### <a name="what-is-maximum-number-of-tables-which-can-be-created"></a>Mit jelent a táblákat, amelyek hozható létre maximális számát?
A táblák száma nem fizikai korlátozott, küldjön egy e-mailt [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) Ha nagyon nagy számú táblák (ahol a teljes állandó mérete meghaladja a 10 TB-nyi adatot), amely a szokásos 10s vagy 100s kell létrehozni. 

### <a name="what-is-the-maximum--of-keyspace-which-we-can-create"></a>Mi az a kulcstér, amelyeket létrehozhatunk maximális száma? 
Mivel ezek metaadat-tárolók van fizikai keyspaces száma korlátlan, küldjön egy e-mailt [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) Ha valamilyen okból keyspaces nagyon nagy számú rendelkezik. 

### <a name="is-it-possible-to-bring-in-lot-of-data-after-starting-from-normal-table"></a>Az is nagy mennyiségű adatot betöltheti a normál táblából elindítása után? 
A tárolási kapacitás automatikusan kezeli, és leküldte a további adatok minél jobban növekszik. Így magabiztosan importálhatja legtöbb adatot nélkül kezelése, és üzembe helyezését a csomópontokon, stb. 

### <a name="is-it-possible-to-supply-yaml-file-settings-to-configure-apache-casssandra-api-of-azure-cosmos-db-behavior"></a>Az Apache Casssandra API az Azure Cosmos DB-viselkedés yaml fájl beállításait adja meg a lehetséges?
Az Apache Cassandra API-t az Azure Cosmos DB platform szolgáltatása. Szolgáltatói használható protokoll biztosítja a műveletek végrehajtása. Elrejti azonnal felügyeleti, monitorozási és konfigurációs bonyolultságát. Fejlesztői/felhasználóként, nem kell aggódnia a rendelkezésre állási, törlésjelzőit, kulcsfontosságú cache sor gyorsítótár, virágzó szűrő és számos egyéb beállítást. Az Azure Cosmos DB Apache Cassandra API megoldásai lehetővé, hogy és olvasási teljesítményt megkövetelése nélkül konfigurálással és felügyelettel járó többletterhelést.

### <a name="will-apache-cassandra-api-for-azure-cosmos-db-support-node-additioncluster-statusnode-status-commands"></a>Az Azure Cosmos DB Apache Cassandra API támogatja csomópont hozzáadása/fürt állapotának/csomópont állapota parancsokat?
Az Apache Cassandra API olyan platformszolgáltatás, ami lehetővé teszi a kapacitástervezés, az átviteli sebesség és a tárhely rugalmassági igényeinek válaszol egy pofonegyszerű. Az Azure Cosmos DB, oszthatnak ki átviteli kapacitásokat van szüksége. Majd méretezhető felfelé és tetszőleges számú alkalommal napon keresztül, nem kell bajlódnunk csomópontok hozzáadásával és eltávolításával, vagy kezelnie azokat. Ez azt jelenti, hogy nem szeretné használni a csomópont, a fürtkezelő túl. 

### <a name="what-happens-with-respect-to-various-config-settings-for-keyspace-creation-like-simplenetwork"></a>Mi történik, különböző konfigurációs beállítások kulcstér létrehozására például egyszerű/hálózati vonatkozóan?
Azure Cosmos DB biztosítja a rendelkezésre állás és a közel valós idejű okokból kulcsrakész globális disztribúciót. Nem kell a telepítő replikák stb. Az összes írási művelet mindig tartósan véglegesítve egy minden olyan régióban, ahol írási művelet során gondoskodik a teljesítményre vonatkozó garanciákat kvórum.  

### <a name="what-happens-with-respect-to-various-settings-for-table-metadata-like-bloom-filter-caching-read-repair-change-gcgrace-compression-memtableflushperiod-etc"></a>Olvassa el vonatkozóan különböző beállításait tábla metaadatainak virágzó szűrőt, például a gyorsítótárazás, mi történik, javítás módosítása, gc_grace, tömörítés memtable_flush_period stb?
Az Azure Cosmos DB olvasási/írási műveleteket és bármely konfigurációs beállítás, és véletlenül kezelésére szolgáló őket érintése nélkül átviteli teljesítményt nyújt.  

### <a name="is-time-to-live-ttl-supported-for-cassandra-tables"></a>Cassandra-táblák esetén támogatott idő – Élettartam (TTL)? 
Igen, TTL támogatott. 

### <a name="is-it-possible-to-monitor-node-status-replica-status-gc-and-os-parameters-earlier-with-various-tools-what-needs-to-be-monitored-now"></a>Ennyi az egész lehet figyelő csomópont állapota, a replika állapota, a globális katalógus és a különböző eszközök révén korábban operációs rendszer paraméterek? Mit kell figyelni most?
Az Azure Cosmos DB egy platformszolgáltatás, amely segít a hatékonyság növeléséhez, és nem kell foglalkoznia kezeléséhez és monitorozásához infrastruktúra. Csak akkor kell gondoskodunk, amely elérhető a portál mérőszámai keresése, ha Ön szabályozott első és növeléséhez vagy csökkentéséhez, hogy az átviteli teljesítmény. A figyelő [SLA-k](monitor-accounts.md).
Használat [metrikák](use-metrics.md) használata [diagnosztikai naplók](logging.md).

### <a name="which-client-sdks-can-work-with-apache-cassandra-api-of-azure-cosmos-db"></a>Melyik ügyfél SDK-k is használható az Apache Cassandra API-t az Azure Cosmos DB?
A privát előzetes verzió az Apache Cassandra SDK-ügyfél illesztőprogramokat, amelyek CQLv3 ügyfélprogramok használták. Ha rendelkezik más illesztőprogramokat használja, vagy ha problémái, az e-mailek küldésére [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com). 

### <a name="is-composite-partition-key-supported"></a>Összetett partíciókulcs támogatott?
Igen, rendszeres szintaxis használatával összetett partíciós kulcs létrehozása. 

### <a name="can-i-use-sstable-loader-for-data-loading"></a>Használhatok sstable betöltő Adatbetöltési?
Nem, az előzetes verzióban sstable betöltő nem támogatott. 

### <a name="can-an-on-premises-cassandra-cluster-be-paired-with-azure-cosmos-dbs-apache-cassandra-api"></a>Is egy a helyszíni cassandra-fürtjére kell megfeleltetni az Azure Cosmos DB Apache Cassandra API-hoz?
A jelenlegi Azure Cosmos DB egy optimalizált megoldás műveletek nélkül terhelés a felhőalapú környezet rendelkezik. Ha párosítás van szüksége, küldjön egy levelet a [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) az az eset leírását.

### <a name="does-cassandra-api-provide-full-backups"></a>Biztosítja a Cassandra API a teljes biztonsági mentés? 
Az Azure Cosmos DB biztosítja a két ingyenes teljes mentésekre négy óra időköz még ma az összes API-k között. Ez biztosítja, hogy nem kell beállítani a biztonsági mentési ütemezést stb. Ha azt szeretné, módosíthatja a megőrzési és gyakoriságát, küldjön egy e-mailek [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) vagy egy támogatási esetet előléptetése. Információ a biztonsági mentési funkció megtalálható a [automatikus online biztonsági mentés és visszaállítás az Azure Cosmos DB](online-backup-and-restore.md) cikk. 

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Hogyan a Cassandra API-fiókot kezeli feladatátvételi Ha leáll egy régióban? 
Az Azure Cosmos DB Cassandra API telephelyeken az Azure Cosmos DB globálisan elosztott platformról. Győződjön meg arról, hogy az alkalmazás működését adatközpont-leállás, engedélyezni kell legalább egy további régióban, a fiók az Azure Cosmos DB portál [fejlesztés a többrégiós Azure Cosmos DB-fiókok](regional-failover.md). A portál használatával beállíthatja az a régió prioritásának [fejlesztés a többrégiós Azure Cosmos DB-fiókok](regional-failover.md). 

A fiók, és szabályozhatja, ahol azt átveheti azáltal, hogy a feladatátvétel prioritási tetszőleges számú régiót adhat hozzá. Az adatbázis használatára, meg kell adnia a-alkalmazást is túl. Ha így tesz, az ügyfelek nem tapasztalható üzemszünet. 

### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Az Apache Cassandra API index egy entitás összes attribútumának alapértelmezés szerint?
Igen, egy entitás összes attribútumának indexelt alapértelmezés szerint az Azure Cosmos DB-hez. További információkért lásd: [Azure Cosmos DB: indexelési szabályzataihoz](indexing-policies.md). Garantált konzisztens indexelő teljesítmény előnyeit kap, és véglegesített tartós kvórum mindig írja. 

### <a name="does-this-mean-i-do-not-have-to-create-multiple-indexes-to-satisfy-the-queries"></a>Ez azt jelenti, nem kell lekérdezéseket kielégítéséhez több indexet hoz létre does? 
Igen, az Azure Cosmos DB biztosít az összes attribútum bármely séma definíció nélküli automatikus indexelés. Ezt az automatizálást a fejlesztők számára, hogy az alkalmazás helyett az index létrehozásának és felügyeletének fókusz szabadít fel. További információkért lásd: [Azure Cosmos DB: indexelési szabályzataihoz](indexing-policies.md).

### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Használható az új Cassandra API SDK-t helyi emulátorral?
Ezen képesség támogatásához a jövőben tervezzük. 

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-changefeed-and-other-functionality-will-these-capabilities-be-added-to-the-cassandra-api"></a>Az Azure Cosmos DB platform úgy tűnik, hogy rengeteg funkciók, például changefeed és más funkciókat. Ezek a képességek hozzáadódik a Cassandra API-t? 
Az Apache Cassandra API Apache Cassandra azonos CQL funkciókat biztosít. Tervezzük megvalósítható-képességek a jövőbeli támogatási megvizsgáljuk.

### <a name="feature-x-of-regular-cassandra-api-is-not-working-as-today-where-can-the-feedback-be-provided"></a>Rendszeres Cassandra API x funkciója nem működik, még ma, ahol is a visszajelzést adni?
Visszajelzés küldése keresztül [uservoice-visszajelzések](https://feedback.azure.com/forums/263030-azure-cosmos-db).

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md
