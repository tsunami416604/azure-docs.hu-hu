---
title: "A módosítást végzett adatcsatorna-támogatás az Azure Cosmos Adatbázisba |} Microsoft Docs"
description: "Azure Cosmos DB módosítás adatcsatorna támogatási használja a dokumentumok nyomon követéséhez és esemény-alapú feldolgozási például eseményindítók és gyorsítótárak és elemzési rendszerek frissítése."
keywords: "Adatcsatorna módosítása"
services: cosmos-db
author: arramac
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 2d7798db-857f-431a-b10f-3ccbc7d93b50
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: 
ms.topic: article
ms.date: 10/30/2017
ms.author: arramac
ms.openlocfilehash: 8ca4c7fb1ccfe1eb026de80e519894c0ff23028a
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="working-with-the-change-feed-support-in-azure-cosmos-db"></a>A módosítás adatcsatorna-támogatás az Azure Cosmos Adatbázisba használata

[Az Azure Cosmos DB](../cosmos-db/introduction.md) gyors és rugalmas globálisan replikált adatbázis, az IoT, játékok, kiskereskedelmi, jól alkalmazható és működési naplózási alkalmazások. Egy közös kialakítási mintában a ezeket az alkalmazásokat, hogy az adatok módosításait használja a további műveletek indítsa. Ezeket a további műveleteket is a következő lehet: 

* Indítására egy értesítést vagy az API-k hívása, amikor egy dokumentum hozzáadásakor vagy módosításakor.
* Az adatfolyam feldolgozása IoT vagy elemzés végrehajtása.
* További adatmozgás szinkronizálása a gyorsítótár, a keresőmotor vagy a data warehouse-ba, vagy a cold tárolási adatok archiválása.

A **módosítás hírcsatorna támogatási** az Azure Cosmos adatbázis létrehozását teszi lehetővé méretezhető és hatékony megoldások az egyes ezeket a mintákat a következő ábrán látható módon:

![Energiagazdálkodási valós idejű elemzési és számítógépes forgatókönyvek eseményvezérelt hírcsatorna használata Azure Cosmos DB módosítása](./media/change-feed/changefeedoverview.png)

> [!NOTE]
> Támogatási hírcsatorna módosítása az összes adatmodellek és a tárolók Azure Cosmos DB valósul meg. A módosítás hírcsatorna azonban a DocumentDB-ügyfél használatával olvasható, ezért rendezi sorba elemek JSON formátumban. Miatt az ügyfelek fog tapasztalni MongoDB formázást, JSON formátumú BSON dokumentumokat és a JSON eltérést formázott hírcsatorna módosítása. 

## <a name="how-does-change-feed-work"></a>Hogyan módosítás munkahelyi hírcsatorna?

Egy Azure Cosmos DB gyűjteményhez módosításai figyel módosításához Azure Cosmos DB works adatcsatorna támogatást. Majd exportálja azt jelzi, hogy módosultak a sorrendet, amelyben a módosítás a rendezett lista is. A változtatások megmaradnak, aszinkron módon történik, és fokozatosan lehet feldolgozni, és a kimeneti párhuzamos feldolgozás egy vagy több fogyasztó számára legyen elosztva. 

A módosítás három különböző módon hírcsatorna el tudja olvasni, ebben a cikkben leírtaknak megfelelően:

1.  [Az Azure Functions használatával](#azure-functions)
2.  [Az Azure Cosmos DB SDK használatával](#rest-apis)
3.  [Az Azure Cosmos DB módosítás hírcsatorna processzor szalagtár használata](#change-feed-processor)

A módosítás hírcsatorna minden partíció kulcs tartományon belül a dokumentumgyűjteményt érhető el, és így képes legyen elosztva párhuzamos feldolgozás egy vagy több fogyasztó számára a következő ábrán látható módon.

![Elosztott feldolgozásához hírcsatorna Azure Cosmos DB módosítása](./media/change-feed/changefeedvisual.png)

További részletek:
* Az összes fiók alapértelmezés szerint engedélyezve van a módosítás adatcsatornát.
* Használhatja a [kiosztott átviteli sebesség](request-units.md) a írási régióban vagy bármely [olvassa el a régió](distribute-data-globally.md) olvasni a hírcsatorna váltás, ugyanúgy, mint bármely más Azure Cosmos DB műveletet.
* A módosítás hírcsatorna beszúrások és a frissítési műveleteket a gyűjteményben lévő dokumentumokon végzett tartalmazza. Rögzítheti a törlések úgy, hogy a "soft-törlés" jelzőt belül a dokumentumok törlése helyett. Azt is megteheti, beállíthat egy véges lejárati időt a dokumentumok keresztül a [TTL funkció](time-to-live.md), például 24 óra és használja a törlések rögzítéséhez tulajdonság értékeként. A megoldás dolgozni a változásokat a TTL lejárata időszaknál rövidebb időintervallumon belül van.
* A dokumentum minden módosításakor pontosan egyszer jelenik meg a hírcsatorna módosítás, és ügyfelek kezelése az ellenőrzőpontok használata logikát. A módosítás adatcsatorna processzor kódtár biztosít automatikus ellenőrzőpont-készítés és a "legalább egyszeri" szemantikáját.
* Egy adott dokumentum csak a legutóbbi változás a módosítási napló tartalmazza. Előfordulhat, hogy köztes módosítások nem érhető el.
* A módosítás hírcsatorna szolgáló belül minden partíciókulcs-értékkel módosítást van rendezve. Nincs garantált rendelés partíciókulcs értékek között.
* Módosítások is szinkronizálhatja a bármely-időpontban, ez azt jelenti, hogy nincs nem rögzített adatmegőrzési időtartam, amelynek módosítások érhetők el.
* Változások a partíció kulcstartományokkal adattömböket írnak érhetők el. Ez a funkció lehetővé teszi, hogy a módosítások több fogyasztók/kiszolgáló párhuzamos feldolgozásra nagy gyűjteményekre.
* Alkalmazások kérhet több módosítás hírcsatornákra egyidejűleg az ugyanahhoz a gyűjteményekhez.

## <a name="use-cases-and-scenarios"></a>Használati esetek és forgatókönyvek

A módosítás hírcsatorna lehetővé teszi nagy mennyiségű írási műveletek nagy adatkészletek hatékony feldolgozás, illetve alternatívája azonosítására, hogy mi változott egy teljes adatkészlet lekérdezése. 

Például a hírcsatorna módosítását, feladatokat végezheti el a következő hatékonyan:

* Frissítse a gyorsítótárat, search-index vagy adatraktár Azure Cosmos DB-ben tárolt adatokkal.
* Alkalmazásszintű adatok rétegezési és archiválási alkalmazására, ez azt jelenti, hogy "gyakran használt adatokkal" tárolása Azure Cosmos DB és elavulnak "ritkán használt adatok", a [Azure Blob Storage](../storage/common/storage-introduction.md) vagy [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md).
* Kötegelt elemzés megvalósítása az adatok használatával [Apache Hadoop](run-hadoop-with-hdinsight.md).
* Egy másik particionálási sémát rendelkező Azure Cosmos DB fiókot nulla várakozási-ideje áttelepítés végrehajtható.
* Alkalmazzon [lambda folyamatok Azure](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) rendelkező Azure Cosmos DB. Azure Cosmos-adatbázis, amely kezeli az adatfeldolgozást és a lekérdezés, és az alacsony TCO lambda architektúrák megvalósítása adatbázis méretezhető megoldást kínál. 
* Kapni az eszközök, érzékelőket, infrastruktúra és az alkalmazások esemény adatainak tárolásához és feldolgozásához ezeket az eseményeket valós idejű [Azure Stream Analytics](../stream-analytics/stream-analytics-documentdb-output.md), [alatt futó Apache Storm](../hdinsight/storm/apache-storm-overview.md), vagy [Apache Spark](../hdinsight/spark/apache-spark-overview.md). 

A következő kép bemutatja, hogyan lambda folyamatok, amelyek mind a betöltési és lekérdezést Azure Cosmos DB használatával használható módosítani adatcsatorna támogatása: 

![Az Azure Cosmos DB-alapú lambda csővezeték adatfeldolgozást és lekérdezés](./media/change-feed/lambda.png)

Emellett belül a [kiszolgáló nélküli](http://azure.com/serverless) webes és mobilalkalmazások, például a felhasználói profil, beállítások, vagy helyre bizonyos műveletek, például a leküldéses értesítések küldése eszközekeit használatávalvalóváltozásokatnyomonkövethetiazeseményeketis[Az azure Functions](#azure-functions). Azure Cosmos DB játék összeállításához használata, is, például használata módosítása adatcsatorna megvalósításához a valós idejű ranglisták befejezett játékok származó eredmények alapján.

<a id="azure-functions"></a>
## <a name="using-azure-functions"></a>Az Azure Functions használatával 

Az Azure Functions használata, a legegyszerűbb egy Azure Cosmos DB módosítás adatcsatornához való kapcsolódáshoz módja egy Azure Cosmos DB eseményindító hozzáadása az Azure Functions alkalmazáshoz. Az Azure Functions alkalmazást egy Azure Cosmos DB eseményindítót hoz létre, amikor csatlakozni az Azure Cosmos DB gyűjtemény választja, és a funkció akkor váltódik ki, amikor a gyűjteménybe módosításakor. 

Eseményindítók hozhatók létre az Azure Functions portálon az Azure Cosmos DB portálon vagy programozottan. További információkért lásd: [Azure Cosmos DB: kiszolgáló nélküli adatbázis számítási Azure Functions használatával](serverless-computing-database.md).

<a id="rest-apis"></a>
## <a name="using-the-sdk"></a>Az SDK használatával

A [DocumentDB SDK-t](documentdb-sdk-dotnet.md) Azure Cosmos DB nyújtott olvassa el és kezelheti a hírcsatorna módosítása a teljesítményt. De a kiváló power származik sok feladatkörök, túl. Ha azt szeretné, ellenőrzési pontjainak felügyelete, a dokumentum sorszámok kezelésére és partíciókulcsok részletes szabályozhatják, majd a SDK használatával lehet a megfelelő módszert.

Ez a szakasz végigvezeti a DocumentDB SDK használatával történő együttműködésre a hírcsatorna módosítása.

1. Indítsa el a következő erőforrások olvasásakor az appconfig. A végpont és a hitelesítési kulcs lekérése a találhatók [frissítse a kapcsolati karakterlánc](create-documentdb-dotnet.md#update-your-connection-string).

    ``` csharp
    DocumentClient client;
    string DatabaseName = ConfigurationManager.AppSettings["database"];
    string CollectionName = ConfigurationManager.AppSettings["collection"];
    string endpointUrl = ConfigurationManager.AppSettings["endpoint"];
    string authorizationKey = ConfigurationManager.AppSettings["authKey"];
    ```

2. Az ügyfél létrehozása az alábbiak szerint:

    ```csharp
    using (client = new DocumentClient(new Uri(endpointUrl), authorizationKey,
    new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp }))
    {
    }
    ```

3. A partíció kulcstartományokkal beolvasása:

    ```csharp
    FeedResponse pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(
        collectionUri,
        new FeedOptions
            {RequestContinuation = pkRangesResponseContinuation });
     
    partitionKeyRanges.AddRange(pkRangesResponse);
    pkRangesResponseContinuation = pkRangesResponse.ResponseContinuation;
    ```

4. Hívás ExecuteNextAsync minden partíciós kulcs címtartomány:

    ```csharp
    foreach (PartitionKeyRange pkRange in partitionKeyRanges){
        string continuation = null;
        checkpoints.TryGetValue(pkRange.Id, out continuation);
        IDocumentQuery<Document> query = client.CreateDocumentChangeFeedQuery(
            collectionUri,
            new ChangeFeedOptions
            {
                PartitionKeyRangeId = pkRange.Id,
                StartFromBeginning = true,
                RequestContinuation = continuation,
                MaxItemCount = -1,
                // Set reading time: only show change feed results modified since StartTime
                StartTime = DateTime.Now - TimeSpan.FromSeconds(30)
            });
        while (query.HasMoreResults)
            {
                FeedResponse<dynamic> readChangesResponse = query.ExecuteNextAsync<dynamic>().Result;
    
                foreach (dynamic changedDocument in readChangesResponse)
                    {
                         Console.WriteLine("document: {0}", changedDocument);
                    }
                checkpoints[pkRange.Id] = readChangesResponse.ResponseContinuation;
            }
    }
    ```

Ha több olvasók, használhatja **ChangeFeedOptions** különböző szálakon vagy a különböző ügyfelek olvasási terhelés elosztása.

Ennyi az egész, ezek néhány sornyi kódot el lehet indítani a módosítás hírcsatorna olvasása. A teljes kód a jelen cikkben használt beszerezheti a [GitHub-tárház](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor).

A fenti 4. lépésben a kódban a **ResponseContinuation** az utolsó sor a legutóbbi logikai sorszáma (LSN) rendelkezik a dokumentum, amely új dokumentumok a sorszám után olvassa el a következő indításakor fogja használni. Használatával a **StartTime** , a **ChangeFeedOption** lekérni a dokumentumok a net szélességűre állítható. Tehát, ha a **ResponseContinuation** null értékű, azonban a **StartTime** vissza időben kerül majd óta megváltoztak a valamennyi dokumentumot elérhetővé válik a **StartTime**. De ha a **ResponseContinuation** értékkel rendelkezik, akkor a rendszer segítséget nyújtanak a dokumentumokat, hogy Naplósorszám óta.

Igen a checkpoint tömb csak megakadályozza a minden partíció esetében Naplósorszám. De ha nem szeretné a partíciók foglalkozik, ellenőrzőpontokat, Naplósorszám, kezdési időpont egyszerűbb beállítás stb., hogy a módosítás hírcsatorna processzor könyvtárban.

<a id="change-feed-processor"></a>
## <a name="using-the-change-feed-processor-library"></a>A módosítás hírcsatorna processzor könyvtár használata 

A [Azure Cosmos DB módosítás hírcsatorna processzor könyvtár](https://docs.microsoft.com/azure/cosmos-db/documentdb-sdk-dotnet-changefeed) esemény feldolgozása könnyen szét több felhasználóból nyújt segítséget. Ezt a szalagtárat olvasási módosítások partíciók és párhuzamosan működik több szál egyszerűbbé teszi.

Változás hírcsatorna processzor könyvtár fő előnye, hogy ne kelljen minden partíció kezelése és folytatási kód, és ne kelljen manuálisan kérdezze le az egyes gyűjtemények.

A módosítás hírcsatorna processzor könyvtár olvasási módosítások partíciók és párhuzamosan működik több szál egyszerűbbé teszi.  A bérleti mechanizmussal partíciók között olvasási módosítások automatikusan kezeli. Mint ha két, a módosítás hírcsatorna processzor könyvtár használó ügyfelek először az alábbi ábrán látható, akkor a munkát egymás között osztják. Növelje az ügyfelek továbbra is, mivel azok tartsa felosztásával a munkát egymás között.

![Elosztott feldolgozásához hírcsatorna Azure Cosmos DB módosítása](./media/change-feed/change-feed-output.png)

A bal oldali ügyfél első indult el, és indulása összes a partíciót, majd a második ügyfél indította el, majd az első segítségével nyissa meg néhány második ügyfél a címbérlet figyelése. Ez látható a töltött módja terjesztése a munkahelyi különböző gépek és az ügyfelek között.

Vegye figyelembe, hogy ha két kiszolgáló nélküli Azure funtions ugyanaz a gyűjtemény figyelése és az azonos bérleti használatával, akkor a két funkciók kaphat attól függően, hogy a processzor könyvtár úgy dönt, hogy észlelést a partíciók különböző dokumentumok.

### <a name="understanding-the-change-feed-processor-library"></a>A módosítás hírcsatorna processzor-könyvtár ismertetése

A módosítás hírcsatorna processzor végrehajtási négy fő összetevőből: a figyelt gyűjteményhez, a címbérlet gyűjteményt, a processzor gazdagép és a fogyasztók. 

> [!WARNING]
> Gyűjtemény létrehozása megegyezik árképzési hatással vannak, az alkalmazás Azure Cosmos DB kommunikálni átviteli lefoglalja. További részletekért tekintse meg a [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/cosmos-db/)
> 
> 

**Figyelt gyűjtemény:** a figyelt gyűjteményhez az adatok, a módosítás hírcsatorna képzésére szolgáló. Bármely beszúrások és a figyelt gyűjtemény módosításait a módosítási hírcsatorna a gyűjtemény is megjelennek. 

**Címbérlet gyűjtemény:** feldolgozása a módosítás több Worker közötti adatcsatorna bérleti gyűjtemény koordinátáit. Egy külön gyűjteményt a bérletek egy bérletet eredményez. partíciónként tárolására szolgál. Célszerű a címbérlet gyűjtemény tárolását a írási terület közelebb a módosítás hírcsatorna processzor futtató egy másik fiókot. A bérleti objektum tartalmazza a következő attribútumokat: 
* Tulajdonos: Adja meg a címbérlet birtokló állomás
* Folytatási: Meghatározza a módosítás egy adott partíció-hírcsatorna helyét (folytatási kód)
* Időbélyeg: Legutóbbi bérleti frissült; az időbélyeg segítségével ellenőrizze, hogy a bérlet lejárt tekinthető 

**Processzor-állomás:** minden állomás határozza meg, hány particionálja alapján más gazdagépek hány példányban vannak-e aktív bérletek folyamathoz. 
1.  A gazdagép indításakor minden gazdagép elosztásához címbérleteket szerez be. Egy gazdagép rendszeres időközönként megújítja címbérleteket,, címbérleteket aktív marad. 
2.  A gazdagép ellenőrzőpontokat az egyes bérletekhez utolsó folytatási olvasni. Párhuzamossági biztonsága érdekében a gazdagép minden egyes bérleti frissítés ETag ellenőrzi. Más ellenőrzőpont stratégiák használata is támogatott.  
3.  Leállásakor egy állomás kiadott összes címbérlet, de a folytatási információkért tartja, így újból engedélyezheti az olvasást a tárolt ellenőrzőponttól később. 

Jelenleg a gazdagépek száma nem lehet nagyobb, mint a partíciók (címbérleteket).

**A fogyasztók:** fogyasztók vagy dolgozó a szál által az egyes állomások által kezdeményezett hírcsatorna módosítása feldolgozási műveleteket. Mindegyik feldolgozó gazdagépnek több felhasználóból lehet. Mindegyik felhasználó olvassa be, a módosítás hírcsatorna a partícióból hozzá van rendelve, és változások a gazdagép értesítést küld, és címbérleteket lejárt.

További megértéséhez, hogy a következő négy elemeinek módosítása hírcsatorna processzor együttműködni, vizsgáljuk meg az alábbi ábra egy példát. A figyelt gyűjtemény tárolja a dokumentumokat, és a "város" használja, mint a partíciós kulcs. Látható, hogy a kék partíció és így tovább tartalmaz-e "A-E" a "város" mezőt a dokumentumokat. Nincsenek két olvasásakor a négy partíció párhuzamosan két fogyasztók rendelkező állomás. A nyilak a fogyasztók olvasásakor a hírcsatorna változásával konkrét helyet. Az első partícióban a sötétebb kék olvasatlan módosítások jelöli, míg a ritka kék jelenti. a módosítás adatcsatorna már olvasási változásaival. A gazdagépek a bérleti gyűjtemény a "Folytatás" érték az aktuális olvasási pozíció az egyes felhasználók nyomon követéséhez tárolására használható. 

![Az Azure Cosmos DB módosítás használatával hírcsatorna processzor állomás](./media/change-feed/changefeedprocessornew.png)

### <a name="working-with-the-change-feed-processor-library"></a>A módosítás hírcsatorna processzor dokumentumtárakat

Változás hírcsatorna processzor NuGet-csomag telepítéséhez először telepíteni: 

* Microsoft.Azure.DocumentDB, 1.13.1 verzió vagy újabb 
* Newtonsoft.Json, 9.0.1 verzió vagy újabb

Telepítse a [Microsoft.Azure.DocumentDB.ChangeFeedProcessor Nuget-csomag](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/) adja hozzá hivatkozásként.

A módosítás hírcsatorna processzor könyvtárban van, hogy végrehajtásához a következő:

1. Alkalmazzon egy **DocumentFeedObserver** objektum, amely **IChangeFeedObserver**.

2. Alkalmazzon egy **DocumentFeedObserverFactory**, amely megvalósítja a **IChangeFeedObserverFactory**.

3. Az a **CreateObserver** metódusában **DocumentFeedObserverFacory**, hozható létre a **ChangeFeedObserver** az 1. lépésben létrehozott, és vissza.

    ```
    public IChangeFeedObserver CreateObserver()
    {
              DocumentFeedObserver newObserver = new DocumentFeedObserver(this.client, this.collectionInfo);
              return newObserver;
    }
    ```

4. Példányosítható **DocumentObserverFactory**.

5. Hozható létre egy **ChangeFeedEventHost**:

    ```csharp
    ChangeFeedEventHost host = new ChangeFeedEventHost(
                     hostName,
                     documentCollectionLocation,
                     leaseCollectionLocation,
                     feedOptions,
                     feedHostOptions);
    ```

6. Regisztrálja a **DocumentFeedObserverFactory** a gazdagéphez.

A kód a 4 – 6. lépéseket a következő: 

```
ChangeFeedOptions feedOptions = new ChangeFeedOptions();
feedOptions.StartFromBeginning = true;

ChangeFeedHostOptions feedHostOptions = new ChangeFeedHostOptions();
 
// Customizing lease renewal interval to 15 seconds.
// Can customize LeaseRenewInterval, LeaseAcquireInterval, LeaseExpirationInterval, FeedPollDelay
feedHostOptions.LeaseRenewInterval = TimeSpan.FromSeconds(15);
 
using (DocumentClient destClient = new DocumentClient(destCollInfo.Uri, destCollInfo.MasterKey))
{
        DocumentFeedObserverFactory docObserverFactory = new DocumentFeedObserverFactory(destClient, destCollInfo);
        ChangeFeedEventHost host = new ChangeFeedEventHost(hostName, documentCollectionLocation, leaseCollectionLocation, feedOptions, feedHostOptions);
        await host.RegisterObserverFactoryAsync(docObserverFactory);
        await host.UnregisterObserversAsync();
}
```

Ennyi az egész. Néhány lépések után dokumentumok beérkező indul a **DocumentFeedObserver ProcessChangesAsync** metódust.

## <a name="next-steps"></a>Következő lépések

Az Azure Functions Azure Cosmos DB használatával kapcsolatos további információk: [Azure Cosmos DB: kiszolgáló nélküli adatbázis számítási Azure Functions használatával](serverless-computing-database.md).

További tájékoztatást a módosítás hírcsatorna processzor kódtár használatával használja a következőket:

* [Információ lap](documentdb-sdk-dotnet-changefeed.md) 
* [Nuget-csomag](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)
* [1-6 lépéseket bemutató mintakód](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor)
* [További minták a Githubon](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)

További információ a módosítás hírcsatorna keresztül az SDK használatával használja a következőket:

* [SDK adatai lap](documentdb-sdk-dotnet.md)
