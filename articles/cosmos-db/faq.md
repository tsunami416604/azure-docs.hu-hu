---
title: Gyakori kérdések az Azure Cosmos DB különböző API-jairól
description: Válaszok at kaphat az Azure Cosmos DB globálisan elosztott, többmodelles adatbázis-szolgáltatással kapcsolatos gyakori kérdésekre. Ismerje meg a kapacitást, a teljesítményszinteket és a méretezést.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 75cef47c1ed2b6da054442a4a1215141e67ad1b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498576"
---
# <a name="frequently-asked-questions-about-different-apis-in-azure-cosmos-db"></a>Gyakori kérdések az Azure Cosmos DB különböző API-jaival kapcsolatban

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Melyek az Azure Cosmos DB tipikus használati esetei?

Az Azure Cosmos DB jó választás új webes, mobil-, játék- és IoT-alkalmazásokhoz, ahol fontos az automatikus méretezés, a kiszámítható teljesítmény, a ezredmásodperces válaszidők gyors sorrendje és a sémamentes adatok lekérdezésének lehetősége. Az Azure Cosmos DB gyors fejlesztést és az alkalmazásadat-modellek folyamatos iterációjának támogatását teszi learathatja. A felhasználók által létrehozott tartalmat és adatokat kezelő alkalmazások [gyakori használati esetek az Azure Cosmos DB számára.](use-cases.md)

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Hogyan kínál kiszámítható teljesítményt az Azure Cosmos DB?

A [kérelemegység](request-units.md) (RU) az Azure Cosmos DB átviteli igényének mértéke. Az 1RU átviteli sebességű átviteli szint megfelel az 1 KB-os dokumentum GET átviteli. Az Azure Cosmos DB minden művelete, beleértve az olvasást, az írásokat, az SQL-lekérdezéseket és a tárolt eljárás-végrehajtásokat, egy determinisztikus RU-értékkel rendelkezik, amely a művelet végrehajtásához szükséges átviteli értéken alapul. Ahelyett, hogy a CPU, az IO és a memória, és hogyan befolyásolják az alkalmazás átviteli sebességét, akkor gondoljon egyetlen RU-mérték.

Konfigurálhatja az egyes Azure Cosmos-tárolók kiosztott átviteli a másodpercenkénti átviteli műveletek szempontjából. Bármilyen méretű alkalmazások esetében értékelheti az egyes kérelmeket a RU-értékek mérésére, és létrehozhat egy tárolót a kérelemegységek teljes összegének kezelésére az összes kérelemben. A tároló átviteli igényének az alkalmazás fejlődésével történő felskálázható vagy skálázható is felskálázhatja vagy csökkentheti az átviteli szintet. A kérelemegységekről és a tároló igényeinek meghatározásához az [átviteli kalkulátorsegítségével](https://www.documentdb.com/capacityplanner)kapcsolatos további információkért.

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Hogyan támogatja az Azure Cosmos DB a különböző adatmodelleket, például a kulcsot/értéket, az oszlopos, a dokumentumot és a grafikont?

Kulcs/érték (tábla), oszlopos, dokumentum- és gráfadat-modellek mind natívan támogatottak az Azure Cosmos DB által épülő ARS (atomok, rekordok és szekvenciák) kialakítása miatt. Az atomok, rekordok és szekvenciák könnyen leképezhetők és kivetíthetők különböző adatmodellekre. A modellek egy részhalmazának API-jai jelenleg elérhetők (SQL, MongoDB, Table és Gremlin), és a jövőben további adatmodellekre jellemző egyéb api-k is elérhetők lesznek.

Az Azure Cosmos DB rendelkezik egy séma független indexelő motor, amely képes automatikusan indexeli az összes beadott adatokat anélkül, hogy a fejlesztőséma vagy másodlagos indexek. A motor logikai indexelrendezések (fordított, oszlopos, fa) készletére támaszkodik, amelyek leválasztják a tárolási elrendezést az index- és lekérdezésfeldolgozó alrendszerektől. Cosmos DB is képes támogatni egy sor vezetékes protokollok és API-k egy bővíthető módon, és fordítsa le őket hatékonyan az alapvető adatmodell (1) és a logikai index elrendezések (2), így egyedülállóan képes támogatni több adatmodell natív módon.

### <a name="can-i-use-multiple-apis-to-access-my-data"></a>Több API-t is használhatok az adataim eléréséhez?

Az Azure Cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása. Ahol többmodelles azt jelenti, hogy az Azure Cosmos DB több API-t és több adatmodellt támogat, a különböző API-k különböző adatformátumokat használnak a tárolási és vezetékes protokollhoz. Például az SQL a JSON-t használja, a MongoDB a BSON-t, a Table az EDM-et, cassandra a CQL-t, a Gremlin a GraphSON-t. Ennek eredményeképpen azt javasoljuk, hogy ugyanazt az API-t használja az adott fiókban lévő adatokhoz való összes hozzáféréshez.

Minden API egymástól függetlenül működik, kivéve a Gremlin és az SQL API- t, amelyek interoperábilisak.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Megfelel az Azure Cosmos DB HIPAA-kompatibilis?

Igen, az Azure Cosmos DB HIPAA-kompatibilis. A HIPAA az egyéni és beazonosítható egészségügyi adatok használatára, nyilvánosságra hozatalára és védelmére vonatkozó követelményeket állapítja meg. További információt a [Microsoft Adatvédelmi központban talál.](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA)

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Milyen tárolási korlátok az Azure Cosmos DB?

Nincs korlátozva, hogy a tároló tárolhatja az Azure Cosmos DB-ben tárolt adatok teljes mennyiségét.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Milyen átviteli korlátok vannak az Azure Cosmos DB-ben?

Nincs korlátozva a teljes átviteli, amely egy tároló támogatja az Azure Cosmos DB. A legfontosabb ötlet az, hogy a számítási feladatok nagyjából egyenletesen között kellően nagy számú partíciókulcsok között.

### <a name="are-direct-and-gateway-connectivity-modes-encrypted"></a>Titkosítva vannak a közvetlen és az átjáró kapcsolati módjai?

Igen mindkét mód mindig teljesen titkosított.

### <a name="how-much-does-azure-cosmos-db-cost"></a>Mennyibe kerül az Azure Cosmos DB költsége?

A részleteket az [Azure Cosmos DB díjszabási részletek](https://azure.microsoft.com/pricing/details/cosmos-db/) oldalon találja. Az Azure Cosmos DB használati díjait a kiosztott tárolók száma, a tárolók online állapotban töltött óráinak száma és az egyes tárolók kiosztott átviteli forgalma határozza meg.

### <a name="is-a-free-account-available"></a>Van egy szabad számla elérhető?

Igen, díjmentesen, kötelezettségvállalás nélkül regisztrálhat egy korlátozott időre szóló fiókra. A regisztrációhoz látogasson el [ingyenesen az Azure Cosmos DB-be,](https://azure.microsoft.com/try/cosmosdb/) vagy olvasson tovább az [Azure Cosmos DB kipróbálásával kapcsolatos gyakori kérdésekben.](#try-cosmos-db)

Ha most jön az Azure-ba, regisztrálhat egy [ingyenes Azure-fiókot,](https://azure.microsoft.com/free/)amely 30 napot és kreditet biztosít az összes Azure-szolgáltatás kipróbálásához. Ha Rendelkezik Visual Studio-előfizetéssel, akkor jogosult az [ingyenes Azure-kreditekre](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) is, amelyeket bármely Azure-szolgáltatásban használhat.

Az [Azure Cosmos DB-emulátor](local-emulator.md) használatával ingyenesen fejlesztheti és tesztelheti az alkalmazást helyileg, anélkül, hogy Azure-előfizetést hozna létre. Amikor már elégedett az alkalmazás működésével az Azure Cosmos DB Emulatorban, átválthat az Azure Cosmos DB-fiók használatára a felhőben.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Hogyan kaphatok további segítséget az Azure Cosmos DB-vel kapcsolatban?

Feltenni egy technikai kérdést, akkor tegye az egyik a két kérdés-válasz fórumok:

* [MSDN fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Verem túlcsordulás](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack túlcsordulás a legjobb programozási kérdések. Győződjön meg róla, hogy a kérdés [on-topic,](https://stackoverflow.com/help/on-topic) és [a lehető legtöbb részletet, így a kérdés egyértelmű és megválaszolható](https://stackoverflow.com/help/how-to-ask).

Új funkciók igényléséhez hozzon létre egy új kérelmet a [Felhasználói hang](https://feedback.azure.com/forums/263030-azure-cosmos-db).

Ha a fiókjával van probléma, nyújtson be [támogatási kérelmet](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) az Azure Portalon.

## <a name="try-azure-cosmos-db-subscriptions"></a><a id="try-cosmos-db"></a>Próbálja ki az Azure Cosmos DB-előfizetéseit

Mostantól ingyenes és kötelezettségvállalások nélkül élvezheti az Azure Cosmos DB időkorlátos élményét. Ha elő szeretne jelentkezni egy Azure Cosmos DB-előfizetésre, nyissa meg [az Azure Cosmos DB-t ingyenesen,](https://azure.microsoft.com/try/cosmosdb/) és használjon bármilyen személyes Microsoft-fiókot (MSA). Ez az előfizetés elkülönül az [Ingyenes Azure-próbaverziótól,](https://azure.microsoft.com/free/)és egy ingyenes Azure-próbaverzióval vagy egy Azure-előfizetéssel együtt használható.

Próbálja ki az Azure Cosmos DB-előfizetések jelennek meg az Azure Portalon a következő egyéb előfizetések társított a felhasználói azonosítót.

Az alábbi feltételek az Azure Cosmos DB-előfizetések kipróbálására vonatkoznak:

* A fiókhoz való hozzáférés személyes Microsoft-fiókokhoz (MSA) biztosítható. Ne használjon Active Directory (AAD) fiókokat vagy vállalati AAD-bérlőkhöz tartozó fiókokat, előfordulhat, hogy olyan korlátozásokkal rendelkeznek, amelyek letilthatják a hozzáférés megadását.
* Előfizetésenként egy [átviteli keresztüli tároló](./set-throughput.md#set-throughput-on-a-container) sql- és Gremlin API-és table-fiókok.
* Előfizetésenként legfeljebb három [átviteli kiépített gyűjtemény](./set-throughput.md#set-throughput-on-a-container) a MongoDB-fiókokhoz.
* Előfizetésenként egy [átviteli-átviteli kiépített adatbázis.](./set-throughput.md#set-throughput-on-a-database) Átviteli létesített adatbázisok tetszőleges számú tárolók belül.
* 10 GB tárolókapacitás.
* A globális replikáció a következő [Azure-régiókban](https://azure.microsoft.com/regions/)érhető el: USA középső régiói, Észak-Európa és Délkelet-Ázsia
* A tároló szintjén kiépített 5 K RU/s maximális átviteli teljesítmény.
* Az adatbázis szintjén kiépített maximális átviteli teljesítmény 20 K RU/s.
* Az előfizetések 30 nap után lejárnak, és összesen legfeljebb 31 napra meghosszabbíthatók.
* Az Azure-támogatási jegyek nem hozhatók létre az Azure Cosmos DB-fiókok kipróbálásához; azonban a meglévő támogatási csomagokkal rendelkező előfizetők számára támogatást nyújtanak.

## <a name="set-up-azure-cosmos-db"></a>Az Azure Cosmos DB beállítása

### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Hogyan regisztrálhatok az Azure Cosmos DB-re?

Az Azure Cosmos DB elérhető az Azure Portalon. Először regisztráljon egy Azure-előfizetésre. Miután regisztrált, hozzáadhat egy Azure Cosmos DB-fiókot az Azure-előfizetéséhez.

### <a name="what-is-a-master-key"></a>Mi a főkulcs?

A főkulcs egy biztonsági jogkivonat, amellyel egy fiók összes erőforrása elérhető. A kulccsal rendelkező személyek olvasási és írási hozzáféréssel rendelkeznek az adatbázisfiók összes erőforrásához. Legyen óvatos a főkulcsok terjesztésekor. Az elsődleges főkulcs és a másodlagos főkulcs az [Azure Portal][azure-portal] **Keys** paneljén érhetők el. A kulcsokkal kapcsolatos további információkért lásd: [Hozzáférési kulcsok megtekintése, másolása és újragenerálása](manage-with-cli.md#list-account-keys).

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>Melyek azok a régiók, amelyeket a PreferredLocations beállítható?

A PreferredLocations érték beállítható az Azure-régiók bármelyikében, ahol a Cosmos DB érhető el. Az elérhető régiók listáját az [Azure-régiók ban.](https://azure.microsoft.com/regions/)

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Van-e valami, amit tudnia kell, amikor adatokat oszt a világ minden tájáról az Azure adatközpontokon keresztül?

Az Azure Cosmos DB az [Azure-régiók](https://azure.microsoft.com/regions/) ban megadott módon jelen van az összes Azure-régióban. Mivel ez az alapvető szolgáltatás, minden új adatközpont rendelkezik egy Azure Cosmos DB jelenléttel.

Ha beállít egy régiót, ne feledje, hogy az Azure Cosmos DB tiszteletben tartja a szuverén és kormányzati felhők. Ez azt, hogy ha létrehoz egy fiókot egy [szuverén régióban,](https://azure.microsoft.com/global-infrastructure/)akkor nem replikálható ki, hogy [a szuverén régióban.](https://azure.microsoft.com/global-infrastructure/) Hasonlóképpen nem engedélyezheti a replikációt más szuverén helyekre egy külső fiókból.

### <a name="is-it-possible-to-switch-from-container-level-throughput-provisioning-to-database-level-throughput-provisioning-or-vice-versa"></a>Lehet-e váltani a tárolószintű átviteli átviteli kiépítés az adatbázis szintű átviteli átviteli kiépítés? Vagy fordítva.

A tároló- és adatbázisszintű átviteli átviteli kiépítés külön ajánlatok, és ezek bármelyike közötti váltáshoz adatok at kell áthelyezni a forrásról a célra. Ami azt jelenti, hogy létre kell hoznia egy új adatbázist vagy egy új tárolót, majd át kell telepítenie az adatokat [a tömeges végrehajtó könyvtár](bulk-executor-overview.md) vagy az Azure Data [Factory](../data-factory/connector-azure-cosmos-db.md)használatával.

### <a name="does-azure-cosmosdb-support-time-series-analysis"></a>Támogatja az Azure CosmosDB az idősorozat-elemzést?

Igen Az Azure CosmosDB támogatja az idősorozat-elemzést, itt egy példa az [idősorozat-minta.](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/samples/Patterns) Ez a minta bemutatja, hogyan lehet a változáscsatorna használatával összesített nézeteket készíteni az idősorozat-adatokról. Ezt a megközelítést kiterjesztheti a spark streamelés vagy egy másik stream adatfeldolgozó használatával.

## <a name="what-are-the-azure-cosmos-db-service-quotas-and-throughput-limits"></a>Mik az Azure Cosmos DB szolgáltatáskvóták és átviteli-korlát

További információkért tekintse meg az Azure Cosmos DB [szolgáltatási kvótákat](concepts-limits.md) és a [tárolónkénti és adatbázis-cikkekre vonatkozó korlátozásokat.](set-throughput.md#comparison-of-models)

## <a name="sql-api"></a>SQL API

### <a name="how-do-i-start-developing-against-the-sql-api"></a>Hogyan kezdhetek el fejlődni az SQL API-val szemben?

Először regisztrálnia kell egy Azure-előfizetésre. Miután feliratkozott egy Azure-előfizetésre, hozzáadhat egy SQL API-tárolót az Azure-előfizetéséhez. Az Azure Cosmos DB-fiók hozzáadásáról az [Azure Cosmos-adatbázisfiók létrehozása című témakörben](create-sql-api-dotnet.md#create-account)talál.

[SDK-k](sql-api-sdk-dotnet.md) a .NET, Python, Node.js, JavaScript és Java esetében érhetők el. A fejlesztők a [RESTful HTTP API-k segítségével](/rest/api/cosmos-db/) is kommunikálhatnak az Azure Cosmos DB különböző platformokról és nyelvekről származó erőforrásokkal.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Hozzáférhetek néhány kész mintához, hogy előnyt szerezzenek?

Az SQL API [.NET,](sql-api-dotnet-samples.md) [Java,](https://github.com/Azure/azure-documentdb-java) [Node.js](sql-api-nodejs-samples.md)és [Python](sql-api-python-samples.md) SDK-k mintái elérhetők a GitHubon.

### <a name="does-the-sql-api-database-support-schema-free-data"></a>Támogatja az SQL API-adatbázis a sémamentes adatokat?

Igen, az SQL API lehetővé teszi, hogy az alkalmazások tetszőleges JSON-dokumentumokat tároljanak sémadefiníciók vagy tippek nélkül. Az adatok azonnal elérhetők az Azure Cosmos DB SQL lekérdezési felületen keresztül lekérdezéshez.

### <a name="does-the-sql-api-support-acid-transactions"></a>Támogatja az SQL API az ACID-tranzakciókat?

Igen, az SQL API támogatja a JavaScript által tárolt eljárásokban és eseményindítókban kifejezett dokumentumok közötti tranzakciókat. A tranzakciók hatóköre egyetlen partícióra van kódolva minden tárolón belül, és az ACID szemantikával "mindent vagy semmit" néven hajtják végre, elkülönítve más egyidejűleg végrehajtó kódtól és felhasználói kérelmektől. Ha a JavaScript-alkalmazáskód kiszolgálóoldali végrehajtása kivételt tesz ki, a rendszer a teljes tranzakciót visszagörgeti. 

### <a name="what-is-a-container"></a>Mi az a tároló?

A tároló dokumentumok és a hozzájuk tartozó JavaScript-alkalmazáslogika csoportja. A tároló egy számlázható entitás, ahol a [költséget](performance-levels.md) az átviteli és a használt tároló határozza meg. A tárolók egy vagy több partícióra vagy kiszolgálóra is kiterjedhetnek, és skálázhatók gyakorlatilag korlátlan mennyiségű tárhely vagy átviteli hang kezeléséhez.

* AZ SQL API-k egy tároló leképezi egy tárolót.
* A Cosmos DB MongoDB-fiókok API-ja számára egy tároló leképezi a gyűjteményt.
* Cassandra és table API-fiókok esetén egy tároló leképezi egy tábla.
* A Gremlin API-fiókok, egy tároló leképezi a Graph.

A tárolók az Azure Cosmos DB számlázási entitásai is. Minden tároló számlázott óránként, a kiosztott átviteli és a használt tárhely alapján. További információ: [Azure Cosmos DB Pricing](https://azure.microsoft.com/pricing/details/cosmos-db/).

### <a name="how-do-i-create-a-database"></a>Hogyan hozható létre adatbázis?

Adatbázisokat hozhat létre az [Azure Portalhasználatával,](https://portal.azure.com)a tároló [hozzáadása](create-sql-api-java.md#add-a-container), az [Azure Cosmos DB SDK-k](sql-api-sdk-dotnet.md)vagy a [REST API-k](/rest/api/cosmos-db/)egyikében leírtak szerint.

### <a name="how-do-i-set-up-users-and-permissions"></a>Hogyan állíthatom be a felhasználókat és engedélyeket?

A [Cosmos DB API SDK-k](sql-api-sdk-dotnet.md) vagy a REST [API-k](/rest/api/cosmos-db/)egyikével hozhat létre felhasználókat és engedélyeket.

### <a name="does-the-sql-api-support-sql"></a>Támogatja az SQL API az SQL-t?

Az SQL API-fiókok által támogatott SQL lekérdezési nyelv az SQL Server által támogatott lekérdezési funkció továbbfejlesztett részhalmaza. Az Azure Cosmos DB SQL lekérdezési nyelv gazdag hierarchikus és relációs operátorok és bővíthetőség javascript-alapú, felhasználó által definiált függvények (UDF) használatával. A JSON-nyelvtan lehetővé teszi a JSON-dokumentumok fákként való modellezését címkézett csomópontokkal, amelyeket az Azure Cosmos DB automatikus indexelési technikája és az Azure Cosmos DB SQL-lekérdezési dialektusa is használ. Az SQL-nyelvhelyesség használatáról az [SQL Query][query] cikkében olvashat.

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>Támogatja az SQL API az SQL összesítési függvényeket?

Az SQL API támogatja az alacsony késleltetésű összesítést `COUNT` `MIN`bármilyen `MAX` `AVG`méretekben `SUM` az összesítő függvények , , , és az SQL nyelvtan on keresztül. További információt az [Összesítő függvények című témakörben talál.](sql-query-aggregates.md)

### <a name="how-does-the-sql-api-provide-concurrency"></a>Hogyan biztosítja az SQL API egyidejűséget?

Az SQL API támogatja az optimista egyidejűség-vezérlést (OCC) HTTP entitáscímkéken vagy ETag-eken keresztül. Minden SQL API-erőforrás rendelkezik egy ETag, és az ETag van beállítva a szerveren minden alkalommal, amikor egy dokumentumot frissít. Az ETag fejléc és az aktuális érték minden válaszüzenetben szerepel. ETags használható az If-Match fejléc, hogy a kiszolgáló dönti el, hogy egy erőforrást frissíteni kell. Az If-Match érték az az ETag érték, amelyhez össze kell hasonlítani. Ha az ETag érték megegyezik a kiszolgáló ETag értékével, az erőforrás frissül. Ha az ETag már nem aktuális, a kiszolgáló elutasítja a műveletet egy "HTTP 412 Precondition failure" válaszkóddal. Az ügyfél ezután újrabehívja az erőforrást az erőforrás aktuális ETag értékének beolvasásához. Emellett az ETag-ek az If-None-Match fejléccel is használhatók annak meghatározásához, hogy szükség van-e egy erőforrás újbóli betöltésére.

Ha optimista egyidejűséget szeretne használni a .NET-ben, használja az [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) osztályt. A .NET-minta: [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) a DocumentManagement-minta a GitHubon.

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>Hogyan hajthatok végre tranzakciókat az SQL API-ban?

Az SQL API támogatja a nyelvbe integrált tranzakciók at JavaScript által tárolt eljárások és eseményindítók. A parancsfájlokon belüli összes adatbázis-művelet pillanatkép-elkülönítés alatt történik. Ha egypartíciós tárolóról van szó, a végrehajtás hatóköre a tárolóba kerül. Ha a tároló particionálva van, a végrehajtás hatóköre a tárolón belüli azonos partíció-kulcs értékkel rendelkező dokumentumokra lesz. A tranzakció kezdetekor pillanatkép készül a dokumentumverziókról (ETag-ek), és csak akkor lesznek véglegesítve, ha a parancsfájl sikeres. Ha a JavaScript hibát jelez, a tranzakció vissza lesz állítva. További információ: [Server-side JavaScript programming for Azure Cosmos DB.](stored-procedures-triggers-udfs.md)

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Hogyan helyezhetek el dokumentumokat tömegesen a Cosmos DB-be?

Az Azure Cosmos DB-be az alábbi módokon szúrhat be dokumentumokat tömegesen:

* A tömeges végrehajtó eszköz, a [.NET tömeges végrehajtó és](bulk-executor-dot-net.md) [a Java-kódor használatával című részben leírtak szerint](bulk-executor-java.md)
* Az adatok áttelepítése eszköz, az [Azure Cosmos DB Adatbázis-áttelepítési eszközében](import-data.md)leírtak szerint.
* Tárolt eljárások, az [Azure Cosmos DB kiszolgálóoldali JavaScript-programozásában](stored-procedures-triggers-udfs.md)leírtak szerint.

### <a name="does-the-sql-api-support-resource-link-caching"></a>Támogatja az SQL API az erőforráskapcsolat gyorsítótárazását?

Igen, mivel az Azure Cosmos DB egy RESTful szolgáltatás, az erőforrás-kapcsolatok nem módosíthatók, és gyorsítótárazhatók. Az SQL API-ügyfelek megadhatnak egy "If-None-Match" fejlécet az olvasásokhoz bármely erőforráshoz hasonló dokumentumhoz vagy tárolóhoz, majd frissíthetik a helyi másolataikat a kiszolgáló verziójának módosítása után.

### <a name="is-a-local-instance-of-sql-api-available"></a>Elérhető az SQL API helyi példánya?

Igen. Az [Azure Cosmos DB-emulátor](local-emulator.md) a Cosmos DB szolgáltatás kiváló minőségű emulációját biztosítja. Támogatja az Azure Cosmos DB-vel azonos funkciókat, beleértve a JSON-dokumentumok létrehozásának és lekérdezésének támogatását, a gyűjtemények kifejlesztését és méretezését, valamint a tárolt eljárások és eseményindítók végrehajtását. Alkalmazásokat fejleszthet és tesztelhet az Azure Cosmos DB-emulátor használatával, és globális szinten telepítheti őket az Azure-ba az Azure Cosmos DB csatlakozási végpontjának egyetlen konfigurációmódosításával.

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>Miért kerekítik a dokumentum hosszú lebegőpontos értékeit a portál adatkezelőjéből való megtekintéskor?

Ez a JavaScript korlátozása. A JavaScript az IEEE 754-ben meghatározott dupla pontosságú lebegőpontos formátumú számokat használ, és csak a -(2<sup>53</sup> - 1) és a 2<sup>53</sup>-1 (azaz a 9007199254740991) közötti számokat tudja biztonságosan tárolni.

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>Hol engedélyezettek az engedélyek az objektumhierarchiában?

Engedélyek létrehozása használatával ResourceTokens engedélyezett a tároló szintjén, és annak leszármazottai (például a dokumentumok, mellékletek). Ez azt jelenti, hogy az adatbázison vagy a fiók szintjén nem lehet engedélyt létrehozni.

## <a name="azure-cosmos-dbs-api-for-mongodb"></a>MongoDB-hez készült Azure Cosmos DB API

### <a name="what-is-the-azure-cosmos-dbs-api-for-mongodb"></a>Mi az Azure Cosmos DB MongoDB API-ja?

Az Azure Cosmos DB MongoDB-hoz készült API-ja egy vezetékprotokoll-kompatibilitási réteg, amely lehetővé teszi az alkalmazások számára, hogy a meglévő, közösség által támogatott SDK-k és illesztőprogramok használatával egyszerűen és átláthatóan kommunikáljanak a natív Azure Cosmos adatbázismotorral. A fejlesztők mostantól a meglévő MongoDB eszközláncok és képességek segítségével hozhatnak létre olyan alkalmazásokat, amelyek kihasználják az Azure Cosmos DB előnyeit. A fejlesztők élvezhetik az Azure Cosmos DB egyedi képességeit, amelyek magukban foglalják a globális terjesztést többfős replikációval, automatikus indexeléssel, biztonsági mentés karbantartásával, pénzügyileg támogatott szolgáltatásiszint-szerződésekkel (SLA-kkal) stb.

### <a name="how-do-i-connect-to-my-database"></a>Hogyan csatlakozhatok az adatbázisomhoz?

Az Azure Cosmos DB MongoDB-alapú API-jával a leggyorsabban csatlakozhat a Cosmos-adatbázishoz, ha az [Azure Portalra](https://portal.azure.com)lép. Nyissa meg a fiókját, majd a bal oldali navigációs menüben kattintson a **Rövid útmutató parancsra.** A rövid útmutató a legjobb módja annak, hogy kódrészleteket kapjon az adatbázishoz való csatlakozáshoz.

Az Azure Cosmos DB szigorú biztonsági követelményeket és szabványokat érvényesít. Az Azure Cosmos DB-fiókok hitelesítést és biztonságos kommunikációt igényelnek SSL-en keresztül, ezért győződjön meg róla, hogy a TLSv1.2-t használja.

További információ: [Csatlakozás a Cosmos-adatbázishoz az Azure Cosmos DB MongoDB API-jával](connect-mongodb-account.md)című témakörben talál.

### <a name="are-there-additional-error-codes-that-i-need-to-deal-with-while-using-azure-cosmos-dbs-api-for-mongodb"></a>Vannak-e további hibakódok, amelyekkel foglalkoznom kell az Azure Cosmos DB MongoDB-hoz való API-jának használata közben?

A közös MongoDB hibakódokkal együtt az Azure Cosmos DB MongoDB API-ja saját hibakódokkal rendelkezik:

| Hiba               | Kód  | Leírás  | Megoldás  |
|---------------------|-------|--------------|-----------|
| TooManyKéri     | 16500 | A felhasznált kérelemegységek teljes száma meghaladja a tároló kiépített kérelemegység-díját, és a rendszer szabályozta. | Fontolja meg a tárolóhoz vagy tárolók készletéhez rendelt átviteli kábel méretezését az Azure Portalról, vagy újra megkísérelje újra. |
| ExceededMemoryLimit | 16501 | Több-bérlős szolgáltatásként a művelet átment az ügyfél memóriaallokációja felett. | Csökkentse a művelet hatókörét szigorúbb lekérdezési feltételekkel vagy az [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)támogatási szolgálatával. <br><br><em> &nbsp; &nbsp;Példa: &nbsp; &nbsp;db.getCollection('users').aggregate([<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$match: {név: "Andy"}}, <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$sort: {életkor: -1}}<br>&nbsp;&nbsp;&nbsp;&nbsp;])</em>) |

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>A MongoDB Simba illesztőprogramja támogatott az Azure Cosmos DB MongoDB API-jával való használatra?

Igen, használhatja a Simba Mongo ODBC illesztőprogramját az Azure Cosmos DB MongoDB API-jával

## <a name="table-api"></a><a id="table"></a>Tábla API

### <a name="how-can-i-use-the-table-api-offering"></a>Hogyan használhatom a Table API-ajánlatot?

Az Azure Cosmos DB Table API érhető el az [Azure Portalon.][azure-portal] Először regisztrálnia kell egy Azure-előfizetésre. Miután regisztrált, hozzáadhat egy Azure Cosmos DB Table API-fiókot az Azure-előfizetéséhez, majd táblákat adhat hozzá a fiókjához.

A támogatott nyelveket és a kapcsolódó gyorsindításokat az [Azure Cosmos DB Table API bevezetés](table-introduction.md)című útmutatójában találja.

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>Szükségem van egy új SDK-ra a Table API használatához?

Nem, a meglévő tároló SDK-k továbbra is működniük kell. Azonban ajánlott, hogy az egyik mindig megkapja a legújabb SDK-k a legjobb támogatást, és sok esetben kiváló teljesítményt. Tekintse meg az elérhető nyelvek listáját az [Azure Cosmos DB Table API bevezetés című témakörében.](table-introduction.md)

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Miben tér el a Table API és az Azure Table Storage működése?

Vannak olyan viselkedésbeli különbségek, amelyeket az Azure Table storage-ból érkező felhasználók, akik táblákat szeretnének létrehozni az Azure Cosmos DB Table API-val, tisztában kell lenniük a következőkkel:

* Az Azure Cosmos DB Table API egy fenntartott kapacitásmodellt használ a garantált teljesítmény biztosítása érdekében, de ez azt jelenti, hogy a tábla létrehozásaután a kapacitásért fizetni kell, még akkor is, ha a kapacitás nincs használatban. Az Azure Table storage-szal csak a felhasznált kapacitásért kell fizetni. Ez segít megmagyarázni, hogy a Table API miért tud 10 ms olvasási és 15 ms-os írási SLA-t kínálni a 99 százalékos szinten, míg az Azure Table storage 10 másodperces SLA-t kínál. Ennek következtében azonban a Table API-táblák, még az üres táblák kérések nélkül, pénzbe kerül annak biztosítása érdekében, hogy a kapacitás elérhető a kérések kezelésére az Azure Cosmos DB által kínált SLA.
* A table API által visszaadott lekérdezési eredmények nincsenek rendezve partíciókulcs/sorkulcs-sorrendben, mivel azok az Azure Table storage-ban vannak.
* A sorkulcsok legfeljebb 255 bájtot érhetnek el
* A kötegek legfeljebb 2 MB-t használhatnak
* A CORS jelenleg nem támogatott
* Az Azure Table storage-ban lévő táblanevek nem érzékenyek a kis- és nagybetűkre, de az Azure Cosmos DB Table API-ban találhatók.
* Az Azure Cosmos DB belső kódolási formátumainak egy része, például a bináris mezők, jelenleg nem olyan hatékonyak, mint az egyik szeretné. Ezért ez váratlan korlátozásokat okozhat az adatok méretében. Például jelenleg nem lehetett használni a táblaentitás teljes megegy megét bináris adatok tárolására, mert a kódolás növeli az adatok méretét.
* Az entitástulajdonság neve (Azonosító' jelenleg nem támogatott
* A TableQuery TakeCount nem korlátozódik 1000-re

A REST API-t tekintve számos végpont/lekérdezési lehetőség van, amelyeket az Azure Cosmos DB Table API nem támogat:

| Pihenési módszer(ek) | Rest-végpont/lekérdezési beállítás | Dokumentum URL-címei | Magyarázat |
| ------------| ------------- | ---------- | ----------- |
| GET, PUT | /?restype=service@comp=tulajdonságok| [Táblaszolgáltatás tulajdonságainak beállítása](https://docs.microsoft.com/rest/api/storageservices/set-table-service-properties) és [A táblaszolgáltatás tulajdonságainak beszereznie](https://docs.microsoft.com/rest/api/storageservices/get-table-service-properties) | Ez a végpont cors-szabályok, tárolási elemzési konfiguráció és naplózási beállítások beállítására szolgál. A CORS jelenleg nem támogatott, és az analitikát és a naplózást eltérően kezeli az Azure Cosmos DB, mint az Azure Storage Tables |
| Lehetőségek | /\<tábla-erőforrás-név> | [Repülés előtti CORS-táblakérés](https://docs.microsoft.com/rest/api/storageservices/preflight-table-request) | Ez a CORS része, amelyet az Azure Cosmos DB jelenleg nem támogat. |
| GET | /?restype=service@comp=statisztika | [Táblaszolgáltatás statisztikáinak beszereznie](https://docs.microsoft.com/rest/api/storageservices/get-table-service-stats) | Tájékoztatást nyújt arról, hogy az adatok milyen gyorsan replikálódnak az elsődleges és a másodlagos adatok között. Erre nincs szükség a Cosmos DB-ben, mivel a replikáció az írások része. |
| GET, PUT | /mytable?comp=acl | [Tábla ACL beszerezése](https://docs.microsoft.com/rest/api/storageservices/get-table-acl) és [beállítása ACL](https://docs.microsoft.com/rest/api/storageservices/set-table-acl) | Ez leveszi és beállítja a megosztott hozzáférésű aláírások (SAS) kezeléséhez használt tárolt hozzáférési házirendeket. Bár a SAS támogatott, ezek beállítása és kezelése eltérő módon történik. |

Emellett az Azure Cosmos DB Table API csak a JSON-formátumot támogatja, az ATOM-et nem.

Míg az Azure Cosmos DB támogatja a megosztott hozzáférésű aláírások (SAS) vannak bizonyos szabályzatok nem támogatja, különösen a felügyeleti műveletek, például a jogot, hogy új táblákat hozzon létre.

Különösen a .NET SDK esetében vannak olyan osztályok és módszerek, amelyeket az Azure Cosmos DB jelenleg nem támogat.

| Osztály | Nem támogatott metódus |
|-------|-------- |
| CloudTableClient | \*Szolgáltatástulajdonságai* |
|                  | \*Szolgáltatássztikumok* |
| CloudTábla | SetPermissions* |
|            | GetPermissions* |
| TableServiceContext | * (ez az osztály elavult) |
| TableServiceEntity | " " |
| TableServiceExtensions | " " |
| TableServiceQuery | " " |

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Hogyan küldhetek visszajelzést az SDK-ról vagy a hibákról?

Visszajelzéseit az alábbi módokon oszthatja meg:

* [Felhasználói hang](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [MSDN fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Verem túlcsordulás](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack túlcsordulás a legjobb programozási kérdések. Győződjön meg róla, hogy a kérdés [on-topic,](https://stackoverflow.com/help/on-topic) és [a lehető legtöbb részletet, így a kérdés egyértelmű és megválaszolható](https://stackoverflow.com/help/how-to-ask).

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Mi az a kapcsolati karakterlánc, amelyet a Table API-hoz való csatlakozáshoz kell használnom?

A kapcsolati karakterlánc a következő:

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```

A kapcsolati karakterlánc az Azure Portalon a Kapcsolati karakterlánc lapon szerezhető be.

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Hogyan felülbírálhatom a table API .NET SDK-ban a kérésbeállítások konfigurációs beállításait?

Egyes beállításokat a CreateCloudTableClient metódus, más pedig az app.config segítségével kezeli a client alkalmazás Beállítások szakaszában. A konfigurációs beállításokról az Azure Cosmos DB képességei című [témakörben olvashat.](tutorial-develop-table-dotnet.md)

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>Vannak-e olyan változások az ügyfelek számára, akik a meglévő Azure Table storage SDK-k?

Nincs. Nincsenek módosítások a meglévő vagy új ügyfelek, akik a meglévő Azure Table storage SDK-k.

### <a name="how-do-i-view-table-data-thats-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Hogyan tekinthetem meg az Azure Cosmos DB-ben tárolt táblaadatokat a Table API-val való használatra?

Az Azure Portal segítségével tallózhat az adatok között. Használhatja a Table API-kódot vagy a következő válaszban említett eszközöket is.

### <a name="which-tools-work-with-the-table-api"></a>Mely eszközök működnek a Table API-val?

Használhatja az [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

A korábban megadott formátumú kapcsolati karakterlánc okán rugalmasan használható eszközök támogathatják az új Table API-t. A táblázateszközök listája az [Azure Storage-ügyféleszközök](../storage/common/storage-explorers.md) lapon található.

### <a name="is-the-concurrency-on-operations-controlled"></a>A műveletek egyidejűségét ellenőrzik?

Igen, az optimista egyidejűséget az ETag mechanizmus segítségével biztosítják.

### <a name="is-the-odata-query-model-supported-for-entities"></a>Az OData lekérdezési modell támogatja az entitások?

Igen, a Table API támogatja az OData és a LINQ lekérdezést.

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>Csatlakozhatok az Azure Table Storage-hoz és az Azure Cosmos DB Table API-hoz egymás mellett ugyanabban az alkalmazásban?

Igen, a CloudTableClient két külön példányának létrehozásával kapcsolódhat, amelyek mindegyike a saját URI-jára mutat a kapcsolati karakterláncon keresztül.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>Hogyan telepíthetek át egy meglévő Azure Table storage-alkalmazást erre az ajánlatra?

[AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) és az [Azure Cosmos DB adatáttelepítési eszköz](import-data.md) egyaránt támogatottak.

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Hogyan történik a tárhely méretének bővítése ehhez a szolgáltatáshoz, ha például *n* GB adattal kezdem, és az adataim idővel 1 TB-ra nőnek?

Az Azure Cosmos DB úgy lett kialakítva, hogy korlátlan tárhelyet biztosítson a horizontális skálázás használatával. A szolgáltatás képes figyelni és hatékonyan növelni a tárhelyet.

### <a name="how-do-i-monitor-the-table-api-offering"></a>Hogyan figyelhetem a Table API-ajánlat?

A Table API **Metrics** ablaktábla segítségével figyelheti a kérelmeket és a tárolási használat.

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Hogyan számíthatom ki a szükséges átviteli szintet?

A kapacitásbecslő segítségével kiszámíthatja a műveletekhez szükséges TableThroughput-ot. További információ: [Request Request Units and Data Storage](https://www.documentdb.com/capacityplanner). Általában megjelenítheti az entitást JSON-ként, és megadhatja a műveletek számát.

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>Használhatom a Table API SDK helyileg az emulátor?

Jelenleg nem.

### <a name="can-my-existing-application-work-with-the-table-api"></a>Működhet a meglévő alkalmazásom a Table API-val?

Igen, ugyanaz tanusítot támogatott.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-dont-want-to-use-the-table-api-features"></a>Át kell telepítenem a meglévő Azure Table storage-alkalmazásokat az SDK-ba, ha nem szeretném használni a Table API-funkciókat?

Nem, hozhat létre és használhat meglévő Azure Table-tárolóeszközöket megszakítás nélkül. Ha azonban nem használja a Table API-t, nem élvezheti az automatikus index, a további konzisztencia-beállítás vagy a globális terjesztés előnyeit.

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-more-than-one-region-of-azure"></a>Hogyan adhatom hozzá az adatok replikációját a Table API-ban az Azure több régiójában?

Az Azure Cosmos DB portál [globális replikációs beállításait](tutorial-global-distribution-sql-api.md#portal) használhatja az alkalmazásnak megfelelő régiók hozzáadásához. Globálisan elosztott alkalmazás fejlesztéséhez is hozzá kell adnia az alkalmazást a PreferredLocation adatokat a helyi régióban az alacsony olvasási késés biztosításához.

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Hogyan módosíthatom a fiók elsődleges írási régióját a Table API-ban?

Az Azure Cosmos DB globális replikációs portál ablaktáblán hozzáadhat egy régiót, majd átveheti a szükséges régiót. További információt a [Fejlesztés többrégiós Azure Cosmos DB-fiókok használatával (Fejlesztés többrégiós Azure Cosmos DB-fiókok) témakörben](high-availability.md)talál.

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Hogyan konfigurálhatom a kívánt olvasási régiókat alacsony késésre az adatok terjesztésekor?

Ha a helyi helyről szeretne olvasni, használja az app.config fájl PreferredLocation kulcsát. Meglévő alkalmazások esetén a Table API hibát okoz, ha a LocationMode be van állítva. Távolítsa el a kódot, mert a Table API felveszi ezeket az információkat az app.config fájlból. 

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>Hogyan kell gondolkodni a konzisztenciaszintek a table API-ban?

Az Azure Cosmos DB jól indokolt kompromisszumot biztosít a konzisztencia, a rendelkezésre állás és a késés között. Az Azure Cosmos DB öt konzisztenciaszintet kínál a Table API-fejlesztőknek, így a megfelelő konzisztenciamodellt választhatja ki a táblázat szintjén, és egyéni kéréseket tehet az adatok lekérdezése közben. Amikor egy ügyfél csatlakozik, megadhatja a konzisztenciaszintet. A szintet a CreateCloudTableClient konzisztenciaSzint argumentumával módosíthatja.

A table API alacsony késésű olvasások a "Saját írások olvasása" a bounded-staleness konzisztencia alapértelmezettként. További információ: [Konzisztenciaszintek](consistency-levels.md).

Alapértelmezés szerint az Azure Table storage erős konzisztenciát biztosít egy régión belül, és a másodlagos helyeken a végleges konzisztencia.

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>Az Azure Cosmos DB Table API több konzisztenciaszintet kínál, mint az Azure Table storage?

Igen, az Azure Cosmos DB elosztott jellegének előnyeiről a [Konzisztenciaszintek című](consistency-levels.md)témakörben talál további információt. Mivel a konzisztenciaszintekre garanciák vannak biztosítva, azokat magabiztosan használhatja.

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Ha a globális terjesztés engedélyezve van, mennyi ideig tart az adatok replikálása?

Az Azure Cosmos DB véglegesíti az adatokat tartósan a helyi régióban, és leküldéses az adatokat más régiókban azonnal ezredmásodpercben. Ez a replikáció csak az adatközpont oda-vissza utazásának idejétől függ. Ha többet szeretne megtudni az Azure Cosmos DB globális terjesztési képességéről, olvassa el az [Azure Cosmos DB: Globálisan elosztott adatbázis-szolgáltatás az Azure-ban című témakört.](distribute-data-globally.md)

### <a name="can-the-read-request-consistency-level-be-changed"></a>Módosítható az olvasási kérelem konzisztenciaszintje?

Az Azure Cosmos DB segítségével beállíthatja a konzisztenciaszintet a tároló szintjén (az asztalon). A .NET SDK használatával módosíthatja a szintet, ha megadja a TableConsistencyLevel kulcs értékét az app.config fájlban. A lehetséges értékek a következők: Erős, Kötött frissesség, Munkamenet, Konzisztens előtag és Egybeesség. További információ: [Hangolható adatkonzisztenciaszintek az Azure Cosmos DB-ben.](consistency-levels.md) A legfontosabb ötlet az, hogy nem állíthatja be a kérelem konzisztencia szintjét a tábla beállításánál nagyobb szinten. Például nem állíthatja be a tábla konzisztenciaszintjét a végleges és a kérelem konzisztencia szintje erős.

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>Hogyan kezeli a Table API feladatátvételt, ha egy régió leáll?

A Table API az Azure Cosmos DB globálisan elosztott platformját használja ki. Annak érdekében, hogy az alkalmazás tolerálja az adatközpont állásidejét, engedélyezze legalább még egy régiót a fiókhoz az Azure Cosmos DB portál [fejlesztése többrégiós Azure Cosmos DB-fiókokkal.](high-availability.md) Beállíthatja a régió prioritását a [Fejlesztés többrégiós Azure Cosmos DB-fiókokkal portál](high-availability.md)használatával.

Annyi régiót adhat hozzá, amennyit csak szeretne a fiókhoz, és azt a vezérlőt, ahol feladatátvételi prioritás térhet át. Az adatbázis használatához ott is meg kell adnia egy alkalmazást. Ha így tesz, az ügyfelek nem tapasztalnak leállást. A [legújabb .NET ügyfél SDK](table-sdk-dotnet.md) automatikus homing, de a többi SDK-k nem. Ez azt, hogy képes észlelni a régiót, amely nem működik, és automatikusan feladatátvételt az új régióba.

### <a name="is-the-table-api-enabled-for-backups"></a>Engedélyezve van a Table API a biztonsági mentéshez?

Igen, a table API az Azure Cosmos DB platformját használja a biztonsági mentések készítéséhez. A biztonsági mentések automatikusan készülnek. További információ: [Online biztonsági mentés és visszaállítás az Azure Cosmos DB-vel.](../synapse-analytics/sql-data-warehouse/backup-and-restore.md)

### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>A Table API alapértelmezés szerint indexeli az entitás összes attribútumát?

Igen, az entitás összes attribútuma alapértelmezés szerint indexelve van. További információ: [Azure Cosmos DB: Indexelési szabályzatok.](index-policy.md)

### <a name="does-this-mean-i-dont-have-to-create-more-than-one-index-to-satisfy-the-queries"></a>Ez azt jelenti, hogy nem kell egynél több indexet létrehoznom a lekérdezések kielégítéséhez?

Igen, az Azure Cosmos DB Table API az összes attribútum automatikus indexelését biztosítja sémadefiníció nélkül. Ez az automatizálás lefekteti a fejlesztőket, hogy az alkalmazásra összpontosítsanak, ne pedig az index létrehozására és kezelésére. További információ: [Azure Cosmos DB: Indexelési szabályzatok.](index-policy.md)

### <a name="can-i-change-the-indexing-policy"></a>Módosíthatom az indexelési szabályzatot?

Igen, az indexdefiníció megadásával módosíthatja az indexálási szabályzatot. Be kell, hogy megfelelően kódolni, és menekülni a beállításokat.

Az SDK-k non-.NET esetében az indexelési házirend csak a portálon állítható be az **Adatkezelőben,** keresse meg a módosítani kívánt táblát, majd lépjen a **Méretezés & beállítások**->indexelési házirend, a kívánt módosítás, majd a Mentés **gombra.**

A .NET SDK fájlból elküldhető az app.config fájlban:

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

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>Az Azure Cosmos DB platformként úgy tűnik, hogy sok képességekkel rendelkezik, például a rendezés, az összesítések, a hierarchia és egyéb funkciók. Hozzáadja ezeket a képességeket a Table API-hoz?

A Table API ugyanazt a lekérdezési funkciót biztosítja, mint az Azure Table storage. Az Azure Cosmos DB a rendezést, az összesítéseket, a földrajzi lekérdezéseket, a hierarchiát és számos különféle beépített funkciót is támogat. További információt az [SQL-lekérdezések](how-to-sql-query.md)című témakörben talál.

### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>Mikor kell módosítani a TableThroughput-ot a Table API-hoz?

A TableThroughput módosítása az alábbi feltételek bármelyikének fennállása esetén:

* Adatok kinyerését, átalakítását és betöltését (ETL) hajtja végre, vagy sok adatot szeretne rövid idő alatt feltölteni.
* Nagyobb átviteli sebességgel kell végezni a tárolóból vagy a háttérben lévő tárolók készletéből. Például láthatja, hogy a használt átviteli nagyobb, mint a kiosztott átviteli, és a kapsz szabályozott. További információ: [Átviteli átviteli közbeni szolgáltatás beállítása az Azure Cosmos-tárolókhoz.](set-throughput.md)

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>Skálázható vagy kicsinyített az átviteli a table API-tábla?

Igen, használhatja az Azure Cosmos DB portal méretezési ablaktábláját az átviteli munkaképes méretéhez. További információ: [Throughputput in t in the Set throughput](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Az újonnan kiépített táblákhoz alapértelmezett TableThroughput van beállítva?

Igen, ha nem bírálja felül a TableThroughput keresztül app.config, és nem használja az azure Cosmos DB előre létrehozott tároló, a szolgáltatás létrehoz egy 400-as átviteli sebességű táblát.

### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Van-e változás a díjszabás a meglévő ügyfelek az Azure Table storage szolgáltatás?

Nincs. A meglévő Azure Table storage-ügyfelek ára nem változik.

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Hogyan számítják ki az árat a Table API-hoz?

Az ár a hozzárendelt TableThroughput-tól függ.

### <a name="how-do-i-handle-any-rate-limiting-on-the-tables-in-table-api-offering"></a>Hogyan kezelhetem a table API-ajánlat tábláinak díjkorlátozását?

Ha a kérelem aránya több, mint az alapul szolgáló tároló vagy tárolók készletének kiosztott átviteli sebessége, hibaüzenetet kap, és az SDK újrapróbálkozik a hívással az újrapróbálkozási szabályzat alkalmazásával.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Miért kell a PartitionKey és a RowKey mellett egy átviteli szintet választanom az Azure Cosmos DB Table API-ajánlatának kihasználásához?

Az Azure Cosmos DB beállítja a tároló alapértelmezett átviteli, ha nem adja meg az app.config fájlban vagy a portálon keresztül.

Az Azure Cosmos DB garantálja a teljesítményt és a késést, a működés felső határa. Ez a garancia akkor lehetséges, ha a motor kényszerítheti a bérlő műveleteinek irányítását. A TableThroughput beállítása biztosítja, hogy megkapja a garantált átviteli és késési kapacitást, mivel a platform fenntartja ezt a kapacitást, és garantálja a működési sikert.

Az átviteli specifikáció használatával rugalmasan módosíthatja azt, hogy kihasználja az alkalmazás szezonalitását, megfeleljen az átviteli igényeknek, és költségeket takarítson meg.

### <a name="azure-table-storage-has-been-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-havent-performed-a-single-transaction-or-stored-anything-can-you-explain"></a>Az Azure Table storage olcsó volt számomra, mert csak az adatok tárolásáért fizetek, és ritkán kérdezek le. Az Azure Cosmos DB Table API-ajánlat úgy tűnik, hogy a töltés nekem annak ellenére, hogy én még nem hajtott végre egyetlen tranzakciót, vagy tárolt semmit. Meg tudja magyarázni?

Az Azure Cosmos DB globálisan elosztott, SLA-alapú rendszer, amely garantálja a rendelkezésre állást, a késést és az átviteli forgalmat. Ha az Azure Cosmos DB-ben lefoglalja az átviteli kapacitást, az más rendszerek átviteli kapacitásával ellentétben garantált. Az Azure Cosmos DB további, az ügyfelek által kért képességeket biztosít, például a másodlagos indexeket és a globális disztribúciót.

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Soha nem kap egy kvóta teljes" értesítést (jelezve, hogy a partíció megtelt), amikor adatokat betöltése az Azure Table storage-ba. A Table API-val ezt az üzenetet kapom. Ez az ajánlat korlátozza nekem, és arra kényszerít, hogy módosítsam a meglévő alkalmazásomat?

Az Azure Cosmos DB egy SLA-alapú rendszer, amely korlátlan skálázást biztosít, garanciákkal a késés, az átviteli, a rendelkezésre állás és a konzisztencia. A garantált prémium teljesítmény biztosítása érdekében győződjön meg arról, hogy az adatok mérete és indexe kezelhető és méretezhető. A partíciókulcsonként i entitások vagy elemek száma 10 GB-os korlát biztosítja, hogy kiváló keresések és lekérdezési teljesítmény. Annak érdekében, hogy az alkalmazás méretezése jól, még az Azure Storage esetében is azt javasoljuk, hogy *ne* hozzon létre egy gyorsválasztó partíciót az összes információ egy partíción való tárolásával és lekérdezésével.

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>Tehát PartitionKey és RowKey továbbra is szükség van a table API-t?

Igen. Mivel a table API felülete hasonló az Azure Table Storage SDK felületéhez, a partíciókulcs hatékony módot biztosít az adatok elosztására. A sorkulcs egyedi az adott partíción belül. A sorkulcsnak jelen kell lennie, és nem lehet null, mint a szabványos SDK-ban. A RowKey hossza 255 bájt, a Partíciókulcs hossza pedig 1 KB.

### <a name="what-are-the-error-messages-for-the-table-api"></a>Mik a table API hibaüzenetei?

Az Azure Table storage és az Azure Cosmos DB Table API ugyanazokat az SDK-kat használja, így a legtöbb hiba ugyanaz lesz.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Miért van szabályos, amikor megpróbálok létrehozni sok táblák egymás után a Table API-ban?

Az Azure Cosmos DB egy SLA-alapú rendszer, amely késést, átviteli, rendelkezésre állást és konzisztencia garanciákat biztosít. Mivel ez egy kiépített rendszer, erőforrásokat tart a követelmények garantálásához. A táblák létrehozásának gyors ütemét észleli és szabályozza. Javasoljuk, hogy tekintse meg a táblák létrehozásának sebességét, és csökkentse azt percenként 5-nél kevesebbre. Ne feledje, hogy a table API egy kiépített rendszer. Abban a pillanatban, ahogy kiépíted, elkezdesz fizetni érte.

## <a name="gremlin-api"></a>Gremlin API

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>A C#/.NET fejlesztéshez használjam a Microsoft.Azure.Graphs csomagot vagy Gremlin.NET?

Az Azure Cosmos DB Gremlin API a nyílt forráskódú illesztőprogramokat használja a szolgáltatás fő összekötőiként. Tehát az ajánlott lehetőség az [Apache Tinkerpop által támogatott illesztőprogramok](https://tinkerpop.apache.org/)használata.

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>Hogyan terheli a RU/s a rendszer a lekérdezések grafikonadatbázison való futtatásakor?

Minden diagramobjektum, csúcsés és él JSON-dokumentumként jelenik meg a háttérben. Mivel egy Gremlin-lekérdezés egyszerre egy vagy több gráfobjektumot módosíthat, a hozzá kapcsolódó költség közvetlenül kapcsolódik a lekérdezés által feldolgozott objektumokhoz, élekhez. Ez ugyanaz a folyamat, amelyet az Azure Cosmos DB az összes többi API-hoz használ. További információ: [Az Azure Cosmos DB kérelemegységei](request-units.md).

A VT-díj a bejárás munkaadatkészletén alapul, nem az eredményhalmazon. Ha például egy lekérdezés célja, hogy ennek eredményeképpen egyetlen csúcspontot kapjon, de az úton több objektumon is át kell haladnia, akkor a költség az összes olyan grafikonobjektumon alapul, amely az egy eredménycsúcs kiszámításához szükséges.

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>Mi a maximális skálázás, amely egy gráf-adatbázis rendelkezhet az Azure Cosmos DB Gremlin API-ban?

Az Azure Cosmos DB [horizontális particionálást](partition-data.md) használ a tárolási és átviteli követelmények növekedésének automatikus kezelésére. A számítási feladatok maximális átviteli és tárolási kapacitását az adott tárolóhoz társított partíciók száma határozza meg. Azonban egy Gremlin API-tároló egy adott irányelvek, hogy biztosítsák a megfelelő teljesítményélményt a nagy méretekben. A particionálásról és az ajánlott eljárásokról az [Azure Cosmos DB-cikk particionálása](partition-data.md) című témakörben olvashat bővebben.

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>Hogyan védhetek a Gremlin illesztőprogramokkal történő injekciós támadások ellen?

A legtöbb natív Apache Tinkerpop Gremlin illesztőprogram lehetővé teszi, hogy a lekérdezés végrehajtásához a paraméterek szótárát biztosítsa. Ez egy példa arra, hogyan kell csinálni [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) és [gremlin-Javascript](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/master/app.js).

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>Miért jelenik meg a "Gremlin Query Fordítási hiba: Nem található semmilyen metódus" hiba?

Az Azure Cosmos DB Gremlin API a Gremlin felületen definiált funkciók egy részét valósítja meg. A támogatott lépésekről és további információkért lásd: [Gremlin support](gremlin-support.md) article.

A legjobb megoldás a szükséges Gremlin lépések újraírása a támogatott funkciókkal, mivel az Azure Cosmos DB minden alapvető Gremlin lépést támogat.

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>Miért jelenik meg a "WebSocketException: A kiszolgáló "200" állapotkódot adott vissza, amikor a "101" állapotkód várt?

Ez a hiba valószínűleg akkor jelenik meg, ha nem a megfelelő végpontot használja. A hibát generáló végpont a következő mintával rendelkezik:

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

Ez a gráfadatbázis dokumentumvégpontja.  A helyes végpont a Gremlin végpont, amely a következő formátummal rendelkezik:

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>Miért jelenik meg a "RequestRateIsTooLarge" hibaüzenet?

Ez a hiba azt jelenti, hogy a lefoglalt kérelem egységek másodpercenként nem elegendő a lekérdezés kiszolgálásához. Ez a hiba általában akkor jelentkezik, ha olyan lekérdezést futtat, amely az összes csúcsot beszerzi:

```
// Query example:
g.V()
```

Ez a lekérdezés megkísérli az összes csúcsot beolvasni a grafikonról. Így a lekérdezés költsége egyenlő lesz legalább a csúcsok száma a RT-k szempontjából. A RU/s beállítást módosítani kell a lekérdezés kezeléséhez.

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>Miért dobják le végül a Gremlin illesztőprogram-kapcsolataimat?

A Gremlin-kapcsolat WebSocket-kapcsolaton keresztül történik. Bár a WebSocket-kapcsolatoknak nincs meghatározott ideje az életre, az Azure Cosmos DB Gremlin API 30 perc inaktivitás után megszakítja az aktív járatokat.

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>Miért nem tudok folyékony AN-hívásokat használni a natív Gremlin-illesztőprogramok esetén?

A Fluent API-hívásokat még nem támogatja az Azure Cosmos DB Gremlin API. A Fluent API-hívásokhoz olyan belső formázási szolgáltatásra van szükség, amelyet bytecode-támogatásnak neveznek, amelyet jelenleg nem támogat az Azure Cosmos DB Gremlin API. Ugyanezen okból a legújabb Gremlin-JavaScript illesztőprogram szintén jelenleg nem támogatott.

### <a name="how-can-i-evaluate-the-efficiency-of-my-gremlin-queries"></a>Hogyan értékelhetem a Gremlin-lekérdezéseim hatékonyságát?

A **executionProfile()** előzetes lépés a lekérdezés végrehajtási tervének elemzésére használható. Ezt a lépést hozzá kell adni minden Gremlin-lekérdezés végéhez, amint azt a következő példa szemlélteti:

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

A fenti profil kimenete azt mutatja, hogy mennyi időt tölt a csúcspontobjektumok, a peremobjektumok és a munkaadatkészlet méretének megszerzésével. Ez az Azure Cosmos DB-lekérdezések elszámolóár-méréseihez kapcsolódik.

## <a name="cassandra-api"></a><a id="cassandra"></a>Cassandra API

### <a name="what-is-the-protocol-version-supported-by-azure-cosmso-db-cassandra-api-is-there-a-plan-to-support-other-protocols"></a>Mi az Azure Cosmso DB Cassandra API által támogatott protokollverzió? Van terv más protokollok támogatására?

Az Apache Cassandra API for Azure Cosmos DB ma támogatja a CQL 4-es verzióját. Ha visszajelzést kap más protokollok támogatásáról, tudassa velünk a felhasználói [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) [hangvisszajelzést,](https://feedback.azure.com/forums/263030-azure-cosmos-db) vagy küldjön e-mailt a rendszernek.

### <a name="why-is-choosing-a-throughput-for-a-table-a-requirement"></a>Miért követelmény egy tábla átviteli igényének kiválasztása?

Az Azure Cosmos DB alapátviteli értéket állít be a tárolóhoz annak alapján, hogy honnan hozza létre a táblát - portálról vagy CQL-ből.
Az Azure Cosmos DB garantálja a teljesítményt és a késést, a működés felső határa. Ez a garancia akkor lehetséges, ha a motor kényszerítheti a bérlő műveleteinek irányítását. Az átviteli képesség beállítása biztosítja, hogy megkapja a garantált átviteli és késési, mert a platform fenntartja ezt a kapacitást, és garantálja a működés sikerességét.
Rugalmasan módosíthatja az átviteli szintet, hogy kihasználhassa az alkalmazás szezonalitását, és költségeket takaríthat meg.

Az átviteli koncepció az [Azure Cosmos DB-ben található kérelemegységek](request-units.md) ismerteti. A tábla átviteli forgalma egyenlően oszlik meg az alapul szolgáló fizikai partíciók között.

### <a name="what-is-the-default-rus-of-table-when-created-through-cql-what-if-i-need-to-change-it"></a>Mi a tábla alapértelmezett RU/s-a cql-on keresztül létrehozva? Mi a teendő, ha meg kell változtatnom?

Az Azure Cosmos DB kérelemegység/másodperc (RU/s) pénznemként használja az átviteli értéket. A CQL-en keresztül létrehozott táblák 400 RU-val rendelkeznek. A vt.-t a portálról módosíthatja.

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

### <a name="what-happens-when-throughput-is-used-up"></a>Mi történik, ha az átviteli áteresztőáltal használt?

Az Azure Cosmos DB garantálja a teljesítményt és a késést, a működés felső határa. Ez a garancia akkor lehetséges, ha a motor kényszerítheti a bérlő műveleteinek irányítását. Ez az átviteli képesség beállítása alapján lehetséges, amely biztosítja a garantált átviteli és késési kapacitást, mivel a platform fenntartja ezt a kapacitást, és garantálja a működés sikerét.
Ha túllépi ezt a kapacitást, túlterhelt hibaüzenet jelenik meg, amely jelzi, hogy a kapacitás a használt volt.
0x1001 Túlterhelt: a kérés nem dolgozható fel, mert a "Kérelem aránya nagy". Ebben a helyzetben fontos, hogy lássuk, milyen műveletek és azok mennyisége okozza ezt a problémát. A portálon metrikákkal rendelkező felhasznált kapacitásról képet kaphat. Ezután biztosítania kell, hogy a kapacitás szinte egyenlően kerüljön felhasználásra az összes alapul szolgáló partíción. Ha látja, hogy az átviteli hang nagy részét egy partíció használja fel, akkor a munkaterhelés ferde.

Metrikák érhetők el, amelyek megmutatják, hogyan átviteli órákban, napokon és hét naponként, partíciók on vagy összesítve. További információ: [Figyelés és hibakeresés metrikákkal az Azure Cosmos DB-ben.](use-metrics.md)

A diagnosztikai naplók az [Azure Cosmos DB diagnosztikai naplózási](logging.md) cikkben találhatók.

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>Az elsődleges kulcs leképezi az Azure Cosmos DB partíciókulcs-koncepcióját?

Igen, a partíciókulcs az entitás megfelelő helyre való elhelyezéséhez használatos. Az Azure Cosmos DB-ben a fizikai partíción tárolt megfelelő logikai partíció megkereséséhez használható. A particionálási koncepció jól ismert az [Azure Cosmos DB-cikkben](partition-data.md) a partíció és a méretezés. Az alapvető elvenni itt az, hogy a logikai partíció nem megy át a 10 GB-os korlátot ma.

### <a name="what-happens-when-i-get-a-quota-full-notification-indicating-that-a-partition-is-full"></a>Mi történik, ha a kvóta teljes" értesítést kapok, amely jelzi, hogy egy partíció megtelt?

Az Azure Cosmos DB egy SLA-alapú rendszer, amely korlátlan skálázást biztosít, garanciákkal a késés, az átviteli, a rendelkezésre állás és a konzisztencia. Ez a korlátlan tárterület alapul horizontális horizontális adatskálázás particionálás, mint a kulcs fogalom használatával. A particionálási koncepció jól ismert az [Azure Cosmos DB-cikkben](partition-data.md) a partíció és a méretezés.

A 10 GB-os korlát az entitások vagy elemek logikai partíciónként i kell tartania. Annak érdekében, hogy az alkalmazás méretezése jól, azt javasoljuk, hogy *ne* hozzon létre egy gyorsválasztó partíciót az összes információ tárolása egy partíción, és lekérdezi azt. Ez a hiba csak akkor jöhet, ha az adatok ferdeek: azaz sok adat&nbsp;van egy partíciókulcshoz (több mint 10 GB). Az adatok eloszlása a tárolóportálon található. A hiba megoldása a tábla újbóli létrehozása és egy részletes elsődleges (partíciókulcs) kiválasztása, amely lehetővé teszi az adatok jobb elosztását.

### <a name="is-it-possible-to-use-cassandra-api-as-key-value-store-with-millions-or-billions-of-individual-partition-keys"></a>Használható-e a Cassandra API kulcsérték-tárolóként több millió vagy milliárd egyedi partíciókulcssal?

Az Azure Cosmos DB korlátlan adattárolást biztosíthat a tárterület horizontális felskálázásával. Ez független az átviteli. Igen, mindig használhatja a Cassandra API-t a kulcs/értékek tárolásához és lekéréséhez a jobb elsődleges/partíciókulcs megadásával. Ezek az egyes kulcsok kap saját logikai partíciót, és üljön a fizikai partíció problémák nélkül.

### <a name="is-it-possible-to-create-more-than-one-table-with-apache-cassandra-api-of-azure-cosmos-db"></a>Létrehozhat egynél több táblát az Azure Cosmos DB Apache Cassandra API-jával?

Igen, az Apache Cassandra API-val több táblát is létrehozhat. Minden ilyen táblák az átviteli és tárolási egységként kezelik.

### <a name="is-it-possible-to-create-more-than-one-table-in-succession"></a>Lehet-e egymás után több táblát létrehozni?

Az Azure Cosmos DB az adat- és vezérlősík-tevékenységek erőforrás által szabályozott rendszere. Tárolók, például a gyűjtemények, táblák futásidejű entitások, amelyek ki vannak építve adott átviteli kapacitás. Ezek a tárolók létrehozása gyors egymásutánban nem várható tevékenység és szabályozott. Ha olyan tesztjei vannak, amelyek azonnal eldobják/létre hozzák a táblákat, próbálja meg kiterezni őket.

### <a name="what-is-maximum-number-of-tables-that-can-be-created"></a>Mi a létrehozható táblák maximális száma?

Nincs fizikai korlátozás a táblák száma, küldjön [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) egy e-mailt, ha nagy számú táblák (ahol a teljes állandó mérete meghaladja a 10 TB-os adatok), hogy létre kell hozni a szokásos 10s vagy 100s.

### <a name="what-is-the-maximum--of-keyspace-that-we-can-create"></a>Mi a maximális # keyspace, hogy tudunk létrehozni?

Nincs fizikai korlát a kulcsterek száma, mivel azok metaadat-tárolók, küldjön egy e-mailt, [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) ha valamilyen okból nagy számú kulcsterülettel rendelkezik.

### <a name="is-it-possible-to-bring-in-lot-of-data-after-starting-from-normal-table"></a>Lehetséges-e sok adatot behozni a normál táblából való kiindulat után?

A tárolókapacitás tautomatikusan kezeli a rendszer, és növekszik, ahogy több adatot szeretne benyomni. Így magabiztosan importálhat annyi adatot, amennyire szüksége van a csomópontok kezelése és kiépítése nélkül, és így tovább.

### <a name="is-it-possible-to-supply-yaml-file-settings-to-configure-apache-casssandra-api-of-azure-cosmos-db-behavior"></a>Meg adható yaml fájlbeállítások az Azure Cosmos DB viselkedésapache Casssandra API-jának konfigurálásához?

Az Azure Cosmos DB Apache Cassandra API-ja egy platformszolgáltatás. Protokollszintű kompatibilitást biztosít a műveletek végrehajtásához. Elrejti a felügyelet, a figyelés és a konfiguráció összetettségét. Fejlesztőként/felhasználóként nem kell aggódnia a rendelkezésre állás, a törlési kövek, a kulcsgyorsítótár, a sorgyorsítótár, a virágzásszűrő és az egyéb beállítások sokasága miatt. Az Azure Cosmos DB Apache Cassandra API-ja olyan olvasási és írási teljesítmény biztosítására összpontosít, amelya konfiguráció és a felügyelet többletterhelése nélkül szükséges.

### <a name="will-apache-cassandra-api-for-azure-cosmos-db-support-node-additioncluster-statusnode-status-commands"></a>Az Apache Cassandra API az Azure Cosmos DB-hez támogatja a csomópont-összeadást/fürt állapot/csomópont állapotparancsokat?

Az Apache Cassandra API egy platformszolgáltatás, amely megkönnyíti a kapacitástervezést, és megfelel az átviteli & tárolási rugalmassági igényeknek. Az Azure Cosmos DB-vel kikell építenie az átviteli, szükséges. Ezután a nap folyamán tetszőleges számú alkalommal skálázhatja anélkül, hogy aggódnia kellene a csomópontok hozzáadása/törlése vagy kezelése miatt. Ez azt jelenti, hogy nem kell használnia a csomópontot, a fürtkezelő eszközt is.

### <a name="what-happens-with-respect-to-various-config-settings-for-keyspace-creation-like-simplenetwork"></a>Mi történik a keyspace létrehozása különböző konfigurációs beállításaival kapcsolatban, például az egyszerű/hálózat?

Az Azure Cosmos DB globális terjesztést biztosít a rendelkezésre állás és az alacsony késés miatt. Nem kell replikákat vagy más dolgokat beállítania. Minden írási mindig tartósan kvórum elkötelezett minden olyan régióban, ahol ír, miközben teljesítménygaranciákat.

### <a name="what-happens-with-respect-to-various-settings-for-table-metadata-like-bloom-filter-caching-read-repair-change-gc_grace-compression-memtable_flush_period-and-more"></a>Mi történik a tábla metaadatainak különböző beállításaival kapcsolatban, például a virágzásszűrővel, a gyorsítótárazással, az olvasási javítási módosítással, a gc_grace, a tömörítési memtable_flush_period stb.

Az Azure Cosmos DB az olvasási/írási és átviteli teljesítmény teljesítményét anélkül biztosítja, hogy meg kellene érintenie a konfigurációs beállításokat, és véletlenül manipulálnia kellene őket.

### <a name="is-time-to-live-ttl-supported-for-cassandra-tables"></a>Az élő (TTL) támogatja a Cassandra táblák?

Igen, a TTL támogatott.

### <a name="is-it-possible-to-monitor-node-status-replica-status-gc-and-os-parameters-earlier-with-various-tools-what-needs-to-be-monitored-now"></a>Lehet-e figyelni a csomópont állapotát, replika állapotát, a gc és az operációs rendszer paramétereit korábban különböző eszközökkel? Mit kell most ellenőrizni?

Az Azure Cosmos DB egy platformszolgáltatás, amely segít növelni a termelékenységet, és nem kell aggódnia az infrastruktúra kezelése és figyelése miatt. Csak a portálmetrikákon elérhető átviteli teljesítményről kell gondoskodnia, hogy megkeresse, ha szabályozásalá kerül, és növeli vagy csökkenti az átviteli fésületi.
Monitor [SlAs](monitor-accounts.md).
[Metrikák](use-metrics.md) használata [Diagnosztikai naplók használata](logging.md).

### <a name="which-client-sdks-can-work-with-apache-cassandra-api-of-azure-cosmos-db"></a>Mely ügyfél-SDK-k működhetnek az Azure Cosmos DB Apache Cassandra API-jával?

Az Apache Cassandra SDK CQLv3-at használó ügyfél-illesztőprogramjait ügyfélprogramokhoz használták. Ha más illesztőprogramokat is használ, vagy ha problémákkal [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)szembesül, küldjön e-mailt a alkalmazásba.

### <a name="is-composite-partition-key-supported"></a>Támogatott az összetett partíciókulcs?

Igen, a normál szintaxissal összetett partíciókulcsot hozhat létre.

### <a name="can-i-use-sstableloader-for-data-loading"></a>Használhatom a sstableloadert az adatok betöltéséhez?

Nem, a sstableloader nem támogatott.

### <a name="can-an-on-premises-apache-cassandra-cluster-be-paired-with-azure-cosmos-dbs-cassandra-api"></a>Párosítható egy helyszíni Apache Cassandra-fürt az Azure Cosmos DB Cassandra API-jával?

Jelenleg az Azure Cosmos DB egy optimalizált élményt felhőalapú környezetben, a műveletek terhelése nélkül. Ha párosításra van szüksége, [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) küldjön e-mailt a forgatókönyv leírásával. Dolgozunk kínál kínál, hogy segítsen párosítani a helyszíni/különböző felhő Cassandra klaszter Cosomos DB Cassandra API.We are working on offering to help pair the on-premises/different cloud Cassandra cluster to Cosomos DB's Cassandra API.

### <a name="does-cassandra-api-provide-full-backups"></a>A Cassandra API teljes biztonsági mentést biztosít?

Az Azure Cosmos DB két ingyenes teljes biztonsági mentést biztosít, amelyeket ma négy órás időközönként az összes API-ban készít. Ez biztosítja, hogy nem kell biztonsági mentési ütemezést és egyéb dolgokat beállítania.
Ha módosítani szeretné a megőrzést és [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) a gyakoriságot, küldjön egy e-mailt, vagy emelje fel a támogatási esetet. A biztonsági mentési képességről az Automatikus online biztonsági mentés és visszaállítás az [Azure Cosmos DB](../synapse-analytics/sql-data-warehouse/backup-and-restore.md) cikkben található.

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Hogyan kezeli a Cassandra API-fiók feladatátvételt, ha egy régió leáll?

Az Azure Cosmos DB Cassandra API az Azure Cosmos DB globálisan elosztott platformjáról kölcsönöz. Annak érdekében, hogy az alkalmazás tolerálja az adatközpont állásidejét, engedélyezze legalább még egy régiót a fiókhoz az Azure Cosmos DB portál [fejlesztése többrégiós Azure Cosmos DB-fiókokkal.](high-availability.md) Beállíthatja a régió prioritását a [Fejlesztés többrégiós Azure Cosmos DB-fiókokkal portál](high-availability.md)használatával.

Annyi régiót adhat hozzá, amennyit csak szeretne a fiókhoz, és azt a vezérlőt, ahol feladatátvételi prioritás térhet át. Az adatbázis használatához ott is meg kell adnia egy alkalmazást. Ha így tesz, az ügyfelek nem tapasztalnak leállást.

### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Az Apache Cassandra API alapértelmezés szerint indexeli egy entitás összes attribútumát?

Cassandra API azt tervezi, hogy támogatja a másodlagos indexelés, hogy segítsen létrehozni a szelektív index bizonyos attribútumok. 


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Használhatom az új Cassandra API SDK-t helyileg az emulátorral?

Igen, ez támogatott.

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-change-feed-and-other-functionality-will-these-capabilities-be-added-to-the-cassandra-api"></a>Az Azure Cosmos DB platformként úgy tűnik, hogy sok képességekkel rendelkezik, például a változás csatorna és egyéb funkciók. Ezek a képességek hozzáadódnak a Cassandra API-hoz?

Az Apache Cassandra API ugyanazt a CQL funkciót biztosítja, mint az Apache Cassandra. Azt tervezzük, hogy vizsgálja meg megvalósíthatóságát támogató különböző képességek a jövőben.

### <a name="feature-x-of-regular-cassandra-api-isnt-working-as-today-where-can-the-feedback-be-provided"></a>A hagyományos Cassandra API x funkciója nem működik, mint ma, hol lehet visszajelzést adni?

Visszajelzés küldése [a felhasználói hangvisszajelzésen](https://feedback.azure.com/forums/263030-azure-cosmos-db)keresztül.

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md
