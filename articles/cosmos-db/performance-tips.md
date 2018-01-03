---
title: ".NET-keretrendszerhez készült Azure Cosmos DB teljesítmény tippek |} Microsoft Docs"
description: "Ismerje meg az ügyfél-konfigurációs beállítások Azure Cosmos DB adatbázis teljesítményének javítása érdekében"
keywords: "adatbázis teljesítményének javításával"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 94ff155e-f9bc-488f-8c7a-5e7037091bb9
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: mimig
ms.openlocfilehash: 84a1913bd218d512f7f2818291f59d98628a7272
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2018
---
> [!div class="op_single_selector"]
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 
> 

# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Teljesítmény tippek az Azure Cosmos DB és .NET

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

Azure Cosmos-adatbázis egy gyors és rugalmas elosztott adatbázis, amely zökkenőmentesen méretezi, garantált késéssel és átviteli sebesség. Nincs a fő architektúra módosításokat, vagy írja meg az adatbázist az Azure Cosmos DB méretezési komplex kódot. Felfelé és lefelé skálázás be lehető legkönnyebben egyetlen API-hívás vagy [SDK metódus hívása](set-throughput.md#set-throughput-sdk). Azonban mivel Azure Cosmos DB hálózati hívások keresztül érhető el nincsenek ügyféloldali optimalizálása, hogy használata esetén a csúcsteljesítmény eléréséhez a [SQL .NET SDK](documentdb-sdk-dotnet.md).

Ezért ha még kérése "Hogyan javítható az adatbázis teljesítménye?" Vegye figyelembe a következő lehetőségeket:

## <a name="networking"></a>Hálózat
<a id="direct-connection"></a>

1. **Kapcsolat házirend: közvetlen kapcsolat mód használata**

    Hogyan ügyfél csatlakozik-e Azure Cosmos DB rendelkezik fontos gyakorolt hatása a teljesítményre, különös tekintettel az megfigyelt ügyféloldali késés. Nincsenek elérhető az ügyfél kapcsolatkezelési házirendet – a kapcsolat konfigurálása két fő konfigurációs beállítások *mód* és a [kapcsolat *protokoll*](#connection-protocol).  A két rendelkezésre álló módok a következők:

   1. Átjáró mód (alapértelmezés)
   2. Közvetlen mód

      Átjáró mód minden SDK-platformon támogatott, és a beállított alapértelmezett.  Ha az alkalmazás fut a vállalati hálózatról szigorú tűzfal korlátozások, az átjáró mód azért, mert a szabványos HTTPS-port és egy végpontot használ a legjobb választás. A teljesítmény kompromisszumot azonban, hogy az átjáró mód érint egy további hálózati ugrások minden alkalommal, amikor adatait olvasni vagy írni az Azure Cosmos DB. Emiatt a közvetlen üzemmódban miatt kevesebb hálózati ugrásokat jobb teljesítményt nyújt.
<a id="use-tcp"></a>
2. **Kapcsolat házirend: a TCP protokoll**

    Ha közvetlen módot, két módon protokoll érhető el:

   * TCP
   * HTTPS

     Azure Cosmos-adatbázis egy egyszerű és nyitott RESTful programozási modellt biztosít, HTTPS-KAPCSOLATON keresztül. Emellett kínál egy hatékony TCP protokoll, amely egyben a RESTful a kommunikációs modellben és a .NET SDK ügyfélen keresztül érhető el. Közvetlen TCP és a HTTPS egyaránt SSL használata a kezdeti hitelesítési és titkosítási forgalom. A legjobb teljesítmény érdekében használjon a TCP protokollt, ha lehetséges.

     Ha TCP az átjáró módban, TCP 443-as porton az Azure Cosmos DB portot, és 10255 a MongoDB API port. Ha TCP közvetlen módban átjáró portokon kívül, gondoskodnia kell arról a port 10000 és 20000 között nyitva, mert az Azure Cosmos DB dinamikus TCP-portot használja. Ha ezeket a portokat nem nyitva, és próbálja meg használni a TCP, hibaüzenet 503-as szolgáltatás nem érhető el.

     A csatlakozási mód úgy van beállítva, a ConnectionPolicy paraméterrel DocumentClient példányának létrehozása során. Közvetlen mód használata esetén a protokoll is megadható a ConnectionPolicy paraméter belül.

    ```C#
    var serviceEndpoint = new Uri("https://contoso.documents.net");
    var authKey = new "your authKey from the Azure portal";
    DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
    new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp
    });
    ```

    TCP csak akkor támogatott a közvetlen mód, ha átjáró módot használ, mert a HTTPS protokoll mindig segítségével található átjáróval kommunikálni, és a ConnectionPolicy protokoll értékét a rendszer figyelmen kívül hagyja.

    ![Az Azure Cosmos DB kapcsolatkezelési házirendet ábrája](./media/performance-tips/connection-policy.png)

3. **Az első kérelem indítási késleltetés elkerülése érdekében OpenAsync hívása**

    Alapértelmezés szerint az első kérelem egy nagyobb késleltetéssel járhat van, mert a cím útvonaltábla beolvasása. Ez az első kérésre indítási késleltetés elkerülése érdekében célszerű hívni OpenAsync() egyszer az inicializálás során az alábbiak szerint.

        await client.OpenAsync();
   <a id="same-region"></a>
4. **A teljesítmény azonos Azure régióban ügyfelek elhelyezésének engedélyezése**

    Ha lehetséges, helyezze el az alkalmazásokat, Azure Cosmos DB hívja az Azure Cosmos DB adatbázis ugyanabban a régióban. Hozzávetőleges összehasonlításhoz hívások Azure Cosmos DB belül ugyanabban a régióban 1-2 ms belül befejeződik, de nyugati és az Egyesült Államok, keleti part közötti késés > 50 ms. Ez a késés valószínűleg eltérőek lehetnek kérelem kérelem attól függően, hogy az útvonal kérés hajtja végre, mivel az ügyfél az Azure-adatközpontban határ adja át. A lehető legkisebb késleltetést érhető el, biztosítva, hogy a hívó alkalmazás helyen belüli és a kiépített Azure Cosmos DB végpont Azure ugyanabban a régióban. Elérhető régiók listáját lásd: [Azure-régiókat](https://azure.microsoft.com/regions/#services).

    ![Az Azure Cosmos DB kapcsolatkezelési házirendet ábrája](./media/performance-tips/same-region.png)
   <a id="increase-threads"></a>
5. **Növeli a szálak/feladatok száma**

    Az Azure Cosmos Adatbázishoz hívások a hálózaton keresztül, mert szükség lehet a kérelmek párhuzamosságát fokának eltérőek, hogy az ügyfélalkalmazás fordít várakozó kérelmek között nagyon rövid ideig. Ha például használata. NET tartozó [feladat párhuzamos könyvtár](https://msdn.microsoft.com//library/dd460717.aspx), sorrendben 100-as egység olvasása vagy írása Azure Cosmos DB feladatok létrehozása.

## <a name="sdk-usage"></a>SDK használata
1. **A legutóbbi SDK telepítése**

    Az Azure Cosmos DB SDK-k a rendszer folyamatosan javult a legjobb teljesítmény elérése érdekében. Tekintse meg a [Azure Cosmos DB SDK](documentdb-sdk-dotnet.md) lapok használatával állapítsa meg a legutóbbi SDK, és tekintse át a fejlesztései.
2. **Az alkalmazás teljes egypéldányos Azure Cosmos DB ügyfél használata**

    Efach DocumentClient példány szálbiztos, végrehajtja a hatékony kapcsolat kezelése és a címek gyorsítótárazása, ha közvetlen módban működik. Engedélyezése hatékony kapcsolat kezelése és DocumentClient által jobb teljesítmény érdekében javasoljuk, hogy egyetlen példány futhat egy AppDomain DocumentClient használni az alkalmazás élettartamának.

   <a id="max-connection"></a>
3. **Növelhető a System.NET névtérbeli MaxConnections állomásonként, ha az átjáró módban**

    Az Azure Cosmos DB kérelmek átjáró üzemmódban HTTPS/REST keresztül történik, és az alapértelmezett kapcsolati felső határ az egyes állomásnév vagy IP-cím kitéve. Szükség lehet ahhoz a MaxConnections (100-1000) magasabb értékre, hogy az ügyféloldali kódtár nyújthatnak Azure Cosmos DB több egyidejű kapcsolatok. A .NET SDK 1.8.0 és az alapértelmezett érték felett [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) 50, és módosítsa az értéket, beállíthatja a [Documents.Client.ConnectionPolicy.MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx)magasabb értékre.   
4. **A particionált gyűjtemények párhuzamos lekérdezések hangolása**

     SQL .NET SDK 1.9.0 verzió vagy újabb támogatási párhuzamos lekérdezések, amelyek lehetővé teszik egy particionált gyűjtemény párhuzamos lekérdezés (lásd: [az SDK-k használata](sql-api-partition-data.md#working-with-the-azure-cosmos-db-sdks) és a kapcsolódó [Kódminták](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs) további információt a). Párhuzamos lekérdezések úgy tervezték, hogy a soros megfelelőjére javítja a lekérdezés-késleltetés és a teljesítményt. Párhuzamos lekérdezések adja meg, hogy a felhasználók észlelheti a rájuk vonatkozó követelményeket, a MaxDegreeOfParallelism egyéni méretezése két paramétert: szabályozhatja a maximális számú partíciót majd, és (b) MaxBufferedItemCount kérdezhetők le: a száma előre lehívott eredmények.

    (a) ***hangolása MaxDegreeOfParallelism\:***  párhuzamos lekérdezés works több partíció párhuzamosan lekérdezésével. Azonban az egyes particionált összegyűjtendő adatok beolvassa Feladattervek tekintetében a lekérdezést. Igen a MaxDegreeOfParallelism beállítást a partíciók számának rendelkezik elérése érdekében a legtöbb performant lekérdezés maximális esélyét megadott egyéb rendszer feltételek változatlanok maradnak. Ha nem ismeri a partíciók számát, a MaxDegreeOfParallelism állíthatja be a magas érték, és a rendszer úgy dönt, mint a MaxDegreeOfParallelism minimális (partíciók, felhasználó által megadott bemeneti száma).

    Fontos megjegyezni, hogy párhuzamos lekérdezések eredményez-e a legjobb előnyöket, ha az adatok tekintetében a lekérdezés összes partíciójára egyenletesen vannak elosztva. Ha a particionált gyűjtemény particionálva van-e olyan módon, hogy néhány partíciók (egy partíciót a legrosszabb esetben) az összes vagy egy lekérdezés által visszaadott adatok többsége van koncentrált, majd a lekérdezés teljesítményét volna kell szűk keresztmetszetét adja ki ezeket a partíció.

    (b) ***hangolása MaxBufferedItemCount\:***  párhuzamos lekérdezések arra tervezték, hogy előre fetch eredmények, amíg a jelenlegi köteg eredményeinek feldolgozása folyamatban van az ügyfél által. A lekérdezések teljes késést fokozása lehívását segít. MaxBufferedItemCount megadása a paraméter előre lehívott eredmények számát. A várt számú eredményt adott vissza a MaxBufferedItemCount (vagy magasabb azonosítószámot) beállítás lehetővé teszi, hogy a lekérdezés maximális haszna nem származik, a lehívását.

    Lehívását függetlenül a MaxDegreeOfParallelism ugyanúgy működik, és minden olyan partíciónak az adatait az egyetlen puffert.  
5. **Kiszolgálóoldali GC bekapcsolása**

    Bizonyos esetekben segíthet a szemétgyűjtés gyakoriságának csökkentését. A .NET, állítsa be [gcServer](https://msdn.microsoft.com/library/ms229357.aspx) igaz értékre.
6. **Leállítási RetryAfter időközönként megvalósítása**

    Teljesítmény tesztelés során terhelés csak a kis mértékben kérelmek get halmozódni növelje meg. Ha szabályozva, az ügyfélalkalmazás kell a késleltetési leállítási a kiszolgáló által megadott újrapróbálkozási időköz. A leállítási tiszteletben biztosítja, hogy az újrapróbálkozások közötti várakozási idő a lehető legrövidebb idő. Újrapróbálkozási házirend támogatása a rendszer része, a verzió 1.8.0 és az újabb, az SQL [.NET](sql-api-sdk-dotnet.md) és [Java](sql-api-sdk-java.md), 1.9.0 verzió vagy újabb verzió, a [Node.js](sql-api-sdk-node.md) és [Python](sql-api-sdk-python.md), és az összes támogatott verziója a [.NET Core](sql-api-sdk-dotnet-core.md) SDK-k. További információkért lásd: [Exceeding fenntartott átviteli sebességének korlátai](request-units.md#RequestRateTooLarge) és [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx).
7. **Horizontális felskálázás az ügyfél-alkalmazások és szolgáltatások**

    Ha nagy átviteli szinten teszteli (> 50 000 RU/mp), az ügyfélalkalmazás gép határértékét el a Processzor- vagy hálózati kihasználtsága miatt a szűk keresztmetszetek válhat. Ha eléri ezt a pontot, továbbra is az Azure Cosmos DB fiók további leküldéses által az ügyfélalkalmazások kiterjesztése több kiszolgáló között.
8. **Gyorsítótár-dokumentum URI-azonosítók az alsó olvasási késleltetés**

    Gyorsítótár-dokumentum URI-k, amikor csak lehetséges, a legjobb olvasási teljesítményt.
   <a id="tune-page-size"></a>
9. **A jobb teljesítmény lekérdezések/olvasás hírcsatornák lapmérete hangolása**

    Olvasási hírcsatorna funkciók (például ReadDocumentFeedAsync) használó dokumentumokat, vagy a tömeges végrehajtása olvasási, egy SQL-lekérdezést kiállításához, eredményeinek szegmentált módon ha eredménykészlet túl nagy. Alapértelmezés szerint eredményeinek 100 elemet vagy 1 MB adattömböket, bármelyik korlát találati első.

    Adjon meg kevesebb hálózati kerekíteni összes vonatkozó eredmények beolvasásához szükséges utakat, növelheti a lap méret használatával [x-ms-maximális elem-darabszám](https://docs.microsoft.com/rest/api/documentdb/common-documentdb-rest-request-headers) legfeljebb 1000 kérelemfejlécet. Azokban az esetekben, ahol csak néhány eredmények megtekintése céljából kell például a felhasználói felület vagy a kérelem API visszatérési értéke csak 10 egyszerre annak az eredménye, csökkentheti a 10-re csökkenteni a teljesítményt, Olvasás, mind a lekérdezések felhasznált mérete.

    Az oldalméret, az elérhető Azure Cosmos DB SDK-k használatával is megadhat.  Példa:

        IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
10. **Növeli a szálak/feladatok száma**

    Lásd: [szálak/feladatok számának növelése](#increase-threads) hálózatkezelés szakaszában.

11. **Használja a 64 bites állomás feldolgozása**

    Az SQL SDK működik a 32 bites gazdafolyamat SQL .NET SDK 1.11.4 verzió használata esetén és újabb verzióit. Azonban használatakor alhálózatok közötti partíció lekérdezések, 64 bites állomás feldolgozása ajánlott javítja a teljesítményt. A következő típusú alkalmazásokat a 32 bites gazdafolyamat, alapértelmezés szerint rendelkezik, így ahhoz, hogy módosítsa, 64 bites, kövesse ezeket a lépéseket az alkalmazás típusától függően:

    - Végrehajtható alkalmazások, ehhez jelének a **előnyben részesítik a 32 bites** beállítást a **projekt Tulajdonságok** ablakban, a a **Build** lapon.

    - VSTest alapú teszt projektek, ehhez kiválasztásával **tesztelése**->**beállítások ellenőrzése**->**X64 processzorarchitektúrát alapértelmezett**, az a **Visual Studio Test** menüjét.

    - Helyileg telepített ASP.NET webes alkalmazásokhoz, ehhez ellenőrzésével a **az IIS Express 64 bites verzióját használja a webhelyek és projektek**a **eszközök**->**beállítások**  -> **Projektek és megoldások**->**webes projektek**.

    - ASP.NET webes alkalmazásokhoz, Azure rendszerbe, ehhez válassza ki a **64 bites platformok** a a **Alkalmazásbeállítások** az Azure portálon.

## <a name="indexing-policy"></a>Indexelési házirend
 
1. **Nem használt elérési utak kizárása a gyorsabb írási az indexelő**

    Cosmos DB tartozó indexelési házirendet is lehetővé teszi, mely dokumentum elérési vagy kizárja a indexelő elérési utakon található (IndexingPolicy.IncludedPaths IndexingPolicy.ExcludedPaths), ami indexelésének megadásához. Indexelő elérési utak használatát is kínál a javított teljesítménye és alacsonyabb index tárolási forgatókönyvek, ahol a lekérdezési mintáknak előzetesen ismert, mivel az indexelő költségek közvetlenül indexelt egyedi elérési utak száma közötti kapcsolatot.  Például a következő kód bemutatja, hogyan egy teljes szakasz a dokumentumok (más néven kizárása egy részfája) indexelési használja a "*" helyettesítő karakter.

    ```C#
    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);
    ```

    További információkért lásd: [Azure Cosmos DB házirendek indexelő](indexing-policies.md).

## <a name="throughput"></a>Teljesítmény
<a id="measure-rus"></a>

1. **Mérését meg és hangolható alacsonyabb kérelem egység/másodperc kihasználtsága**

    Azure Cosmos-adatbázis adatbázis-műveleteket, köztük a felhasználó által megadott függvények, tárolt eljárások és eseményindítók – összes működési egy adatbázis-gyűjteményben lévő dokumentumokon hierarchikus és relációs lekérdezések széles skáláját kínál. E műveletek költsége attól függően változik, a CPU IO és memória a művelet befejezéséhez szükséges. Továbbléphetnek és hardver-erőforrások kezelése helyett tulajdonképpen egy kérelem egységet (RU) egyetlen intézkedésként az adatbázis különböző műveleteket végezhet, és az alkalmazás kérelem kiszolgálásához szükséges erőforrásokhoz.

    Átviteli sebesség ki van építve a száma alapján [egységek kérelem](request-units.md) minden egyes tároló beállítása. Kérelem egység fogyasztás másodpercenkénti történik. Alkalmazások, amelyek mérete meghaladja a kiépített kérelmek egység aránya a tároló korlátozva, amíg a sebesség esik ahhoz a tárolóhoz kiosztott szint alatt. Ha az alkalmazás egy magasabb szintű teljesítmény, a további kérelemegység kiépítés is megnövelheti az átviteli sebesség. 

    A lekérdezés összetettsége hatással van kérelem egységek művelet végrehajtásánál. Predikátumok a száma, a predikátum, felhasználó által megadott függvények száma és a forrás adatkészlet összes mérete befolyásolja a lekérdezési műveletek költségét.

    A terhelést növelni az összes műveletet mérésére (létrehozása, frissítése vagy törlése), vizsgálja meg a [x-ms-kérelem-kell fizetni](https://docs.microsoft.com/rest/api/documentdb/common-documentdb-rest-response-headers) fejléc (vagy a megfelelő RequestCharge tulajdonságot az ResourceResponse<T> vagy FeedResponse<T> a a. NETTÓ SDK) használni ezeket a műveleteket kérelem egységek számának mérésére.

    ```C#
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

    A kérelem kell fizetni, ezt a fejlécet a visszaadott részét a létesített átviteli sebesség (azaz 2000 RUs / másodperc). Például ha az előző lekérdezés függvény 1000 1KB-dokumentumok, a költség, a művelet érték 1000. Ilyen belül egy második, a kiszolgáló eleget tegyen csak két ilyen kérelmeket előtt szabályozás későbbi kérelmeket. További információkért lásd: [egységek kérelem](request-units.md) és a [kérelem egység Számológép](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **Kezeli a sebesség korlátozása/kérelmek aránya túl nagy**

    Amikor az ügyfél meghaladja a fenntartott átviteli sebesség egy olyan fiók, nincs teljesítmény csökkenése nélkül működhet a kiszolgálón, és felül a fenntartott átviteli sebesség nincs használatban. A kiszolgáló megelőző jelleggel a RequestRateTooLarge (HTTP-állapotkód: 429) kérelem befejezése és térjen vissza a [x-ms-újrapróbálkozási-után-ms](https://docs.microsoft.com/rest/api/documentdb/common-documentdb-rest-response-headers) idő ezredmásodpercben, amely a felhasználó kell várnia, mielőtt megoldódhat jelző fejléc a kérelmet.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Az SDK-k minden implicit módon dolgozza fel ezt a választ, tiszteletben tartják a kiszolgáló által megadott újrapróbálkozási után fejlécet, és próbálkozzon újra a kéréssel. Kivéve, ha a fiók több ügyfélnek egyszerre használja, a következő újrapróbálkozási sikeres lesz.

    Ha egynél több ügyfél felett a kérelmek aránya következetesen összesítve működő, az alapértelmezett újrapróbálkozások száma pillanatnyilag beállított 9 belső az ügyfél nem lehetséges, hogy elegendő; Ebben az esetben az ügyfél egy DocumentClientException állapotkóddal 429 jelez az alkalmazást. Az alapértelmezett újrapróbálkozások száma módosítható úgy, hogy a RetryOptions ConnectionPolicy-példányon. Alapértelmezés szerint a DocumentClientException 429. állapotkód: esetén visszaadott 30 másodperc halmozódó várakozási idő után a kérelem folytatja a működést a kérelmek aránya fent. Ez akkor fordul elő, még ha az aktuális újrapróbálkozások maximális számát nem éri el az újrapróbálkozások maximális száma, hogy azt az alapértelmezett 9 vagy egy felhasználó által definiált értéket.

    Az automatizált újrapróbálkozásra segít a rugalmasság és a legtöbb alkalmazás használhatóság javítása érdekében, miközben, előfordulhat, hogy térjen bármikor odds teljesítménymutatókat, során, különösen akkor, ha a késleltetés mérése.  Az ügyfél-megfigyelt késés fog lefoglalását, ha a kísérlet a kiszolgáló szabályozásának találatok és következtében az ügyfél SDK használatával beavatkozás nélkül próbálja meg újra. Teljesítmény kísérletek során késleltetés igényeiben jelentkező elkerüléséhez mérjük a díj minden művelet által visszaadott, és győződjön meg arról, hogy a kérelmek működnek-e a fenntartott kérelmek aránya alatt. További információkért lásd: [egységek kérelem](request-units.md).
3. **Nagyobb átviteli sebesség eléréséhez kisebb dokumentumok kialakítása**

    A kérelem költség (azaz Kérelemfeldolgozás költség) egy adott művelet közvetlenül visszamenőleges korrelációban állnak a dokumentum méretét. Műveletek nagy dokumentumok drágább, mint műveletek kis dokumentumokhoz.

## <a name="next-steps"></a>További lépések
Olyan mintaalkalmazásért, nagy teljesítményű forgatókönyvek néhány ügyfélszámítógépekre Azure Cosmos DB értékeléséhez használt, lásd: [teljesítmény és méretezhetőség Azure Cosmos DB tesztelték](performance-testing.md).

Az alkalmazás a méretezés és magas teljesítménnyel megtervezésével kapcsolatos további tudnivalókért lásd még [particionálás és az Azure Cosmos Adatbázisba skálázás](partition-data.md).
