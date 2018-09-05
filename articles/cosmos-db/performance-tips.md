---
title: Az Azure Cosmos DB teljesítményével kapcsolatos tippek .NET |} A Microsoft Docs
description: Ismerje meg az ügyfél-konfigurációs beállításokat az Azure Cosmos DB adatbázis-teljesítmény javítása
keywords: adatbázis teljesítményének növelése
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 01/24/2018
ms.author: sngun
ms.openlocfilehash: 47896b681c2ba43d4ca41682ec51e0dfd167c9d5
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43700956"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Teljesítménnyel kapcsolatos tippek az Azure Cosmos DB- és .NET

> [!div class="op_single_selector"]
> * [Aszinkron Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Az Azure Cosmos DB egy gyors és rugalmas elosztott adatbázis, teljesítmény és a késés garantált az zökkenőmentesen méretezhető. Nem rendelkezik architektúra jelentős módosításokat, vagy az adatbázis az Azure Cosmos DB méretezése összetett programkód írása. Felfelé és lefelé skálázás nem kell csak egyetlen API-hívás vagy [SDK metódushívás](set-throughput.md#set-throughput-sdk). Azonban mivel a Azure Cosmos DB a hálózati hívások segítségével érhető el vannak ügyféloldali optimalizálást is végezhet használata esetén a csúcsteljesítmény érhet el a [SQL .NET SDK](documentdb-sdk-dotnet.md).

Így ha Ön kérő "Hogyan javíthatom adatbázis teljesítmény?" Vegye figyelembe a következő beállításokat:

## <a name="networking"></a>Hálózat
<a id="direct-connection"></a>

1. **Csatlakozási házirend: közvetlen kapcsolat mód használata**

    Hogyan ügyfél csatlakozik-e az Azure Cosmos DB következményeket vonhat fontos a teljesítmény, különösen a megfigyelt ügyféloldali késés szempontjából. Nincsenek elérhető ügyfél kapcsolódási szabályzat – a kapcsolat konfigurálásához két fő konfigurációs beállítások *mód* és a [kapcsolat *protokoll*](#connection-protocol).  A két rendelkezésre álló beállításokat a következők:

   * Átjáró mód (alapértelmezett)
      
     Átjáró mód az összes SDK-platformon támogatott, és a konfigurált alapértelmezett. Ha az alkalmazás fut a vállalati hálózaton belül szigorú tűzfal korlátozásokkal, az átjáró mód nem a legjobb választás, mivel a szabványos HTTPS-port és a egy végpontot használja. A teljesítményre vonatkozóan kompromisszumot jelent, azonban nem, hogy az átjáró mód érint egy további hálózati Ugrás minden alkalommal, amikor adatokat olvasni vagy írni az Azure Cosmos DB. Emiatt a közvetlen üzemmódban miatt kevesebb hálózati útválasztók ugrásainak jobb teljesítményt nyújt. Átjáró kapcsolati módot is ajánlott csak korlátozott számú szoftvercsatorna-kapcsolatok, például az Azure Functions, illetve ha a használatalapú csomag használatakor a környezetek alkalmazások futtatásakor. 

   * Közvetlen mód

     Közvetlen mód támogatja a TCP- és HTTPS protokollok keresztüli kapcsolat. Jelenleg közvetlen támogatott .NET Standard 2.0 csak Windows-platform. Közvetlen mód használatakor kétféle protokoll érhető el:

    * TCP
    * HTTPS

    Átjáró mód használatakor az Azure Cosmos DB használ-e a 443-as porton, és a MongoDB API 10250, 10255 és 10256 portokat használ. A georeplikáció és a Mongodb-példányban georeplikációs funkciókkal 10255/10256 portok leképezés nélkül egy alapértelmezett Mongodb-példányban 10250 port leképezések. Ha használ TCP közvetlen üzemmódban átjáró portokon kívül, gondoskodnia kell arról, a port 10000 és 20000 közötti nyitva, mert az Azure Cosmos DB dinamikus TCP-portot használja. Ha ezeket a portokat nem nyitott, és próbálja meg használni, a TCP, hibaüzenet 503-as szolgáltatás nem érhető el. A következő táblázat különböző API-k és a portok felhasználói rendelkezésre álló csatlakozási módot mutatja az egyes API:

    |Kapcsolat módja  |Támogatott protokollok  |Támogatott SDK-k  |API-szolgáltatás portja  |
    |---------|---------|---------|---------|
    |Átjáró  |   HTTPS    |  Az összes SDK-k    |   SQL(443), Mongo (10250, 10255, 10256), Table(443), Cassandra(443), Graph(443)    |
    |Közvetlen    |    HTTPS     |  .NET, a Java SDK    |    SQL(443)   |
    |Közvetlen    |     TCP    |  .NET SDK    | 10 000-20 000 tartományon belüli portok |

    Az Azure Cosmos DB egy egyszerű, és nyissa meg RESTful programozási modellt kínál a HTTPS-kapcsolaton keresztül. Ezenkívül kínál egy hatékony TCP protokoll, amely egyben a RESTful a kommunikációt a modellben, és a .NET ügyféloldali SDK keresztül érhető el. Közvetlen TCP és a HTTPS SSL használata a kezdeti hitelesítésre és a titkosított forgalmat. A legjobb teljesítmény érdekében használja a TCP protokollt, amikor csak lehetséges.

    A csatlakozási mód a DocumentClient példányának a ConnectionPolicy paramétere a konstrukció során van konfigurálva. Ha a közvetlen üzemmódban használja, a protokoll is megadható belül ConnectionPolicy paramétere.

    ```csharp
    var serviceEndpoint = new Uri("https://contoso.documents.net");
    var authKey = new "your authKey from the Azure portal";
    DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
    new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp
    });
    ```

    TCP átjáró mód használata esetén csak akkor támogatott a közvetlen módban, mert a HTTPS protokollt mindig közötti kommunikációra szolgál az átjáróval, és a ConnectionPolicy protokoll értékét a rendszer figyelmen kívül hagyja.

    ![Az Azure Cosmos DB kapcsolati házirend ábrája](./media/performance-tips/connection-policy.png)

2. **Első kérésre indítási késleltetés elkerülése érdekében OpenAsync hívása**

    Alapértelmezés szerint az első kérelem egy magasabb késleltetésű rendelkezik, mert a cím útválasztási táblázat beolvasása. Ez az első kérelem indítási késést elkerülése érdekében célszerű hívni OpenAsync() egyszer inicializálása során a következő.

        await client.OpenAsync();
   <a id="same-region"></a>
3. **A teljesítmény Fájlmegosztáséval azonos régióban lévő ügyfelek elhelyezésének engedélyezése**

    Ha lehetséges, helyezze el az alkalmazásokat Azure Cosmos DB hívja meg az Azure Cosmos DB-adatbázis és ugyanabban a régióban. 1 – 2 ms belül végezze az azonos régión belüli Azure Cosmos DB-hívások hozzávetőleges összehasonlításáért, de a nyugati és keleti parti területei az Egyesült Államok a késés > 50 ms. Ez a késés kérelem kérés valószínűleg függvényében az útvonal, az ügyfél az Azure-adatközpont határt adja át a kérés által készített. A lehető legkisebb késleltetést úgy, hogy a hívó alkalmazás megtalálható az üzembe helyezett Azure Cosmos DB-végpontként az azonos Azure-régióban érhető el. Az elérhető régiók listáját lásd: [Azure-régiók](https://azure.microsoft.com/regions/#services).

    ![Az Azure Cosmos DB kapcsolati házirend ábrája](./media/performance-tips/same-region.png)
   <a id="increase-threads"></a>
4. **Növelje a szálak/feladatok száma**

    Mivel az Azure Cosmos DB-hívások a hálózaton keresztül, szükség lehet a kérelmet párhuzamosság foka változnak, hogy az ügyfélalkalmazás várólistában nagyon rövid ideig várakozik az kérések között. Például ha használ. A NET [párhuzamos feladatok könyvtára](https://msdn.microsoft.com//library/dd460717.aspx), hozzon létre feladatokat az Azure Cosmos DB írása és olvasása 100s sorrendjében.

## <a name="sdk-usage"></a>SDK-használata
1. **A legújabb SDK telepítése**

    Az Azure Cosmos DB SDK kat folyamatosan fejlesztik a lehető legjobb teljesítményt. Tekintse meg a [Azure Cosmos DB SDK](documentdb-sdk-dotnet.md) oldalt, hogy a legújabb SDK határozza meg, és tekintse át a fejlesztései.
2. **Az alkalmazás teljes élettartama egyedülálló Azure Cosmos DB-ügyfél használata**

    Minden egyes DocumentClient-példányt szálbiztos, és hatékony kapcsolat kezelése és a címek gyorsítótárazása, közvetlen üzemmódban hajt végre. Ahhoz, hogy a kapcsolat hatékony kezelése és a DocumentClient jobb teljesítményt, ajánlott az alkalmazás teljes élettartama alkalmazástartományonként DocumentClient egyetlen példányát használja.

   <a id="max-connection"></a>
3. **Növelje az System.Net MaxConnections gazdagépenként átjáró mód használatakor**

    Az Azure Cosmos DB kérelmek végrehajtott HTTPS/REST átjáró mód használatakor, és vannak kitéve, állomásnév vagy IP-cím alapértelmezett kapcsolathoz megadott korlátot. Szükség lehet beállítani a MaxConnections értéke (100-1000), hogy az ügyféloldali kódtár képes használni az Azure Cosmos DB több egyidejű kapcsolatot. A .NET SDK 1.8.0-as és újabb, az alapértelmezett érték a [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) 50, és módosítsa az értéket, beállíthatja a [Documents.Client.ConnectionPolicy.MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx)értéke.   
4. **Particionált gyűjteményeknél végezzen párhuzamos lekérdezések hangolása**

     SQL .NET SDK 1.9.0-s verzió vagy újabb támogatási párhuzamos lekérdezések, amelyek lehetővé teszik egy particionált gyűjteménybe a párhuzamos lekérdezés (lásd: [az SDK-k használata](sql-api-partition-data.md#working-with-the-azure-cosmos-db-sdks) és a kapcsolódó [Kódminták](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs) további információ). Javítja a Lekérdezések késése és az átviteli sebesség soros többszörözi a párhuzamos lekérdezések lettek kialakítva. Párhuzamos lekérdezések adja meg, hogy a felhasználók hangolhassa a rájuk vonatkozó követelményeket, a Maxanalyticsunits illeszkedjen a két paramétert: vezérlőelemre, majd a partíciók maximális száma a párhuzamos és (b) MaxBufferedItemCount kérdezhetők le: a száma előzetesen beolvasott eredményeket.

    (a) ***hangolása Maxanalyticsunits\:***  párhuzamos lekérdezés működését több partíció párhuzamosan lekérdezésével. Azonban az egyes particionált gyűjtése származó adatok letöltődött tárolókonfigurációhoz garanciát a lekérdezést. Így a Maxanalyticsunits beállítása a partíciók számát rendelkezik jövedelmezőbb munkát tesznek lehetővé a legtöbb nagy teljesítményű lekérdezési maximális esélyét megadott rendszer összes egyéb feltételek változatlanok maradnak. Ha nem ismeri a partíciók száma, beállíthatja a Maxanalyticsunits nagy számú, és a rendszer úgy dönt, mint a Maxanalyticsunits minimális (partíciók, felhasználó által megadott bemeneti száma).

    Fontos megjegyezni, hogy párhuzamos lekérdezések a legjobb előnyöket előállításához, ha az adatok egyenletesen garanciát a lekérdezés összes partíciók között. Ha a particionált gyűjtemény particionált oly módon, hogy az összes vagy egy lekérdezés által visszaadott adatok többsége összpontosul néhány partíciók (egy partíciót a legrosszabb esetben), majd a lekérdezés teljesítményének lenne bottlenecked, ezek a partíciók szerint.

    (b) ***hangolása MaxBufferedItemCount\:***  párhuzamos lekérdezés úgy tervezték, hogy előre olvashatók be eredményeket, amíg az eredmények az aktuális köteg feldolgozása folyamatban van az ügyfél által. A lekérdezés teljes késés fokozása lehívását segít. MaxBufferedItemCount az a paraméter előre lehívott eredmények számának korlátozására. A lekérdezést úgy, hogy fogadjon a legnagyobb előny lehívását MaxBufferedItemCount adatsorban visszaadott várt számú (vagy nagyobb) beállítás lehetővé teszi.

    Lehívását attól függetlenül, a Maxanalyticsunits ugyanúgy működik, és az összes partíciót az adatok egyetlen puffert.  
5. **Kapcsolja be a kiszolgálóoldali globális Katalógus**

    Bizonyos esetekben segíthet a szemétgyűjtés gyakoriságának csökkentését. A .NET-ben, állítsa be [gcServer](https://msdn.microsoft.com/library/ms229357.aspx) igaz értékre.
6. **Leállítási megvalósítása RetryAfter időközönként**

    Teljesítmény tesztelése során terhelés mindaddig, amíg egy kis méretű kérések másodpercenkénti száma leszabályozza növelje meg. Ha szabályozott, az ügyfélalkalmazás kell leállásait, szabályozása az a kiszolgáló által megadott újrapróbálkozási időköz. A leállítási tiszteletben biztosítja, hogy az újrapróbálkozások közötti várakozási idő mennyisége minimális idő. Újrapróbálkozási házirend támogatás is biztosított, a verzió 1.8.0-as és újabb, az SQL [.NET](sql-api-sdk-dotnet.md) és [Java](sql-api-sdk-java.md), verzió 1.9.0-s és újabb, a [Node.js](sql-api-sdk-node.md) és [Python](sql-api-sdk-python.md), és az összes támogatott verzióján a [.NET Core](sql-api-sdk-dotnet-core.md) SDK-k. További információkért lásd: [több mint szolgáltatás számára fenntartott átviteli sebesség korlátok](request-units.md#RequestRateTooLarge) és [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx).
    
    1,19 és a .NET SDK újabb verziójú nincs olyan mechanizmus, hogy további diagnosztikai adatok és az alábbi mintában látható módon késési problémák elhárítása. A diagnosztikai karakterláncot a kéréseket, amelyek rendelkeznek a nagyobb olvasási késés jelentkezhet. A rögzített diagnosztikai karakterlánc segít megérteni, hogy hányszor megfigyelte 429s egy adott kérés esetében.
    ```csharp
    ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
    readDocument.RequestDiagnosticsString 
    ```
    
7. **Az ügyfél-alkalmazások és szolgáltatások méretezése**

    Ha nagy átviteli sebesség szinten teszteli (> 50 000 RU/s), az ügyfélalkalmazás miatt a gép kapacitástól meg a Processzor vagy a hálózati kihasználtság a szűk keresztmetszetté válhat. Ha eléri ezt a pontot, továbbra is küldje le az Azure Cosmos DB-fiókot további horizontális felskálázása az ügyfélalkalmazásokat, több kiszolgáló között.
8. **Gyorsítótár dokumentum URI-k kisebb olvasási késés**

    Gyorsítótár-dokumentum URI-k, amikor csak lehetséges, a legjobb olvasási teljesítmény. Meg kell adnia a logika gyorsítótárazza az erőforrás-azonosító, ha az erőforrás létrehozásához. Erőforrás-azonosító-alapú keresések neve alapján kereséseket, gyorsabban, így ezeket az értékeket gyorsítótárazás növeli a teljesítményt. 

   <a id="tune-page-size"></a>
1. **Az oldal méretét a lekérdezések és olvasási hírcsatornák, a jobb teljesítmény hangolása**

    Olvasási hírcsatorna-funkciókat (például ReadDocumentFeedAsync) használó dokumentumokat, vagy a tömeges végrehajtása olvasási, egy SQL-lekérdezést kiadásakor, az eredmény akkor szegmentált módon ha túl nagy az eredményhalmaz. Alapértelmezés szerint az eredmény akkor 100 elemet vagy 1 MB-os blokkonként, bármelyik korlát nyomja le az első.

    Kevesebb hálózati kerekíteni lelassítja az összes vonatkozó eredmények beolvasásához szükséges, növelhető a méret használatával [x-ms-max-item-count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) legfeljebb 1000 kérelem fejléce. Azokban az esetekben, ahol csak néhány eredmények megjelenítéséhez szüksége például, ha a felhasználói felület vagy a kérelem API függvény csak 10 eredménye egy idő, is csökkentheti, ha az oldal méretét a 10-re az olvasást és lekérdezések felhasznált átviteli sebesség csökkentése érdekében.

    Az oldal méretét a rendelkezésre álló Azure Cosmos DB SDK-k használatával is beállíthatja.  Példa:

        IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
10. **Növelje a szálak/feladatok száma**

    Lásd: [növelje a szálak/feladatok számát](#increase-threads) a hálózatkezelés szakaszban.

11. **Használja a 64 bites gazdagép feldolgozása**

    Az SQL SDK működik egy 32 bites gazdafolyamat SQL .NET SDK 1.11.4 verzió használata esetén, vagy újabb. Azonban ha közötti partíció lekérdezéseket használ, 64 bites gazdagép feldolgozása ajánlott jobb teljesítmény. A következő típusú alkalmazások a 32 bites gazdafolyamat, alapértelmezés szerint rendelkezik, így annak érdekében, hogy módosíthatja, hogy a 64 bites, kövesse ezeket a lépéseket az alkalmazás típusa alapján:

    - Futtatható alkalmazások esetében ezt megteheti eszközhitelesítést a **Prefer 32-bites** beállítást a **projekt tulajdonságai** ablakban, a a **hozhat létre** fülre.

    - VSTest alapú tesztelési projekt, ezt megteheti kiválasztásával **tesztelése**->**beállítások ellenőrzése**->**X64 processzorarchitektúrát alapértelmezett**, az a **Visual Studio Test** menüpont.

    - A helyileg üzembe helyezett ASP.NET webes alkalmazásokhoz, ezt megteheti úgy a **64 bites verzióját használja, az IIS Express webhelyek és projektek**alatt **eszközök**->**beállítások**  -> **Projektek és megoldások**->**webes projektek**.

    - Az ASP.NET webalkalmazások üzembe helyezése az Azure-ban, ehhez válassza ki a **64 bites platformként** a a **Alkalmazásbeállítások** az Azure Portalon.

## <a name="indexing-policy"></a>Indexelési házirend
 
1. **A fel nem használt elérési utak kizárása a gyorsabb írások indexelése**

    Cosmos DB indexelési házirendet is lehetővé teszi, hogy adja meg, mely a dokumentum elérési útjait belefoglalása vagy kizárása indexelő útvonalak (IndexingPolicy.IncludedPaths és IndexingPolicy.ExcludedPaths) kihasználásával az indexelés. Indexelő elérési utak használatát is kínál a továbbfejlesztett írási teljesítmény- és alacsonyabb index forgatókönyvekhez, ahol a lekérdezési mintáknak előzetesen ismert, mivel az indexelő költségek közvetlenül indexelt egyedi elérési utak száma közötti kapcsolatot.  Például a következő kód bemutatja, hogyan zárhat ki (más néven a dokumentumok egy teljes szakasz részfa) az indexelő használatával a "*" helyettesítő karakter.

    ```csharp
    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);
    ```

    További információkért lásd: [indexelési szabályzatok az Azure Cosmos DB](indexing-policies.md).

## <a name="throughput"></a>Teljesítmény
<a id="measure-rus"></a>

1. **Mérheti és finomhangolási feladat alacsonyabb kérelem kérelemegység/s használat**

    Az Azure Cosmos DB adatbázis-műveletek, beleértve a relációs és hierarchikus lekérdezéseket UDF-EK, tárolt eljárásokkal és eseményindítókkal adatbázis-gyűjteményekben dokumentumok gazdag választékát kínálja. A műveletekhez kapcsolódó költségek a CPU, IO és a művelet végrehajtásához szükséges alapján változik. Szem előtt tartva és hardver-erőforrások kezelése, helyett is felfoghatók kérelemegység (RU) azon erőforrások számos adatbázis-műveletek végrehajtásához, és adott alkalmazáskérelem kiszolgálásához szükséges egyetlen érték.

    Átviteli sebesség száma alapján van kiépítve [kérelemegységek](request-units.md) állítsa be az egyes tárolók. Kérelem-egységek felhasználását másodpercenkénti minősül. Alkalmazások, amelyek túllépik a tárolóban kiosztott kérelem egység díjaival korlátozva, mindaddig, amíg az arány a tároló a kiépített szint alá csökken. Ha az alkalmazás egy magasabb adatátviteli kapacitást igényel, további kérelemegységet kiépítésével növelheti az átviteli sebességet. 

    A lekérdezés összetettségétől hatással van egy művelet felhasznált Kérelemegységek számát. A predikátumok száma, a predikátumok UDF-EK számát és méretét a forrás adatkészlet összes jellege befolyásolhatják a lekérdezési műveletek költségét.

    Bármilyen műveletet járó többletterhelést mérésére (létrehozása, frissítése vagy törlése), vizsgálja meg a [x-ms-kérelem-díj](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) fejléc (vagy az azzal egyenértékű RequestCharge tulajdonság a ResourceResponse<T> vagy FeedResponse<T> a a. NETTÓ SDK), ezek a műveletek által felhasznált kérelemegységek számát.

    ```csharp
    // Measure the performance (request units) of writes
    ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);
    Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
    // Measure the performance (request units) of queries
    IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();
    while (queryable.HasMoreResults)
         {
              FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>();
              Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
         }
    ```             

    A kérelem díja a fejléc adja vissza a kiosztott átviteli sebesség töredékéért (azaz a 2000 kérelemegység / s). Például ha az előző lekérdezés 1000 1KB-dokumentumokat ad vissza, a költség, a művelet: 1000. Emiatt a belül egy második, a kiszolgáló figyelembe veszi előtt sebességével későbbi kérelmeket csak két ilyen kérelmeket. További információkért lásd: [Kérelemegységek](request-units.md) és a [kérelem egység Számológép](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **Kezeli a sebesség korlátozása/kérelmek arányának túl nagy**

    Amikor az ügyfél meghaladja a fenntartott átviteli sebesség egy olyan fiók, nincs teljesítmény csökkenése nélkül működhet a kiszolgálón, és felül a fenntartott átviteli kapacitás használatának tilalma. A kiszolgáló előrelátó módon RequestRateTooLarge (HTTP-állapotkódot 429-es) a kérelem befejezése, és adja vissza a [x-ms-újrapróbálkozási-után – az ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) jelző idő ezredmásodpercben, amely a felhasználónak várakoznia kell, mielőtt újrapróbálkozni azoknál a fejléc a kérelmet.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Az SDK-k az összes implicit módon ezt a választ a tényleges, a kiszolgáló által megadott retry-after fejléccel tiszteletben, és ismételje meg a kérelmet. Kivéve, ha a fiók több ügyfélnek egyszerre használja, a következő újrapróbálkozás sikeres lesz.

    Ha egynél több ügyfél felett a kérések mennyisége következetesen összesítve működő, az alapértelmezett újrapróbálkozások jelenlegi beállítása 9 belsőleg az ügyfél által esetleg nem elegendő; Ebben az esetben az ügyfél egy 429-es állapotkód DocumentClientException jelez az alkalmazásnak. Az alapértelmezett újrapróbálkozások ConnectionPolicy-példányon a RetryOptions beállításával módosítható. Alapértelmezés szerint a 429-es állapotkód DocumentClientException adja vissza egy halmozódó várakozási idő pedig 30 másodperc után, ha a kérés továbbra is működőképes fent a kérések aránya. Ez akkor fordul elő, még ha az aktuális újrapróbálkozások nem éri el az újrapróbálkozások maximális számát, legyen az alapértelmezett 9 vagy egy felhasználó által definiált értéket.

    Az automatikus újrapróbálkozási viselkedés segít a rugalmasság és a legtöbb alkalmazás használhatóság javítása érdekében, miközben, előfordulhat, hogy származnak, bármikor odds esetén teljesítményteszteket, különösen akkor, amikor a késleltetés mérése.  Az ügyfél-megfigyelt késés fog hirtelen, ha eléri a kiszolgáló szabályozása a kísérletet, és az ügyfél SDK-val csendes módban próbálja újra. Teljesítmény kísérletek során késés ugrásszerűen elkerüléséhez a díjat az egyes műveletek által visszaadott mérjük, és győződjön meg arról, hogy a kérelmek működnek-e alatt a szolgáltatás számára fenntartott kérések mennyisége. További információkért lásd: [Kérelemegységek](request-units.md).
3. **A nagyobb sebesség érdekében kisebb méretű dokumentumokra tervezése**

    A kérelem díja (azaz a kérelem feldolgozása költség) egy adott műveletnek közvetlenül visszamenőleges korrelációban állnak a dokumentum mérete. A nagy dokumentumok Operations drágább, mint a kisméretű dokumentumok műveletek.

## <a name="next-steps"></a>További lépések
Egy Azure Cosmos DB kiértékelése néhány ügyfél gépek a nagy teljesítményű forgatókönyvekhez használt mintaalkalmazás, lásd: [teljesítmény és méretezhetőség tesztelése az Azure Cosmos DB](performance-testing.md).

Ezenkívül az alkalmazás a méretezés és a nagy teljesítményű megtervezésével kapcsolatos további tudnivalókért lásd: [particionálás és skálázás az Azure Cosmos DB](partition-data.md).
