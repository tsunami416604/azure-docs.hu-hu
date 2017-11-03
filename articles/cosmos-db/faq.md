---
title: "Az Azure Cosmos DB kapcsolatos gyakori kérdések |} Microsoft Docs"
description: "Azure Cosmos DB, egy globálisan elosztott, több modellre adatbázis szolgáltatás gyakran feltett kérdésekre adott válaszok. További tudnivalók a kapacitás, a teljesítményszintet és a méretezés."
keywords: "Az adatbázisra vonatkozó kérdések, gyakran ismételt kérdéseket, a documentdb, az azure, a Microsoft azure"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: b68d1831-35f9-443d-a0ac-dad0c89f245b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2017
ms.author: mimig
ms.openlocfilehash: e8ac4bef5af7a98857278b82304bb58f1ac6e342
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-faq"></a>Az Azure Cosmos DB – gyakori kérdések
## <a name="azure-cosmos-db-fundamentals"></a>Az Azure Cosmos DB – alapok
### <a name="what-is-azure-cosmos-db"></a>Mi az Azure Cosmos DB?
Azure Cosmos-adatbázis egy globálisan replikált, több modellre adatbázis biztosító szolgáltatás, amely sémamentes adatok gazdag lekérdezését nyújt konfigurálható és megbízható teljesítményt, és gyors fejlesztést tesz lehetővé. Az összes érhető el, egy felügyelt platform, amely a kiemelt alapját, és a Microsoft Azure sokoldalúsága keresztül. 

Azure Cosmos-adatbázis az ideális megoldás a webes, mobil-, játék- és IoT-alkalmazásokhoz, amikor a kiszámítható átviteli sebességet, magas rendelkezésre állású, alacsony késést és a sémamentesadat-modell kapcsolatos követelményeket. Séma rugalmasságát és a gazdag indexelési biztosítja, és több dokumentumos tranzakciótámogatást JavaScript-integrációval ellátott tartalmazza. 

További adatbázis kérdéseket, válaszokat, valamint útmutatást és központi telepítéséhez, és használhatja a szolgáltatást, tekintse meg a [Azure Cosmos DB dokumentációs oldal](https://azure.microsoft.com/documentation/services/cosmos-db/).

### <a name="what-happened-to-documentdb"></a>Mi történt a DocumentDB?
A DocumentDB API egyike a támogatott API-k és az Azure Cosmos DB adatmodellekben. Emellett Azure Cosmos DB támogatja, a Graph API-val (előzetes verzió), a tábla API (előzetes verzió) és a MongoDB API. További információkért lásd: [kérdések a DocumentDB-ügyfelek](#moving-to-cosmos-db).

### <a name="how-do-i-get-to-my-documentdb-account-in-the-azure-portal"></a>Hogyan szerezhetek saját DocumentDB-fiók az Azure-portálon?
Az Azure-portálon kattintson a Azure Cosmos DB ikonra a bal oldali ablaktáblán. Ha egy DocumentDB-fiókot, mielőtt, most már rendelkezik Azure Cosmos DB adatait, a számlázási módosítása nélkül.

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Mik az Azure Cosmos DB jellemző használati esetei?
Azure Cosmos DB jó választás az új webes, mobil-, játék- és IoT-alkalmazásokhoz, ahol automatikus méretezési, kiszámítható teljesítményt, gyors ezredmásodperces válaszidők sorrendje, illetve a lekérdezés képessége sémamentes adatok azért fontos. Az Azure Cosmos DB adatmodelljeinek gyors fejlesztést és az azt támogató alkalmazás-adatmodellek folyamatos ismétlését. Felhasználó által létrehozott tartalmakat és adatokat kezelő alkalmazások [Azure Cosmos DB gyakori alkalmazási esetei](use-cases.md). 

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Hogyan Azure Cosmos DB kiszámítható teljesítményt nyújtanak?
A [kérelem egység](request-units.md) (RU) azt fejezi ki, az Azure Cosmos Adatbázisba az átviteli sebesség. Egy 1-RU átviteli sebesség egy 1 KB-os dokumentum GET felel meg. Minden Azure Cosmos DB, beleértve az olvasások, írások, SQL-lekérdezések és tárolt eljárás végrehajtások, a művelet egy determinisztikus RU értékkel rendelkezik, amely a művelet végrehajtásához szükséges átviteli alapul. CPU, IO, és a memória és a az alkalmazás átviteli sebességére gyakorolt ezek mindegyike továbbléphetnek helyett kérelemegységet kell figyelembe RU egyetlen mértéket.

Minden Azure Cosmos DB tárolóhoz kiosztott átviteli sebesség szempontjából másodpercenkénti RUs foglalhat. Az alkalmazások valamilyen skálát elvégez egy teljesítménytesztet a RU értékek egyes kérelmeket, és jogosultságok kiosztása a tároló az összes kérelem egység összes kérelem összes kérelemegységének kezeléséhez. Is növelheti vagy csökkentheti a tároló átviteli, az alkalmazás igényeinek változásával igényeinek. Kapcsolatos további információt, és segítséget meghatározása a tárolóban van szüksége, lásd [átviteli igények becslése](request-units.md#estimating-throughput-needs) , és próbálja meg a [átviteli Számológép](https://www.documentdb.com/capacityplanner). A kifejezés *tároló* itt hivatkozik a DocumentDB API gyűjtemény, a Graph API graph, a MongoDB API gyűjtemény és a tábla API tábla hivatkozik. 

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Hogyan támogatja az Azure Cosmos DB különböző adatmodellekben, például a kulcs/érték, oszlopos, a dokumentum és a graph?

Kulcs/érték (tábla), oszlopos, dokumentum és modellek rendszer natív módon támogatja a ars-re (atom, rekordok és sorozatok) miatt az összes kialakítása, hogy Azure Cosmos DB Diagramadatok épül. Atom, a rekordok és a feladatütemezések egyszerűen leképezve és különböző adatmodellekben vetítve. Modellek egy része számára az API-k elérhető jobb most (DocumentDB MongoDB, Table és Graph API-k) és mások vonatkozó további adatmodellekben elérhető lesz a jövőben.

Azure Cosmos-adatbázis egy séma független indexelési motor képes automatikusan indexelő azt ingests anélkül, hogy semmilyen sémát, illetve másodlagos indexek a fejlesztőtől származó összes adatot tartalmaz. A motor logikai index elrendezések (fordított, oszlopos, fa), amely a tárolási elrendezés az index és a lekérdezés feldolgozása alrendszerek absztrakcióhoz készlete támaszkodik. A cosmos DB is van lehetősége, bővíthető módon támogatja az átviteli protokollokat és API-k olyan készlete, és kéréseivé átalakítani azokat hatékonyan core adatmodell (1) és (2) így egyedi módon képes több adatmodellekben natív módon támogató logikai index elrendezések.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Azure Cosmos DB HIPAA megfelel?
Igen, az Azure Cosmos DB HIPAA-szabványnak megfelelő. A HIPAA az egyéni és beazonosítható egészségügyi adatok használatára, nyilvánosságra hozatalára és védelmére vonatkozó követelményeket állapítja meg. További információkért lásd: [Microsoft Adatvédelmi központ](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>A tárolási korlátai Azure Cosmos-adatbázis
A teljes adatmennyiség egy tárolót tárolhat Azure Cosmos DB nincs korlátozva van.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Mik azok a Azure Cosmos DB átviteli sebességének korlátai?
Összesen mekkora átviteli egy tároló által támogatott az Azure Cosmos Adatbázisba korlátozva van. A kulcs lényege, terjeszteni a munkaterhelés nagyjából egyenlően megfelelően nagy számú partíciós kulcsok között.

### <a name="how-much-does-azure-cosmos-db-cost"></a>Milyen mértékű nem költségű Azure Cosmos DB?
További információkért tekintse meg a [Azure Cosmos DB díjszabása](https://azure.microsoft.com/pricing/details/cosmos-db/) lap. Azure Cosmos DB használati díjak kiosztott tárolók, a tárolók volt online, órák száma számát határozza meg, és minden egyes tároló a létesített átviteli sebesség. A kifejezés *tárolók* Itt a DocumentDB API gyűjtemény, a Graph API graph, a MongoDB API gyűjtemény és a tábla API táblák hivatkozik. 

### <a name="is-a-free-account-available"></a>Van egy ingyenes fiókot?
Igen, regisztrálhat egy időben korlátozott fiók díjmentesen, elkötelezettség nélkül. A feliratkozáshoz látogasson el a [Azure Cosmos DB szabad próbálja](https://azure.microsoft.com/try/cosmosdb/) vagy további információ: a [próbálja Azure Cosmos DB GYIK](#try-cosmos-db).

Ha most ismerkedik az Azure-ba, a regisztrálhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/), ami 30 nap és, és az összes Azure-szolgáltatás kipróbálására. Ha a Visual Studio-előfizetéssel rendelkezik, akkor jogosultak is a [ingyenes Azure-krediteket](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) bármely Azure-szolgáltatáshoz felhasználhat. 

Használhatja a [Azure Cosmos DB emulátor](local-emulator.md) fejlesztéséhez és teszteléséhez az alkalmazás helyileg szabad, Azure-előfizetés létrehozása nélkül. Ha elégedett az alkalmazás az Azure Cosmos DB emulátorban alakulását, átválthat a felhőben Azure Cosmos DB fiókkal.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Hogyan kaphatok további segítséget a Azure Cosmos DB?
Ha segítségre van szüksége, kapcsolatba velünk a [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-cosmosdb) vagy a [MSDN fórum](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=AzureDocumentDB), vagy az Azure Cosmos DB mérnöki csapathoz beszélgetne csevegni ütemezése üzenet küldésével [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com). 

<a id="try-cosmos-db"></a>
## <a name="try-azure-cosmos-db-subscriptions"></a>Próbálja meg Azure Cosmos DB előfizetések

Mostantól egy időben korlátozott Azure Cosmos DB élmény előfizetés, át ingyenesen és kötelezettségvállalás nélkül. Regisztráljon egy Azure Cosmos DB próbálja meg előfizetés, keresse fel [Azure Cosmos DB szabad próbálja](https://azure.microsoft.com/try/cosmosdb/). Ez az előfizetés nem csatlakozik a [ingyenes Azure](https://azure.microsoft.com/free/), és használható mellett egy ingyenes Azure vagy az Azure-előfizetéssel. 

Próbálja meg Azure Cosmos DB előfizetések jelennek meg az Azure-portálon mellett más előfizetésekkel társított a felhasználói azonosítóját. 

Azure Cosmos DB próbálja előfizetések a következő feltételek vonatkoznak:

* Egy tároló SQL (a DocumentDB API), a Gremlin (Graph API-val) és a tábla fiókok előfizetésenként.
* A MongoDB-fiókok előfizetésenként legfeljebb 3 gyűjtemények.
* 10 GB-os tárolási kapacitás.
* Globális replikációs érhető el a következő [Azure-régiók](https://azure.microsoft.com/regions/): USA középső RÉGIÓJA, Észak-Európában és Délkelet-Ázsia
* Maximális átviteli sebességgel 5 K RU/mp.
* Előfizetések 24 óra után elévül, és annak teljes 48 óránál.
* Nem hozható létre az Azure támogatási jegyek próbálja Azure Cosmos DB fiókok; azonban támogatja-e a meglévő támogatási csomagokat előfizetők. 

## <a name="set-up-azure-cosmos-db"></a>Azure Cosmos DB beállítása
### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Do I regisztrálás módja az Azure Cosmos DB?
Azure Cosmos DB érhető el az Azure portálon. Először regisztráljon az Azure-előfizetésre. Most jelentkezett, miután az Azure-előfizetéshez a DocumentDB API, a Graph API-val (előzetes verzió), a tábla API (előzetes verzió) vagy a MongoDB API-fiók adhat.

### <a name="what-is-a-master-key"></a>Mi a főkulcs?
A főkulcs egy biztonsági jogkivonat, amellyel egy fiók összes erőforrása elérhető. A főkulccsal rendelkező egyének olvasási és írási hozzáférés az adatbázis-fiókot az összes erőforrást. Legyen körültekintő főkulcsok terjesztésekor. Az elsődleges és másodlagos főkulcsok érhetők el a **kulcsok** panel a [Azure-portálon][azure-portal]. Kulcsokkal kapcsolatos további információkért lásd: [megtekintése, másolása és újragenerálása hívóbetűk](manage-account.md#keys).

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>Mik azok a területek, amelyek PreferredLocations állítható be? 
A PreferredLocations érték bármely Azure-régiókban Cosmos DB érhető állítható be. Elérhető régiók listáját lásd: [Azure-régiók](https://azure.microsoft.com/regions/).

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Van-e, hogy ügyelnie, ha osztja el az adatokat az Azure adatközpontjaiban keresztül a világon között? 
Azure Cosmos-adatbázis az összes Azure-régiók megadott között megtalálható a [Azure-régiók](https://azure.microsoft.com/regions/) lap. Mivel az alapvető szolgáltatásához, minden új datacenter rendelkezik egy Azure Cosmos DB jelenlétét. 

Ha úgy állítja be a régió, ne feledje, hogy Azure Cosmos DB tiszteletben tartja a állami és kormányzati felhők. Ez azt jelenti, hogy egy szuverén régióban létrehoz egy fiókot, ha nem replikálja szuverén régió kívül. Hasonlóképpen nem engedélyezhető a replikálás be más szuverén helyeken kívül fiókból. 

## <a name="develop-against-the-documentdb-api"></a>A documentdb használatával API fejlesztése

### <a name="how-do-i-start-developing-against-the-documentdb-api"></a>Hogyan kezdhetem meg, hogy a DocumentDB API fejlesztésről?
Microsoft DocumentDB API érhető el a [Azure-portálon][azure-portal]. Először regisztrálnia kell az Azure-előfizetéssel. Regisztrálás után az Azure-előfizetéssel, adhat hozzá a DocumentDB API tároló az Azure-előfizetéshez. Egy Azure Cosmos DB fiók hozzáadására vonatkozó utasításokért lásd: [Azure Cosmos-adatbázis adatbázis-fiók létrehozása](create-documentdb-dotnet.md#create-account). Ha korábban rendelkezett egy DocumentDB-fiókot, most már rendelkezik egy Azure Cosmos DB fiókot. 

[SDK-k](documentdb-sdk-dotnet.md) a .NET, Python, Node.js, JavaScript és Java esetében érhetők el. A fejlesztők is használhatja a [RESTful HTTP API-k](/rest/api/documentdb/) kommunikál a különböző platformok és nyelvek Azure Cosmos DB erőforrásokat.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Tudok hozzáférni bizonyos előre elkészített minták segítséget?
Minták a DocumentDB API [.NET](documentdb-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](documentdb-nodejs-samples.md), és [Python](documentdb-python-samples.md) SDK a Githubon érhetők el.


### <a name="does-the-documentdb-api-database-support-schema-free-data"></a>Támogatja a DocumentDB API-adatbázis a sémamentes adatokat?
Igen, a DocumentDB API lehetővé teszi, hogy sémadefiníciók vagy mutatók nélkül tetszőleges JSON-dokumentumok tárolására alkalmazások. Adatok érhető el azonnal a Azure Cosmos adatbázis SQL-lekérdezési felületén keresztül.  

### <a name="does-the-documentdb-api-support-acid-transactions"></a>Támogatja a DocumentDB API ACID-tranzakciókat?
Igen, a DocumentDB API támogatja a JavaScript-ben tárolt eljárásokként és eseményindítókként kifejezett dokumentumok közötti tranzakciókat. Tranzakciók vannak egyes gyűjteményeken belül egyetlen partícióra hatókörű végrehajtásuk pedig az ACID szemantikákkal "mindent vagy semmit,", a többi párhuzamosan végrehajtott kódtól vagy felhasználói kérelmektől elkülönítve. A kivételek jelentkeznek a JavaScript alkalmazáskód kiszolgálóoldali végrehajtása, ha a teljes tranzakció vissza lesz állítva. Tranzakciókkal kapcsolatos további információkért lásd: [adatbázis-program tranzakciók](programming.md#database-program-transactions).

### <a name="what-is-a-collection"></a>Mi a gyűjtemény?
A gyűjtemény olyan dokumentumokat és a kapcsolódó JavaScript-alkalmazáslogikát. Egy gyűjtemény egy számlázható entitás, ahol a [költség](performance-levels.md) határozza meg az átviteli sebesség és a tárhelyet használja. Gyűjtemények egy vagy több partíciót, vagy kiszolgálók is kiterjedhet, és gyakorlatilag korlátlan mennyiségű tárterület vagy átviteli sebesség kezelésére méretezhetők.

Gyűjtemények egyaránt az Azure Cosmos DB számlázási egységei. Egyes gyűjtemények számlázása óránként történik, a létesített átviteli sebesség alapján, és tárolóhely használt. További információkért lásd: [Azure Cosmos DB árképzési](https://azure.microsoft.com/pricing/details/cosmos-db/). 

### <a name="how-do-i-create-a-database"></a>Hogyan hozható létre adatbázis?
Adatbázisok segítségével létrehozható a [Azure-portálon](https://portal.azure.com)leírtak szerint [egy gyűjtemény hozzáadása](create-documentdb-dotnet.md#create-collection), egy, a [Azure Cosmos DB SDK-k](documentdb-sdk-dotnet.md), vagy a [REST API-k](/rest/api/documentdb/). 

### <a name="how-do-i-set-up-users-and-permissions"></a>Hogyan állíthatom be a felhasználókat és engedélyeket?
Egyikének használatával hozhat létre felhasználókat és engedélyeket a [Cosmos DB API SDK-k](documentdb-sdk-dotnet.md) vagy a [REST API-k](/rest/api/documentdb/).  

### <a name="does-the-documentdb-api-support-sql"></a>Támogatja a DocumentDB API SQL?
Az SQL lekérdező nyelve az SQL által támogatott lekérdezési funkcionalitás továbbfejlesztett alkészlete. Az Azure Cosmos DB SQL lekérdező nyelve gazdag hierarchikus és relációs operátorokat és bővíthetőséget JavaScript-alapú, felhasználó által megadott funkciókat (UDF) biztosít. A JSON-szintaxis lehetővé teszi a modellezési JSON-dokumentumok fákként való címkézett csomópontok, amelyeket az Azure Cosmos DB automatikus indexelési technikái és Azure Cosmos-adatbázis SQL-lekérdezési dialektusa egyaránt használ. SQL-szintaxis használatával kapcsolatos információkért lásd: a [QueryDocumentDB] [ query] cikk.

### <a name="does-the-documentdb-api-support-sql-aggregation-functions"></a>Támogatja a DocumentDB API SQL összesítő függvények?
A DocumentDB API támogatja a kis késleltetésű összesítési keresztül összesítő függvények bármilyen léptékben `COUNT`, `MIN`, `MAX`, `AVG`, és `SUM` az SQL-szintaxis használatával. További információkért lásd: [Aggregátumfüggvények](documentdb-sql-query.md#Aggregates).

### <a name="how-does-the-documentdb-api-provide-concurrency"></a>Hogyan biztosítja a DocumentDB API egyidejűségi?
A DocumentDB API támogatja az egyidejű hozzáférések optimista vezérlését (OCC) HTTP entitáscímkék vagy ETag-EK keresztül. Minden DocumentDB API erőforrás rendelkezik egy ETag-gel, és az ETag állítsa be a kiszolgálón, minden alkalommal, amikor egy dokumentum frissül. Az ETag fejrészt és a jelenlegi érték szerepel az összes válaszüzenetek. Az If-Match fejléc megengedik döntse el, hogy egy erőforrás frissíteni kell a kiszolgáló ETag-EK is használható. Az If-Match értéke összevetni az ETag érték. Ha az ETag érték a kiszolgálói ETag érték megegyezik, az erőforrás frissül. Ha az ETag már nem aktuális, a kiszolgáló elutasítja a műveletet egy "HTTP 412 sikertelen előfeltétel" válaszkódot. Az ügyfél majd újból lekéri az beszerzése az erőforrás jelenlegi ETag érték erőforrás. Ezenkívül ETag-EK segítségével az If-None-Match fejléc határozza meg, hogy szükség van-e az erőforrás újból beolvasni.

A .NET egyidejű hozzáférések optimista használatához a [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) osztály. .NET mintát, lásd: [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) DocumentManagement minta a Githubon.

### <a name="how-do-i-perform-transactions-in-the-documentdb-api"></a>Hogyan végezhetők tranzakciók a DocumentDB az API-ban?
A DocumentDB API támogatja a nyelvintegrált tranzakciókat JavaScript-tárolt eljárások és eseményindítók keresztül. A parancsfájlban az összes művelet pillanatkép-elkülönítés lesz végrehajtva. Ha egy egypartíciós gyűjtemény, a végrehajtási a gyűjtemény hatókörét. Ha a gyűjtemény particionálva van, a végrehajtási a gyűjteményen belül azonos partíciókulcs értékkel rendelkező dokumentumok hatókörét. A tranzakció kezdetekor pillanatkép készül a dokumentumverziókról (ETag-ek), és csak akkor lesznek véglegesítve, ha a parancsfájl sikeres. Ha a JavaScript hibát jelez, a tranzakció vissza lesz állítva. További információkért lásd: [Azure Cosmos DB kiszolgálóoldali JavaScript programozás](programming.md).

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Hogyan képes vagyok tömeges beszúrási dokumentumok Cosmos DB be?
Akkor is tömeges beszúrási dokumentumokat az Azure Cosmos DB két módon:

* Az adatáttelepítés eszközzel, a [adatbázis áttelepítési eszköz az Azure Cosmos DB](import-data.md).
* Tárolt eljárások, a [Azure Cosmos DB kiszolgálóoldali JavaScript programozás](programming.md).

### <a name="does-the-documentdb-api-support-resource-link-caching"></a>Nem, a DocumentDB API támogatási erőforrás-hivatkozások gyorsítótárazását?
Igen, mert Azure Cosmos DB egy RESTful szolgáltatás, erőforrás-hivatkozások nem módosíthatók és ezáltal gyorsítótárazhatók. A DocumentDB API ügyfelek adjon meg egy "If-None-Match" fejlécet bármilyen erőforrás-szerű dokumentum vagy gyűjtemény olvasása, és ezután frissítse a helyi példányok, miután a kiszolgáló verziója megváltozott.

### <a name="is-a-local-instance-of-documentdb-api-available"></a>Egy helyi példányát a DocumentDB API érhető el?
Igen. A [Azure Cosmos DB emulátor](local-emulator.md) egy valósághű emuláció a Cosmos DB szolgáltatást biztosít. Támogatja a funkciót, amely azonos Azure Cosmos DB, például létrehozása, és lekérdezi a JSON-dokumentumokat, kiépítés és gyűjtemények skálázás, és végrehajtása tárolt eljárásokként és eseményindítókként. Fejlesztése és tesztelése az alkalmazások az Azure Cosmos DB Emulator használatával, és telepítheti őket egy globális léptékű azáltal, hogy módosítsa a csatlakozási végpont az Azure Cosmos DB egyetlen konfigurációja.

## <a name="develop-against-the-api-for-mongodb"></a>Az API-t elleni mongodb fejlesztése
### <a name="what-is-the-azure-cosmos-db-api-for-mongodb"></a>Mi az az Azure Cosmos DB API a MongoDB?
Az Azure Cosmos DB API a MongoDB, amely lehetővé teszi az alkalmazások egyszerűen és könnyen kommunikáljanak a natív Azure Cosmos-adatbázis adatbázis-kezelő Apache MongoDB API-k és az illesztőprogramok meglévő, a Közösség által támogatott kompatibilitási réteg. A fejlesztők most már használhatja meglévő MongoDB eszköz láncok és képességek előnyeit Azure Cosmos DB alkalmazásokat hozhatnak létre. A fejlesztők igénybe vehesse az Azure Cosmos DB, az egyedi képességeit, például az automatikus indexeléshez, a biztonsági mentési karbantartási, pénzügyi biztonsági szolgáltatásszint-szerződések (SLA), és így tovább.

### <a name="how-do-i-connect-to-my-api-for-mongodb-database"></a>Hogyan saját API-t a MongoDB adatbázis csatlakozni?
A leggyorsabban kapcsolódni az Azure Cosmos DB API, a MongoDB-hoz való head keresztül a [Azure-portálon](https://portal.azure.com). Nyissa meg a fiókját, és ezt követően kattintson a bal oldali navigációs menü **gyors üzembe helyezés**. Gyors üzembe helyezési a legjobb módja kódrészletek az adatbázishoz való kapcsolódáshoz. 

Azure Cosmos-adatbázis kényszeríti a szigorú biztonsági követelményeket és előírásokat. Azure Cosmos DB fiókok hitelesítési és SSL-en keresztüli biztonságos kommunikációhoz szükséges, ezért TLSv1.2 használja.

További információkért lásd: [az API-t a MongoDB adatbázis kapcsolódás](connect-mongodb-account.md).

### <a name="are-there-additional-error-codes-for-an-api-for-mongodb-database"></a>Vannak további hibakódokat egy API-t a MongoDB-adatbázist?
MongoDB gyakori hibakódokat mellett a MongoDB API a saját konkrét hibakódok rendelkezik:


| Hiba               | Kód  | Leírás  | Megoldás  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | A felhasznált kérelem egységek száma a gyűjtemény a kiépített kérelem-egység arány meghaladta, és már elindította. | Fontolja meg az Azure-portálon a gyűjtemény átviteli skálázás, vagy újra próbálkozna. |
| ExceededMemoryLimit | 16501 | A több-bérlős szolgáltatásként a művelet túllépte az ügyfél memória allokációs arányt. | Csökkentse a műveletet az szigorúbb feltételek hatókörét, vagy forduljon a támogatási szolgálathoz a [Azure-portálon](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). <br><br>Példa:  *&nbsp; &nbsp; &nbsp; &nbsp;db.getCollection('users').aggregate ([<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$match: {név: "ADAM"}}, <br> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;{$sort: {kor: -1}}<br>&nbsp;&nbsp;&nbsp;&nbsp;])*) |

## <a name="develop-with-the-table-api-preview"></a>Fejlesztés és a tábla API (előzetes verzió)

### <a name="terms"></a>Fogalmak 
Az Azure Cosmos DB: Tábla API (előzetes verzió) a prémium szintű Azure Cosmos DB tábla támogatásához a Build 2017 bejelentette ajánlat hivatkozik. 

A szabványos tábla SDK a meglévő Azure Storage-tábla SDK. 

### <a name="how-can-i-use-the-new-table-api-preview-offering"></a>Hogyan használhatók az új tábla API (előzetes verzió) elérhető? 
Az Azure Cosmos DB tábla API érhető el a [Azure-portálon][azure-portal]. Először regisztrálnia kell az Azure-előfizetéssel. Most jelentkezett, miután egy Azure Cosmos DB tábla API-fiók hozzáadása az Azure-előfizetéshez, és adja táblák a fiókját. 

A próbaidőszak alatt Ha [SDK-k](../cosmos-db/table-sdk-dotnet.md) vannak érhető el a .NET, elkezdheti; Ehhez hajtsa végre a [tábla API](../cosmos-db/create-table-dotnet.md) gyors üzembe helyezési cikk.

### <a name="do-i-need-a-new-sdk-to-use-the-table-api-preview"></a>Kell egy új SDK az a tábla API (előzetes verzió)? 
Igen, a [Windows Azure Storage prémium Table (előzetes verzió) SDK](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable) NuGet érhető el. További információ áll rendelkezésre a [Azure Cosmos DB táblában .NET API: Töltse le és a kibocsátási megjegyzésekben](https://github.com/Microsoft/azure-docs-pr/cosmos-db/table-sdk-dotnet.md) lap. 

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Hogyan nyújtanak az SDK vagy hibák visszajelzést?
Visszajelzése megoszthatja a következő módokon:

* [Uservoice](https://feedback.azure.com/forums/599062-azure-cosmos-db-table-api)
* [MSDN-fórum](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=AzureDocumentDB)
* [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-cosmosdb)

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api-preview"></a>Mi az a kapcsolati karakterláncot, amely szükség van kapcsolódni a tábla API (előzetes verzió)?
A kapcsolati karakterlánc::
```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountNameFromDocumentDB>.documents.azure.com
```
A kapcsolati karakterláncot az Azure-portálon a kulcsok lapon kérheti le. 

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-new-table-api-preview"></a>Hogyan bírálja felül a beállításokat az új tábla API (előzetes verzió) konfigurációs beállításait?
Konfigurációs beállításaival kapcsolatos további információkért lásd: [Azure Cosmos DB képességek](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities). Az ügyfélalkalmazás appSettings szakaszában app.config való hozzáadással megváltoztathatják a beállításokat.

    <appSettings>
        <add key="TableConsistencyLevel" value="Eventual|Strong|Session|BoundedStaleness|ConsistentPrefix"/>
        <add key="TableThroughput" value="<PositiveIntegerValue"/>
        <add key="TableIndexingPolicy" value="<jsonindexdefn>"/>
        <add key="TableUseGatewayMode" value="True|False"/>
        <add key="TablePreferredLocations" value="Location1|Location2|Location3|Location4>"/>....
    </appSettings>


### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-standard-table-sdk"></a>Vannak-e a módosításokat a meglévő szabványos tábla SDK használó ügyfelek számára?
nincs. Nincsenek a meglévő szabványos tábla SDK használó meglévő vagy új ügyfelek nem módosult. 

### <a name="how-do-i-view-table-data-that-is-stored-in-azure-cosmos-db-for-use-with-the-table-api-review"></a>Hogyan tekinthetem meg a tábla API (áttekintése) való használathoz az Azure Cosmos DB tárolt tábla adatai? 
Az Azure portál segítségével keresse meg az adatokat. A tábla API (előzetes verzió) kóddal vagy az eszközök szerepel a következő választ is használható. 

### <a name="which-tools-work-with-the-table-api-preview"></a>Mely eszközök használható a tábla API (előzetes verzió)? 
Használhatja az Azure Explorer (0.8.9) régebbi verzióját.

Eszközök igénybe vehet egy kapcsolati karakterláncot a megadott formátumban rugalmassága korábban is támogatja az új tábla API (előzetes verzió). Egy tábla eszközök listája a a [Azure Storage ügyféleszközök elől](../storage/common/storage-explorers.md) lap. 

### <a name="do-powershell-or-azure-cli-work-with-the-new-table-api-preview"></a>PowerShell vagy Azure CLI működnek az új tábla API-t (előzetes verzió)?
Tábla API (előzetes verzió) támogatásához a PowerShell és az Azure parancssori felület tervezzük. 

### <a name="is-the-concurrency-on-operations-controlled"></a>Az a feldolgozási műveletek ellenőrzött?
Igen, egyidejű hozzáférések optimista ETag mechanizmus segítségével valósul meg. 

### <a name="is-the-odata-query-model-supported-for-entities"></a>Az OData-lekérdezési modellel támogatja az entitások? 
Igen, a tábla API (előzetes verzió) támogatja az OData-lekérdezés és a LINQ lekérdezés. 

### <a name="can-i-connect-to-the-standard-azure-table-and-the-new-premium-table-api-preview-side-by-side-in-the-same-application"></a>Képes kapcsolódni a standard Azure-tábla és az új premium tábla API (előzetes verzió) párhuzamos ugyanabban az alkalmazásban? 
Igen, hozzon létre két külön példányának a CloudTableClient, minden egyes merevlemezre mutat-e a saját URI a kapcsolati karakterlánc használatával is elérheti.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-new-offering"></a>Hogyan át egy meglévő Azure Table storage-alkalmazás új ajánlathoz?
Az új tábla API elérhető kihasználásához a meglévő tábla tárolási adatai, lépjen kapcsolatba [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com). 

### <a name="what-is-the-roadmap-for-this-service-and-when-will-you-offer-other-standard-table-api-functionality"></a>A terv ezt a szolgáltatást, és mikor fog kínált más szabványos tábla API-funkciók?
Tervezzük SAS-tokenje, ServiceContext, statisztikák, ügyféloldali titkosítás, elemzés és egyéb szolgáltatások támogatása, a Folytatás GA felé Akkor is küldjön visszajelzést a [Uservoice](https://feedback.azure.com/forums/599062-azure-cosmos-db-table-api). 

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Hogyan van elvégezni ezt a szolgáltatást, ha például indításakor a tároló méretének bővítése  *n*  GB adatok és az adatok és 1 TB tájékoztatás idővel bővülni fog? 
Azure Cosmos DB használatával a horizontális skálázás korlátlan tárolást szolgál. A szolgáltatás figyelése, és hatékonyan növelése tárhelyét. 

### <a name="how-do-i-monitor-the-table-api-preview-offering"></a>Hogyan figyelhetek az tábla API (előzetes verzió) elérhető?
Tábla API-hoz (előzetes verzió) **metrikák** ablaktábla kérelmek és tárhely-használat figyelését. 

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Hogyan kiszámítja a az átviteli sebesség I igényelnek?
A kapacitás négyzetgyökének használhatja a TableThroughput, amely a műveletekhez szükséges kiszámításához. További információkért lásd: [becsült kérelem egységek és az adatok tárolási](https://www.documentdb.com/capacityplanner). Általában a JSON-ként entitást képviselik, és adja meg a számokat az operatív. 

### <a name="can-i-use-the-new-table-api-preview-sdk-locally-with-the-emulator"></a>Az új tábla API (előzetes verzió) helyileg emulátorral SDK használata
Igen, használhatja a tábla API-t (előzetes verzió) a helyi emulátorral az új SDK használatakor. Új emulátor letöltéséhez keresse fel [Emulatorral az Azure Cosmos DB helyi fejlesztési és tesztelési](local-emulator.md). Az App.config fájlban a StorageConnectionString érték egyike lehet:

```
DefaultEndpointsProtocol=https;AccountName=localhost;AccountKey=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==;TableEndpoint=https://localhost:8081`. 
```

### <a name="can-my-existing-application-work-with-the-table-api-preview"></a>A meglévő alkalmazás dolgozhat a tábla API (előzetes verzió)? 
A meglévő Azure standard tábla SDK a létrehozás, törlés, frissítés és lekérdezési műveletek a .NET API (előzetes verzió) új táblában Alkalmazásprogramozási felületének esetén. Ellenőrizze, hogy sorkulcsa, mert a tábla API (előzetes verzió) partíciós kulcs és a sorkulcs van szükség. Is tervezzük felé szolgáltatás ajánlat GA Folytatás további SDK támogatását.

### <a name="do-i-need-to-migrate-my-existing-azure-table-based-applications-to-the-new-sdk-if-i-do-not-want-to-use-the-table-api-preview-features"></a>Kell áttelepíteni a meglévő Azure table-alapú alkalmazások az új SDK, ha nem akarja használni a tábla API (előzetes verzió) szolgáltatásokat?
Nem hozhat létre és használja a meglévő szabványos tábla eszközök bármilyen megszakítás nélkül. Azonban ha nem használja az új tábla API (előzetes verzió), nem kihasználhatja az automatikus index, a további konzisztencia beállítás vagy a globális terjesztési. 

### <a name="how-do-i-add-replication-of-the-data-in-the-premium-table-api-preview-across-multiple-regions-of-azure"></a>Hogyan adja hozzá az adatok replikálása a különféle régiókban Azure Premium szinten tábla API (előzetes verzió)?
Használhatja az Azure Cosmos DB portal [globális replikációs beállítások](tutorial-global-distribution-documentdb.md#portal) hozzáadása az alkalmazáshoz megfelelő régiók. Globálisan elosztott alkalmazások fejlesztéséhez, akkor is fel kell az alkalmazás beállítása a helyi régió biztosítani azt az olvasási kis késleltetésű PreferredLocation adatokkal. 

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-premium-table-api-preview"></a>Hogyan változtathatom meg a elsődleges írási terület, a fiók a prémium szintű tábla API (előzetes verzió)?
Az Azure Cosmos DB globális replikációs portál ablaktábla segítségével adja hozzá egy régiót, és ezután átveheti a szükséges terület. Útmutatásért lásd: [fejlesztés az Azure Cosmos DB fiókok több területi](regional-failover.md). 

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Konfigurálása a saját elsődleges olvasási régiók az alacsony késleltetés amikor I terjesztése adataimat? 
Annak érdekében, olvassa el a helyi helyről a PreferredLocation billentyűvel az app.config fájlban. Meglévő alkalmazások esetében a tábla API (előzetes verzió) hibát jelez, ha LocationMode be van állítva. Távolítható el, hogy a kód, mert a prémium szintű tábla API (előzetes verzió) szerzi be ezt az információt az app.config fájlból. További információkért lásd: [Azure Cosmos DB képességek](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities).

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api-preview"></a>Hogyan kell a tábla API (előzetes verzió) konzisztenciaszintek fontolnunk? 
Azure Cosmos DB biztosít jól indokolt kompromisszumot konzisztencia, a rendelkezésre állás és a késleltetés között. Azure Cosmos-adatbázis öt konzisztenciaszintek felajánlja, hogy a tábla API (előzetes verzió) fejlesztők számára, így válassza ki a megfelelő konzisztencia modelljét tábla szintű és az egyes kéréseket az adatok lekérdezése közben. Amikor egy ügyfél csatlakozik, a konzisztenciaszint határozhatja meg. A szint a TableConsistencyLevel kulcsnak az értéke az app.config beállítás segítségével módosítható. 

A tábla API (előzetes verzió) biztosít a kis késleltetésű olvassa be a "olvasása" saját írási műveletekről, az alapértelmezett munkamenet-konzisztencia. További információkért lásd: [konzisztenciaszintek](consistency-levels.md). 

Alapértelmezés szerint a Azure Table storage régión belül az erős konzisztencia és a másodlagos helyeken Eventual konzisztencia biztosítja. 

### <a name="does-azure-cosmos-db-offer-more-consistency-levels-than-standard-tables"></a>Azure Cosmos DB nyújtja a több, mint a hagyományos táblázatokban konzisztenciaszintek?
Igen, Azure Cosmos DB elosztott jellege kihasználják kapcsolatos információkért lásd: [konzisztenciaszintek](consistency-levels.md). Garantálja a konzisztenciaszintek-okat, mert az vetett bizalmat használhatja őket. További információkért lásd: [Azure Cosmos DB képességek](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities).

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Ha engedélyezve van a globális terjesztési, mennyi időt vesz igénybe az adatot replikálni?
A Microsoft véglegesíteni az adatokat a helyi régióban tartósan, és az adatok leküldése más régiókban azonnal be ideje (MS). A replikáció csak az üzenetváltási időt (RTT) az Adatközpont függ. A globális terjesztési funkció Azure Cosmos DB kapcsolatos további információkért lásd: [Azure Cosmos DB: Azure globálisan elosztott adatbázis szolgáltatás](distribute-data-globally.md).

### <a name="can-the-read-request-consistency-level-be-changed"></a>Módosíthatja az olvasási kérelem konzisztenciaszint?
Az Azure Cosmos DB amelyen beállíthatja a konzisztenciaszint a tároló szintjén (a táblázat). Az SDK segítségével módosíthatja a szint TableConsistencyLevel kulcs az app.config fájlban az érték megadásával. A lehetséges értékek: erős, kötött elavulási, munkamenet, egységes előtagot, valamint Eventual. További információkért lásd: [konzisztenciaszintek hangolható adatokat az Adatbázisba az Azure Cosmos](consistency-levels.md). A kulcs lényege, hogy nem állítható be a kérelem konzisztencia szint: több, mint a beállítás a következő táblázatban. A következő táblázatban a konzisztenciaszint például Eventual és az erős konzisztencia szint a kérelem nem állítható. 

### <a name="how-does-the-premium-table-api-preview-account-handle-failover-if-a-region-goes-down"></a>Hogyan nem a prémium szintű tábla API (előzetes verzió) fiókot kezelik feladatátvételi Ha leáll egy régiót? 
A prémium szintű Azure Cosmos-adatbázis a globálisan elosztott platformról alapszik tábla API (előzetes verzió). Győződjön meg arról, hogy az alkalmazás működését datacenter állásidő, engedélyezze a fiókot az Azure Cosmos DB portálon legalább egy további régió [fejlesztés az Azure Cosmos DB fiókok több területi](regional-failover.md). A portál használatával beállíthatja a prioritás a régió [fejlesztés az Azure Cosmos DB fiókok több területi](regional-failover.md). 

A fiók, és szabályozhatja, ahol azt átveheti a feladatátvételi prioritási megadásával annyi régiókban is hozzáadhat. Természetesen az adatbázis használatához meg kell adnia egy alkalmazás túl. Ha így tesz, az ügyfelek nem fog tapasztalni állásidő. Az SDK-ügyfél automatikus homing. Ez azt jelenti, hogy a régiót, amelyben nem működik, és automatikusan áthelyezze a feladatokat az új régióban azonosítására képes.

### <a name="is-the-premium-table-api-preview-enabled-for-backups"></a>A prémium szintű tábla API (előzetes verzió) engedélyezve van a biztonsági mentésekhez?
Igen, a prémium szintű tábla API (előzetes verzió) alapszik a platformról Azure Cosmos-adatbázis a biztonsági mentésekhez. Biztonsági mentések a automatikusan történik. További információkért lásd: [Online biztonsági mentés és helyreállítás Azure Cosmos DB](online-backup-and-restore.md).

 
### <a name="does-the-table-api-preview-index-all-attributes-of-an-entity-by-default"></a>A tábla API (előzetes verzió) index entitás összes attribútumának alapértelmezés szerint?
Igen, az entitás összes attribútum indexelt alapértelmezés szerint. További információkért lásd: [Azure Cosmos DB: Indexelő házirendek](indexing-policies.md). 

### <a name="does-this-mean-i-do-not-have-to-create-multiple-indexes-to-satisfy-the-queries"></a>A középérték nem kell teljesíteni a lekérdezések több indexek létrehozása does? 
Igen, Azure Cosmos DB biztosít az összes attribútum nélkül bármely sémadefiníciót automatikus indexeléshez. Ezt az automatizálást szabadít fel a fejlesztők számára, hogy az alkalmazás és nem index létrehozását és kezelését a fókuszt. További információkért lásd: [Azure Cosmos DB: Indexelő házirendek](indexing-policies.md).

### <a name="can-i-change-the-indexing-policy"></a>Az indexelési házirendet is megváltozik?
Igen, módosíthatja az indexelési házirendet azáltal, hogy az index definícióját. További információkért lásd: [Azure Cosmos DB képességek](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities). Megfelelően kódolja, és a beállítások escape kell. 

Karakterlánc json formátumban az app.config fájlban:
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

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>Azure Cosmos DB platformként úgy tűnik, hogy számos képesség, rendezés, összesítések, hierarchiát és egyéb funkciókat. Ön hozzáadja ezeket a képességeket a tábla API-val? 
A képen a tábla API Azure Table storage azonos lekérdezés funkciókat biztosít. Azure Cosmos-adatbázis is támogatja a rendezést, összesítések, a földrajzi lekérdezést, a hierarchia és a számos különféle beépített funkciók. A tábla API jövőbeli szolgáltatásfrissítés további funkciók lesz elérhető. További információkért lásd: [Azure Cosmos DB DocumentDB API SQL-lekérdezések](../documentdb/documentdb-sql-query.md).
 
### <a name="when-should-i-change-tablethroughput-for-the-table-api-preview"></a>Mikor kell TableThroughput megváltoztatása a tábla API (előzetes verzió)?
TableThroughput kell módosítani, ha érvényes a következő feltételek valamelyike:
* Helyreállítást hajt végre egy kinyerési, átalakítási és betöltési (ETL) az adatok, vagy nagy mennyiségű adat rövid időn belül feltölteni kívánt. 
* További átviteli, a háttér-tárolójából van szüksége. Például láthatja, hogy a használt átviteli sebesség több, mint a létesített átviteli sebesség, és hogy szabályozott első. További információkért lásd: [Set átviteli az Azure Cosmos DB tárolókat](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-preview-table"></a>Növelheti vagy csökkentheti a teljesítményt a tábla API (előzetes verzió) tábla? 
Igen, az Azure Cosmos DB portal méretezési ablaktábla segítségével méretezhető, az átviteli sebesség. További információkért lásd: [Set átviteli](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Az egy alapértelmezésben TableThroughput újonnan kiosztott táblák?
Igen, ha nem bírálja felül a TableThroughput app.config keresztül, és nem használható egy előre létrehozott tároló Azure Cosmos DB, a szolgáltatás létrehoz egy táblát 400 átviteli sebességgel.
 
### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-standard-table-api"></a>Van-e bármilyen módosítás a meglévő ügyfeleknek a szabványos tábla API árképzési?
nincs. Nincs meglévő szabványos tábla API-ügyfeleknek az ár nem változik. 

### <a name="how-is-the-price-calculated-for-the-table-api-preview"></a>Hogyan számítható ára tábla API-hoz (előzetes verzió)? 
Az ár attól függ, hogy a lefoglalt TableThroughput. 

### <a name="how-do-i-handle-any-throttling-on-the-tables-in-table-api-preview-offering"></a>Hogyan kezelik, a sávszélesség-szabályozás a táblák ajánlat tábla API (előzetes verzió)? 
A kérelmek aránya meghaladja a létesített átviteli sebesség a mögöttes tároló kapacitását, ha hiba lép fel, és az SDK-t fog próbálja meg újra a hívást úgy, hogy az újrapróbálkozási házirendet alkalmazza.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-premium-table-api-preview-offering-of-azure-cosmos-db"></a>Miért kell választania egy átviteli sebesség előnyeit az prémium tábla API (előzetes verzió) elérhető Azure Cosmos DB PartitionKey és RowKey?
Azure Cosmos-adatbázis a egy alapértelmezett átviteli sebesség a tároló beállítja, ha nem ad meg az app.config fájlban. 

Azure Cosmos DB garanciákat nyújt a teljesítmény és a késés, a felső határai, a műveletet. A garantált a motor kényszerítheti a cégirányítási műveletek a bérlő esetén lehetséges. TableThroughput beállítása biztosítja, hogy kap a garantált átviteli sebesség és a késleltetés, mivel a platform fenntartja a kapacitás, és biztosítja, hogy az operatív sikeres. 

Az átviteli sebesség meghatározása segítségével rugalmasan módosíthatja úgy, hogy igénybe vehesse a szezonalitás értékének az alkalmazás az átviteli sebesség igényeinek és költségek csökkentése.

### <a name="azure-storage-sdk-has-been-very-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-new-azure-cosmos-db-offering-seems-to-be-charging-me-even-though-i-have-not-performed-a-single-transaction-or-stored-anything-can-you-please-explain"></a>Az Azure Storage szolgáltatás SDK a számomra, nagyon alacsony költségű volt, mert az csak a tárolja az adatokat, és szeretnék ritkán lekérdezés kell fizetni. Az új Azure Cosmos DB elérhető úgy tűnik, hogy lehet díjszabási me annak ellenére, hogy I nem egyetlen tranzakció végre vagy bármi tárolja. Adjon melyek?

Azure Cosmos-adatbázis egy rendelkezésre állási, a késés és átviteli garanciák globálisan elosztott, SLA-alapú rendszer tervezték. Amikor lefoglalni Azure Cosmos DB átviteli sebességet, válik biztossá, szemben a más rendszerekkel átviteli sebességgel. Azure Cosmos-adatbázis, amely az ügyfelek kért, például a másodlagos indexek és globális terjesztési további képességeket biztosít. A próbaidőszak alatt egy átviteli optimalizált modell nyújtunk, és végül azt tervezi, hogy biztosítja a felhasználók igényeinek tárolási optimalizált modelljét. 

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-table-storage-with-the-table-api-preview-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Soha nem jelenik meg a "teljes" kvótaértesítéshez (arról, hogy a partíció teljes) Ha I betöltik az adatokat a Table storage-be. A tábla API-t (előzetes verzió) Ez az üzenet jelenik meg. Ez kínál me korlátozása van, és módosíthatja a meglévő alkalmazás me?

Azure Cosmos-adatbázis egy SLA-alapú rendszeren, amely korlátlan méretezési biztosít garanciák késés, teljesítményt, rendelkezésre állási és konzisztencia. Garantált prémium szintű teljesítmény biztosítása érdekében ellenőrizze, hogy az adatok mérete és az index kezelhető és méretezhető. A 10 GB-os korlátot az entitások vagy elemek száma a partíciós kulcs annak érdekében, hogy nyújtunk remek keresési és lekérdezéseivel kapcsolatos teljesítményt. Győződjön meg arról, hogy az alkalmazás is jól az Azure Storage méretezze át, javasoljuk, hogy Ön *nem* hozza létre a gyakran használt adatok partíciót egy partíció összes információ tárolása és lekérdezéssel. 

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-new-table-api-preview"></a>Ezért PartitionKey és RowKey továbbra is szükség az új tábla API-t (előzetes verzió)? 
Igen. Mivel a Table storage SDK hasonló felületének a tábla API (előzetes verzió), a partíciós kulcs teszi lehetővé az adatok terjesztéséhez. Sor fontos, hogy egyedi. A sorkulcs kell elhelyezkednie, és nem lehet null értékű, mint a szokásos SDK. RowKey hossza 255 bájt és PartitionKey hossza 100 bájtok (hamarosan-ra, 1 KB). 

### <a name="what-are-the-error-messages-for-the-table-api-preview"></a>Mik a hibaüzenetek a tábla API (előzetes verzió)?
Ez az előzetes kiadás nem kompatibilis a normál táblával, mert a hibák többségét felelteti meg a hibákat a szabványos táblából. 

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api-preview"></a>Miért tegye I get halmozódni való létrehozása nagy mennyiségű táblák egymás után a tábla API (előzetes verzió)?
Azure Cosmos-adatbázis az SLA-alapú rendszerek késés, teljesítményt, rendelkezésre állását és konzisztencia biztosítja biztosító. Mivel a kiépített rendszert, ezek a követelmények biztosításához erőforrások foglalja le. Táblázatok létrehozása a gyors aránya észlelt és szabályozva. Azt javasoljuk, hogy tekintse meg a táblák létrehozását aránya, és csökkentése a kevesebb mint 5 percenként. Ne feledje, hogy a tábla API (előzetes verzió) kiépített rendszer. A kiépítése, hamarosan kész kezdve látni fogja azt kell fizetnie. 

## <a name="develop-against-the-graph-api-preview"></a>A Graph API (előzetes verzió) ellen fejlesztése
### <a name="how-can-i-apply-the-functionality-of-graph-api-preview-to-azure-cosmos-db"></a>Hogyan alkalmazhatók az Graph API-val (előzetes verzió) funkcióit Azure Cosmos DB?
Egy bővítmény könyvtár segítségével alkalmazhat a Graph API-val (előzetes verzió) funkcióit. Ezt a szalagtárat a Microsoft Azure diagramjait nevezik, és a NuGet érhető el. 

### <a name="it-looks-like-you-support-the-gremlin-graph-traversal-language-do-you-plan-to-add-more-forms-of-query"></a>Úgy tűnik, a Gremlin graph átjárás nyelv támogatott. Biztosítani kívánja a lekérdezés további formáit hozzáadása?
Igen, tervezzük adja hozzá a jövőben más mechanizmusok lekérdezéshez. 

### <a name="how-can-i-use-the-new-graph-api-preview-offering"></a>Hogyan használhatók a Graph API-val (előzetes verzió) az új ajánlat? 
Először végezze el a [Graph API](../cosmos-db/create-graph-dotnet.md) gyors üzembe helyezési cikk.

<a id="moving-to-cosmos-db"></a>
## <a name="questions-from-documentdb-customers"></a>Kérdések a DocumentDB-ügyfelek
### <a name="why-are-you-moving-to-azure-cosmos-db"></a>Miért meg áthelyezi az Azure Cosmos Adatbázishoz? 

Azure Cosmos-adatbázis a következő nagy termékek globálisan elosztott, a méretezési felhő adatbázisokban. A DocumentDB ügyfélként most már rendelkezik hozzáféréssel a áttörést jelentő rendszer és az Azure Cosmos DB által kínált lehetőségeket.

Azure Cosmos-adatbázis, "Projekt Firenzében" elhárítása a Microsoft vállalaton belül nagyméretű alkalmazások fejlesztése során a fejlesztők által tapasztalt problémás pontok 2010 elindítása. Kihívásai globálisan elosztott alkalmazások létrehozása nem egyediek a Microsoftnak, így azt a technológia első generációs elérhetővé tett 2015 Azure DocumentDB formájában az Azure a fejlesztők. 

Azóta előre hozzáadott új funkciók és bevezetett jelentős új képességekkel rendelkezik. Az Azure Cosmos DB eredménye. Ebben a kiadásban részeként ügyfelek a DocumentDB-vel adataikat, automatikusan és zökkenőmentesen vásárlás Azure Cosmos DB. Ezek a képességek a core adatbázismotor, valamint a globális terjesztési, a rugalmas méretezhetőség és a iparágvezető, átfogó SLA-k szerepelnek. Pontosabban azt az Azure Cosmos DB adatbázismotor hatékonyan hozzárendelését az alapul szolgáló adatmodellt Azure Cosmos-adatbázis az összes népszerű adatmodellekben típus rendszerek és API-k továbbfejlődtek rendelkezik. 

Az aktuális fejlesztői irányuló megnyilvánulása a munka az újonnan bevezetett támogatása [Gremlin](../cosmos-db/graph-introduction.md) és [Table storage API-kkal](../cosmos-db/table-introduction.md). És ez csak a kezdő. Adja hozzá az egyéb népszerű API-k és az újabb adatmodellekben adott idő alatt, további fejlett teljesítmény- és tárolási globális léptékű tervezzük. 

Fontos, hogy a DocumentDB mutasson [SQL dialektus](../documentdb/documentdb-sql-query.md) mindig az, hogy az alapul szolgáló Azure Cosmos DB támogató sok API-kat csak egyike. A fejlesztők számára, például az Azure Cosmos Database egy teljes körűen felügyelt szolgáltatást alkalmazó a szolgáltatáshoz csak illesztőfelület az API-k által a szolgáltatás. Semmi valóban módosítja a meglévő DocumentDB-ügyfelek. Az Azure Cosmos Adatbázisba pontosan az azonos SQL API-t, amely a DocumentDB kínál beolvasása. És most (és a jövőben) érheti el más korábban nem elérhető képességek 

A folyamatos munka egy másik megnyilvánulása az kiterjesztett alapja a globális és a rugalmas méretezhetőség érdekében az átviteli sebesség és tárterület. Továbbfejlesztettük több eligazodást globális terjesztési alrendszerére. Az számos ilyen fejlesztői irányuló szolgáltatások egyike a konzisztens előtag konzisztencia modell, amely lehetővé teszi egy teljes öt jól meghatározott konzisztencia modellek. Sok érdekesebb képességek, valamint azt ad ki. 

### <a name="what-do-i-need-to-do-to-ensure-that-my-documentdb-resources-continue-to-run-on-azure-cosmos-db"></a>Mit kell tennie, győződjön meg arról, hogy a DocumentDB-erőforrások továbbra is az Azure Cosmos-adatbázis futtatásához?

Minden nem módosítani kell. A DocumentDB-erőforrások Azure Cosmos DB erőforrásokat, és hiba történt a szolgáltatás megszakítás nélküli fenntartása, az áthelyezés előfordulásakor.

### <a name="what-changes-do-i-need-to-make-for-my-app-to-work-with-azure-cosmos-db"></a>Milyen módosításokat kell az Azure Cosmos DB együttműködni my alkalmazás legyen?

Nincsenek módosítások végrehajtásához. Osztályok, a névterek és a NuGet csomag neve nem módosultak. Ennek mindig azt javasoljuk, hogy Ön az SDK-k naprakészen tartása a legújabb szolgáltatásait és fejlesztéseit előnyeit. 

### <a name="whats-changed-in-the-azure-portal"></a>Változások az Azure-portálon?

A DocumentDB már nem jelenik meg a portálon az Azure-szolgáltatások. Helyette van egy új Azure Cosmos DB ikonra, a következő ábrán látható módon. A gyűjtemények érhetők el, előtt voltak, és továbbra is méretezheti átviteli, a módosítás konzisztenciaszintek és a figyelő SLA-k. Az adatok kezelővel (előzetes verzió) képességeit tovább lett fejlesztve. Mostantól megtekintheti és szerkeszthetik a dokumentumokat, hozzon létre és lekérdezéseket futtathat, és dolgozhassanak tárolt eljárások, eseményindítók és UDF egy oldal, a következő ábrán látható módon: 

![Az Azure Cosmos DB Gyűjtemények panel](./media/faq/cosmos-db-data-explorer.png)

### <a name="are-there-changes-to-pricing"></a>Vannak-e árképzési módosításait?

Nem, az alkalmazás futtatásának az Azure-Cosmos adatbázis megegyezik a előtti.

### <a name="are-there-changes-to-the-slas"></a>Vannak-e a szolgáltatásiszint-szerződések módosításait?

Nem, a rendelkezésre állási, a konzisztencia, a késés és a átviteli SLA nem változik, és továbbra is megjelennek a portálon. További információkért lásd: [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/).
   
![Tennivalók app mintaadatokkal](./media/faq/azure-cosmosdb-portal-metrics-slas.png)


[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md
