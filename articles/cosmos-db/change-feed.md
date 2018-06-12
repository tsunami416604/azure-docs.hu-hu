---
title: A módosítást végzett adatcsatorna-támogatás az Azure Cosmos Adatbázisba |} Microsoft Docs
description: Azure Cosmos DB módosítás adatcsatorna támogatási használja a dokumentumok nyomon követéséhez és esemény-alapú feldolgozási például eseményindítók és gyorsítótárak és elemzési rendszerek frissítése.
keywords: Adatcsatorna módosítása
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: rafats
ms.openlocfilehash: 2600565493a334c7227e5c0d67a5808f30751108
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261068"
---
# <a name="working-with-the-change-feed-support-in-azure-cosmos-db"></a>A módosítás adatcsatorna-támogatás az Azure Cosmos Adatbázisba használata

[Az Azure Cosmos DB](../cosmos-db/introduction.md) gyors és rugalmas globálisan replikált adatbázis, az IoT, játékok, kiskereskedelmi, jól alkalmazható és működési naplózási alkalmazások. Egy közös kialakítási mintában a ezeket az alkalmazásokat, hogy az adatok módosításait használja a további műveletek indítsa. Ezeket a további műveleteket is a következő lehet: 

* Indítására egy értesítést vagy az API-k hívása, amikor egy dokumentum hozzáadásakor vagy módosításakor.
* Az adatfolyam feldolgozása IoT vagy elemzés végrehajtása.
* További adatmozgás szinkronizálása a gyorsítótár, a keresőmotor vagy a data warehouse-ba, vagy a cold tárolási adatok archiválása.

A **módosítás hírcsatorna támogatási** az Azure Cosmos adatbázis létrehozását teszi lehetővé méretezhető és hatékony megoldások az egyes ezeket a mintákat a következő ábrán látható módon:

![Energiagazdálkodási valós idejű elemzési és számítógépes forgatókönyvek eseményvezérelt hírcsatorna használata Azure Cosmos DB módosítása](./media/change-feed/changefeedoverview.png)

> [!NOTE]
> Támogatási hírcsatorna módosítása az összes adatmodellek és a tárolók Azure Cosmos DB valósul meg. A módosítás hírcsatorna azonban az SQL-ügyfélprogrammal olvasható, ezért rendezi sorba elemek JSON formátumban. Miatt az ügyfelek fog tapasztalni MongoDB formázást, JSON formátumú BSON dokumentumokat és a JSON eltérést formázott hírcsatorna módosítása.

Az alábbi videó az Azure Cosmos DB Programvezető Andrew Liu bemutatja, hogyan Azure Cosmos DB módosítása hírcsatorna működik.

> [!VIDEO https://www.youtube.com/embed/mFnxoxeXlaU]
>
>

## <a name="how-does-change-feed-work"></a>Hogyan módosítás munkahelyi hírcsatorna?

Egy Azure Cosmos DB gyűjteményhez módosításai figyel módosításához Azure Cosmos DB works adatcsatorna támogatást. Majd exportálja azt jelzi, hogy módosultak a sorrendet, amelyben a módosítás a rendezett lista is. A változtatások megmaradnak, aszinkron módon történik, és fokozatosan lehet feldolgozni, és a kimeneti párhuzamos feldolgozás egy vagy több fogyasztó számára legyen elosztva. 

A módosítás három különböző módon hírcsatorna el tudja olvasni, ebben a cikkben leírtaknak megfelelően:

*   [Az Azure Functions használatával](#azure-functions)
*   [Az Azure Cosmos DB SDK használatával](#sql-sdk)
*   [Az Azure Cosmos DB módosítás használatával hírcsatorna processzor könyvtár](#change-feed-processor)

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
* ChangeFeedOptions.StartTime is használható, például egy kezdeti kiindulási pont, így, megadott idő megfelelő folytatási kereséséhez. A ContinuationToken, ha meg van adva, a WINS-be a StartTime és StartFromBeginning értékek. A pontosság ChangeFeedOptions.StartTime ~ 5 másodperc. 

## <a name="use-cases-and-scenarios"></a>Használati esetek és forgatókönyvek

A módosítás hírcsatorna lehetővé teszi nagy mennyiségű írási műveletek nagy adatkészletek hatékony feldolgozás, illetve alternatívája azonosítására, hogy mi változott egy teljes adatkészlet lekérdezése. 

Például a hírcsatorna módosítását, feladatokat végezheti el a következő hatékonyan:

* Frissítse a gyorsítótárat, search-index vagy adatraktár Azure Cosmos DB-ben tárolt adatokkal.
* Alkalmazásszintű adatok rétegezési és archiválási alkalmazására, ez azt jelenti, hogy "gyakran használt adatokkal" tárolása Azure Cosmos DB és elavulnak "ritkán használt adatok", a [Azure Blob Storage](../storage/common/storage-introduction.md) vagy [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md).
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

<a id="sql-sdk"></a>
## <a name="using-the-sdk"></a>Az SDK használata

A [SQL SDK](sql-api-sdk-dotnet.md) Azure Cosmos DB nyújtott olvassa el és kezelheti a hírcsatorna módosítása a teljesítményt. De a kiváló power származik sok feladatkörök, túl. Ha azt szeretné, ellenőrzési pontjainak felügyelete, a dokumentum sorszámok kezelésére és partíciókulcsok részletes szabályozhatják, majd a SDK használatával lehet a megfelelő módszert.

Ez a szakasz végigvezeti az SQL-SDK használatával történő együttműködésre a hírcsatorna módosítása.

1. Indítsa el a következő erőforrások olvasásakor az appconfig. A végpont és a hitelesítési kulcs lekérése a találhatók [frissítse a kapcsolati karakterlánc](create-sql-api-dotnet.md#update-your-connection-string).

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

> [!NOTE]
> Ahelyett, hogy `ChangeFeedOptions.PartitionKeyRangeId`, használhat `ChangeFeedOptions.PartitionKey` adható meg a egypartíciós kulcs az adatcsatorna-változás beszerzéséhez. Például: `PartitionKey = new PartitionKey("D8CFA2FD-486A-4F3E-8EA6-F3AA94E5BD44")`.
> 
>

Ha több olvasók, használhatja **ChangeFeedOptions** különböző szálakon vagy a különböző ügyfelek olvasási terhelés elosztása.

Ennyi az egész, ezek néhány sornyi kódot el lehet indítani a módosítás hírcsatorna olvasása. A teljes kód a jelen cikkben használt beszerezheti a [GitHub-tárház](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeed).

A fenti 4. lépésben a kódban a **ResponseContinuation** az utolsó sor a legutóbbi logikai sorszáma (LSN) rendelkezik a dokumentum, amely új dokumentumok a sorszám után olvassa el a következő indításakor fogja használni. Használatával a **StartTime** , a **ChangeFeedOption** lekérni a dokumentumok a net szélességűre állítható. Tehát, ha a **ResponseContinuation** null értékű, azonban a **StartTime** vissza időben kerül majd óta megváltoztak a valamennyi dokumentumot elérhetővé válik a **StartTime**. De ha a **ResponseContinuation** értékkel rendelkezik, akkor a rendszer segítséget nyújtanak a dokumentumokat, hogy Naplósorszám óta.

Igen a checkpoint tömb csak megakadályozza a minden partíció esetében Naplósorszám. De ha nem szeretné a partíciók foglalkozik, ellenőrzőpontokat, Naplósorszám, kezdési idő stb. a egyszerűbb lehetőséget, hogy a processzor könyvtár hírcsatorna módosítás.

<a id="change-feed-processor"></a>
## <a name="using-the-change-feed-processor-library"></a>A módosítás használatával hírcsatorna processzor könyvtár 

A [Azure Cosmos DB módosítás hírcsatorna processzor könyvtár](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet-changefeed) esemény feldolgozása könnyen szét több felhasználóból nyújt segítséget. Ezt a szalagtárat olvasási módosítások partíciók és párhuzamosan működik több szál egyszerűbbé teszi.

Változáskezelési adatcsatorna processzor könyvtár fő előnye, hogy ne kelljen minden partíció kezelése és folytatási kód, és ne kelljen manuálisan kérdezze le az egyes gyűjtemények.

Az adatcsatorna processzor változáskezelési könyvtár olvasási módosítások partíciók és párhuzamosan működik több szál egyszerűbbé teszi.  A bérleti mechanizmussal partíciók között olvasási módosítások automatikusan kezeli. Mint ha két-ügyfelek a hírcsatorna processzor szalagtár módosítása az alábbi képen látható, akkor a munkát egymás között osztják. Növelje az ügyfelek továbbra is, mivel azok tartsa felosztásával a munkát egymás között.

![Elosztott feldolgozásához hírcsatorna Azure Cosmos DB módosítása](./media/change-feed/change-feed-output.png)

A bal oldali ügyfél első indult el, és indulása összes a partíciót, majd a második ügyfél indította el, majd az első segítségével nyissa meg néhány második ügyfél a címbérlet figyelése. Ez látható a töltött módja terjesztése a munkahelyi különböző gépek és az ügyfelek között.

Vegye figyelembe, hogy ha két kiszolgáló nélküli Azure funtions ugyanaz a gyűjtemény figyelése és az azonos bérleti használatával, akkor a két funkciók kaphat attól függően, hogy a processzor könyvtár úgy dönt, hogy észlelést a partíciók különböző dokumentumok.

<a id="understand-cf"></a>
### <a name="understanding-the-change-feed-processor-library"></a>A módosítás ismertetése hírcsatorna processzor könyvtár

Az adatcsatorna processzor változáskezelési könyvtár végrehajtási négy fő összetevőből: a figyelt gyűjteményhez, a címbérlet gyűjteményt, a processzor gazdagép és a fogyasztók. 

> [!WARNING]
> A gyűjtemények létrehozása a díjszabásra is hatással van, mivel átviteli sebességet tart fenn az alkalmazás számára az Azure Cosmos DB-vel folytatott kommunikációhoz. További részletekért látogasson el az [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

**Figyelt gyűjtemény:** a figyelt gyűjteményhez az adatok, a módosítás hírcsatorna képzésére szolgáló. Bármely beszúrások és a figyelt gyűjtemény módosításait a módosítási hírcsatorna a gyűjtemény is megjelennek. 

**Címbérlet gyűjtemény:** feldolgozása a módosítás több Worker közötti adatcsatorna bérleti gyűjtemény koordinátáit. Egy külön gyűjteményt a bérletek egy bérletet eredményez. partíciónként tárolására szolgál. Célszerű a címbérlet gyűjtemény tárolását a írási terület közelebb a hírcsatorna processzor módosítás futtató egy másik fiókot. A bérleti objektum tartalmazza a következő attribútumokat: 
* Tulajdonos: Adja meg a címbérlet birtokló állomás
* Folytatási: Meghatározza a módosítás egy adott partíció-hírcsatorna helyét (folytatási kód)
* Időbélyeg: Legutóbbi bérleti frissült; az időbélyeg segítségével ellenőrizze, hogy a bérlet lejárt tekinthető 

**Processzor-állomás:** minden állomás határozza meg, hány particionálja alapján más gazdagépek hány példányban vannak-e aktív bérletek folyamathoz. 
1.  A gazdagép indításakor minden gazdagép elosztásához címbérleteket szerez be. Egy gazdagép rendszeres időközönként megújítja címbérleteket,, címbérleteket aktív marad. 
2.  A gazdagép ellenőrzőpontokat az egyes bérletekhez utolsó folytatási olvasni. Párhuzamossági biztonsága érdekében a gazdagép minden egyes bérleti frissítés ETag ellenőrzi. Más ellenőrzőpont stratégiák használata is támogatott.  
3.  Leállásakor egy állomás kiadott összes címbérlet, de a folytatási információkért tartja, így újból engedélyezheti az olvasást a tárolt ellenőrzőponttól később. 

Jelenleg a gazdagépek száma nem lehet nagyobb, mint a partíciók (címbérleteket).

**A fogyasztók:** fogyasztók vagy dolgozó a szál által az egyes állomások által kezdeményezett hírcsatorna módosítása feldolgozási műveleteket. Mindegyik feldolgozó gazdagépnek több felhasználóból lehet. Mindegyik felhasználó olvassa be, a módosítás hírcsatorna a partícióból hozzá van rendelve, és változások a gazdagép értesítést küld, és címbérleteket lejárt.

További tudni, hogyan következő négy elemeinek módosítása adatcsatorna processzor munkahelyi együtt, vizsgáljuk meg az alábbi ábra egy példát. A figyelt gyűjtemény tárolja a dokumentumokat, és a "város" használja, mint a partíciós kulcs. Látható, hogy a kék partíció és így tovább tartalmaz-e "A-E" a "város" mezőt a dokumentumokat. Nincsenek két olvasásakor a négy partíció párhuzamosan két fogyasztók rendelkező állomás. A nyilak a fogyasztók olvasásakor a hírcsatorna változásával konkrét helyet. Az első partícióban a sötétebb kék olvasatlan módosítások jelöli, míg a ritka kék jelenti. a módosítás adatcsatorna már olvasási változásaival. A gazdagépek a bérleti gyűjtemény a "Folytatás" érték az aktuális olvasási pozíció az egyes felhasználók nyomon követéséhez tárolására használható. 

![Az Azure Cosmos DB módosítás használatával hírcsatorna processzor állomás](./media/change-feed/changefeedprocessornew.png)

### <a name="working-with-the-change-feed-processor-library"></a>A módosítás hírcsatorna processzor könyvtár használata

Mielőtt processzor NuGet-csomag módosítása telepítése hírcsatornát, először telepíteni: 

* Microsoft.Azure.DocumentDB, 1.13.1 verzió vagy újabb 
* Newtonsoft.Json, 9.0.1 verzió vagy újabb

Telepítse a [Microsoft.Azure.DocumentDB.ChangeFeedProcessor Nuget-csomag](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/) adja hozzá hivatkozásként.

A módosítás adatcsatorna processzor könyvtárban van, hogy végrehajtásához a következő:

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

Ennyi az egész. Néhány lépések után dokumentumok beérkező indul a **DocumentFeedObserver ProcessChangesAsync** metódust. A fenti kódot található [GitHub-tárház](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor)

## <a name="faq"></a>GYIK

### <a name="what-are-the-different-ways-you-can-read-change-feed-and-when-to-use-each-method"></a>Mik a különböző módon el tudja olvasni a módosítás hírcsatorna? és az egyes módszerek használatával?

Ahhoz, hogy a módosítás hírcsatorna olvasása három lehetőség áll rendelkezésre:

* **[Az Azure Cosmos DB SQL API .NET SDK használatával](#sql-sdk)**
   
   Ez a módszer használatával alacsony szintű vezérlésre a módosítás adatcsatorna beolvasása. Az ellenőrzőpont kezelheti, elérheti, hogy egy adott partíció kulcs stb. Ha több olvasók, használhatja [ChangeFeedOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.changefeedoptions?view=azure-dotnet) különböző szálakon vagy a különböző ügyfelek olvasási terhelés elosztása. .

* **[Az Azure Cosmos DB módosítás használatával hírcsatorna processzor könyvtár](#change-feed-processor)**

   Ha azt szeretné, nagy mennyiségű módosítás adatcsatorna összetettsége kihelyező változáskezelési adatcsatorna processzor könyvtár használhatja. Ezt a szalagtárat elrejti a nagy mennyiségű összetettségét, de továbbra is a teljes felügyeletet biztosít módosítása adatcsatorna. Ebben a könyvtárban a következő egy [megfigyelő mintát](https://en.wikipedia.org/wiki/Observer_pattern), a feldolgozási függvény hívása esetén az SDK-ban. 

   Ha a magas teljesítmény módosítása hírcsatornát, példányosítható több ügyfelek számára a módosítás hírcsatorna olvasása. Mivel "adatcsatorna processzor szalagtár módosítása" használ, automatikusan elosztja a terhelést különböző ügyfelek között. Önnek nincs teendője. A összetettsége SDK kezeli. Azonban ha saját terheléselosztóját szeretne használni, majd IParitionLoadBalancingStrategy tekintetében megvalósítható egyéni partíció stratégia. Megvalósítása IPartitionProcessor – egyéni feldolgozási módosítások partícióra. SDK-t és feldolgozhatja a partíciótartomány azonban ha szeretne dolgozni egy adott partíciókulcs akkor rendelkezik SQL API-hoz az SDK használatához.

* **[Az Azure Functions használatával](#azure-functions)** 
   
   Az Azure-függvény utolsó lehetőség a legegyszerűbb lehetőség. Ez a beállítás használatát javasoljuk. Az Azure Functions alkalmazást egy Azure Cosmos DB eseményindítót hoz létre, amikor csatlakozni az Azure Cosmos DB gyűjtemény választja, és a funkció akkor váltódik ki, amikor a gyűjteménybe módosításakor. Tekintse meg a [képernyő típuskonverzió](https://www.youtube.com/watch?v=Mnq0O91i-0s&t=14s) Azure használatával működnek, és módosítsa az adatcsatorna

   Eseményindítók hozhatók létre az Azure Functions portálon az Azure Cosmos DB portálon vagy programozottan. A Visual Studio és a Visual STUDIO Code rendelkezik Azure-függvény írni kiváló támogatást. Írási és a kód hibakereséséhez az asztalon, és telepíteni a függvény egy kattintással. További információkért lásd: [Azure Cosmos DB: kiszolgáló nélküli adatbázis számítási Azure Functions használatával](serverless-computing-database.md) cikk.

### <a name="what-is-the-sort-order-of-documents-in-change-feed"></a>Mi az a rendezési sorrend hírcsatorna módosítása a dokumentumok?

Adatcsatorna-dokumentumok módosítása ahhoz, azok módosításának ideje származnak. Ez a rendezési sorrend csak partíciónként garantáltan.

### <a name="for-a-multi-region-account-what-happens-to-the-change-feed-when-the-write-region-fails-over-does-the-change-feed-also-failover-would-the-change-feed-still-appear-contiguous-or-would-the-fail-over-cause-change-feed-to-reset"></a>Több területi fiók mi történik a hírcsatorna, amikor az írási régió sikertelen átvevő módosítás? Nem a módosítás hírcsatorna is feladatátvételi? A módosítás hírcsatorna továbbra is jelent összefüggő vagy megváltozna a feladatátvételi okát új adatcsatorna?

Igen, módosítás adatcsatorna fog működni a kézi feladatátvételi művelet között, és összefüggő lesz.

### <a name="how-long-change-feed-persist-the-changed-data-if-i-set-the-ttl-time-to-live-property-for-the-document-to--1"></a>Mennyi ideig módosítás adatcsatorna őrzi meg a megváltozott adatok a TTL-t (Live idő) tulajdonságot a dokumentum -1 értékre állítható be?

Változás adatcsatorna tartja az egészen addig megmarad. Adatok nem törlődnek, ha a módosítás adatcsatorna marad.

### <a name="how-can-i-configure-azure-functions-to-read-from-a-particular-region-as-change-feed-is-available-in-all-the-read-regions-by-default"></a>Hogyan beállítható egy adott régióban, olvasni az Azure functions módosítás adatcsatorna áll rendelkezésre, alapértelmezés szerint minden olvasási régióban?

Jelenleg nincs lehetőség a konfigurálása az Azure Functions egy adott régióban olvasni. Van egy GitHub-hiba az előnyben részesített régióiba bármely Azure Cosmos DB kötés és az indítási beállítása az Azure Functions tárházban.

Az Azure Functions az alapértelmezett kapcsolati házirendet használja. Az Azure Functions, és alapértelmezés szerint csatlakozási mód konfigurálhatók, olvassa be az írási-régióban, a legjobb egy szalagra helyezni az Azure Functions az ugyanabban a régióban.

### <a name="what-is-the-default-size-of-batches-in-azure-functions"></a>Mi az az Azure Functions kötegek alapértelmezett mérete?

az Azure Functions minden meghívása 100 dokumentumok. Azonban ez a szám nem konfigurálható a function.json fájlban. Itt befejeződött [konfigurációs listájának](../azure-functions/functions-run-local.md). Ha helyileg fejleszt, frissítse az alkalmazásbeállítások belül a [local.settings.json](../azure-functions/functions-run-local.md) fájlt.

### <a name="i-am-monitoring-a-collection-and-reading-its-change-feed-however-i-see-i-am-not-getting-all-the-inserted-document-some-documents-are-missing-what-is-going-on-here"></a>Figyelésekor egy gyűjtemény és a módosítás olvasása hírcsatornát, azonban látom az összes beszúrt dokumentum nem érkezik, egyes dokumentumok hiányoznak. Mi a helyzet Itt?

Győződjön meg arról, hogy van-e más függvény. az azonos bérleti gyűjteménnyel ugyanaz a gyűjtemény olvasása. Ez eddig nekem, és később I is tudjuk a hiányzó dokumentumok dolgozza fel a többi az Azure functions, az azonos bérleti is használ.

Ezért több Azure Functions olvasni létrehozásakor azonos módosítása adatcsatorna akkor kell különböző bérleti gyűjtemény vagy a "leasePrefix" konfigurációt használja ahhoz, hogy ugyanaz a gyűjtemény. Változáskezelési adatcsatorna processzor könyvtár használatakor a funkció több példányát is elindítható, és SDK elosztja a dokumentumok automatikusan az Ön különböző példányai között.

### <a name="my-document-is-updated-every-second-and-i-am-not-getting-all-the-changes-in-azure-functions-listening-to-change-feed"></a>A dokumentum másodpercenként frissül, és nem érkezik a módosításokat az Azure Functions adatcsatorna módosítása figyel.

Az Azure Functions szavazások módosítás hírcsatorna 5 másodpercenként, 5 másodperc között végrehajtott módosítások elvesznek. Azure Cosmos-adatbázis a csak egyetlen verzióban az 5 másodpercentként tárolja, hogy az 5. Módosítsa a dokumentum elérhetővé válik. Azonban ha azt szeretné, nyissa meg 5 másodperc alatt, és kérdezze le a hírcsatorna másodpercenként módosítás szeretné, akkor állítsa be a lekérdezési alkalommal "feedPollTime" szoftverben: [Azure Cosmos DB kötések](../azure-functions/functions-bindings-cosmosdb.md#trigger---configuration). Az 5000 alapértelmezett érték ezredmásodpercben van definiálva. 1 másodperc alatt nem lehetséges, de nem javasolt, további CPU írás indul.

### <a name="i-inserted-a-document-in-the-mongo-api-collection-but-when-i-get-the-document-in-change-feed-it-shows-a-different-id-value-what-is-wrong-here"></a>A Mongo API gyűjtemény beszúrt egy dokumentumot, de a dokumentum módosítása adatcsatorna jelenik meg, ha egy másik azonosítót értéket mutatja. Miért nem megfelelő Itt?

A gyűjtemény Mongo API gyűjtemény. Ne feledje, hogy módosítás adatcsatorna olvasható az SQL-ügyfélprogrammal, és rendezi sorba elemek JSON formátumban. Miatt az ügyfelek fog tapasztalni MongoDB formázást, JSON formátumú BSON dokumentumokat és a JSON eltérést formázott hírcsatorna módosítása. Ön a JSON-ban BSON dokumentum ábrázolása. Ha a bináris attribútumokhoz a Mongo fiókokat használ, telepítésekké lesznek átalakítva JSON.

### <a name="is-there-a-way-to-control-change-feed-for-updates-only-and-not-inserts"></a>Van egy módszerre, amellyel szabályozhatja a frissítések csak az adatcsatorna változás, és nem beszúrása?

Nem ma de ez a funkció a terv. Ma enyhe jelölő adhat meg a dokumentum a frissítéseket.

### <a name="is-there-a-way-to-get-deletes-in-change-feed"></a>Van mód beolvasása a törlések módosítás hírcsatorna?

Jelenleg a módosítás adatcsatorna nem jelentkezzen törli. Változás adatcsatorna is folyamatosan javítja, és ez a funkció terv. Ma a dokumentumon törlendő enyhe jelölő is hozzáadhat. Attribútum hozzáadása a dokumentum "törölt" nevű és "true"értékre állítani, és a dokumentum állítsa be a TTL-t, hogy automatikusan törlődjenek.

### <a name="can-i-read-change-feed-for-historic-documentsfor-example-documents-that-were-added-5-years-back-"></a>Elolvashatják a hírcsatorna történelmi dokumentumok (például dokumentumok vissza 5 éves hozzáadott) módosítása?

Igen, ha a dokumentum nem törlődik el tudja olvasni a módosítás adatcsatorna lehetőség szerint a gyűjtemény forrása.

### <a name="can-i-read-change-feed-using-javascript"></a>Elolvashatják a JavaScript használatával módosítás hírcsatorna?

Igen, a Node.js SDK kezdeti támogatás módosítás adatcsatorna nemrégiben hozzáadott. Ahogy az az alábbi példában adjon aktuális verzióra frissítés documentdb modult a kód futtatása előtt használható:

```js

var DocumentDBClient = require('documentdb').DocumentClient;
const host = "https://your_host:443/";
const masterKey = "your_master_key==";
const databaseId = "db";
const collectionId = "c1";
const dbLink = 'dbs/' + databaseId;
const collLink = dbLink + '/colls/' + collectionId;
var client = new DocumentDBClient(host, { masterKey: masterKey });
let options = {
    a_im: "Incremental feed",
    accessCondition: {
        type: "IfNoneMatch",        // Use: - empty condition (or remove accessCondition entirely) to start from beginning.
        //      - '*' to start from current.
        //      - specific etag value to start from specific continuation.
        condition: ""
    }
};
 
var query = client.readDocuments(collLink, options);
query.executeNext((err, results, headers) =&gt; {
    // Now we have headers.etag, which can be used in next readDocuments in accessCondition option.
    console.log(results);
    console.log(headers.etag);
    console.log(results.length);
    options.accessCondition = { type: "IfNoneMatch", condition: headers.etag };
    var query = client.readDocuments(collLink, options);
    query.executeNext((err, results, headers) =&gt; {
        console.log("next one:", results[0]);
    });
});<span id="mce_SELREST_start" style="overflow:hidden;line-height:0;"></span>

```

### <a name="can-i-read-change-feed-using-java"></a>Elolvashatják a módosítás adatcsatorna Java használatával?

Java kódtár módosítás adatcsatorna elolvasására is elérhető [Github-tárházban](https://github.com/Azure/azure-documentdb-changefeedprocessor-java). Azonban Java kódtár jelenleg néhány verziók .NET kódtár mögött. Mindkét a szalagtárak hamarosan le lesz szinkronban.

### <a name="can-i-use-etag-lsn-or-ts-for-internal-bookkeeping-which-i-get-in-response"></a>Használhatok _etag, _lsn vagy _ts a belső könyvelés, a válasz jelenik meg?

_etag formátuma belső és meg kell nem használják (nem elemezhető azt), mert bármikor megváltoztathatja.
_ts módosítása vagy a létrehozás időpontjának időbélyegét. Az időrendi összehasonlításhoz _ts is használhatja.
_lsn van egy kötegazonosítót, csak a módosítás adatcsatorna hozzáadott, a tranzakció azonosítóját jelzi az áruházból... Előfordulhat, hogy hány dokumentum azonos _lsn.
Egy dolog vegye figyelembe a FeedResponse ETag nem egyezik a _etag megtekintheti a dokumentumot. _etag belső azonosító és feldolgozási használatával, a dokumentumot a verziójával kapcsolatos információt szolgáltat arról és ETag használható műveleti sorrend a hírcsatorna.

### <a name="does-reading-change-feed-add-any-additional-cost-"></a>Változás hírcsatorna olvasása már minden további költség nélkül?

A RU felhasznált, azaz az Azure Cosmos DB gyűjtemények mindkét bármikor felhasználhatja a RU számolnak. A bérleti gyűjtemény által felhasznált RU felhasználók fogjuk számlázni.

### <a name="can-multiple-azure-functions-read-one-collections-change-feed"></a>Elolvashatják a több Azure Functions egy gyűjtemény módosítás hírcsatorna?

Igen. Több Azure Functions ugyanaz a gyűjtemény megváltoztatása adatcsatorna olvashatja. Azonban az Azure Functions kell rendelkeznie egy külön definiált leaseCollectionPrefix.

### <a name="should-the-lease-collection-be-partitioned"></a>A bérleti gyűjtemény particionálható?

Nem, bérleti gyűjtemény lehet meghatározni. Nincs szükség a particionált bérleti gyűjteményt, és jelenleg nem támogatott.

### <a name="can-i-read-change-feed-from-spark"></a>Tudom is módosítás hírcsatornát Spark on az?

Igen. Ellenőrizze a [Azure Cosmos DB Spark összekötő](spark-connector.md). Íme egy [képernyő típuskonverzió](https://www.youtube.com/watch?v=P9Qz4pwKm_0&t=1519s) jelenít meg, hogyan tudja dolgozni a strukturált adatfolyamként hírcsatorna módosítása.

### <a name="if-i-am-processing-change-feed-by-using-azure-functions-say-a-batch-of-10-documents-and-i-get-an-error-at-7th-document-in-that-case-the-last-three-documents-are-not-processed-how-can-i-start-processing-from-the-failed-documentie-7th-document-in-my-next-feed"></a>I vagyok dolgozza fel az Azure Functions használatával hírcsatorna módosítása, egy 10 dokumentumok kötegelt fel, és a 7. a dokumentum jelenik meg hibaüzenet. Ebben az esetben az utolsó három dokumentumok nincsenek feldolgozva hogyan lehet indításakor feldolgozása a hibás dokumentumból (azaz 7. a dokumentum) található a következő adatcsatorna?

Kezelje a hiba, a javasolt minta nem csomagolásához try-catch blokkon a kódot. Dolgozza fel a hiba, és a dokumentum elhelyezése egy üzenetsort (kézbesítetlen), és majd megadása logikát, amely a hiba előállított a dokumentumok kezelése érdekében. Ezzel a módszerrel a 200-dokumentum kötegelt, és nem sikerült, csak egy dokumentumot rendelkező nincs a dobni a teljes kötegelt.

Abban az esetben, ha a hiba akkor kell nem visszatekerés a jelölőnégyzet pont vissza elejéig más meg fog is folyton ezekben a dokumentumokban módosítás csatornáról. Ne feledje, hogy módosítás adatcsatorna a memóriában tárolja a dokumentumokat, ezt, mert az utolsó végső beépülő kapcsolatos hibaüzenetet elveszítheti a dokumentum az előző pillanatképet. módosítás adatcsatorna tartja a dokumentum csak egy utolsó verzióját, és a kettő között más folyamatok származnak, és módosítani a dokumentumot.

A kód tartsa kijavítása, mivel hamarosan találja egyetlen dokumentum kézbesítetlen levelek várólistájára.
Az Azure Functions módosítás adatcsatorna rendszer által automatikusan nevezik, és ellenőrzés pont stb Azure-függvény belsőleg kezeli. Ha azt szeretné, állítsa vissza a jelölőnégyzet pontot, és minden szempontját szabályozásához, érdemes lehet processzor SDK használatával módosítás hírcsatorna.

## <a name="next-steps"></a>További lépések

Az Azure Functions Azure Cosmos DB használatával kapcsolatos további információk: [Azure Cosmos DB: kiszolgáló nélküli adatbázis számítási Azure Functions használatával](serverless-computing-database.md).

További információ az adatcsatorna processzor szalagtár módosítása használatával használja a következőket:

* [Információ lap](sql-api-sdk-dotnet-changefeed.md) 
* [Nuget-csomag](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)
* [1-6 lépéseket bemutató mintakód](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor)
* [További minták a Githubon](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)

További információ a módosítás hírcsatorna keresztül az SDK használatával használja a következőket:

* [SDK adatai lap](sql-api-sdk-dotnet.md)
