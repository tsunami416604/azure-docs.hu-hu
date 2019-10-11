---
title: Gyakori kérdések az Azure Cosmos DB különböző API-jaival kapcsolatban
description: Választ kaphat a Azure Cosmos DBával, egy globálisan elosztott, többmodelles adatbázis-szolgáltatással kapcsolatos gyakori kérdésekre. A kapacitás, a teljesítményszint és a skálázás megismerése.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 15e4fc3ce412807908c75fe25acecac0fe86d261
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72262802"
---
# <a name="frequently-asked-questions-about-different-apis-in-azure-cosmos-db"></a>Gyakori kérdések az Azure Cosmos DB különböző API-jaival kapcsolatban

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Melyek a Azure Cosmos DB tipikus használati esetei?

A Azure Cosmos DB jó választás az új webes, mobil-, játék-és IoT-alkalmazások számára, amelyekben az automatikus méretezés, a kiszámítható teljesítmény, az ezredmásodperces válaszidő gyors sorrendje és a séma nélküli adatmennyiség lekérdezése fontos. Azure Cosmos DB a gyors fejlesztéshez és az alkalmazás-adatmodellek folyamatos ismétlésének támogatásához is alkalmas. A felhasználó által létrehozott tartalmat és adatokat kezelő alkalmazások a [Azure Cosmos db gyakori használati esetei](use-cases.md).

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Hogyan nyújt Azure Cosmos DB kiszámítható teljesítményt?

A [kérési egység](request-units.md) (ru) a Azure Cosmos db teljesítményének mértéke. A 1RU átviteli sebessége megfelel egy 1 KB-os dokumentum lekérésének átviteli sebességének. Azure Cosmos DB összes művelete, beleértve az olvasásokat, az írásokat, az SQL-lekérdezéseket és a tárolt eljárások végrehajtását, a művelet végrehajtásához szükséges átviteli sebesség alapján determinisztikus RU-értékkel rendelkezik. A CPU-t, az IO-t és a memóriát, valamint azt, hogy az egyes alkalmazások hogyan befolyásolják az alkalmazás átviteli sebességét, egyetlen RU-mérték alapján is meggondolhatja magát.

Az egyes Azure Cosmos-tárolók kiosztott átviteli sebességgel konfigurálhatók a másodpercenkénti adatátviteli sebesség alapján. Bármilyen méretű alkalmazás esetében az egyes kérelmeket az RU-értékek mérésére, valamint egy tároló kiépítésére használhatja az összes kérelem összes kérelmi egységének kezeléséhez. Az alkalmazás igényeinek megfelelően méretezheti vagy méretezheti a tároló átviteli sebességét is. Ha további információt szeretne a kérelmekkel kapcsolatos egységekről, és segítségre van szüksége a tároló igényeinek meghatározásához, próbálkozzon az [átviteli sebesség kalkulátorával](https://www.documentdb.com/capacityplanner).

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Hogyan támogatja az Azure Cosmos DB a különböző adatmodelleket, például a kulcs/érték, az oszlopos, a dokumentum és a gráf használatát?

A kulcs/érték (tábla), az oszlopos, a dokumentum és a Graph adatmodellek natív módon támogatottak az ARS (atomok, rekordok és sequences) kialakításával, amely Azure Cosmos DB épül. Az atomok, rekordok és a különböző adatmodellek könnyen leképezhetők és kiállíthatók. A modellekhez tartozó API-k mostantól elérhetők (SQL-, MongoDB-, tábla-és Gremlin), és a további adatmodellekhez kapcsolódó egyebek a jövőben lesznek elérhetők.

Azure Cosmos DB rendelkezik egy séma agnosztikus indexelési motorral, amely képes automatikusan indexelni az összes betöltött adatot anélkül, hogy a fejlesztőtől sémát vagy másodlagos indexet kellene igényelni. A motor a logikai indexek (fordított, oszlopos, fa) készletére támaszkodik, amely leválasztja a tárolási elrendezést az indexből, és lekérdezi az alrendszereket. Cosmos DB emellett bővíthető módon támogatja a vezetékes protokollok és API-k készletét, és hatékonyan lefordíthatja őket az alapszintű adatmodellre (1) és a logikai indexek elrendezéseit (2), így az egyedi módon képes támogatni több adatmodell natív használatát.

### <a name="can-i-use-multiple-apis-to-access-my-data"></a>Használhatok több API-t az adataim eléréséhez?

Azure Cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása. Ahol a többmodelles Azure Cosmos DB több API-t és több adatmodellt is támogat, a különböző API-k különböző adatformátumokat használnak a Storage és a Wire protokoll számára. Például az SQL JSON-t használ, a MongoDB pedig a BSON-t használja, a EDM, Cassandra pedig a CQL, a Gremlin pedig a GraphSON-t használja. Ennek eredményeképpen azt javasoljuk, hogy ugyanazt az API-t használja az adott fiókban lévő adathoz való hozzáféréshez.

Az egyes API-k egymástól függetlenül működnek, kivéve a Gremlin és az SQL API-t, amelyek együttműködnek egymással.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Megfelelő-e Azure Cosmos DB HIPAA?

Igen, Azure Cosmos DB HIPAA-kompatibilis. A HIPAA az egyéni és beazonosítható egészségügyi adatok használatára, nyilvánosságra hozatalára és védelmére vonatkozó követelményeket állapítja meg. További információkért lásd: [Microsoft Adatvédelmi központ](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Mik a Azure Cosmos DB tárolási korlátai?

A tárolók által Azure Cosmos DB tárolt adatmennyiségnek nincs korlátja.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Mik a Azure Cosmos DB átviteli sebességének korlátai?

A tároló által támogatott átviteli sebességek teljes mennyisége nem korlátozható Azure Cosmos DBban. A legfontosabb elképzelés az, hogy a számítási feladatokat egyenletesen, elég nagy számú partíciós kulcs között ossza el.

### <a name="are-direct-and-gateway-connectivity-modes-encrypted"></a>Titkosítva vannak-e a közvetlen és az átjáró csatlakozási módjai?

Igen, mindkét mód mindig teljes mértékben titkosítva van.

### <a name="how-much-does-azure-cosmos-db-cost"></a>Mennyibe kerül a Azure Cosmos DB?

További részletekért tekintse meg a [Azure Cosmos db díjszabását ismertető](https://azure.microsoft.com/pricing/details/cosmos-db/) oldalt. Azure Cosmos DB a használati díjakat a kiosztott tárolók száma, a tárolók online állapotának száma és az egyes tárolók kiépített átviteli sebessége határozza meg.

### <a name="is-a-free-account-available"></a>Elérhető-e ingyenes fiók?

Igen, az időkorlátot díjmentesen, kötelezettségvállalás nélkül is regisztrálhat. A regisztrációhoz látogasson el a kipróbálás Azure Cosmos DB ingyen, vagy olvassa el a [következőt](https://azure.microsoft.com/try/cosmosdb/) : [Try Azure Cosmos db FAQ](#try-cosmos-db).

Ha még nem ismeri az Azure-t, regisztrálhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/), amely 30 napot és egy kreditet biztosít az összes Azure-szolgáltatás kipróbálásához. Ha Visual Studio-előfizetéssel rendelkezik, jogosult [ingyenes Azure-kreditek](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) használatára is bármely Azure-szolgáltatásban.

Az [Azure Cosmos db Emulator](local-emulator.md) használatával az alkalmazást ingyenesen, Azure-előfizetés létrehozása nélkül fejlesztheti és tesztelheti az alkalmazását. Amikor már elégedett az alkalmazás működésével az Azure Cosmos DB Emulatorban, átválthat az Azure Cosmos DB-fiók használatára a felhőben.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Hogyan Kérhetek további segítséget a Azure Cosmos DB?

Ha technikai kérdést szeretne feltenni, tegye közzé a következő két kérdés-válasz fórum egyikét:

* [MSDN-fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). A Stack Overflow a programozási kérdésekre legmegfelelőbb. Győződjön meg arról, hogy a kérdés [a témában](https://stackoverflow.com/help/on-topic) van, és a [lehető legtöbb részletet biztosít, így a kérdés egyértelmű és megválaszolható](https://stackoverflow.com/help/how-to-ask).

Új szolgáltatások igényléséhez hozzon létre egy új kérelmet a [felhasználói hangon](https://feedback.azure.com/forums/263030-azure-cosmos-db).

Ha a fiókjával van probléma, nyújtson be [támogatási kérelmet](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) az Azure Portalon.

A csapatnak további kérdéseket is elküldheti [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com); Ez azonban nem egy technikai támogatási alias.

## <a id="try-cosmos-db"></a>Azure Cosmos DB-előfizetések kipróbálása

Az előfizetések, a díjmentes és a kötelezettségvállalások nélkül is élvezheti a korlátozott Azure Cosmos DB élményt. Ha regisztrálni szeretne a Try Azure Cosmos DB-előfizetésre, lépjen a [kipróbálás Azure Cosmos db ingyen](https://azure.microsoft.com/try/cosmosdb/). Ez az előfizetés külön az [ingyenes Azure-próbaverziótól](https://azure.microsoft.com/free/), és az ingyenes Azure-próbaverzióval vagy egy Azure fizetős előfizetéssel együtt használható.

Próbálja meg Azure Cosmos DB előfizetéseket a felhasználói AZONOSÍTÓhoz társított Azure Portal következő egyéb előfizetések között.

A Azure Cosmos DB-előfizetések kipróbálásához a következő feltételek érvényesek:

* Egy, az SQL, a Gremlin API és a Table accounts-előfizetések által [kiosztott tároló](./set-throughput.md#set-throughput-on-a-container) .
* Akár három, a MongoDB-fiókokra vonatkozó előfizetéshez tartozó [kiosztott gyűjtemény](./set-throughput.md#set-throughput-on-a-container) .
* Egy [átviteli sebesség kiépített adatbázisa](./set-throughput.md#set-throughput-on-a-database) előfizetéssel. Az átviteli sebesség kiépített adatbázisai tetszőleges számú tárolót tartalmazhatnak.
* 10 GB tárolókapacitás.
* A globális replikáció a következő Azure- [régiókban](https://azure.microsoft.com/regions/)érhető el: USA középső régiója, Észak-Európa és Délkelet-Ázsia
* A maximális átviteli sebesség 5 K RU/s a tároló szintjén kiépítve.
* Az adatbázis szintjén kiépített 20 K/s maximális átviteli sebesség.
* Az előfizetések 30 nap elteltével lejárnak, és legfeljebb 31 napra bővíthetők.
* Nem hozhatók létre Azure-támogatási jegyek az Azure Cosmos DB-fiókok kipróbálásához; azonban támogatást biztosítanak a meglévő támogatási csomagokkal rendelkező előfizetőknek.

## <a name="set-up-azure-cosmos-db"></a>Azure Cosmos DB beállítása

### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Hogyan regisztrálni a Azure Cosmos DBra?

A Azure Cosmos DB a Azure Portalben érhető el. Először regisztráljon egy Azure-előfizetésre. A regisztráció után hozzáadhat egy Azure Cosmos DB fiókot az Azure-előfizetéséhez.

### <a name="what-is-a-master-key"></a>Mi a főkulcs?

A főkulcs egy biztonsági jogkivonat, amellyel egy fiók összes erőforrása elérhető. A kulccsal rendelkező személyeknek olvasási és írási hozzáférésük van az adatbázis-fiók összes erőforrásához. A főkulcsok terjesztésekor körültekintően járjon el. Az elsődleges főkulcs és a másodlagos főkulcs a [Azure Portal][azure-portal] **kulcsok** paneljén érhető el. A kulcsokkal kapcsolatos további információkért lásd: [Hozzáférési kulcsok megtekintése, másolása és újragenerálása](manage-with-cli.md#list-account-keys).

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>Mik azok a régiók, amelyeknek a PreferredLocations beállítható?

A PreferredLocations érték bármely olyan Azure-régióhoz beállítható, amelyben Cosmos DB elérhető. Az elérhető régiók listáját az [Azure-régiók](https://azure.microsoft.com/regions/)című részben tekintheti meg.

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Van valami, amit tudni kell, ha az Azure-adatközpontok révén a világ különböző pontjain osztja el az adatelosztást?

Azure Cosmos DB az összes Azure-régióban megtalálható az [Azure-régiók](https://azure.microsoft.com/regions/) lapon megadott módon. Mivel ez az alapszolgáltatás, minden új adatközpont Azure Cosmos DB jelenléttel rendelkezik.

Amikor beállítja a régiót, ne feledje, hogy Azure Cosmos DB a szuverén és a kormányzati felhők tiszteletben tartását. Ez azt eredményezi, hogy ha egy [szuverén régióban](https://azure.microsoft.com/global-infrastructure/)hoz létre fiókot, akkor az adott [szuverén régióból](https://azure.microsoft.com/global-infrastructure/)nem lehet replikálni. Hasonlóképpen nem engedélyezheti a replikációt más szuverén helyekre egy külső fiókból.

### <a name="is-it-possible-to-switch-from-container-level-throughput-provisioning-to-database-level-throughput-provisioning-or-vice-versa"></a>Lehetséges a tárolók átviteli sebességének átváltása az adatbázis szintű átviteli sebesség kiépítés esetén? Vagy fordítva

A tároló-és adatbázis-szintű átviteli sebesség kiépítés külön ajánlat, és az ilyen típusú váltáshoz a forrásról a célhelyre kell áttelepítenie az adatok egyikét. Ez azt jelenti, hogy létre kell hoznia egy új adatbázist vagy egy új tárolót, majd át kell telepítenie az adatátvitelt [tömeges végrehajtó kódtár](bulk-executor-overview.md) vagy [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)használatával.

### <a name="does-azure-cosmosdb-support-time-series-analysis"></a>Támogatja az Azure CosmosDB a Time Series-elemzést?

Igen, az Azure CosmosDB a Time Series-elemzést is támogatja, itt az [idősorozat-minta](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/samples/Patterns). Ez a minta azt mutatja be, hogyan használható a Change feed az összesített nézetek idősorozat-adatokat tartalmazó létrehozásához. Ezt a megközelítést a Spark streaming vagy egy másik adatfolyam-adatfeldolgozó használatával terjesztheti ki.

## <a name="what-are-the-azure-cosmos-db-service-quotas-and-throughput-limits"></a>A Azure Cosmos DB szolgáltatási kvóták és az átviteli sebesség korlátai

További információkért tekintse meg a Azure Cosmos DB [szolgáltatási kvótákat](concepts-limits.md) és az [összes korlátot a tárolóban és az adatbázis](set-throughput.md#comparison-of-models) cikkeiben.

## <a name="sql-api"></a>SQL API

### <a name="how-do-i-start-developing-against-the-sql-api"></a>Hogyan megkezdeni az SQL API fejlesztését?

Először regisztrálnia kell egy Azure-előfizetésre. Az Azure-előfizetésre való regisztráció után hozzáadhat egy SQL API-tárolót az Azure-előfizetéséhez. Azure Cosmos DB fiók hozzáadásával kapcsolatos útmutatásért lásd: [Azure Cosmos Database-fiók létrehozása](create-sql-api-dotnet.md#create-account).

[SDK-k](sql-api-sdk-dotnet.md) a .NET, Python, Node.js, JavaScript és Java esetében érhetők el. A fejlesztők a REST- [alapú http API](/rest/api/cosmos-db/) -kkal is használhatják a különböző platformokról és nyelvekről származó Azure Cosmos db erőforrásokat.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Elérhető néhány kész minta a kezdő kezdéshez?

Az SQL API [.net](sql-api-dotnet-samples.md), a [Java](https://github.com/Azure/azure-documentdb-java), a [Node. js](sql-api-nodejs-samples.md)és a [Python](sql-api-python-samples.md) SDK minták a githubon érhetők el.

### <a name="does-the-sql-api-database-support-schema-free-data"></a>Támogatja az SQL API-adatbázis a séma nélküli adatgyűjtést?

Igen, az SQL API lehetővé teszi, hogy az alkalmazások a séma-definíciók és a javaslatok nélkül tárolják az tetszőleges JSON-dokumentumokat. Az Azure Cosmos DB SQL-lekérdezési felületen keresztül azonnal elérhetők az adatlekérdezések.

### <a name="does-the-sql-api-support-acid-transactions"></a>Támogatja az SQL API a savas tranzakciókat?

Igen, az SQL API támogatja a JavaScript által tárolt eljárásként és eseményindítóként kifejezett dokumentumok közötti tranzakciókat. A tranzakciók az egyes tárolók egyetlen partícióján vannak kiképezve, és a savas szemantika "All vagy Nothing" néven lett végrehajtva. Ha a kivételeket a JavaScript alkalmazás kódjának kiszolgálóoldali végrehajtása hajtja végre, a teljes tranzakció vissza lesz állítva. 

### <a name="what-is-a-container"></a>Mi az a tároló?

A tároló dokumentumok egy csoportja és a hozzájuk társított JavaScript-alkalmazás logikája. A tároló számlázható entitás, ahol a [költségeket](performance-levels.md) az átviteli sebesség és a felhasznált tárterület határozza meg. A tárolók egy vagy több partíciót vagy kiszolgálót is kiterjedhetnek, és méretezhetők a gyakorlatilag korlátlan mennyiségű tárterület vagy átviteli sebesség kezelésére.

* Az SQL API-hoz egy tároló képezi le a tárolót.
* A MongoDB-fiókok Cosmos DB API-ját egy tároló képezi le egy gyűjteményhez.
* A Cassandra és a Table API fiókok esetében egy tároló képezi le a táblát.
* A Gremlin API-fiókok esetében a tároló egy gráfhoz rendeli a diagramot.

A tárolók a Azure Cosmos DB számlázási entitásai is. Minden egyes tároló számlázása óránként történik, a kiosztott átviteli sebesség és a felhasznált tárolóhely alapján. További információ: [Azure Cosmos db díjszabása](https://azure.microsoft.com/pricing/details/cosmos-db/).

### <a name="how-do-i-create-a-database"></a>Hogyan hozható létre adatbázis?

Az adatbázisokat a [Azure Portal](https://portal.azure.com)használatával hozhatja létre a [tároló hozzáadása](create-sql-api-java.md#add-a-container), a [Azure Cosmos db SDK](sql-api-sdk-dotnet.md)-k egyike vagy a [REST API](/rest/api/cosmos-db/)-k című témakörben leírtak szerint.

### <a name="how-do-i-set-up-users-and-permissions"></a>Hogyan állíthatom be a felhasználókat és engedélyeket?

Felhasználókat és engedélyeket a [Cosmos db API SDK](sql-api-sdk-dotnet.md) -k vagy a [REST API](/rest/api/cosmos-db/)-k egyikével hozhat létre.

### <a name="does-the-sql-api-support-sql"></a>Támogatja az SQL API az SQL-t?

Az SQL API-fiókok által támogatott SQL-lekérdezési nyelv a SQL Server által támogatott lekérdezési funkció továbbfejlesztett részhalmaza. A Azure Cosmos DB SQL-lekérdezési nyelv gazdag hierarchikus és a kapcsolódó operátorokat és bővíthetőséget biztosít a JavaScript-alapú, felhasználó által definiált függvények (UDF-k) segítségével. A JSON-nyelvtan lehetővé teszi a JSON-dokumentumok faszerkezetű, címkézett csomópontokkal történő modellezését, amelyeket a Azure Cosmos DB automatikus indexelési módszer és az Azure Cosmos DB SQL-lekérdezési dialektusa használ. További információ az SQL-nyelvtan használatáról: [SQL-lekérdezési][query] cikk.

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>Támogatja az SQL API az SQL aggregációs funkciókat?

Az SQL API bármilyen méretezéssel támogatja az alacsony késésű összesítést az összesítő függvények `COUNT`, `MIN`, `MAX`, `AVG` és `SUM` használatával az SQL-nyelvtanon keresztül. További információ: [összesítő függvények](sql-query-aggregates.md).

### <a name="how-does-the-sql-api-provide-concurrency"></a>Hogyan biztosítja az SQL API az egyidejűséget?

Az SQL API támogatja az optimista Egyidejűség-vezérlést (OCC) a HTTP-entitások címkéi vagy Etagek használatával. Minden SQL API-erőforrás rendelkezik egy ETag, és a ETag minden alkalommal be van állítva a kiszolgálón, amikor frissülnek a dokumentumok. A ETag fejléc és az aktuális érték szerepel az összes válaszüzenetben. A Etagek az IF-Match fejléc használatával engedélyezheti, hogy a kiszolgáló döntse el, hogy frissíteni kell-e egy erőforrást. Az IF-Match érték az a ETag érték, amelyet ellenőrizni kell. Ha a ETag értéke megegyezik a kiszolgáló ETag értékével, az erőforrás frissül. Ha a ETag már nem aktuális, a kiszolgáló elutasítja a műveletet "HTTP 412 előfeltételi hiba" hibakódtal. Az ügyfél ezután visszakéri az erőforrást az erőforrás aktuális ETag értékének beolvasásához. Emellett a Etagek is használható az If-None-Match fejléctel annak megállapításához, hogy szükség van-e egy erőforrás visszahívására.

Ha optimista párhuzamosságot szeretne használni a .NET-ben, használja a [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) osztályt. .NET-minta esetén lásd: [program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) a DocumentManagement-mintában a githubon.

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>Hogyan végre tranzakciókat az SQL API-ban?

Az SQL API támogatja a nyelvre épülő tranzakciókat a JavaScript által tárolt eljárások és eseményindítók használatával. A szkripteken belüli összes adatbázis-művelet a pillanatkép-elkülönítés alatt fut. Ha egypartíciós tároló, a végrehajtás hatóköre a tárolóra terjed ki. Ha a tároló particionálva van, a végrehajtás a tárolón belül ugyanazzal a partíciós kulccsal rendelkező dokumentumokra terjed ki. A tranzakció kezdetekor pillanatkép készül a dokumentumverziókról (ETag-ek), és csak akkor lesznek véglegesítve, ha a parancsfájl sikeres. Ha a JavaScript hibát jelez, a tranzakció vissza lesz állítva. További információ: [kiszolgálóoldali JavaScript-programozás Azure Cosmos db számára](stored-procedures-triggers-udfs.md).

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Hogyan szúrhatók be dokumentumok a Cosmos DBba?

A dokumentumokat a következő módszerek egyikével lehet tömegesen beszúrni Azure Cosmos DBba:

* A tömeges végrehajtó eszköz a [tömeges végrehajtó .net-kódtár használata](bulk-executor-dot-net.md) és a [tömeges végrehajtó Java-függvénytár](bulk-executor-java.md) használata című témakörben leírtak szerint
* Az adatáttelepítési eszköz a [Azure Cosmos db adatbázis-áttelepítési eszközében](import-data.md)leírtak szerint.
* Tárolt eljárások a [Azure Cosmos db kiszolgálóoldali JavaScript-programozása](stored-procedures-triggers-udfs.md)című témakörben leírtak szerint.

### <a name="does-the-sql-api-support-resource-link-caching"></a>Támogatja az SQL API az erőforrás-hivatkozás gyorsítótárazását?

Igen, mivel Azure Cosmos DB egy REST-szolgáltatás, az erőforrás-hivatkozások nem változtathatók meg, és a gyorsítótárba helyezhetők. Az SQL API-ügyfelek megadhatnak egy "If-None-Match" fejlécet bármilyen erőforrás-szerű dokumentumhoz vagy tárolóhoz való olvasáshoz, majd a kiszolgáló verziójának módosítása után frissíthetik a helyi másolatokat.

### <a name="is-a-local-instance-of-sql-api-available"></a>Elérhető-e az SQL API helyi példánya?

Igen. A [Azure Cosmos db Emulator](local-emulator.md) a Cosmos db szolgáltatás kiváló hűségű emulációját biztosítja. Támogatja a Azure Cosmos DB azonos funkciókat, beleértve a JSON-dokumentumok létrehozását és lekérdezését, a gyűjtemények üzembe helyezését és méretezését, valamint a tárolt eljárások és triggerek végrehajtását. Az Azure Cosmos DB Emulator használatával fejlesztheti és tesztelheti az alkalmazásokat, és globális szinten helyezheti üzembe őket az Azure-ban azáltal, hogy egyetlen konfigurációs módosítást végez a Azure Cosmos DBhoz tartozó kapcsolódási végponton.

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>A portálon található adatkezelőből kikerekített dokumentum hosszú, lebegőpontos értékének meghatározása.

Ez a JavaScript korlátozása. A JavaScript dupla pontosságú lebegőpontos formátumú számokat használ az IEEE 754-ben megadott értékekkel, és biztonságosan képes tárolni a számokat – (2<sup>53</sup> – 1) és 2<sup>53</sup>-1 (például 9007199254740991).

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>Hol engedélyezettek az engedélyek az objektum-hierarchiában?

Az engedélyek ResourceTokens használatával történő létrehozása a tároló szintjén és annak leszármazottai (például dokumentumok, mellékletek) esetében engedélyezett. Ez azt jelenti, hogy az adatbázis vagy a fiók szintjén jelenleg nem engedélyezett az engedély létrehozása.

## <a name="azure-cosmos-dbs-api-for-mongodb"></a>MongoDB-hez készült Azure Cosmos DB API

### <a name="what-is-the-azure-cosmos-dbs-api-for-mongodb"></a>Mi a Azure Cosmos DB API-MongoDB?

A MongoDB-hez készült Azure Cosmos DB API-protokoll kompatibilitási réteg, amely lehetővé teszi az alkalmazások számára, hogy a meglévő, Közösség által támogatott SDK-k és illesztőprogramok használatával könnyedén és transzparens módon kommunikáljanak a natív Azure Cosmos-adatbázis-motorral. A fejlesztők mostantól a meglévő MongoDB eszközlánccal és képességeiket is használhatják olyan alkalmazások készítéséhez, amelyek kihasználják a Azure Cosmos DB. A fejlesztők a Azure Cosmos DB egyedi képességeivel részesülnek, amelyek globális elosztást biztosítanak a több főkiszolgálós replikációval, az automatikus indexeléssel, a biztonsági mentési karbantartással, a pénzügyi támogatással rendelkező szolgáltatói szerződésekkel (SLA) stb.

### <a name="how-do-i-connect-to-my-database"></a>Hogyan csatlakozni az adatbázishoz?

A MongoDB Azure Cosmos DB API-jával a Cosmos-adatbázishoz való kapcsolódás leggyorsabb módja a [Azure Portal](https://portal.azure.com). Lépjen a fiókjába, majd a bal oldali navigációs menüben kattintson a **gyorskonfigurálás**elemre. A gyors üzembe helyezés a legjobb módszer az adatbázishoz való kapcsolódáshoz szükséges kódrészletek beszerzésére.

Azure Cosmos DB kényszeríti a szigorú biztonsági követelményeket és szabványokat. Azure Cosmos DB fiókoknak hitelesítésre és biztonságos kommunikációra van szükségük SSL-n keresztül, ezért mindenképpen a TLS 1.2-es verzióját használja.

További információ: a [Cosmos-adatbázishoz való kapcsolódás Azure Cosmos db API-MongoDB](connect-mongodb-account.md).

### <a name="are-there-additional-error-codes-that-i-need-to-deal-with-while-using-azure-cosmos-dbs-api-for-mongodb"></a>Vannak-e további hibakódok, amelyekre szükség van a Azure Cosmos DB API-MongoDB való használatához?

A Common MongoDB-hibakódokkal együtt a Azure Cosmos DB API-ját a MongoDB saját specifikus hibakódokkal rendelkezik:

| Hiba               | Kód  | Leírás  | Megoldás  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | A felhasználható kérelmek teljes száma nagyobb, mint a tárolóhoz kiépített kérelem-egység sebessége, és a rendszer szabályozott. | Érdemes lehet egy tárolóhoz rendelt átviteli sebességet, vagy a Azure Portal tárolók készletét vagy újrapróbálkozni. |
| ExceededMemoryLimit | 16501 | Több-bérlős szolgáltatásként a művelet túllépte az ügyfél memóriájának kiosztását. | Csökkentse a művelet hatókörét szigorúbb lekérdezési feltételekkel, vagy forduljon a [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)támogatási szolgálatához. <br><br>Példa: <em> @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4db. getCollection ("Users"). aggregált ([<br>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 {$match: {név: "Andy"}}, <br>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 {$sort: {Age:-1}}<br>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3]) </em>) |

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>Támogatott-e a MongoDB-illesztőprogram a Azure Cosmos DB API-MongoDB való használatra?

Igen, használhatja a Simba Mongo ODBC-illesztőjét Azure Cosmos DB API-val a MongoDB

## <a id="table"></a>Table API

### <a name="how-can-i-use-the-table-api-offering"></a>Hogyan használhatom a Table API ajánlatot?

A Azure Cosmos DB Table API a [Azure Portalban][azure-portal]érhető el. Először regisztrálnia kell egy Azure-előfizetésre. A regisztráció után hozzáadhat egy Azure Cosmos DB Table API fiókot az Azure-előfizetéséhez, majd hozzáadhat táblákat a fiókjához.

A támogatott nyelveket és a kapcsolódó gyors kezdéseket a [Azure Cosmos DB Table API bevezetésében](table-introduction.md)találhatja meg.

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>Szükségem van egy új SDK-ra a Table API használatához?

Nem, a meglévő Storage SDK-k továbbra is működni tudnak. Azt azonban javasoljuk, hogy az egyik mindig a legújabb SDK-kat kapja a legjobb támogatáshoz, és sok esetben a kiváló teljesítményt. Tekintse meg az elérhető nyelvek listáját a [Azure Cosmos DB Table API bevezetésében](table-introduction.md).

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Hol Table API nem azonos az Azure Table Storage viselkedésével?

Vannak olyan viselkedési eltérések, amelyeket a felhasználók az Azure Table Storage-ból érkeznek, és a Azure Cosmos DB Table API táblákat szeretnének létrehozni:

* Azure Cosmos DB a Table API fenntartott kapacitási modellt használ a garantált teljesítmény biztosításához, de ez azt jelenti, hogy az egyik a tábla létrehozásakor a kapacitást a lehető leghamarabb kifizeti, még akkor is, ha a kapacitás nincs használatban. Az Azure Table Storage szolgáltatással csak a felhasznált kapacitásért fizet. Ez segít megmagyarázni, hogy a Table API miért nyújthat 10 MS Read és 15 MS írási SLA-t a esetek 99% percentilis-ben, az Azure Table Storage pedig 10 másodperces SLA-t biztosít. Ennek következményeként azonban Table API táblákat, akár üres táblákat is anélkül, hogy kérések nélkül, pénzbe kerül, hogy a kapacitás elérhető legyen a Azure Cosmos DB által kínált SLA-ra irányuló kérések kezeléséhez.
* A Table API által visszaadott lekérdezési eredményeket nem rendezi a rendszer a partíciós kulcs/sor kulcs sorrendjében, mivel azok az Azure Table Storage-ban találhatók.
* A sorok kulcsa legfeljebb 255 bájt lehet.
* A kötegek legfeljebb 2 MB-ot tartalmazhatnak
* A CORS jelenleg nem támogatott
* Az Azure Table Storage-beli táblanév nem különbözteti meg a kis-és nagybetűket, Azure Cosmos DB Table API
* A kódolási információk (például bináris mezők) Azure Cosmos DB belső formátuma jelenleg nem annyira hatékony, mint az egyik. Emiatt ez váratlan korlátozásokat okozhat az adatméretnél. Például jelenleg nem sikerült a Table entitás teljes készletének használata a bináris adat tárolására, mert a kódolás növeli az adat méretét.
* Az entitás "ID" tulajdonságának neve jelenleg nem támogatott
* A TableQuery TakeCount nem korlátozódik a 1000-re

A REST API a Azure Cosmos DB Table API által nem támogatott végpontok/lekérdezési beállítások száma:

| Rest metódus (ok) | Rest-végpont/lekérdezési beállítás | Doc URL-címek | Magyarázat |
| ------------| ------------- | ---------- | ----------- |
| LETÖLTÉS, PUT | /? RESTYPE = service@comp = tulajdonságok| A [Table szolgáltatás tulajdonságainak beállítása](https://docs.microsoft.com/rest/api/storageservices/set-table-service-properties) és a [Table szolgáltatás tulajdonságainak beolvasása](https://docs.microsoft.com/rest/api/storageservices/get-table-service-properties) | Ez a végpont a CORS-szabályok, a tárolási elemzési konfiguráció és a naplózási beállítások beállítására szolgál. A CORS jelenleg nem támogatott, és az elemzési és a naplózási módokat az Azure Storage-táblák Azure Cosmos DB eltérő módon kezelik. |
| BEÁLLÍTÁSOK | / @ no__t-1Table-Resource-Name > | [Repülés előtti CORS-tábla kérése](https://docs.microsoft.com/rest/api/storageservices/preflight-table-request) | Ez a CORS része, amely Azure Cosmos DB jelenleg nem támogatott. |
| GET | /? RESTYPE = service@comp = stats | [Table szolgáltatás statisztikáinak beolvasása](https://docs.microsoft.com/rest/api/storageservices/get-table-service-stats) | Információt nyújt arról, hogy az adatok milyen gyorsan replikálódnak az elsődleges és a formátumú másodlagos zónák között. Ez nem szükséges Cosmos DBban, mert a replikálás része az írásoknak. |
| LETÖLTÉS, PUT | /mytable? COMP = ACL | [Táblázat ACL lekérése](https://docs.microsoft.com/rest/api/storageservices/get-table-acl) és a [tábla ACL beállítása](https://docs.microsoft.com/rest/api/storageservices/set-table-acl) | Ez lekérdezi és beállítja a megosztott hozzáférési aláírások (SAS) kezelésére használt tárolt hozzáférési szabályzatokat. Bár a SAS támogatott, a rendszer másként állítja be és kezeli őket. |

Emellett a Azure Cosmos DB Table API csak a JSON formátumot támogatja, az ATOMot nem.

Míg a Azure Cosmos DB támogatja a közös hozzáférésű aláírásokat (SAS), bizonyos szabályzatok nem támogatottak, kifejezetten a felügyeleti műveletekhez kapcsolódóan, például az új táblák létrehozásához való jogosultsággal.

A .NET SDK esetében bizonyos osztályok és metódusok vannak, amelyeket a Azure Cosmos DB jelenleg nem támogat.

| Osztály | Nem támogatott metódus |
|-------|-------- |
| CloudTableClient | @no__t – 0ServiceProperties * |
|                  | @no__t – 0ServiceStats * |
| CloudTable | SetPermissions* |
|            | GetPermissions* |
| TableServiceContext | * (ez az osztály elavult) |
| TableServiceEntity | " " |
| TableServiceExtensions | " " |
| TableServiceQuery | " " |

Ha bármelyik különbség a projekt problémája, vegye fel a kapcsolatot [askcosmosdb@microsoft.com-](mailto:askcosmosdb@microsoft.com) vel, és tudassa velünk.

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Hogyan visszajelzést ad az SDK-ról vagy a hibákról?

A következő módokon megoszthatja a visszajelzéseit:

* [Felhasználói hang](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [MSDN-fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). A Stack Overflow a programozási kérdésekre legmegfelelőbb. Győződjön meg arról, hogy a kérdés [a témában](https://stackoverflow.com/help/on-topic) van, és a [lehető legtöbb részletet biztosít, így a kérdés egyértelmű és megválaszolható](https://stackoverflow.com/help/how-to-ask).

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Milyen kapcsolati sztringet kell használnia a Table APIhoz való kapcsolódáshoz?

A kapcsolatok karakterlánca:

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```

A Azure Portal a kapcsolatok karakterlánc oldaláról kérheti le a kapcsolatok karakterláncát.

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Hogyan felülbírálja a kérés beállításainak konfigurációs beállításait a .NET SDK-ban a Table APIhoz?

Egyes beállítások kezelése a CreateCloudTableClient metódussal történik, az ügyfélalkalmazás appSettings szakaszában pedig az app. config fájlon keresztül. További információ a konfigurációs beállításokról: [Azure Cosmos db képességek](tutorial-develop-table-dotnet.md).

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>Van-e változás a meglévő Azure Table Storage SDK-kat használó ügyfeleknél?

Nincs. A meglévő és az új ügyfelek esetében nem történt változás a meglévő Azure Table Storage SDK-k használatával.

### <a name="how-do-i-view-table-data-thats-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Hogyan a Azure Cosmos DB tárolt táblázatos adattábla-adatmegjelenítést a Table API használatával?

A Azure Portal használatával böngészheti az adatfájlokat. Használhatja a következő válaszban említett Table API kódot vagy eszközöket is.

### <a name="which-tools-work-with-the-table-api"></a>Mely eszközök működnek a Table API?

Használhatja a [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

Azok az eszközök, amelyeknek a rugalmassága a korábban megadott formátumban a kapcsolódási karakterlánc elvégzésére szolgál, támogathatja az új Table API. A tábla eszközeinek listáját az [Azure Storage-ügyfél eszközei](../storage/common/storage-explorers.md) oldalon található.

### <a name="is-the-concurrency-on-operations-controlled"></a>Az a párhuzamosság az ellenőrzött műveleteken?

Igen, az optimista párhuzamosságot a ETag mechanizmus használatával biztosítjuk.

### <a name="is-the-odata-query-model-supported-for-entities"></a>Támogatott-e az OData-lekérdezési modell az entitások esetében?

Igen, a Table API támogatja a OData-lekérdezési és a LINQ-lekérdezést.

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>Csatlakozhatok az Azure Table Storagehoz, és Azure Cosmos DB Table API egymás mellett ugyanazon alkalmazásban?

Igen, csatlakozhat a CloudTableClient két különálló példányának létrehozásával, amelyek mindegyike a saját URI-ra mutat a kapcsolati karakterláncon keresztül.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>Hogyan migrálni egy meglévő Azure Table Storage-alkalmazást erre az ajánlatra?

A [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) és a [Azure Cosmos db adatáttelepítési eszköz](import-data.md) egyaránt támogatott.

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Hogyan történik a szolgáltatás tárolási méretének bővítése, ha például egy *n* GB adattal kezdek, és az adataim az idő múlásával 1 TB-ra növekednek?

Azure Cosmos DB úgy lett kialakítva, hogy a horizontális skálázás használatával korlátlan tárhelyet biztosítson. A szolgáltatás nyomon követheti és hatékonyan növelheti a tárterületet.

### <a name="how-do-i-monitor-the-table-api-offering"></a>Hogyan figyeli a Table API ajánlatot?

A kérelmek és a tárhely használatának figyeléséhez használhatja a Table API **metrikák** ablaktáblát.

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Hogyan kiszámítja a szükséges átviteli sebességet?

A kapacitás-kalkulátor használatával kiszámíthatja a műveletekhez szükséges TableThroughput. További információ: [becsült kérelmek egységei és adattárolása](https://www.documentdb.com/capacityplanner). Általánosságban megjelenítheti az entitást JSON-ként, és megadhatja a műveleteinek számát.

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>Helyileg is használhatom az Table API SDK-t az emulátorral?

Jelenleg nem.

### <a name="can-my-existing-application-work-with-the-table-api"></a>Használhatom a meglévő alkalmazást a Table API?

Igen, ugyanazt az API-t támogatja.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-dont-want-to-use-the-table-api-features"></a>Át kell-e telepíteni a meglévő Azure Table Storage-alkalmazásokat az SDK-ba, ha nem szeretném használni az Table API-szolgáltatásokat?

Nem, a meglévő Azure Table Storage-eszközöket bármilyen típusú megszakítás nélkül hozhatja létre és használhatja. Ha azonban nem használja a Table API, az Automatikus indexelés, a további konzisztencia-beállítás vagy a globális terjesztés nem részesülhet előnyben.

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-more-than-one-region-of-azure"></a>Hogyan az Azure-ban több régióban is megadhatja az adatreplikációt a Table APIban?

Az Azure Cosmos DB portál [globális replikációs beállításaival](tutorial-global-distribution-sql-api.md#portal) hozzáadhat olyan régiókat, amelyek megfelelőek az alkalmazáshoz. Globálisan elosztott alkalmazások fejlesztéséhez az alkalmazásnak a helyi régió számára beállított PreferredLocation-információval kell rendelkeznie, hogy alacsony olvasási késést biztosítson.

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Hogyan módosítja a fiók elsődleges írási régióját a Table API?

A Azure Cosmos DB globális replikációs portál panel használatával hozzáadhat egy régiót, majd átadhatja a feladatokat a szükséges régióhoz. Útmutatásért lásd: [fejlesztés többrégiós Azure Cosmos db-fiókokkal](high-availability.md).

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Hogyan a saját előnyben részesített olvasási régiókat az adatelosztáskor alacsony késés érdekében?

A helyi helyről való olvasáshoz használja a PreferredLocation kulcsot az app. config fájlban. Meglévő alkalmazások esetén a Table API hibát jelez, ha a Blobrequestoptions locationmode be van állítva. Távolítsa el ezt a kódot, mert a Table API az app. config fájlból veszi fel ezeket az információkat. 

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>Hogyan érdemes meggondolni az Table API konzisztencia-szintjét?

Azure Cosmos DB jól indokolt kompromisszumot biztosít a konzisztencia, a rendelkezésre állás és a késés között. A Azure Cosmos DB öt konzisztencia-Table API szintet biztosít a fejlesztők számára, így kiválaszthatja a megfelelő konzisztencia-modellt a tábla szintjén, és egyéni kéréseket végezhet az adatlekérdezés során. Amikor egy ügyfél csatlakozik, megadhatja a konzisztencia szintjét. A szintet a CreateCloudTableClient consistencyLevel argumentumának használatával módosíthatja.

A Table API alacsony késleltetésű olvasást biztosít a "saját írások olvasása" kifejezéssel, amely az alapértelmezett értékkel határos késéssel rendelkezik. További információ: konzisztencia- [szintek](consistency-levels.md).

Alapértelmezés szerint az Azure Table Storage erős konzisztenciát biztosít a régión belül és a másodlagos helyen lévő végleges konzisztenciát.

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>Azure Cosmos DB Table API az Azure Table Storage-nál nagyobb konzisztencia-szintet kínál?

Igen, a Azure Cosmos DB elosztott jellegéből származó előnyökkel kapcsolatos információkért lásd: [konzisztencia-szintek](consistency-levels.md). Mivel a rendszer garanciákat biztosít a konzisztencia-szintekhez, nyugodtan használhatja őket.

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Ha engedélyezve van a globális terjesztés, mennyi ideig tart az adatreplikálás?

Azure Cosmos DB véglegesíti az tartósan a helyi régióban, és azonnal leküldi az adatmennyiséget a többi régióba ezredmásodpercben. Ez a replikálás csak az adatközpont időpontra (RTT) függ. Ha többet szeretne megtudni a Azure Cosmos DB globális elosztási képességeiről, tekintse meg a [Azure Cosmos db: globálisan elosztott adatbázis-szolgáltatás az Azure](distribute-data-globally.md)-ban című témakört.

### <a name="can-the-read-request-consistency-level-be-changed"></a>Módosítható az olvasási kérelmek konzisztenciáji szintje?

A Azure Cosmos DB használatával beállíthatja a konzisztencia szintjét a tároló szintjén (a táblán). A .NET SDK használatával megváltoztathatja a szintet úgy, hogy megadja a TableConsistencyLevel kulcs értékét az app. config fájlban. A lehetséges értékek: erős, kötött elavulás, munkamenet, konzisztens előtag és végleges. További információkért lásd: [Azure Cosmos db hangolt adatkonzisztencia-szintjei](consistency-levels.md). A legfontosabb elképzelés az, hogy a kérelem konzisztencia-szintje nem állítható be a tábla beállításától eltérő értékre. Nem állíthatja be például a tábla konzisztencia-szintjét, és a kérések konzisztencia-szintje erős.

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>Hogyan kezeli a Table API a feladatátvételt, ha egy régió leáll?

A Table API a Azure Cosmos DB globálisan elosztott platformját használja. Annak érdekében, hogy az alkalmazás képes legyen az adatközpontok leállásának elvégzésére, legalább egy régiót be kell állítani a Azure Cosmos DB-portálon a [többrégiós Azure Cosmos db-fiókokkal fejlesztett](high-availability.md)fiókhoz. A régió prioritását beállíthatja a [többrégiós Azure Cosmos db-fiókokkal fejlesztett](high-availability.md)portál használatával.

A fiókhoz tetszőleges számú régiót adhat hozzá, és szabályozhatja, hogy a feladatátvételi prioritás biztosításával miként lehet átadni a feladatokat. Az adatbázis használatához meg kell adnia egy alkalmazást is. Ha így tesz, az ügyfelek nem fognak tapasztalni állásidőt. A [legújabb .net Client SDK](table-sdk-dotnet.md) automatikus vezérlés, de a többi SDK nem. Ez azt is megteheti, hogy felismeri a régiót, és automatikusan átadja a feladatátvételt az új régiónak.

### <a name="is-the-table-api-enabled-for-backups"></a>Engedélyezve van-e a biztonsági mentések Table API?

Igen, a Table API a biztonsági mentések Azure Cosmos DB platformját használja. A biztonsági mentések automatikusan történnek. További információkért tekintse meg [az online biztonsági mentést és a visszaállítást a Azure Cosmos DBával](online-backup-and-restore.md).

### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>Az Table API indexeli az entitás összes attribútumát alapértelmezés szerint?

Igen, az entitások összes attribútuma alapértelmezés szerint indexelve van. További információ [: Azure Cosmos db: indexelési házirendek](index-policy.md).

### <a name="does-this-mean-i-dont-have-to-create-more-than-one-index-to-satisfy-the-queries"></a>Ez azt jelenti, hogy nem kell egynél több indexet létrehozni a lekérdezések kielégítéséhez?

Igen, Azure Cosmos DB Table API az összes attribútum automatikus indexelését biztosítja a séma definíciója nélkül. Ez az Automation felszabadítja a fejlesztőket, hogy az index létrehozása és kezelése helyett az alkalmazásra összpontosítsanak. További információ [: Azure Cosmos db: indexelési házirendek](index-policy.md).

### <a name="can-i-change-the-indexing-policy"></a>Módosíthatom az indexelési szabályzatot?

Igen, az indexelési házirendet megadhatja az index definíciójának megadásával. A beállításokat megfelelően kell kódolni és elmenekülnie.

A non-.NET SDK-k esetében az indexelési házirend csak a portálon állítható be **Adatkezelőon**, navigáljon a módosítani kívánt táblához, majd nyissa meg a **méretezési & beállításait**– > indexelési házirend, végezze el a kívánt módosításokat, majd **mentse** .

A .NET SDK-ból az app. config fájlban is elküldhető:

```JSON
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

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>Úgy tűnik, hogy a platform számos képességgel rendelkezik, mint például a rendezés, az összesítések, a hierarchia és az egyéb funkciók. Azure Cosmos DB Felveszi ezeket a képességeket a Table API?

A Table API ugyanazokat a lekérdezési funkciókat biztosítja, mint az Azure Table Storage. Az Azure Cosmos DB a rendezést, az összesítéseket, a földrajzi lekérdezéseket, a hierarchiát és számos különféle beépített funkciót is támogat. További információ: SQL- [lekérdezések](how-to-sql-query.md).

### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>Mikor kell módosítani a Table API TableThroughput?

A TableThroughput akkor kell módosítania, ha a következő feltételek valamelyike teljesül:

* Az adatok kinyerését, átalakítását és betöltését (ETL) végzi, vagy rövid idő alatt nagy mennyiségű adatmennyiséget szeretne feltölteni.
* Több átviteli sebességre van szüksége a tárolóból vagy a háttérbeli tárolók készletében. Láthatja például, hogy a felhasznált átviteli sebesség meghaladja a kiépített átviteli sebességet, és a szabályozása folyamatban van. További információ: [az átviteli sebesség beállítása az Azure Cosmos-tárolók számára](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>Felskálázás vagy leskálázás a Table API-táblázat átviteli sebességét?

Igen, az adatátviteli sebesség méretezéséhez használhatja a Azure Cosmos DB portál méretezési paneljét. További információ: [set átviteli sebesség](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Alapértelmezett TableThroughput van beállítva az újonnan kiosztott táblákhoz?

Igen, ha nem bírálja felül a TableThroughput az app. config fájlon keresztül, és nem használ előre létrehozott tárolót Azure Cosmos DBban, a szolgáltatás létrehoz egy táblázatot, amelynek átviteli sebessége 400.

### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Módosult az Azure Table Storage szolgáltatás meglévő ügyfeleinek díjszabása?

Nincs. A meglévő Azure Table Storage-ügyfelek árát nem lehet megváltoztatni.

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Hogyan számítja ki a Table API árát?

Az ár a lefoglalt TableThroughput függ.

### <a name="how-do-i-handle-any-rate-limiting-on-the-tables-in-table-api-offering"></a>Hogyan kezelni a Table API ajánlat tábláira vonatkozó korlátozásokat?

Ha a kérelmek aránya nagyobb, mint az alapul szolgáló tároló vagy a tárolók készletének kiépített átviteli kapacitása, hibaüzenetet kap, és az SDK újrapróbálkozik a hívással az újrapróbálkozási szabályzat alkalmazásával.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Miért van szükség a PartitionKey és a RowKey melletti átviteli sebesség kiválasztására, hogy kihasználhassa a Azure Cosmos DB Table API ajánlatát?

Azure Cosmos DB beállítja a tároló alapértelmezett átviteli sebességét, ha nem ad meg egyet az app. config fájlban vagy a portálon keresztül.

A Azure Cosmos DB a teljesítményre és a késésre vonatkozó garanciákat biztosít, és a felső korláttal működik. Ez a garancia akkor lehetséges, ha a motor érvényesítheti a bérlő műveleteinek irányítását. A TableThroughput beállítása biztosítja, hogy megkapja a garantált átviteli sebességet és a késést, mivel a platform fenntartja ezt a kapacitást, és garantálja a működés sikerességét.

Az átviteli sebesség meghatározásával rugalmasan változtathatja meg az alkalmazás szezonális használatát, teljesítheti az adatátviteli igényeket, és megtakaríthatja a költségeket.

### <a name="azure-table-storage-has-been-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-havent-performed-a-single-transaction-or-stored-anything-can-you-explain"></a>Az Azure Table Storage szolgáltatás olcsó volt számomra, mert csak az Adattárolásért kell fizetnie, és ritkán kérdezem le. Úgy tűnik, hogy a Azure Cosmos DB Table API ajánlat akkor is megtöltődik, ha egyetlen tranzakciót sem hajtottak végre, vagy semmit nem tároltam. El tudja magyarázni?

A Azure Cosmos DB globálisan elosztott, SLA-alapú rendszernek van kialakítva, amely garantálja a rendelkezésre állást, a késést és az átviteli sebességet. Ha Azure Cosmos DB-ban foglalt adatátviteli kapacitást foglal le, akkor az más rendszerek átviteli sebességével ellentétben garantált. Azure Cosmos DB az ügyfelek által kért további képességeket biztosít, mint például a másodlagos indexek és a globális terjesztés.

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Soha nem kaptam meg a kvóta teljes "értesítését (amely azt jelzi, hogy a partíció megtelt), amikor az Azure Table Storage-ba tölt be adatot. A Table API a következő üzenet jelenik meg:. Ez az ajánlat korlátozza a szolgáltatást, és kényszeríti a meglévő alkalmazás módosítását?

Azure Cosmos DB egy SLA-alapú rendszer, amely korlátlan léptéket biztosít, és garantálja a késést, az átviteli sebességet, a rendelkezésre állást és a konzisztenciát. A prémium szintű garantált teljesítmény biztosításához ellenőrizze, hogy az adatméret és az index kezelhető és méretezhető-e. Az entitások vagy elemek számának 10 GB-os korlátozása a partíciós kulcson alapul, így biztosítva, hogy nagy keresési és lekérdezési teljesítményt biztosítunk. Annak biztosítása érdekében, hogy az alkalmazás megfelelően méretezhető legyen, még az Azure Storage esetében is, javasoljuk, hogy *ne* hozzon létre egy gyors partíciót úgy, hogy az összes információt egy partícióban tárolja, és lekérdezi azt.

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>Így PartitionKey és RowKey is szükség van a Table API?

Igen. Mivel a Table API felülete hasonló az Azure Table Storage SDK-hoz, a partíciós kulcs hatékony módszert biztosít az adatterjesztéshez. A sor kulcsa egyedi a partíción belül. A sor kulcsának jelen kell lennie, és nem lehet null értékű a standard SDK-ban. A RowKey hossza 255 bájt, és a PartitionKey hossza 1 KB.

### <a name="what-are-the-error-messages-for-the-table-api"></a>Mik a Table API hibaüzenetei?

Az Azure Table Storage és a Azure Cosmos DB Table API ugyanazokat az SDK-kat használja, így a legtöbb hiba ugyanaz lesz.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Miért kell szabályozni a szabályozást, amikor megpróbálok sok táblázatot létrehozni egy másik után a Table API?

Azure Cosmos DB egy SLA-alapú rendszer, amely késést, teljesítményt, rendelkezésre állást és konzisztencia-garanciákat biztosít. Mivel ez egy kiépített rendszer, a megtartja az erőforrásokat a követelmények garantálása érdekében. A táblázatok gyors létrehozásának gyakorisága észlelhető és szabályozható. Javasoljuk, hogy tekintse meg a táblák létrehozásának sebességét, és csökkentse kevesebb, mint 5 percenként. Ne feledje, hogy a Table API egy kiépített rendszer. A kiépítés pillanatában meg kell fizetnie.

## <a name="gremlin-api"></a>Gremlin API

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>A C#kódon-fejlesztéshez a Microsoft. Azure. graphs csomagot vagy a Gremlin.net kell használnia?

Azure Cosmos db Gremlin API kihasználja a nyílt forráskódú illesztőprogramokat a szolgáltatás fő összekötői. Ezért az ajánlott lehetőség az [Apache Tinkerpop által támogatott illesztőprogramok](https://tinkerpop.apache.org/)használata.

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>Hogyan történik az RU/s díja a lekérdezéseknek a Graph-adatbázison való futtatásakor?

Az összes gráf-objektum, csúcs és szegély a háttér JSON-dokumentumaiként jelenik meg. Mivel egy Gremlin-lekérdezés egyszerre egy vagy több gráf-objektumot is módosíthat, a hozzá társított díj közvetlenül kapcsolódik az objektumokhoz, a lekérdezés által feldolgozott szegélyekhez. Ez ugyanaz a folyamat, amelyet a Azure Cosmos DB az összes többi API-hoz használ. További információ: [kérelmek egységei a Azure Cosmos DBban](request-units.md).

Az RU-díj a bejárás munkahalmazán alapul, nem az eredményhalmazban. Ha például egy lekérdezés egyetlen csúcspont beszerzését célozza meg, de több mint egy másik objektumra van szüksége, akkor a díj az összes gráf-objektumon alapul, amelyet az egyetlen eredmény csúcspontjának kiszámításához fog végezni.

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>Mi a Graph-adatbázisok maximális mérete Azure Cosmos DB Gremlin API-ban?

A Azure Cosmos DB a [horizontális particionálások](partition-data.md) használatával automatikusan megjavítja a tárolási és az átviteli sebességre vonatkozó követelményeket. A számítási feladatok maximális átviteli sebességét és tárolókapacitását az adott tárolóhoz társított partíciók száma határozza meg. A Gremlin API-tárolók azonban az irányelvek egy adott készletével biztosítják a megfelelő teljesítménybeli teljesítményt. A particionálással és az ajánlott eljárásokkal kapcsolatos további információkért lásd: [particionálás Azure Cosmos db](partition-data.md) cikkben.

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>Hogyan biztosíthatok védelmet a Gremlin-illesztőprogramokat használó injekciós támadásokkal szemben?

A legtöbb natív Apache Tinkerpop Gremlin-illesztőprogram lehetővé teszi, hogy a lekérdezés végrehajtásához paraméterek szótárát adja meg. Ez egy példa arra, hogyan végezheti el a [Gremlin.net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) -ben és a [Gremlin-JavaScriptben](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/master/app.js).

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>Miért kapok a "Gremlin-lekérdezés fordítási hibája: nem található metódus" hibaüzenetet?

Azure Cosmos DB Gremlin API a Gremlin felületén definiált funkciók egy részhalmazát valósítja meg. A támogatott lépések és további információk a [Gremlin-támogatással](gremlin-support.md) foglalkozó cikkben találhatók.

A legjobb megoldás a szükséges Gremlin lépések újraírása a támogatott funkciókkal, mivel a Azure Cosmos DB az összes alapvető Gremlin-lépést támogatja.

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>Miért kapok "WebSocketException: a kiszolgáló a (z) 200-as állapotkódot adta vissza, amikor a rendszer a következő állapotkódot várta:" 101 ".

Ez a hiba valószínűleg akkor fordul elő, ha rossz végpontot használ. A hibát generáló végpont a következő mintával rendelkezik:

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

Ez a Graph-adatbázis dokumentumok végpontja.  A használni kívánt végpont a Gremlin végpont, amelynek formátuma a következő:

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>Miért kapok "RequestRateIsTooLarge" hibaüzenetet?

Ez a hiba azt jelenti, hogy a lefoglalt kérelmek másodpercenkénti száma nem elegendő a lekérdezés kiszolgálásához. Ez a hiba általában akkor látható, ha olyan lekérdezést futtat, amely az összes csúcspontot beszerzi:

```
// Query example:
g.V()
```

Ez a lekérdezés megkísérli a gráf összes csúcspontjának lekérését. Így a lekérdezés díja legalább a csúcspontok számának felel meg az RUs szempontjából. Az RU/s beállítást úgy kell beállítani, hogy foglalkozzon a lekérdezéssel.

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>Miért nem sikerül a Gremlin-illesztőprogram kapcsolatainak eldobása?

A Gremlin-kapcsolat egy WebSocket-kapcsolaton keresztül történik. Bár a WebSocket-kapcsolatok nem rendelkeznek adott időponttal, Azure Cosmos DB Gremlin API 30 perc inaktivitás után leáll az üresjárati kapcsolatokon.

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>Miért nem használhatom fluent API hívásokat a natív Gremlin-illesztőprogramokban?

A Azure Cosmos DB Gremlin API még nem támogatja a Fluent API-hívásokat. A Fluent API-hívások olyan belső formázási funkciót igényelnek, amely a Azure Cosmos DB Gremlin API által jelenleg nem támogatott bytecode-támogatás. Ugyanezen ok miatt a legújabb Gremlin-JavaScript-illesztőprogram is jelenleg nem támogatott.

### <a name="how-can-i-evaluate-the-efficiency-of-my-gremlin-queries"></a>Hogyan lehet kiértékelni a Gremlin-lekérdezések hatékonyságát?

A **executionProfile ()** előzetes verziójának használatával megadhatja a lekérdezés végrehajtási tervének elemzését. Ezt a lépést fel kell venni a Gremlin-lekérdezések végére, ahogy az a következő példában látható:

**Példa lekérdezésre**

```
g.V('mary').out('knows').executionProfile()
```

**Példa kimenetre**

```json
[
  {
    "gremlin": "g.V('mary').out('knows').executionProfile()",
    "totalTime": 8,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 3,
        "annotations": {
          "percentTime": 37.5
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 62.5
        },
        "counts": {
          "resultCount": 0
        },
        "storeOps": [
          {
            "count": 0,
            "size": 0,
            "time": 0.6
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      }
    ]
  }
]
```

A fenti profil kimenete azt mutatja, hogy mennyi idő telt el a csúcspont-objektumok, a peremhálózat-objektumok és a munkahalmaz méretének beszerzése során. Ez a Azure Cosmos DB lekérdezések standard díjszabásával kapcsolatos.

## <a id="cassandra"></a>Cassandra API

### <a name="what-is-the-protocol-version-supported-by-azure-cosmso-db-cassandra-api-is-there-a-plan-to-support-other-protocols"></a>Mi az Azure Cosmso DB Cassandra API által támogatott protokoll-verzió? Van terv a más protokollok támogatásához?

A Apache Cassandra API for Azure Cosmos DB támogatja a ma CQL 4-es verzióját. Ha visszajelzést szeretne küldeni más protokollok támogatásáról, tudassa velünk a [felhasználói visszajelzések](https://feedback.azure.com/forums/263030-azure-cosmos-db) segítségével, vagy küldjön egy e [-mailt a askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)címre.

### <a name="why-is-choosing-a-throughput-for-a-table-a-requirement"></a>Miért van szükség a szükséges átviteli sebességre a táblázatban?

Azure Cosmos DB beállítja a tároló alapértelmezett átviteli sebességét azon alapul, hogy hol hozza létre a táblát a-Portalról vagy a CQL.
A Azure Cosmos DB a teljesítményre és a késésre vonatkozó garanciákat biztosít, és a felső korláttal működik. Ez a garancia akkor lehetséges, ha a motor érvényesítheti a bérlő műveleteinek irányítását. Az átviteli sebesség beállításával biztosíthatja a garantált átviteli sebességet és a késést, mivel a platform fenntartja ezt a kapacitást, és garantálja a művelet sikerességét.
Rugalmasan módosíthatja az átviteli sebességet, és kihasználhatja az alkalmazás szezonális előnyeit, és megtakaríthatja a költségeket.

Az átviteli sebesség fogalmát Azure Cosmos DB cikkben [szereplő kérelmek egységei](request-units.md) ismertetik. A tábla átviteli sebessége a mögöttes fizikai partíciók között oszlik meg.

### <a name="what-is-the-default-rus-of-table-when-created-through-cql-what-if-i-need-to-change-it"></a>Mi a tábla alapértelmezett RU/s értéke, ha a CQL-on keresztül jött létre? What If módosítania kell?

A Azure Cosmos DB a másodpercenkénti kérelmek egységét (RU/s) használja az átviteli sebesség biztosításához. A CQL-en keresztül létrehozott táblák 400 RU-vel rendelkeznek. Az RU-t a portálról módosíthatja.

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

### <a name="what-happens-when-throughput-is-used-up"></a>Mi történik, ha a rendszer felhasználja az átviteli sebességet?

A Azure Cosmos DB a teljesítményre és a késésre vonatkozó garanciákat biztosít, és a felső korláttal működik. Ez a garancia akkor lehetséges, ha a motor érvényesítheti a bérlő műveleteinek irányítását. Ez az átviteli sebesség beállítása alapján lehetséges, ami biztosítja a garantált átviteli sebesség és a késés biztosítását, mivel a platform fenntartja ezt a kapacitást, és a sikeres működést garantálja.
Ha túllépi ezt a kapacitást, a rendszer túlterhelt hibaüzenetet jelenít meg, amely jelzi, hogy a rendszer felhasználta a kapacitást.
0x1001 túlterhelt: a kérés nem dolgozható fel, mert a kérelmek aránya nagy. Ebben az esetben alapvető fontosságú, hogy megtekintse, milyen műveletek és azok mennyisége okozza a problémát. Megtudhatja, milyen felhasználható kapacitásra lesz szüksége a kiosztott kapacitásról a portálon található mérőszámokkal. Ezt követően gondoskodnia kell arról, hogy a kapacitás az összes mögöttes partíción csaknem egyformán legyen felhasználva. Ha úgy látja, hogy az átviteli sebesség nagy részét egy partíció használja, a számítási feladatok eldöntése megtörténik.

A metrikák elérhetők, amelyek bemutatják, hogyan használják az átviteli sebességet óra, nap és hét nap alatt, a partíciók között vagy összesítve. További információ: [monitorozás és hibakeresés a metrikákkal a Azure Cosmos db](use-metrics.md).

A diagnosztikai naplókat a [Azure Cosmos db diagnosztikai naplózási](logging.md) cikkben ismertetjük.

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>Az elsődleges kulcs a Azure Cosmos DB partíciós kulcsának fogalmát képezi?

Igen, a partíciós kulcs az entitás megfelelő helyen való elhelyezésére szolgál. A Azure Cosmos DB a fizikai partíción tárolt megfelelő logikai partíciók megtalálására szolgál. A particionálási koncepció jól magyarázható a [partícióban és a skálázás Azure Cosmos db](partition-data.md) cikkben. Fontos, hogy a logikai partíciók még ma ne lépjék át a 10 GB-os korlátot.

### <a name="what-happens-when-i-get-a-quota-full-notification-indicating-that-a-partition-is-full"></a>Mi történik, ha teljes kvóta beolvasása "értesítés arról, hogy a partíció megtelt?

Azure Cosmos DB egy SLA-alapú rendszer, amely korlátlan léptéket biztosít, és garantálja a késést, az átviteli sebességet, a rendelkezésre állást és a konzisztenciát. Ez a korlátlan tárterület az adathorizontális felskálázáson alapul, és a particionálás a legfontosabb koncepcióként történik. A particionálási koncepció jól magyarázható a [partícióban és a skálázás Azure Cosmos db](partition-data.md) cikkben.

A 10 GB-os korlát azon entitások vagy elemek száma alapján, amelyeken logikai partíciókat kell betartani. Annak érdekében, hogy az alkalmazás jól méretezhető legyen, azt javasoljuk, hogy *ne* hozzon létre egy gyors partíciót úgy, hogy az összes információt egy partícióban tárolja, és lekérdezi azt. Ez a hiba csak akkor állhat elő, ha az adatai elferdítve vannak: ez egy partíciós kulcs (több mint 10 @ no__t-0GB) adatait is tartalmazhatja. Az adateloszlás a Storage Portal használatával található. Ennek a hibának a kijavításához újra létre kell hoznia a táblázatot, és ki kell választania egy szemcsés elsődleges (partíciós kulcs), amely lehetővé teszi az adatelosztást.

### <a name="is-it-possible-to-use-cassandra-api-as-key-value-store-with-millions-or-billions-of-individual-partition-keys"></a>Lehetséges több millió vagy több milliárd egyedi partíciós kulcs használatával Cassandra APIként használni a Key Value Store-t?

A Azure Cosmos DB a tárterület horizontális felskálázásával korlátlan mennyiségű adattárolást képes tárolni. Ez független az átviteli sebességtől. Igen, a megfelelő elsődleges/partíciós kulcs megadásával mindig egyszerűen használhatja a Cassandra API a kulcsok/értékek tárolására és lekérésére. Ezek az egyéni kulcsok a saját logikai partícióját kapják meg, és a fizikai partíciókat a probléma nélkül ülve.

### <a name="is-it-possible-to-create-more-than-one-table-with-apache-cassandra-api-of-azure-cosmos-db"></a>Lehet egynél több olyan táblát létrehozni, amelynek Apache Cassandra API Azure Cosmos DB?

Igen, több táblát is létrehozhat Apache Cassandra API. Ezeket a táblákat az átviteli sebesség és a tárolás egységként kezeli a rendszer.

### <a name="is-it-possible-to-create-more-than-one-table-in-succession"></a>Lehet egymás után több táblát létrehozni?

Azure Cosmos DB az erőforrás-vezérelt rendszer az adatkezelési és vezérlési sík tevékenységekhez. A tárolók, például a gyűjtemények, a táblák olyan futásidejű entitások, amelyek az adott átviteli kapacitáshoz vannak kiépítve. A tárolók gyors egymásutánban való létrehozása nem várt tevékenység és szabályozott. Ha olyan tesztekkel rendelkezik, amelyek azonnal elvégzik a táblázatok eldobását vagy létrehozását, próbálja meg a helyet kipróbálni.

### <a name="what-is-maximum-number-of-tables-that-can-be-created"></a>Legfeljebb hány tábla hozható létre?

Nincs fizikai korlát a táblák számánál, küldjön egy e-mailt a [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) címen, ha nagy számú táblával rendelkezik (ahol a teljes állandó méret 10 TB-nyi adaton halad át), amelyeket a szokásos 10-es vagy 100s-es verzióból kell létrehozni.

### <a name="what-is-the-maximum--of-keyspace-that-we-can-create"></a>Mi az a maximális lemezterület, amelyet létre tudunk hozni?

Nincs fizikai korlát a kulcstárolók számára, mint a metaadatok tárolói, küldjön e-mailt a [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) címen, ha valamilyen okból nagy számú szóköz van.

### <a name="is-it-possible-to-bring-in-lot-of-data-after-starting-from-normal-table"></a>Lehet-e nagy mennyiségű adattal bevezetni a normál táblázatból való Kiindulás után?

A rendszer automatikusan kezeli a tárolókapacitást, és növeli a további adattovábbítást. Így magabiztosan importálhatja a szükséges mennyiségű adatmennyiséget a csomópontok felügyelete és kiépítése nélkül, és így tovább.

### <a name="is-it-possible-to-supply-yaml-file-settings-to-configure-apache-casssandra-api-of-azure-cosmos-db-behavior"></a>Lehetséges YAML-beállításokat megadni a Azure Cosmos DB viselkedés Apache Casssandra API-ját?

A Azure Cosmos DB Apache Cassandra API egy platform-szolgáltatás. A protokollok szintjének kompatibilitását biztosítja a műveletek végrehajtásához. Elrejti a felügyelet, a figyelés és a konfiguráció összetettségét. Fejlesztőként vagy felhasználóként nem kell aggódnia a rendelkezésre állás, a sírkövek, a kulcs gyorsítótára, a sor gyorsítótára, a Bloom Filter és számos más beállítás alapján. A Azure Cosmos DB Apache Cassandra API a konfiguráció és a felügyelet nélkül szükséges olvasási és írási teljesítmény biztosítására koncentrál.

### <a name="will-apache-cassandra-api-for-azure-cosmos-db-support-node-additioncluster-statusnode-status-commands"></a>A Azure Cosmos DB támogatja a csomópontok hozzáadását, illetve a fürt állapotát és a csomópontok állapotára vonatkozó parancsokat is Apache Cassandra API?

A Apache Cassandra API egy olyan platform-szolgáltatás, amely a kapacitás megtervezését teszi lehetővé, és a rugalmasságra vonatkozó igényekre reagálva & Storage a Breeze. Az adatátviteli kapacitás kiépítésének Azure Cosmos DBához a szükséges. Ezután a nap folyamán tetszőleges számú alkalommal méretezheti azt anélkül, hogy a csomópontok hozzáadásával/törlésével vagy kezelésével kellene foglalkoznia. Ez azt jelenti, hogy nem kell a csomópontot, a Fürtfelügyelő eszközt is használni.

### <a name="what-happens-with-respect-to-various-config-settings-for-keyspace-creation-like-simplenetwork"></a>Mi történik a további konfigurációs beállításokkal kapcsolatban, például az egyszerű/hálózat?

Azure Cosmos DB globális elosztást biztosít a rendelkezésre állási és alacsony késési okok miatt. Nem szükséges replikákat vagy más dolgokat beállítania. Minden írás mindig tartósan a kvórumot minden olyan régióban, ahol a teljesítményre vonatkozó garanciákat ír.

### <a name="what-happens-with-respect-to-various-settings-for-table-metadata-like-bloom-filter-caching-read-repair-change-gc_grace-compression-memtable_flush_period-and-more"></a>Mi történik a táblázat metaadatainak különböző beállításaival, például a Bloom Filter, a gyorsítótárazás, a javítási változtatások, a gc_grace, a tömörítési memtable_flush_period és egyebek tekintetében?

A Azure Cosmos DB az olvasási/írási és az átviteli sebesség teljesítményének növelését teszi lehetővé anélkül, hogy meg kellene érintenie a konfigurációs beállításokat, és véletlenül módosítaná őket.

### <a name="is-time-to-live-ttl-supported-for-cassandra-tables"></a>Az élettartam (TTL) támogatott a Cassandra Tables esetében?

Igen, az élettartam támogatott.

### <a name="is-it-possible-to-monitor-node-status-replica-status-gc-and-os-parameters-earlier-with-various-tools-what-needs-to-be-monitored-now"></a>Lehetséges a csomópontok állapotának, a replika állapotának, a GC-nek és az operációs rendszer paramétereinek a figyelése a különböző eszközökkel? Mit kell figyelni most?

A Azure Cosmos DB egy olyan platform-szolgáltatás, amely segít a hatékonyság növelésében, és nem kell aggódnia az infrastruktúra kezelésével és figyelésével. Csak a portál metrikái által elérhető átviteli sebességről kell gondoskodnia, hogy megtudja, ha a szabályozása folyamatban van, és növelje vagy csökkentse az átviteli sebességet.
A [SLA](monitor-accounts.md)-kat figyeli.
A [metrikák](use-metrics.md) használata [diagnosztikai naplókat](logging.md)használ.

### <a name="which-client-sdks-can-work-with-apache-cassandra-api-of-azure-cosmos-db"></a>Mely ügyféloldali SDK-k működhetnek a Azure Cosmos DB Apache Cassandra API?

Az Apache Cassandra SDK az CQLv3-t használó ügyféloldali illesztőprogramjait használták az ügyfélalkalmazások számára. Ha más illesztőprogramokat használ, vagy ha problémák merülnek fel, küldjön e [-mailt askcosmosdbcassandra@microsoft.com-](mailto:askcosmosdbcassandra@microsoft.com)re.

### <a name="is-composite-partition-key-supported"></a>Támogatott-e az összetett partíciós kulcs?

Igen, használhat reguláris szintaxist összetett partíciós kulcs létrehozásához.

### <a name="can-i-use-sstableloader-for-data-loading"></a>Használhatom a sstableloader az betöltéshez?

Nem, a sstableloader nem támogatott.

### <a name="can-an-on-premises-apache-cassandra-cluster-be-paired-with-azure-cosmos-dbs-cassandra-api"></a>Párosítható a helyszíni Apache Cassandra-fürt a Azure Cosmos DB Cassandra APIával?

Jelenleg Azure Cosmos DB a Felhőbeli környezet számára a műveletek terhelése nélkül lett optimalizálva. Ha párosításra van szüksége, küldjön e-mailt a [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) értékre a forgatókönyv leírásával. Arra törekszünk, hogy a helyszíni/eltérő Cloud Cassandra-fürtöt a Cosomos adatbázis Cassandra APIához párosítsa.

### <a name="does-cassandra-api-provide-full-backups"></a>A Cassandra API teljes biztonsági mentést biztosít?

A Azure Cosmos DB az összes API-ban jelenleg négy óra intervallumban készített két ingyenes teljes biztonsági mentést biztosít. Ez biztosítja, hogy ne kelljen biztonsági mentési ütemtervet és egyéb dolgokat beállítania.
Ha módosítani kívánja az adatmegőrzést és a gyakoriságot, küldjön egy e [-mailt askcosmosdbcassandra@microsoft.com-](mailto:askcosmosdbcassandra@microsoft.com) re, vagy növelje a támogatási esetet. A biztonsági mentési képességgel kapcsolatos tudnivalókat az [automatikus online biztonsági mentés és visszaállítás Azure Cosmos db](online-backup-and-restore.md) cikkben találja.

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Hogyan kezeli a Cassandra API fiók a feladatátvételt, ha egy régió leáll?

A Azure Cosmos DB Cassandra API a Azure Cosmos DB globálisan elosztott platformján kölcsönöz. Annak érdekében, hogy az alkalmazás képes legyen az adatközpontok leállásának elvégzésére, legalább egy régiót be kell állítani a Azure Cosmos DB-portálon a [többrégiós Azure Cosmos db-fiókokkal fejlesztett](high-availability.md)fiókhoz. A régió prioritását beállíthatja a [többrégiós Azure Cosmos db-fiókokkal fejlesztett](high-availability.md)portál használatával.

A fiókhoz tetszőleges számú régiót adhat hozzá, és szabályozhatja, hogy a feladatátvételi prioritás biztosításával miként lehet átadni a feladatokat. Az adatbázis használatához meg kell adnia egy alkalmazást is. Ha így tesz, az ügyfelek nem fognak tapasztalni állásidőt.

### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Az Apache Cassandra API indexeli az entitás összes attribútumát alapértelmezés szerint?

Cassandra API azt tervezi, hogy támogatja a másodlagos indexelést, hogy bizonyos attribútumokon a szelektív indexet hozzon létre. 


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Helyileg is használhatom az új Cassandra API SDK-t az emulátorral?

Igen, ez támogatott.

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-change-feed-and-other-functionality-will-these-capabilities-be-added-to-the-cassandra-api"></a>Úgy tűnik, hogy a platform számos képességgel rendelkezik, például a hírcsatornák és egyéb funkciók módosítására. Azure Cosmos DB A rendszer hozzáadja ezeket a képességeket a Cassandra APIhoz?

A Apache Cassandra API ugyanazokat az CQL funkciókat biztosítja, mint az Apache Cassandra. Megtervezjük, hogy megvizsgáljuk a jövőben a különböző képességek támogatásának megvalósíthatóságát.

### <a name="feature-x-of-regular-cassandra-api-isnt-working-as-today-where-can-the-feedback-be-provided"></a>A rendszeres Cassandra API funkció x nem működik a mai napon, hol lehet a visszajelzést megadni?

Visszajelzés küldése a [felhasználói](https://feedback.azure.com/forums/263030-azure-cosmos-db)visszajelzések használatával.

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md
