---
title: A módosítást végzett adatcsatorna-támogatás az Azure Cosmos Adatbázisba |} Microsoft Docs
description: Azure Cosmos DB módosítás adatcsatorna támogatási használja a dokumentumok nyomon követéséhez és esemény-alapú feldolgozási például eseményindítók és gyorsítótárak és elemzési rendszerek frissítése.
keywords: adatcsatorna módosítása
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: rafats
ms.openlocfilehash: 6b0aaa075b8b2881e269d79a67e75528d0d9a86a
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37129858"
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

* Microsoft.Azure.DocumentDB, a legújabb verzióra.
* Newtonsoft.Json, legújabb verzió

Telepítse a [Microsoft.Azure.DocumentDB.ChangeFeedProcessor Nuget-csomag](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/) adja hozzá hivatkozásként.

A módosítás adatcsatorna processzor könyvtárban van, hogy végrehajtásához a következő:

1. Alkalmazzon egy **DocumentFeedObserver** objektum, amely **IChangeFeedObserver**.
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.ChangeFeedProcessor.FeedProcessing;
    using Microsoft.Azure.Documents.Client;

    /// <summary>
    /// This class implements the IChangeFeedObserver interface and is used to observe 
    /// changes on change feed. ChangeFeedEventHost will create as many instances of 
    /// this class as needed. 
    /// </summary>
    public class DocumentFeedObserver : IChangeFeedObserver
    {
    private static int totalDocs = 0;

        /// <summary>
        /// Initializes a new instance of the <see cref="DocumentFeedObserver" /> class.
        /// Saves input DocumentClient and DocumentCollectionInfo parameters to class fields
        /// </summary>
        /// <param name="client"> Client connected to destination collection </param>
        /// <param name="destCollInfo"> Destination collection information </param>
        public DocumentFeedObserver()
        {
            
        }

        /// <summary>
        /// Called when change feed observer is opened; 
        /// this function prints out observer partition key id. 
        /// </summary>
        /// <param name="context">The context specifying partition for this observer, etc.</param>
        /// <returns>A Task to allow asynchronous execution</returns>
        public Task OpenAsync(IChangeFeedObserverContext context)
        {
            Console.ForegroundColor = ConsoleColor.Magenta;
            Console.WriteLine("Observer opened for partition Key Range: {0}", context.PartitionKeyRangeId);
            return Task.CompletedTask;
        }

        /// <summary>
        /// Called when change feed observer is closed; 
        /// this function prints out observer partition key id and reason for shut down. 
        /// </summary>
        /// <param name="context">The context specifying partition for this observer, etc.</param>
        /// <param name="reason">Specifies the reason the observer is closed.</param>
        /// <returns>A Task to allow asynchronous execution</returns>
        public Task CloseAsync(IChangeFeedObserverContext context, ChangeFeedObserverCloseReason reason)
        {
            Console.ForegroundColor = ConsoleColor.Cyan;
            Console.WriteLine("Observer closed, {0}", context.PartitionKeyRangeId);
            Console.WriteLine("Reason for shutdown, {0}", reason);
            return Task.CompletedTask;
        }

        public Task ProcessChangesAsync(IChangeFeedObserverContext context, IReadOnlyList<Document> docs, CancellationToken cancellationToken)
        {
            Console.ForegroundColor = ConsoleColor.Green;
            Console.WriteLine("Change feed: PartitionId {0} total {1} doc(s)", context.PartitionKeyRangeId, Interlocked.Add(ref totalDocs, docs.Count));
            foreach (Document doc in docs)
            {
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine(doc.Id.ToString());
            }

            return Task.CompletedTask;
        }
    }
    ```

2. Alkalmazzon egy **DocumentFeedObserverFactory**, amely megvalósítja a **IChangeFeedObserverFactory**.
    ```csharp
     using Microsoft.Azure.Documents.ChangeFeedProcessor.FeedProcessing;

    /// <summary>
    /// Factory class to create instance of document feed observer. 
    /// </summary>
    public class DocumentFeedObserverFactory : IChangeFeedObserverFactory
    {
        /// <summary>
        /// Initializes a new instance of the <see cref="DocumentFeedObserverFactory" /> class.
        /// Saves input DocumentClient and DocumentCollectionInfo parameters to class fields
        /// </summary>
        public DocumentFeedObserverFactory()
        {
        }

        /// <summary>
        /// Creates document observer instance with client and destination collection information
        /// </summary>
        /// <returns>DocumentFeedObserver with client and destination collection information</returns>
        public IChangeFeedObserver CreateObserver()
        {
            DocumentFeedObserver newObserver = new DocumentFeedObserver();
            return newObserver as IChangeFeedObserver;
        }
    }
    ```

3. Adja meg *CancellationTokenSource* és *ChangeFeedProcessorBuilder*

    ```csharp
    private readonly CancellationTokenSource cancellationTokenSource = new CancellationTokenSource();
    private readonly ChangeFeedProcessorBuilder builder = new ChangeFeedProcessorBuilder();
    ```

5. build a **ChangeFeedProcessorBuilder** a releváns objektumok meghatározása után 

    ```csharp
            string hostName = Guid.NewGuid().ToString();
      
            // monitored collection info 
            DocumentCollectionInfo documentCollectionInfo = new DocumentCollectionInfo
            {
                Uri = new Uri(this.monitoredUri),
                MasterKey = this.monitoredSecretKey,
                DatabaseName = this.monitoredDbName,
                CollectionName = this.monitoredCollectionName
            };
            
            DocumentCollectionInfo leaseCollectionInfo = new DocumentCollectionInfo
                {
                    Uri = new Uri(this.leaseUri),
                    MasterKey = this.leaseSecretKey,
                    DatabaseName = this.leaseDbName,
                    CollectionName = this.leaseCollectionName
                };
            DocumentFeedObserverFactory docObserverFactory = new DocumentFeedObserverFactory();
            ChangeFeedOptions feedOptions = new ChangeFeedOptions();

            /* ie customize StartFromBeginning so change feed reads from beginning
                can customize MaxItemCount, PartitonKeyRangeId, RequestContinuation, SessionToken and StartFromBeginning
            */

            feedOptions.StartFromBeginning = true;
        
            ChangeFeedProcessorOptions feedProcessorOptions = new ChangeFeedProcessorOptions();

            // ie. customizing lease renewal interval to 15 seconds
            // can customize LeaseRenewInterval, LeaseAcquireInterval, LeaseExpirationInterval, FeedPollDelay 
            feedProcessorOptions.LeaseRenewInterval = TimeSpan.FromSeconds(15);

            this.builder
                .WithHostName(hostName)
                .WithFeedCollection(documentCollectionInfo)
                .WithLeaseCollection(leaseCollectionInfo)
                .WithProcessorOptions (feedProcessorOptions)
                .WithObserverFactory(new DocumentFeedObserverFactory());               
                //.WithObserver<DocumentFeedObserver>();  If no factory then just pass an observer

            var result =  await this.builder.BuildAsync();
            await result.StartAsync();
            Console.Read();
            await result.StopAsync();    
            ```

That’s it. After these few steps documents will start showing up into the **DocumentFeedObserver.ProcessChangesAsync** method.

Above code is for illustration purpose to show different kind of objects and their interaction. You have to define proper variables and initiate them with correct values. You can get the complete code used in this article from the [GitHub repo](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessorV2).

> [!NOTE]
> You should never have a master key in your code or in config file as shown in above code. Please see [how to use Key-Vault to retrive the keys](https://sarosh.wordpress.com/2017/11/23/cosmos-db-and-key-vault/).


## FAQ

### What are the different ways you can read Change Feed? and when to use each method?

There are three options for you to read change feed:

* **[Using Azure Cosmos DB SQL API .NET SDK](#sql-sdk)**
   
   By using this method, you get low level of control on change feed. You can manage the checkpoint, you can access a particular partition key etc. If you have multiple readers, you can use [ChangeFeedOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.changefeedoptions?view=azure-dotnet) to distribute read load to different threads or different clients. .

* **[Using the Azure Cosmos DB change feed processor library](#change-feed-processor)**

   If you want to outsource lot of complexity of change feed then you can use change feed processor library. This library hides lot of complexity, but still gives you complete control on change feed. This library follows an [observer pattern](https://en.wikipedia.org/wiki/Observer_pattern), your processing function is called by the SDK. 

   If you have a high throughput change feed, you can instantiate multiple clients to read the change feed. Because you are using “change feed processor library”, it will automatically divide the load among different clients. You do not have to do anything. All the complexity is handled by SDK. However, if you want to have your own load balancer, then you can implement IParitionLoadBalancingStrategy for custom partition strategy. Implement IPartitionProcessor – for custom processing changes on a partition. However, with SDK, you can process a partition range but if you want to process a particular partition key then you have to use SDK for SQL API.

* **[Using Azure Functions](#azure-functions)** 
   
   The last option Azure Function is the simplest option. We recommend using this option. When you create an Azure Cosmos DB trigger in an Azure Functions app, you select the Azure Cosmos DB collection to connect to and the function is triggered whenever a change to the collection is made. watch a [screen cast](https://www.youtube.com/watch?v=Mnq0O91i-0s&t=14s) of using Azure function and change feed

   Triggers can be created in the Azure Functions portal, in the Azure Cosmos DB portal, or programmatically. Visual Studio and VS Code has great support to write Azure Function. You can write and debug the code on your desktop, and then deploy the function with one click. For more information, see [Azure Cosmos DB: Serverless database computing using Azure Functions](serverless-computing-database.md) article.

### What is the sort order of documents in change feed?

Change feed documents comes in order of their modification time. This sort order is guaranteed only per partition.

### For a multi-region account, what happens to the change feed when the write-region fails-over? Does the change feed also failover? Would the change feed still appear contiguous or would the fail-over cause change feed to reset?

Yes, change feed will work across the manual failover operation and it will be contiguous.

### How long change feed persist the changed data if I set the TTL (Time to Live) property for the document to -1?

Change feed will persist forever. If data is not deleted, it will remain in change feed.

### How can I configure Azure functions to read from a particular region, as change feed is available in all the read regions by default?

Currently it’s not possible to configure Azure Functions to read from a particular region. There is a GitHub issue in the Azure Functions repo to set the preferred regions of any Azure Cosmos DB binding and trigger.

Azure Functions uses the default connection policy. You can configure connection mode in Azure Functions and by default, it reads from the write region, so it is best to co-locate Azure Functions on the same region.

### What is the default size of batches in Azure Functions?

100 documents at every invocation of Azure Functions. However, this number is configurable within the function.json file. Here is complete [list of configuration options](../azure-functions/functions-run-local.md). If you are developing locally, update the application settings within the [local.settings.json](../azure-functions/functions-run-local.md) file.

### I am monitoring a collection and reading its change feed, however I see I am not getting all the inserted document, some documents are missing. What is going on here?

Please make sure that there is no other function reading the same collection with the same lease collection. It happened to me, and later I realized the missing documents are processed by my other Azure functions, which is also using the same lease.

Therefore, if you are creating multiple Azure Functions to read the same change feed then they must use different lease collection or use the “leasePrefix” configuration to share the same collection. However, when you use change feed processor library you can start multiple instances of your function and SDK will divide the documents between different instances automatically for you.

### My document is updated every second, and I am not getting all the changes in Azure Functions listening to change feed.

Azure Functions polls change feed for every 5 seconds, so any changes made between 5 seconds are lost. Azure Cosmos DB stores just one version for every 5 seconds so you will get the 5th change on the document. However, if you want to go below 5 second, and want to poll change Feed every second, You can configure the polling time “feedPollTime”, see [Azure Cosmos DB bindings](../azure-functions/functions-bindings-cosmosdb.md#trigger---configuration). It is defined in milliseconds with a default of 5000. Below 1 second is possible but not advisable, as you will start burning more CPU.

### I inserted a document in the Mongo API collection, but when I get the document in change feed, it shows a different id value. What is wrong here?

Your collection is Mongo API collection. Remember, change feed is read using the SQL client and serializes items into JSON format. Because of the JSON formatting, MongoDB clients will experience a mismatch between BSON formatted documents and the JSON formatted change feed. You are seeing is the representation of a BSON document in JSON. If you use binary attributes in a Mongo accounts, they are converted to JSON.

### Is there a way to control change feed for updates only and not inserts?

Not today, but this functionality is on roadmap. Today, you can add a soft marker on the document for updates.

### Is there a way to get deletes in change feed?

Currently change feed doesn’t log deletes. Change feed is continuously improving, and this functionality is on roadmap. Today, you can add a soft marker on the document for delete. Add an attribute on the document called “deleted” and set it to “true” and set a TTL on the document so that it can be automatically deleted.

### Can I read change feed for historic documents(for example, documents that were added 5 years back) ?

Yes, if the document is not deleted you can read the change feed as far as the origin of your collection.

### Can I read change feed using JavaScript?

Yes, Node.js SDK initial support for change feed is recently added. It can be used as shown in the following example, please update documentdb module to current version before you run the code:

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
