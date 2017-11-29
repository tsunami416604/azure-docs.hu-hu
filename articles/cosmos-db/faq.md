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
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: 0f45468616884a6866bd95ef53acab71b4fed06c
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="azure-cosmos-db-faq"></a>Az Azure Cosmos DB – gyakori kérdések
## <a name="azure-cosmos-db-fundamentals"></a>Az Azure Cosmos DB – alapok
### <a name="what-is-azure-cosmos-db"></a>Mi az Azure Cosmos DB?
Azure Cosmos-adatbázis egy globálisan replikált, több modellre adatbázis biztosító szolgáltatás, amely sémamentes adatok gazdag lekérdezését nyújt konfigurálható és megbízható teljesítményt, és gyors fejlesztést tesz lehetővé. Az összes érhető el, egy felügyelt platform, amely a kiemelt alapját, és a Microsoft Azure sokoldalúsága keresztül. 

Azure Cosmos-adatbázis az ideális megoldás a webes, mobil-, játék- és IoT-alkalmazásokhoz, amikor a kiszámítható átviteli sebességet, magas rendelkezésre állású, alacsony késést és a sémamentesadat-modell kapcsolatos követelményeket. Séma rugalmasságát és a gazdag indexelési biztosítja, és több dokumentumos tranzakciótámogatást JavaScript-integrációval ellátott tartalmazza. 

A további adatbázisra vonatkozó kérdések, válaszok és központi telepítésével és a szolgáltatás használatával kapcsolatos útmutatásért lásd az [Azure Cosmos DB-dokumentáció oldalán] ((https://docs.microsoft.com/azure/cosmos-db/).

### <a name="what-happened-to-documentdb"></a>Mi történt a DocumentDB?
A DocumentDB API egyike a támogatott API-k és az Azure Cosmos DB adatmodellekben. Emellett Azure Cosmos DB támogatja, a Graph API-val (előzetes verzió), a tábla API és a MongoDB API. További információkért lásd: [kérdések a DocumentDB-ügyfelek](#moving-to-cosmos-db).

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
Ha segítségre van szüksége, kapcsolatba velünk a [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-cosmosdb) vagy a [MSDN fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureDocumentDB), vagy az Azure Cosmos DB mérnöki csapathoz beszélgetne csevegni ütemezése üzenet küldésével [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com). 

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
Azure Cosmos DB érhető el az Azure portálon. Először regisztráljon az Azure-előfizetésre. Most jelentkezett, miután az Azure-előfizetéshez a DocumentDB API, a Graph API-val (előzetes verzió), a tábla API vagy a MongoDB API-fiók adhat.

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

## <a name="develop-with-the-table-api"></a>A tábla API fejlesztése

### <a name="how-can-i-use-the-table-api-offering"></a>Hogyan használható a tábla API ajánlat? 
Az Azure Cosmos DB tábla API érhető el a [Azure-portálon][azure-portal]. Először regisztrálnia kell az Azure-előfizetéssel. Most jelentkezett, miután egy Azure Cosmos DB tábla API-fiók hozzáadása az Azure-előfizetéshez, és adja táblák a fiókját. 

A támogatott nyelveket és a kapcsolódó quick elindul a megtalálhatja a [Bevezetés az Azure Cosmos DB tábla API](table-introduction.md).

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>Kell egy új SDK szolgáltatáshoz, hogy a tábla API-val? 
Nem, a meglévő tárolási SDK-k továbbra is működnek. Azt ajánljuk, hogy egy mindig lekéri a legfrissebb SDK-k számára a legjobb támogatási és sok esetben kiváló teljesítményt. A választható nyelvek listáját a [Bevezetés az Azure Cosmos DB tábla API](table-introduction.md).

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Ahol nincs tábla API azonos Azure Table storage viselkedő?
Bizonyos különbségek vannak a viselkedést, amely az Azure Table storage érkező táblák létrehozása Azure Cosmos DB tábla API-val kívánó felhasználóknak tisztában kell lennie:

* Az Azure Cosmos DB tábla API garantált teljesítmény biztosítása érdekében lefoglalt kapacitás modellt használ, de ez azt jelenti, hogy egy fizet a kapacitás, amint az a táblázat jön létre, akkor is, ha a kapacitás nincs használatban. Az Azure Table storage egy csak fizet ténylegesen használt kapacitás. Ez segítséget nyújt annak magyarázata, hogy miért tábla API kínálhat 10 ms olvassa el és 15 ms írható SLA-t, a 99th PERCENTILIS, amíg a Azure Table storage egy 10 második SLA-t kínál. De következtében tábla API táblákkal, anélkül, hogy minden kérést, a költség pénz annak érdekében, a kapacitás érhető el a tanúsítványigénylések bármely számukra az SLA-t, még akkor is üres táblák által kínált Azure Cosmos DB.
* A tábla API által visszaadott lekérdezés eredményei nem sorrendje partíció kulcs/sor fő szerint Azure Table storage-ban.
* Sor kulcsok csak lehet legfeljebb 255 bájt
* Kötegekben csak tartalmazhat legfeljebb 2 MB
* A rögzített és a többi tábla művelettől RUs szereplő külön felügyeleti késleltetési által szabályozott CreateIfNotExists hívások. Ez azt jelenti, hogy azokat, így CreateIfNotExists nagy mennyiségű beolvasása szabályozva, és nem fogja tudni megtennie, semmit, mert a korlát nem származik a RUs.
* A CORS jelenleg nem támogatott
* A táblanevek, az Azure Table storage-és nagybetűk nem, de a Azure Cosmos DB tábla API
* Alább Azure Cosmos DB belső formátumok kódolási információkat, például bináris mezők jelenleg nem annyira hatékony, például előfordulhat, hogy egy. Ezért ennek hatására váratlan korlátozások adatok mérete. Például jelenleg egyik nem használható a teljes 1 Meg egy tábla entitás bináris adatok tárolására, mert a kódolás növeli az adatok méretét.

A REST API tekintetében számos végpontok/lekérdezési lehetőségek Azure Cosmos DB tábla API által nem támogatott:
| REST-metódussal | REST-végpont/lekérdezési lehetőség | DOC URL-címek | Magyarázat |
| ------------| ------------- | ---------- | ----------- |
| SZÜKSÉGE VAN, AMELYRE AZ | /? restype =service@comp= tulajdonságai| [Table szolgáltatás tulajdonságainak beállítása](https://docs.microsoft.com/rest/api/storageservices/set-table-service-properties) és [Table szolgáltatás tulajdonságainak beolvasása](https://docs.microsoft.com/rest/api/storageservices/get-table-service-properties) | Ezt a végpontot a naplózási beállítások, a CORS szabályok és a tárolási konfigurációja beállítására használatos. A CORS jelenleg nem támogatott, és elemzés és a naplózás az Azure Cosmos Adatbázisba az Azure Storage-táblákat mint másképpen kezeli |
| BEÁLLÍTÁSOK | / <-erőforrás-táblanév > | [Előzetes repülési CORS tábla kérelem](https://docs.microsoft.com/rest/api/storageservices/preflight-table-request) | A CORS, amely Azure Cosmos DB jelenleg nem támogatja a része. |
| GET | /? restype =service@comp= statisztikák | [Table szolgáltatás statisztikák beolvasása](https://docs.microsoft.com/rest/api/storageservices/get-table-service-stats) | Milyen gyorsan adat replikálódik elsődleges és másodlagos adatbázisok közötti információkat nyújt. Ez nem szükséges Cosmos DB, a replikáció írások részét képezi. |
| SZÜKSÉGE VAN, AMELYRE AZ | /mytable? comp = acl | [ACL-tábla](https://docs.microsoft.com/rest/api/storageservices/get-table-acl) és [tábla hozzáférés-vezérlési lista beállítása](https://docs.microsoft.com/rest/api/storageservices/set-table-acl) | Ez lekérdezi és beállítja a tárolt hozzáférési házirendek kezelése megosztott hozzáférési aláírásokkal (SAS) használt. SAS támogatják, de ezeket beállítása és másképp kezeli. |

Azure Cosmos DB tábla API csak támogatja továbbá a JSON formátum nem ATOM.

Míg Azure Cosmos DB közös hozzáférésű aláírás (SAS) bizonyos szabályzatokat nem támogatja, kifejezetten a kapcsolódó felügyeleti műveletek, például a jogot, hogy hozzon létre új táblákat támogatja.

A .NET SDK-ban, van néhány osztályok és az Azure Cosmos DB jelenleg nem támogatja metódusok.

| Osztály | A metódus nem támogatott |
|-------|-------- |
| CloudTableClient | \*ServiceProperties * |
|                  | \*ServiceStats * |
| CloudTable | SetPermissions * |
|            | GetPermissions * |
| TableServiceContext | * (Ez az osztály ténylegesen elavult) |
| TableServiceEntity | " " |
| TableServiceExtensions | " " |
| TableServiceQuery | " " |

Ha ezek a különbségek bármelyike a projekthez probléma forduljon [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com) és ossza meg velünk.

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Hogyan nyújtanak az SDK vagy hibák visszajelzést?
Visszajelzése megoszthatja a következő módokon:

* [Uservoice](https://feedback.azure.com/forums/599062-azure-cosmos-db-table-api)
* [MSDN-fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureDocumentDB)
* [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-cosmosdb)

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Mi az a kapcsolati karakterláncot, amely a tábla API-val való csatlakozáskor használandó kell?
A kapcsolati karakterlánc::
```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountNameFromDocumentDB>.table.cosmosdb.azure.com
```
A kapcsolati karakterlánc lekérheti a csatlakozási karakterlánc oldal az Azure portálon. 

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Hogyan bírálja felül a beállításokat a .NET SDK-ban a tábla API-konfigurációs beállításait?
Konfigurációs beállításaival kapcsolatos további információkért lásd: [Azure Cosmos DB képességek](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities). Egyes beállítások a CreateCloudTableClient metódus és egyéb kezeli, az ügyfélalkalmazás appSettings szakaszában app.config keresztül.

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>Vannak-e a módosításokat a meglévő Azure Table storage SDK-k használó ügyfelek számára?
nincs. Nincsenek a meglévő Azure Table storage SDK-k használó meglévő vagy új ügyfelek nem módosult. 

### <a name="how-do-i-view-table-data-that-is-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Hogyan tekinthetem meg a tábla API-val való használatra az Azure Cosmos DB tárolt tábla adatai? 
Az Azure portál segítségével keresse meg az adatokat. A tábla API-kódban vagy szerepel a következő választ eszközök is használható. 

### <a name="which-tools-work-with-the-table-api"></a>Mely eszközök használható a tábla API-t? 
Használhatja a [Azure Tártallózó](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

Eszközök igénybe vehet egy kapcsolati karakterláncot a megadott formátumban rugalmassága korábban képes támogatni az új tábla API-t. Egy tábla eszközök listája a a [Azure Storage ügyféleszközök elől](../storage/common/storage-explorers.md) lap. 

### <a name="do-powershell-or-azure-cli-work-with-the-table-api"></a>PowerShell vagy Azure CLI működnek tábla API-val?
Támogatott [PowerShell](table-powershell.md). Azure parancssori felületi támogatása jelenleg nem áll rendelkezésre.

### <a name="is-the-concurrency-on-operations-controlled"></a>Az a feldolgozási műveletek ellenőrzött?
Igen, egyidejű hozzáférések optimista ETag mechanizmus segítségével valósul meg. 

### <a name="is-the-odata-query-model-supported-for-entities"></a>Az OData-lekérdezési modellel támogatja az entitások? 
Igen, a tábla API támogatja az OData-lekérdezés és a LINQ lekérdezés. 

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>Képes kapcsolódni Azure Table Storage és Azure Cosmos DB tábla API párhuzamos ugyanabban az alkalmazásban? 
Igen, hozzon létre két külön példányának a CloudTableClient, minden egyes merevlemezre mutat-e a saját URI a kapcsolati karakterlánc használatával is elérheti.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>Hogyan át egy meglévő Azure Table storage-alkalmazás ajánlathoz?
[AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) és a [Azure Cosmos DB adatáttelepítési eszköz](import-data.md) biztosan is támogatott.

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Hogyan van elvégezni ezt a szolgáltatást, ha például indításakor a tároló méretének bővítése  *n*  GB adatok és az adatok és 1 TB tájékoztatás idővel bővülni fog? 
Azure Cosmos DB használatával a horizontális skálázás korlátlan tárolást szolgál. A szolgáltatás figyelése, és hatékonyan növelése tárhelyét. 

### <a name="how-do-i-monitor-the-table-api-offering"></a>Hogyan figyelhetek az tábla API elérhető?
A tábla API-hoz **metrikák** ablaktábla kérelmek és tárhely-használat figyelését. 

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Hogyan kiszámítja a az átviteli sebesség I igényelnek?
A kapacitás négyzetgyökének használhatja a TableThroughput, amely a műveletekhez szükséges kiszámításához. További információkért lásd: [becsült kérelem egységek és az adatok tárolási](https://www.documentdb.com/capacityplanner). Általában a JSON-ként entitást képviselik, és adja meg a számokat az operatív. 

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>Használható a tábla API SDK helyileg emulátorral?
Jelenleg nem.

### <a name="can-my-existing-application-work-with-the-table-api"></a>A meglévő alkalmazás dolgozhat a tábla API-t? 
Igen, az azonos API támogatja.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-do-not-want-to-use-the-table-api-features"></a>Nem szeretném használni a tábla API-funkciókat telepítse át a meglévő Azure Table storage alkalmazásaimat az SDK kell?
Nem hozhat létre és használja a meglévő Azure Table storage eszközök bármilyen megszakítás nélkül. Azonban ha nem használja a tábla API-t, nem kihasználhatja a az automatikus index, a további konzisztencia beállítás vagy a globális terjesztési. 

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-multiple-regions-of-azure"></a>Hogyan vehető fel az adatok replikálása a tábla API Azure különféle régiókban?
Használhatja az Azure Cosmos DB portal [globális replikációs beállítások](tutorial-global-distribution-documentdb.md#portal) hozzáadása az alkalmazáshoz megfelelő régiók. Globálisan elosztott alkalmazások fejlesztéséhez, akkor is fel kell az alkalmazás beállítása a helyi régió biztosítani azt az olvasási kis késleltetésű PreferredLocation adatokkal. 

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Hogyan változtathatom meg a tábla API a fiók elsődleges írási régióját?
Az Azure Cosmos DB globális replikációs portál ablaktábla segítségével adja hozzá egy régiót, és ezután átveheti a szükséges terület. Útmutatásért lásd: [fejlesztés az Azure Cosmos DB fiókok több területi](regional-failover.md). 

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Konfigurálása a saját elsődleges olvasási régiók az alacsony késleltetés amikor I terjesztése adataimat? 
Annak érdekében, olvassa el a helyi helyről a PreferredLocation billentyűvel az app.config fájlban. Meglévő alkalmazások esetében a tábla API hibát jelez, ha LocationMode be van állítva. Távolítsa el ezt a kódot, mert a tábla API szerzi be ezt az információt az app.config fájlból. További információkért lásd: [Azure Cosmos DB képességek](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities).

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>Hogyan kell a tábla API konzisztenciaszintek fontolnunk? 
Azure Cosmos DB biztosít jól indokolt kompromisszumot konzisztencia, a rendelkezésre állás és a késleltetés között. Azure Cosmos DB öt konzisztenciaszintek felajánlja, hogy a tábla API-fejlesztők számára, hogy válassza ki a megfelelő konzisztencia modelljét tábla szintű egyedi kéréseket az adatok lekérdezése közben. Amikor egy ügyfél csatlakozik, a konzisztenciaszint határozhatja meg. A szint CreateCloudTableClient consistencyLevel argumentuma keresztül módosítható. 

A tábla API biztosít a kis késleltetésű olvassa be a "olvasása" saját írások Bounded-elavulási konzisztencia alapértelmezés szerint. További információkért lásd: [konzisztenciaszintek](consistency-levels.md). 

Alapértelmezés szerint a Azure Table storage régión belül az erős konzisztencia és a másodlagos helyeken Eventual konzisztencia biztosítja. 

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>Azure Cosmos DB tábla API nyújtja a Azure Table storage-nál több konzisztenciaszintek?
Igen, Azure Cosmos DB elosztott jellege kihasználják kapcsolatos információkért lásd: [konzisztenciaszintek](consistency-levels.md). Garantálja a konzisztenciaszintek-okat, mert az vetett bizalmat használhatja őket. További információkért lásd: [Azure Cosmos DB képességek](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities).

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Ha engedélyezve van a globális terjesztési, mennyi időt vesz igénybe az adatot replikálni?
Azure Cosmos-adatbázis véglegesíti az adatokat a helyi régió tartósan, és azonnal ezredmásodperc be más régiókban leküldi az adatok. A replikáció csak az üzenetváltási időt (RTT) az Adatközpont függ. A globális terjesztési funkció Azure Cosmos DB kapcsolatos további információkért lásd: [Azure Cosmos DB: Azure globálisan elosztott adatbázis szolgáltatás](distribute-data-globally.md).

### <a name="can-the-read-request-consistency-level-be-changed"></a>Módosíthatja az olvasási kérelem konzisztenciaszint?
Az Azure Cosmos DB amelyen beállíthatja a konzisztenciaszint a tároló szintjén (a táblázat). A .NET SDK használatával módosíthatja a szint TableConsistencyLevel kulcs az app.config fájlban az érték megadásával. A lehetséges értékek: erős, kötött elavulási, munkamenet, egységes előtagot, valamint Eventual. További információkért lásd: [konzisztenciaszintek hangolható adatokat az Adatbázisba az Azure Cosmos](consistency-levels.md). A kulcs lényege, hogy nem állítható be a kérelem konzisztencia szint: több, mint a beállítás a következő táblázatban. A következő táblázatban a konzisztenciaszint például Eventual és az erős konzisztencia szint a kérelem nem állítható. 

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>Hogyan nem a tábla API kezelnek feladatátvételi Ha leáll egy régiót? 
A tábla API kihasználja a globálisan elosztott platform Azure Cosmos-adatbázis. Győződjön meg arról, hogy az alkalmazás működését datacenter állásidő, engedélyezze a fiókot az Azure Cosmos DB portálon legalább egy további régió [fejlesztés az Azure Cosmos DB fiókok több területi](regional-failover.md). A portál használatával beállíthatja a prioritás a régió [fejlesztés az Azure Cosmos DB fiókok több területi](regional-failover.md). 

A fiók, és szabályozhatja, ahol azt átveheti a feladatátvételi prioritási megadásával annyi régiókban is hozzáadhat. Természetesen az adatbázis használatához meg kell adnia egy alkalmazás túl. Ha így tesz, az ügyfelek nem fog tapasztalni állásidő. A [legújabb .NET ügyfél SDK](table-sdk-dotnet.md) automatikus homing van, de egyéb SDK-k nem. Ez azt jelenti, hogy a régiót, amelyben nem működik, és automatikusan áthelyezze a feladatokat az új régióban azonosítására képes.

### <a name="is-the-table-api-enabled-for-backups"></a>A tábla API engedélyezve van a biztonsági mentésekhez?
Igen, a tábla API-t használja a platform Azure Cosmos-adatbázis a biztonsági mentésekhez. Biztonsági mentések a automatikusan történik. További információkért lásd: [Online biztonsági mentés és helyreállítás Azure Cosmos DB](online-backup-and-restore.md).

 
### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>A tábla API index entitás összes attribútumának alapértelmezés szerint?
Igen, az entitás összes attribútum indexelt alapértelmezés szerint. További információkért lásd: [Azure Cosmos DB: Indexelő házirendek](indexing-policies.md). 

### <a name="does-this-mean-i-do-not-have-to-create-multiple-indexes-to-satisfy-the-queries"></a>A középérték nem kell teljesíteni a lekérdezések több indexek létrehozása does? 
Igen, Azure Cosmos DB tábla API biztosít az összes attribútum nélkül bármely sémadefiníciót automatikus indexeléshez. Ezt az automatizálást szabadít fel a fejlesztők számára, hogy az alkalmazás és nem index létrehozását és kezelését a fókuszt. További információkért lásd: [Azure Cosmos DB: Indexelő házirendek](indexing-policies.md).

### <a name="can-i-change-the-indexing-policy"></a>Az indexelési házirendet is megváltozik?
Igen, módosíthatja az indexelési házirendet azáltal, hogy az index definícióját. További információkért lásd: [Azure Cosmos DB képességek](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities). Megfelelően kódolja, és a beállítások escape kell. 

az a nem - .NET SDK-k az indexelési házirendet csak állítható be a portálon, a **adatkezelő**, keresse meg az adott tábla módosítása, és folytassa a a **skála & beállítások**-> indexelési házirend a kívánt módosítást, majd **mentése**.

A .NET SDK-ból elküldi az app.config fájlban:
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
A tábla API Azure Table storage azonos lekérdezés funkciókat biztosít. Azure Cosmos-adatbázis is támogatja a rendezést, összesítések, a földrajzi lekérdezést, a hierarchia és a számos különféle beépített funkciók. A tábla API jövőbeli szolgáltatásfrissítés további funkciók lesz elérhető. További információkért lásd: [Azure Cosmos DB DocumentDB API SQL-lekérdezések](../documentdb/documentdb-sql-query.md).
 
### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>Mikor kell módosítani a táblázat API TableThroughput?
TableThroughput kell módosítani, ha érvényes a következő feltételek valamelyike:
* Helyreállítást hajt végre egy kinyerési, átalakítási és betöltési (ETL) az adatok, vagy nagy mennyiségű adat rövid időn belül feltölteni kívánt. 
* További átviteli, a háttér-tárolójából van szüksége. Például láthatja, hogy a használt átviteli sebesség több, mint a létesített átviteli sebesség, és hogy szabályozott első. További információkért lásd: [Set átviteli az Azure Cosmos DB tárolókat](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>Növelheti vagy csökkentheti a teljesítményt a tábla API tábla? 
Igen, az Azure Cosmos DB portal méretezési ablaktábla segítségével méretezhető, az átviteli sebesség. További információkért lásd: [Set átviteli](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Az egy alapértelmezésben TableThroughput újonnan kiosztott táblák?
Igen, ha nem bírálja felül a TableThroughput app.config keresztül, és nem használható egy előre létrehozott tároló Azure Cosmos DB, a szolgáltatás létrehoz egy táblát 400 átviteli sebességgel.
 
### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Van-e bármilyen módosítás a meglévő ügyfeleknek az Azure Table storage szolgáltatás díjszabása?
nincs. Nincs meglévő Azure Table storage-ügyfeleknek az ár nem változik. 

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Hogyan kiszámítása a tábla API-hoz. az ár? 
Az ár attól függ, hogy a lefoglalt TableThroughput. 

### <a name="how-do-i-handle-any-throttling-on-the-tables-in-table-api-offering"></a>Hogyan kezelik, a sávszélesség-szabályozás a táblák tábla API ajánlatban? 
Ha a kérelmek aránya meghaladja a létesített átviteli sebesség a mögöttes tároló kapacitását, hibaüzenetet kap, és az SDK újrapróbálkozik a hívás úgy, hogy az újrapróbálkozási házirendet alkalmazza.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Miért kell választania egy átviteli sebesség előnyeit az Azure Cosmos DB tábla API elérhető PartitionKey és RowKey?
Azure Cosmos-adatbázis a egy alapértelmezett átviteli sebesség a tároló beállítja, ha nem ad meg az app.config fájlban, illetve a portálon. 

Azure Cosmos DB garanciákat nyújt a teljesítmény és a késés, a felső határai, a műveletet. A garantált a motor kényszerítheti a cégirányítási műveletek a bérlő esetén lehetséges. TableThroughput beállítása biztosítja, hogy kap a garantált átviteli sebesség és a késleltetés, mivel a platform fenntartja a kapacitás, és biztosítja, hogy az operatív sikeres. 

Az átviteli sebesség meghatározása segítségével rugalmasan módosíthatja úgy, hogy igénybe vehesse a szezonalitás értékének az alkalmazás az átviteli sebesség igényeinek és költségek csökkentése.

### <a name="azure-table-storage-has-been-very-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-have-not-performed-a-single-transaction-or-stored-anything-can-you-please-explain"></a>Az Azure Table storage a számomra, nagyon alacsony költségű volt, mert az csak a tárolja az adatokat, és szeretnék ritkán lekérdezés kell fizetni. Az Azure Cosmos DB tábla API elérhető úgy tűnik, hogy lehet díjszabási me annak ellenére, hogy I nem egyetlen tranzakció végre vagy bármi tárolja. Adjon melyek?

Azure Cosmos-adatbázis egy rendelkezésre állási, a késés és átviteli garanciák globálisan elosztott, SLA-alapú rendszer tervezték. Amikor lefoglalni Azure Cosmos DB átviteli sebességet, válik biztossá, szemben a más rendszerekkel átviteli sebességgel. Azure Cosmos-adatbázis, amely az ügyfelek kért, például a másodlagos indexek és globális terjesztési további képességeket biztosít.  

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Soha nem jelenik meg a "teljes" kvótaértesítéshez (arról, hogy a partíció teljes) Ha I betöltik az adatokat az Azure Table storage. A tábla API-t ez az üzenet jelenik meg. Ez kínál me korlátozása van, és módosíthatja a meglévő alkalmazás me?

Azure Cosmos-adatbázis egy SLA-alapú rendszeren, amely korlátlan méretezési biztosít garanciák késés, teljesítményt, rendelkezésre állási és konzisztencia. Garantált prémium szintű teljesítmény biztosítása érdekében ellenőrizze, hogy az adatok mérete és az index kezelhető és méretezhető. A 10 GB-os korlátot az entitások vagy elemek száma a partíciós kulcs annak érdekében, hogy nyújtunk remek keresési és lekérdezéseivel kapcsolatos teljesítményt. Győződjön meg arról, hogy az alkalmazás méretezze át, akkor az Azure Storage, javasoljuk, hogy Ön *nem* hozza létre a gyakran használt adatok partíciót egy partíció összes információ tárolása és lekérdezéssel. 

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>Ezért PartitionKey és RowKey továbbra is szükség a tábla API-t? 
Igen. Mivel a tábla API felületet biztosít az Azure Table storage SDK hasonló, a partíciós kulcs teszi lehetővé az adatok terjesztéséhez. Sor fontos, hogy egyedi. A sorkulcs kell elhelyezkednie, és nem lehet null értékű, mint a szokásos SDK. RowKey hossza 255 bájt és PartitionKey hossza 1 KB. 

### <a name="what-are-the-error-messages-for-the-table-api"></a>Mik a hibaüzenetek a tábla API-hoz?
Az Azure Table storage és Azure Cosmos DB tábla API használ az azonos SDK-k, így a hibák többségét ugyanaz lesz.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Miért tegye I get halmozódni létrehozásához táblák számos egymás után a tábla API meg?
Azure Cosmos-adatbázis az SLA-alapú rendszerek késés, teljesítményt, rendelkezésre állási és konzisztencia biztosítja biztosító. Mivel a kiépített rendszert, ezek a követelmények biztosításához erőforrások foglalja le. Táblázatok létrehozása a gyors aránya észlelt és szabályozva. Azt javasoljuk, hogy tekintse meg a táblák létrehozását aránya, és csökkentése a kevesebb mint 5 percenként. Ne feledje, hogy a tábla API kiépített rendszer. A kiépítése, hamarosan kész kezdve látni fogja azt kell fizetnie. 

## <a name="develop-against-the-graph-api-preview"></a>A Graph API (előzetes verzió) ellen fejlesztése
### <a name="how-can-i-apply-the-functionality-of-graph-api-preview-to-azure-cosmos-db"></a>Hogyan alkalmazhatók az Graph API-val (előzetes verzió) funkcióit Azure Cosmos DB?
Egy bővítmény könyvtár segítségével alkalmazhat a Graph API-val (előzetes verzió) funkcióit. Ezt a szalagtárat a Microsoft Azure diagramjait nevezik, és a NuGet érhető el. 

### <a name="it-looks-like-you-support-the-gremlin-graph-traversal-language-do-you-plan-to-add-more-forms-of-query"></a>Úgy tűnik, a Gremlin graph átjárás nyelv támogatott. Biztosítani kívánja a lekérdezés további formáit hozzáadása?
Igen, tervezzük adja hozzá a jövőben más mechanizmusok lekérdezéshez. 

### <a name="how-can-i-use-the-new-graph-api-preview-offering"></a>Hogyan használhatók a Graph API-val (előzetes verzió) az új ajánlat? 
Először végezze el a [Graph API](../cosmos-db/create-graph-dotnet.md) gyors üzembe helyezési cikk.

<a id="cassandra"></a> 
## <a name="develop-with-the-apache-cassandra-api-preview"></a>Az Apache Cassandra API-t (előzetes verzió) fejlesztése

### <a name="what-is-the-protocol-version-supported-in-the-private-preview-is-there-a-plan-to-support-other-protocols"></a>Mi az a magán előnézetben támogatott protokollverziót? A terv más protokollt támogató van?
Apache Cassandra API for Azure Cosmos DB ma CQL 4-es verzióját támogatja. Ha más protokollt támogató visszajelzést, ossza meg velünk keresztül [uservoice-visszajelzések](https://feedback.azure.com/forums/263030-azure-cosmos-db) vagy egy e-mailek küldése [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com). 

### <a name="why-is-choosing-a-throughput-for-a-table-a-requirement"></a>Ezért megválasztása a táblázat átviteli követelmény?
Azure Cosmos-adatbázis alapértelmezett átviteli beállítja a tároló alapján ahol hoz létre a tábla - portál vagy CQL. Azure Cosmos DB garanciákat nyújt a teljesítmény és a késés, a felső határai, a műveletet. A garantált a motor kényszerítheti a cégirányítási műveletek a bérlő esetén lehetséges. Beállítás átviteli biztosítja, hogy kap a garantált átviteli sebesség és a késleltetés, mivel a platform fenntartja a kapacitás, és biztosítja, hogy a művelet sikerült. Átviteli sebesség kihasználhassa a szezonalitás értékének az alkalmazás és a költségek csökkentése rugalmasan módosíthatja.

Az átviteli sebesség koncepció az ismertetése a [Azure Cosmos DB egység kérelem](request-units.md) cikk. Egy tábla átviteli egyenlően a mögöttes fizikai partíciók között.  

### <a name="what-is-the-default-rus-of-table-when-created-through-cql-what-if-i-need-to-change-it"></a>Mi az az alapértelmezett RU/mp tábla létrehozásakor CQL keresztül? Mi történik, ha kell megváltoztatnia?
Azure Cosmos-adatbázis a pénznem biztosítani azt az átviteli kérelem egység / másodperc (RU/mp) használja. Létrehozott CQL keresztül kell 400 RU. Módosíthatja a RU a portálról. 

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

### <a name="what-happens-when-throughput-is-exceeded"></a>Mi történik, amikor meghaladja a átviteli? 
Azure Cosmos DB garanciákat nyújt a teljesítmény és a késés, a felső határai, a műveletet. A garantált a motor kényszerítheti a cégirányítási műveletek a bérlő esetén lehetséges. Ez akkor lehetséges, az átviteli sebességben, ami garantálja, hogy kap a garantált átviteli sebesség és a késleltetés, mert a platform fenntartja a kapacitás, és biztosítja, hogy a művelet sikeres beállítás alapján. Ha ez a kapacitás túllépi elérhetővé túlterhelt hiba jelenik meg, miszerint a kapacitása túllépve. 0x1001 túlterhelt: a kérés nem teljesíthető, mert"lekérdezési gyakorisága nagy". Ebben a pillanatban elengedhetetlen, hogy milyen műveleteket, és a kötet okozza a problémát. A metrikák a portál kiosztott kapacitást meghaladja, a felhasznált kapacitásával kapcsolatos képet kaphat. Ezt követően győződjön meg arról, kapacitás majdnem felhasznált egyaránt összes partíciójára alapul szolgáló. Ha látja az átviteli sebesség a legtöbb fel egy partíciót, hogy eltérésére munkaterhelés. 

Metrikák érhetők el, amely megmutatja, átviteli felhasznált óra, nap, és hét naponta, partíciók vagy Összesítés. További információkért lásd: [figyelés és az Azure Cosmos DB a metrikák hibakeresés](use-metrics.md).

Diagnosztikai naplók magyarázata a [Azure Cosmos DB diagnosztikai naplózás](logging.md) cikk.

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>A partíciós kulcs fogalmának Azure Cosmos-adatbázis elsődleges kulcs térkép használ?
Igen, a partíciós kulcs szolgál az entitás megfelelő helyen helyezze el. Az Azure Cosmos Adatbázisba szolgál egy fizikai partícióján tárolt jobb logikai partíció található. A particionáló koncepció is leírtak a [partíció és a skála Azure Cosmos DB](partition-data.md) cikk. Alapvető fontosságú a lekérési számítógépnél itt található, hogy a logikai partíció nem haladhatja meg a 10 GB-os korlát ma. 

### <a name="what-happens-when-i-get-a-quota-full-notification-indicating-that-a-partition-is-full"></a>Mi történik, amikor a "teljes" kvótaértesítéshez azt jelzi, hogy a partíció megtelt?
Azure Cosmos-adatbázis egy SLA-alapú rendszeren, amely korlátlan méretezési biztosít garanciák késés, teljesítményt, rendelkezésre állási és konzisztencia. Cassandra API túl lehetővé teszi az adatok korlátlan tárterület. A korlátlan tárterület adatok használatával, a kulcs koncepció particionálás vízszintes scaleout alapul. A particionáló koncepció is leírtak a [partíció és a skála Azure Cosmos DB](partition-data.md) cikk.

A 10 GB-os korlátot az entitások vagy a logikai partíciónként elemek számának meg kell felelnie. Győződjön meg arról, hogy az alkalmazás jól méretezi, javasoljuk, hogy Ön *nem* hozza létre a gyakran használt adatok partíciót egy partíció összes információ tárolása és lekérdezéssel. Ez a hiba csak származnak, ha az adatok akkor válik egyenetlenné – Ez az Ön, azaz rendelkezik nagy mennyiségű adat esetében egy partíciókulcs - lehet több mint 10 GB-os. Az adatok tárolási portál használatával terjesztési található. Javítsa ki a hibát úgy recrete a tábla és egy részletes elsődleges (partíciós kulcs), amely lehetővé teszi az adatok jobb terjesztési válassza.

### <a name="is-it-possible-to-use-cassandra-api-as-key-value-store-with-millions-or-billions-of-individual-partition-keys"></a>Az Cassandra API-kulcs-érték tárolóként használható a több millió vagy egyedi partíciókulcsok milliárd?
Az Azure Cosmos DB korlátlan adatait tárolhatja skálázással, hogy a tároló. Ez az az átviteli független. Igen mindig csak segítségével Cassandra API tárolásához és lekéréséhez a kulcs/érték megfelelő elsődleges/partíciós kulcs megadásával. A egyedi kulcsok beolvasása a saját logikai partíciót, és hibák nélkül fizikai partíció elveire elhelyezkedik. 

### <a name="is-it-possible-to-create-multiple-tables-with-apache-cassandra-api-of-azure-cosmos-db"></a>Az lehet több táblát is Apache Cassandra API az Azure Cosmos DB készíteni?
Igen, akkor lehet Kréta több táblát is Apache Cassandra API-t. Azokat a táblákat a rendszer az átviteli sebesség és a tárolási egység. 

### <a name="is-it-possible-to-create-multiple-tables-in-succession"></a>Az egymás után több tábla létrehozására?
Azure Cosmos-adatbázis egy erőforrás szabályozott rendszer, az adatok és a vezérlő vezérlősík tevékenységeit. Tárolók, például gyűjtemények, a táblák olyan futásidejű entitások, amelyek a megadott átviteli sebesség kiépített. Ezek a tárolók gyors egymásutánban várt tevékenység nincs és szabályozva. Ha vizsgálatokat, amelyeket az vetett/táblák létrehozása azonnal - újra a lemezterület a.

### <a name="what-is-maximum-number-of-tables-which-can-be-created"></a>Mi az, amely hozhatók létre táblák maximális száma?
A táblák száma fizikai korlátozva van, kérjük, küldjön egy e-mailt [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) Ha nagyon nagy számú (ahol a teljes állandó mérete meghaladja a 10 TB-nyi adat)-táblázatot, amely a szokásos 10 egység vagy 100-as egység kell létrehozni. 

### <a name="what-is-the-maximum--of-keyspace-which-we-can-create"></a>Mi az a maximális száma kulcstérértesítések használatával, amely hozhatunk létre? 
Nincs keyspaces számú fizikai korlát, mivel ezek metaadatok tárolók, küldjön egy e-mailt [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) Ha nagyon nagy számú keyspaces valamilyen okból. 

### <a name="is-it-possible-to-bring-in-lot-of-data-after-starting-from-normal-table"></a>Az nagy mennyiségű adat kapcsolja a normál táblából elindítása után is? 
A tárolási kapacitás automatikusan kezeli, és a további adatok leküldéses növekszik. Így magabiztosan importálhat adatmennyiség van lehetősége nélkül kezelése és a kiépítés csomópontok stb. 

### <a name="is-it-possible-to-supply-yaml-file-settings-to-configure-apache-casssandra-api-of-azure-cosmos-db-behavior"></a>Van lehetőség a yam fájl beállításainak konfigurálása Apache Casssandra API az Azure Cosmos DB viselkedés úgy?
Apache Cassandra API az Azure Cosmos DB egy olyan platform szolgáltatás. Protokoll szintű használható biztosít műveletek végrehajtása. Elrejti számítógépnél felügyeletének, megfigyelésének és konfigurációs összetettsége. Fejlesztői/felhasználónak nem kell foglalkoznia a rendelkezésre állás, törlendő elemek, kulcs gyorsítótárában, sor gyorsítótár, virágzó szűrő és számos egyéb beállításokat. Azure Cosmos DB Apache Cassandra API fókusz szolgáltatásában olvasási és írási teljesítményt igényelnek nem növeli a konfigurálása és kezelése.

### <a name="will-apache-cassandra-api-for-azure-cosmos-db-support-node-additioncluster-statusnode-status-commands"></a>Apache Cassandra API-t Azure Cosmos DB a csomópont hozzáadása/fürt állapot/csomópont állapota parancsok támogatja?
Apache Cassandra API egy olyan platform szolgáltatás, így a kapacitástervezés válaszol az átviteli sebesség és a tárolási rugalmasság iránti igények kielégítése érdekében egy kezelheti. Azure Cosmos DB az átviteli sebesség kell kiépíteni. Majd méretezheti azt fel és le a tetszőleges számú alkalommal napon keresztül anélkül, hogy csomópontok hozzáadása és törlése vagy a kezeli őket. Ez azt jelenti, hogy nem szeretné használni, a csomópont, a fürt kezelőeszközével túl. 

### <a name="what-happens-with-respect-to-various-config-settings-for-keyspace-creation-like-simplenetwork"></a>Mi történik, különböző konfigurációs beállítások kulcstérértesítések használatával létrehozásához például az egyszerű és a hálózati kapcsolatban?
Azure Cosmos-adatbázis rendelkezésre állást és a kis késleltetésű okok miatt nem globális terjesztési biztosít. Nem kell a telepítőt replikákra stb. Összes írási műveleteket a rendszer mindig tartósan kvórum véglegesítve lett a bármely régióban, ahol írása a ugyanakkor biztosítható a teljesítményt garantálja.  

### <a name="what-happens-with-respect-to-various-settings-for-table-metadata-like-bloom-filter-caching-read-repair-change-gcgrace-compression-memtableflushperiod-etc"></a>Olvassa el tekintetében számos beállítása virágzó szűrő, például a tábla metaadatainak gyorsítótárazása, mi történik, javítási változások, a gc_grace, a tömörítési memtable_flush_period stb?
Az Azure Cosmos DB olvasása/írása és bármelyik konfigurációs beállításait, és akár véletlenül művelet érintése nélkül átviteli teljesítményt biztosít.  

### <a name="is-time-to-live-ttl-supported-for-cassandra-tables"></a>Támogatott idő-Élettartam (TTL) Cassandra táblák? 
Igen, támogatja a TTL-t. 

### <a name="is-it-possible-to-monitor-node-status-replica-status-gc-and-os-parameters-earlier-with-various-tools-what-needs-to-be-monitored-now"></a>Az lehet a figyelő csomópont állapota, másodpéldány állapotát, a globális katalógus és paramétereket az operációs rendszer korábbi különböző eszközöket? Mit kell figyelni most?
Azure Cosmos-adatbázis olyan platform szolgáltatás, amely segít a hatékonyság növelésére, és nem kell foglalkoznia kezelése és infrastruktúra figyelése. Csupán be kell irányuló átviteli elérhető portál metrikák található, ha első szabályozott és növeli vagy csökkenti a teljesítményt. A figyelő [SLA-k](monitor-accounts.md).
Használjon [metrikák](use-metrics.md) használata [diagnosztikai naplók](logging.md).

### <a name="which-client-sdks-can-work-with-apache-cassandra-api-of-azure-cosmos-db"></a>Melyik ügyfél SDK-k együttműködhet Apache Cassandra API az Azure Cosmos DB?
A private Preview verziójára Apache Cassandra SDK-ügyfél illesztőprogramokat, amelyek alapján a CQLv3 ügyfélprogramok használták. Ha más illesztőprogramokat használja, vagy hogy használata során szembesülnek problémákat, ha üzenet küldése a [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com). 

### <a name="is-composite-primary-key-supported"></a>Támogatja a rendszer összetett elsődleges kulcsot?
Igen, rendszeres szintaxis segítségével összetett partíciós kulcs létrehozása. 

### <a name="can-i-use-sstable-loader-for-data-loading"></a>Használható az adatok betöltése sstable betöltő?
Nem, előzetes sstable betöltője nem támogatott. 

### <a name="can-an-on-premises-cassandra-cluster-be-paired-with-azure-cosmos-dbs-apache-cassandra-api"></a>Egy helyszíni cassandra fürt párosítható is Azure Cosmos DB Apache Cassandra API-t?
Jelen Azure Cosmos DB rendelkezik egy felhőalapú környezetben többletterhelés nélkül műveletek optimalizált élményét. Ha párosítás van szüksége, küldjön egy levelet a [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) adott esetben leíró.

### <a name="does-cassandra-api-provide-full-backups"></a>Adja meg a teljes biztonsági mentést Cassandra API? 
Azure Cosmos DB biztosít két szabad teljes biztonsági másolatokat négyórás időközönként ma minden API-k között. Ez biztosítja, nem kell beállítani a biztonsági mentési ütemezést stb. Ha azt szeretné, megőrzés és gyakoriságának módosítása, küldjön egy e-mailek [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) vagy támogatási esetet ablakába. Biztonsági mentési lehetőséget kapcsolatos információkat a [automatikus online biztonsági mentés és helyreállítás Azure Cosmos DB](online-backup-and-restore.md) cikk. 

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Hogyan nem a Cassandra API fiókot kezelik feladatátvételi Ha leáll egy régiót? 
Az Azure Cosmos DB Cassandra API Azure Cosmos-adatbázis a globálisan elosztott platformról alapszik. Győződjön meg arról, hogy az alkalmazás működését datacenter állásidő, engedélyezze a fiókot az Azure Cosmos DB portálon legalább egy további régió [fejlesztés az Azure Cosmos DB fiókok több területi](regional-failover.md). A portál használatával beállíthatja a prioritás a régió [fejlesztés az Azure Cosmos DB fiókok több területi](regional-failover.md). 

A fiók, és szabályozhatja, ahol azt átveheti a feladatátvételi prioritási megadásával annyi régiókban is hozzáadhat. Az adatbázis használatához adja meg az alkalmazás túl kell. Ha így tesz, az ügyfelek nem fog tapasztalni állásidő. 

### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Az Apache Cassandra API index entitás összes attribútumának alapértelmezés szerint?
Igen, az entitás összes attribútum indexelt Azure Cosmos DB alapértelmezés szerint. További információkért lásd: [Azure Cosmos DB: Indexelő házirendek](indexing-policies.md). Garantált teljesítmény konzisztens indexelő előnyei kap, és tartós kvórum véglegesített mindig írja. 

### <a name="does-this-mean-i-do-not-have-to-create-multiple-indexes-to-satisfy-the-queries"></a>A középérték nem kell teljesíteni a lekérdezések több indexek létrehozása does? 
Igen, Azure Cosmos DB biztosít az összes attribútum nélkül bármely sémadefiníciót automatikus indexeléshez. Ezt az automatizálást szabadít fel a fejlesztők számára, hogy az alkalmazás és nem index létrehozását és kezelését a fókuszt. További információkért lásd: [Azure Cosmos DB: Indexelő házirendek](indexing-policies.md).

### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Használható az új Cassandra API SDK helyileg emulátorral?
Ezen képesség támogatásához a jövőben tervezzük. 

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-changefeed-and-other-functionality-will-these-capabilities-be-added-to-the-cassandra-api"></a>Az Azure Cosmos DB platformként úgy tűnik, hogy nagy mennyiségű képességeit, például changefeed és egyéb funkciókat. Ezeket a képességeket nem kerülnek be a Cassandra API-t? 
Az Apache Cassandra API Apache Cassandra azonos CQL funkciókat biztosít. Tervezzük megismerhetők megvalósíthatósági különböző képességek a jövőbeli támogatására.

### <a name="feature-x-of-regular-cassandra-api-is-not-working-as-today-where-can-the-feedback-be-provided"></a>Szolgáltatás x rendszeres Cassandra API nem működik, ma, ahol a visszajelzés megadható?
Visszajelzés küldése keresztül [uservoice-visszajelzések](https://feedback.azure.com/forums/263030-azure-cosmos-db).

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

Nem kell minden módosításokat. A DocumentDB-erőforrások Azure Cosmos DB erőforrásokat, és hiba történt a szolgáltatás megszakítás nélküli fenntartása, az áthelyezés előfordulásakor.

### <a name="what-changes-do-i-need-to-make-for-my-app-to-work-with-azure-cosmos-db"></a>Milyen módosításokat kell az Azure Cosmos DB együttműködni my alkalmazás legyen?

Nincsenek módosítások végrehajtásához. Osztályok, a névterek és a NuGet csomag neve nem módosultak. Ennek mindig azt javasoljuk, hogy Ön az SDK-k naprakészen tartása a legújabb szolgáltatásait és fejlesztéseit előnyeit. 

### <a name="whats-changed-in-the-azure-portal"></a>Változások az Azure-portálon?

A DocumentDB már nem jelenik meg a portálon az Azure-szolgáltatások. Helyette van egy új Azure Cosmos DB ikonra, a következő ábrán látható módon. A gyűjtemények érhetők el, előtt voltak, és továbbra is méretezheti átviteli, a módosítás konzisztenciaszintek és a figyelő SLA-k. Az adatok kezelővel (előzetes verzió) képességeit tovább lett fejlesztve. Mostantól megtekintheti és szerkeszthetik a dokumentumokat, hozzon létre és lekérdezéseket futtathat, és dolgozhassanak tárolt eljárások, eseményindítók és UDF egy oldal, a következő ábrán látható módon: 

![Az Azure Cosmos DB gyűjtemények lap](./media/faq/cosmos-db-data-explorer.png)

### <a name="are-there-changes-to-pricing"></a>Vannak-e árképzési módosításait?

Nem, az alkalmazás futtatásának az Azure-Cosmos adatbázis megegyezik a előtti.

### <a name="are-there-changes-to-the-slas"></a>Vannak-e a szolgáltatásiszint-szerződések módosításait?

Nem, a rendelkezésre állási, a konzisztencia, a késés és a átviteli SLA nem változik, és továbbra is megjelennek a portálon. További információkért lásd: [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/).
   
![Tennivalók app mintaadatokkal](./media/faq/azure-cosmosdb-portal-metrics-slas.png)


[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md
