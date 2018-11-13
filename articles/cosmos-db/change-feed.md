---
title: A módosítás használatának hírcsatorna támogatása az Azure Cosmos DB |} A Microsoft Docs
description: Használja az Azure Cosmos DB módosítási hírcsatorna támogatása a dokumentumok nyomon követésére, és végezze el a feldolgozási eseményalapú eseményindítókat például és a gyorsítótárak és elemzési rendszerek naprakészen tartása.
keywords: a módosítási hírcsatornáról
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: rafats
ms.openlocfilehash: ad2032345332519713b177596df0556e4c35c18c
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51567740"
---
# <a name="working-with-the-change-feed-support-in-azure-cosmos-db"></a>A módosítási hírcsatorna támogatása az Azure Cosmos DB használata

[Az Azure Cosmos DB](../cosmos-db/introduction.md) gyors és rugalmas globálisan replikált adatbázis, az IoT-játékok, kiskereskedelmi, jól alkalmazható tevékenységekről és a működési naplózási alkalmazások. Ezeket az alkalmazásokat a gyakori tervezési minta, hogy az adatok módosításait, további műveletekkel. Ezek további műveletek a következők bármelyike lehet: 

* Indítására egy értesítést vagy egy API-hívás, amikor a dokumentum hozzáadásakor vagy módosításakor.
* Stream analytics az IoT feldolgozása illetve.
* További adatmozgatás szinkronizálása a gyorsítótár, a keresőmotor vagy a data warehouse-ba, vagy a ritka elérésű tárolási adatok archiválása.

A **módosítási hírcsatorna támogatása** az Azure Cosmos DB lehetővé teszi, hogy ezek a minták minden hatékony és méretezhető megoldások létrehozását a következő képen látható módon:

![Power valós idejű elemzési és számítási eseményvezérelt forgatókönyvek használatával az Azure Cosmos DB-módosítási hírcsatorna](./media/change-feed/changefeedoverview.png)

> [!NOTE]
> A módosítási hírcsatorna támogatása az összes adatmodellek és tárolók az Azure Cosmos DB biztosítunk. A módosítási hírcsatorna azonban az SQL-ügyfél használatával olvasható, és szerializálja elemek JSON formátumba. A JSON formázását, MongoDB ügyfelek tapasztalható miatt egyeznek a BSON, formázott dokumentumok és a JSON formátumú – csatorna módosítása.

## <a name="how-does-change-feed-work"></a>Hogyan módosítási munkahelyi hírcsatorna?

Módosítási hírcsatorna támogatása az Azure Cosmos DB működését úgy, hogy történt egy Azure Cosmos DB-gyűjtemény. Majd megjeleníti a dokumentumok a sorrendben, amelyben a módosítás módosult a listán. A módosítások meg lesznek őrizve, feldolgozhatók aszinkron és fokozatos módon is, a kimenet pedig több fogyasztó között is elosztható a párhuzamos feldolgozáshoz. 

A változáscsatorna három különböző módon olvashat ebben a cikkben leírt módon:

*   [Az Azure Functions használatával](#azure-functions)
*   [Az Azure Cosmos DB SDK használatával](#sql-sdk)
*   [Használatával az Azure Cosmos DB-módosítási hírcsatorna feldolgozói kódtára](#change-feed-processor)

A módosítási hírcsatorna egyes partíciókulcs-tartományok belül a dokumentum egy dokumentumgyűjteményben érhető el, és ily módon – szét lehetnek osztva egy vagy több ügyfél párhuzamos feldolgozásra az alábbi képen látható módon.

![Elosztott feldolgozásához Azure Cosmos DB – csatorna módosítása](./media/change-feed/changefeedvisual.png)

További részletek:
* A változáscsatorna alapértelmezés szerint minden fiókhoz engedélyezve van.
* Használhatja a [kiosztott átviteli sebesség](request-units.md) az írási régiót, vagy bármely [olvasási régió](distribute-data-globally.md) olvasni a változáscsatorna, ugyanúgy, mint az Azure Cosmos DB fel más műveletekhez.
* A módosítási hírcsatorna beszúrások és a gyűjteményben lévő dokumentumokon végrehajtott frissítési műveletek tartalmazza. Törlések rögzítheti is egy "helyreállítható törlés" jelző törlése helyett a dokumentumokon belül. Másik lehetőségként beállíthatja egy véges lejárati idejét via a dokumentumok a [TTL képesség](time-to-live.md), például 24 óra és a törlések rögzítéséhez tulajdonság értékét használja. Ezzel a megoldással hogy a TTL lejárata időszaknál rövidebb időközt belül feldolgozza a módosításokat.
* Minden egyes dokumentum módosítása pontosan egyszer jelenik meg a változáscsatorna, és az ügyfelek kezelése az ellenőrzőpontok használata logic. A változáscsatorna feldolgozói könyvtárával biztosít automatikus ellenőrzőpont-készítés és a "legalább egyszer" szemantika.
* Csak a legutóbbi módosítása egy adott dokumentum a módosítási napló tartalmazza. Közbenső változások nem érhető el.
* A módosítási hírcsatorna szolgáló módosításának belül minden egyes partíciókulcs-értékkel van rendezve. Nincs garantált rendelés partíciókulcs értékek között.
* Módosítások származó bármely-időponthoz szinkronizálhatók, vagyis nem nem rögzített Adatmegőrzés időtartama, amelyekhez le változások.
* Módosítások partíció kulcstartományokkal blokkonként érhetők el. Ez a funkció lehetővé teszi, hogy a változásokat a nagy gyűjteményeknek több fogyasztók/kiszolgáló párhuzamos feldolgozásra.
* Alkalmazások egyidejűleg ugyanabban a gyűjteményben található több módosítás adatcsatorna kérhetnek.
* ChangeFeedOptions.StartTime is használható, adja meg például egy kezdeti kiindulási pont, és keresse meg a folytatási kód megadott idő értékeknek megfelelő. A continuationtoken argumentumot használja, ha meg van adva, a WINS-keresztül a StartTime és StartFromBeginning értékeket. A pontosság ChangeFeedOptions.StartTime ~ 5 másodperc. 

## <a name="use-cases-and-scenarios"></a>Használati esetekre és forgatókönyvekre

A változáscsatorna lehetővé teszi, hogy az írási műveletek nagy mennyiségű nagy adatkészletek hatékony feldolgozás, és alternatívája lekérdezése egy teljes adatkészleten, mi változott a azonosításához. 

Például a csatorna módosítása, a következő feladatokat végezheti hatékonyan:

* Frissítse az Azure Cosmos DB-ben tárolt adatok egy cache, search-index vagy data warehouse-bA.
* Alkalmazás-szintű adatok rétegezési és archiválási megvalósítását, azt jelenti, "gyors elérésű adatok" tárolására az Azure Cosmos DB és "ritkán használt adatok" elavulnak, [Azure Blob Storage](../storage/common/storage-introduction.md) vagy [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md).
* Nulla, egy másik Azure Cosmos DB-fiókot egy másik particionálási séma-ideje migrálások végrehajtása.
* Alkalmazzon [lambda folyamatokat az Azure-ban](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) az Azure Cosmos DB használatával. Az Azure Cosmos DB, amelyek adatfeldolgozást és a lekérdezés, és alacsony teljes bekerülési Költséget a lambda architektúra megvalósítása méretezhető adatbázis megoldást kínál. 
* Fogadása és tárolja az eseményadatokat az eszközök, érzékelők, az infrastruktúra és alkalmazások, és ezek az események valós időben dolgozhat fel [Azure Stream Analytics](../stream-analytics/stream-analytics-documentdb-output.md), [Apache Storm](../hdinsight/storm/apache-storm-overview.md), vagy [Apache A Spark](../hdinsight/spark/apache-spark-overview.md). 

A következő kép bemutatja, hogyan fogadása és használhatja az Azure Cosmos DB lekérdezési lambda folyamatok módosítani a hírcsatorna támogatása: 

![Az Azure Cosmos DB-alapú lambda folyamat fenntartásával és a lekérdezések](./media/change-feed/lambda.png)

Ezenkívül belül a [kiszolgáló nélküli](http://azure.com/serverless) webes és mobil alkalmazások, például a felhasználói profil, beállítások vagy helyre bizonyos műveleteket, például a leküldéses értesítések küldése használatávaltörténőindításáhozanyomonkövethetiazeseményeketis[Az azure Functions](#azure-functions). Készítsen játékot használata Azure Cosmos DB, akkor is, például használata módosításcsatornáját befejezett játékok a pontszámok alapján a valós idejű ranglisták megvalósításához.

<a id="azure-functions"></a>
## <a name="using-azure-functions"></a>Az Azure Functions használatával 

Azure Functions használata, a legegyszerűbb szeretne csatlakozni egy Azure Cosmos DB-módosítási hírcsatorna egy Azure Cosmos DB-eseményindító hozzáadása az Azure Functions-alkalmazás. Ha az Azure Functions alkalmazásokban hoz létre egy Azure Cosmos DB-eseményindítóval, választja, az Azure Cosmos DB-gyűjtemények való csatlakozáshoz, és a függvény akkor aktiválódik, amikor a gyűjteménybe módosításakor. 

Eseményindítók hozható létre az Azure Functions portálon az Azure Cosmos DB Portalon vagy programozott módon. További információkért lásd: [Azure Cosmos DB: az Azure Functions szolgáltatással kiszolgáló nélküli adatbázis-használat](serverless-computing-database.md).

<a id="sql-sdk"></a>
## <a name="using-the-sdk"></a>Az SDK használata

A [SQL SDK](sql-api-sdk-dotnet.md) az Azure Cosmos DB lehetővé teszi az összes írását és felügyeletét egy csatorna módosítása. De hatalommal tartalmaz felelősségekről, rengeteg túl. Ha azt szeretné, ellenőrzőpontok kezelése, dokumentum sorozatszámok foglalkozik és szabályozható partíciókulcsok rendelkezik, majd az SDK-val lehet a megfelelő módszert.

Ez a szakasz végigvezeti a változáscsatorna használata az SQL SDK használatával.

1. A következő források az appconfig elolvasásával kezdje. A végpont és a hitelesítési kulcs lekérésével útmutatást [a kapcsolati karakterlánc frissítése](create-sql-api-dotnet.md#update-your-connection-string).

    ``` csharp
    DocumentClient client;
    string DatabaseName = ConfigurationManager.AppSettings["database"];
    string CollectionName = ConfigurationManager.AppSettings["collection"];
    string endpointUrl = ConfigurationManager.AppSettings["endpoint"];
    string authorizationKey = ConfigurationManager.AppSettings["authKey"];
    ```

2. Az ügyfél a következőképpen hozhat létre:

    ```csharp
    using (client = new DocumentClient(new Uri(endpointUrl), authorizationKey,
    new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp }))
    {
    }
    ```

3. A partíció kulcstartományokkal lekérése:

    ```csharp
    FeedResponse pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(
        collectionUri,
        new FeedOptions
            {RequestContinuation = pkRangesResponseContinuation });
     
    partitionKeyRanges.AddRange(pkRangesResponse);
    pkRangesResponseContinuation = pkRangesResponse.ResponseContinuation;
    ```

4. Minden egyes partíciókulcs-tartományok ExecuteNextAsync igényelnek:

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
> Helyett `ChangeFeedOptions.PartitionKeyRangeId`, használhat `ChangeFeedOptions.PartitionKey` , adja meg, amelyik egyetlen partíciókulcsot, egy csatorna módosítása betöltéséhez. Például: `PartitionKey = new PartitionKey("D8CFA2FD-486A-4F3E-8EA6-F3AA94E5BD44")`.
> 
>

Ha több olvasók, használhat **ChangeFeedOptions** a olvasási terheléselosztásban, különböző szálakon vagy különböző ügyfelek részére.

És ennyi az egész, ezek néhány sornyi kóddal elkezdheti a módosítási hírcsatorna olvasása. Ebben a cikkben használt teljes kódot kap a [GitHub-adattárat](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeed).

A fenti 4. lépésben a kódban a **ResponseContinuation** az utolsó sort az utolsó logikai sorszáma (LSN) rendelkezik a dokumentum, melyiket fogja használni a következő alkalommal, amikor új dokumentumok elolvasása után ez a sorszám. Használatával a **StartTime** , a **ChangeFeedOption** is szélesíti a net lekérni a dokumentumokat. Tehát ha a **ResponseContinuation** érték null, azonban a **StartTime** visszatér az idő óta az összes dokumentumokat kap, majd a **StartTime**. De ha a **ResponseContinuation** értékkel rendelkezik, akkor a rendszer beolvassa a dokumentumokat, Naplósorszám óta.

Az ellenőrzőpont-tömb így csak megakadályozza a a Naplósorszám minden partíció esetében. De a partíciók kezelésére nem szeretné, ha ellenőrzőpontokat, Naplósorszám, kezdési idő, stb. a egyszerűbb lehetőség a változáscsatorna feldolgozói kódtára használandó.

<a id="change-feed-processor"></a>
## <a name="using-the-change-feed-processor-library"></a>Használatával a módosítási hírcsatorna feldolgozói kódtára 

A [Azure Cosmos DB-módosítási hírcsatorna feldolgozói kódtára](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet-changefeed) segítségével könnyedén feloszthatja az események feldolgozása több ügyfél között. Ebben a könyvtárban olvasási módosítások egyszerűbbé teszi a partíciók és több szál párhuzamos használata.

Változáscsatorna feldolgozói könyvtárával fő előnye, hogy nem kell kezelni az egyes partíciók és a folytatási tokent, és nem rendelkezik az egyes gyűjtemények manuális lekérdezésére.

A változáscsatorna feldolgozói könyvtárával olvasási módosítások egyszerűbbé teszi a partíciók és több szál párhuzamos használata.  A bérlet mechanizmussal partíciójára olvasási módosítások automatikusan kezeli. Ha két a változáscsatorna feldolgozói kódtára használó ügyfelek az alábbi képen, láthatjuk, ahogy azok a munkát egymás között osztja fel. Növelheti az ügyfelek továbbra is, mivel azok tartsa osztani a munkát egymás között.

![Elosztott feldolgozásához Azure Cosmos DB – csatorna módosítása](./media/change-feed/change-feed-output.png)

A bal oldali ügyfél első lett elindítva, és indulása, figyelés, az összes partíciót, majd a második ügyfél lett elindítva, majd az első lehetővé teszik a bérletek második ügyfélnek némelyikének nyissa meg. Amint láthatja, ez a nagyszerű módja a munkát egymás között különböző gépek és az ügyfelek között.

Vegye figyelembe, hogy ha két kiszolgáló nélküli Azure functions monitorozási ugyanabba a gyűjteménybe, és használja ugyanazt a bérlet rendelkezik majd a két függvényt kaphat attól függően, hogy hogyan a feldolgozói kódtára úgy dönt, hogy a partíciók feldolgozása különböző dokumentumokon.

<a id="understand-cf"></a>
### <a name="understanding-the-change-feed-processor-library"></a>Feldolgozói kódtára ismertetése a módosítási hírcsatorna

A változáscsatorna feldolgozói könyvtárával végrehajtási négy fő összetevőből áll: a figyelt gyűjteményhez, a bérletek gyűjteményének, a processzor gazdagép és a fogyasztók számára. 

> [!WARNING]
> A gyűjtemények létrehozása a díjszabásra is hatással van, mivel átviteli sebességet tart fenn az alkalmazás számára az Azure Cosmos DB-vel folytatott kommunikációhoz. További részletekért látogasson el az [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

**Figyelt gyűjtemény:** a figyelt gyűjteményhez az adatok, amelyről a módosítási hírcsatorna jön létre. Bármely beszúrások és, a figyelt gyűjtemény módosításait a módosítási hírcsatorna a gyűjtemény is megjelennek. 

**A bérletek gyűjteményének:** a bérlet gyűjtemény koordináták feldolgozása a változáscsatorna feldolgozó között. Egy külön gyűjteményt a partíciónként egy bérlet a bérletek tárolásához használni kívánt szolgál. Célszerű tárolni a bérletek gyűjteményének az írási régió közelebb hol futnak a változáscsatorna processzor egy másik fiókot. A bérlet objektum tartalmazza a következő attribútumokat: 
* Tulajdonos: Itt adhatja meg a gazdagépet, amely a bérlet tulajdonosa
* Folytatási: Adja meg a pozíció (folytatási kód) egy adott partíció a változáscsatorna
* Időbélyeg: Legutóbbi bérleti frissítése megtörtént; az időbélyeg segítségével ellenőrizze, hogy a bérlet lejárt számít 

**Processor Host:** minden gazdagépen határozza meg, hány particionálja folyamat gazdagépek hány példánya van aktív bérleteket alapján. 
1.  A gazdagép indításakor az összes gazdagép a terhelés elosztása érdekében a bérletek szerez be. Egy gazdagép rendszeres időközönként megújítja bérleteket, a bérletek aktív marad, így. 
2.  Egy gazdagép ellenőrzőpontokat olvassa el a legutóbbi folytatási token az egyes bérletét. A párhuzamosság biztonság érdekében, egy gazdagépet ellenőrzi az ETag bérleti frissítése. Ellenőrzőpont biztosító egyéb stratégiák is támogatottak.  
3.  Leállásakor egy gazdagép összes címbérlet-kiadások, de megőrzi a folytatási adatokat, így, folytathatja a tárolt ellenőrzőpont később olvasásakor. 

Jelenleg a gazdagépek száma nem lehet nagyobb, mint a partíciók (bérletek).

**A fogyasztók:** fogyasztók számára, és a feldolgozókhoz, a változáscsatorna feldolgozást egyes állomások által indított hozzászóláslánc. Minden egyes processzor gazdagép több fogyasztó is rendelkezhet. Összes fogyasztóvédelmi olvassa be, a módosítás hírcsatorna oszlopát a partícióból hozzá van rendelve, és értesíti a változások gazdagépe és bérleteket lejárt.

Így jobban megismerheti az ezen négy módosítási hírcsatorna elemeinek processzor munkahelyi együtt, nézzük meg az alábbi ábrán egy példa. A figyelt gyűjteményhez tárolja a dokumentumokat, és a "city" használja partíciókulcsként. Láthatjuk, hogy a kék partíció "A-E" a "city" mezőt a dokumentumok tartalmazza, és így tovább. Nincsenek a négy partíció párhuzamosan olvasásakor két fogyasztóval rendelkező két gazdagép. A nyilak a fogyasztók a csatorna módosítása egy adott helyszínen olvasásakor. Az első partícióban a sötétebb kék olvasatlan módosítások jelöli, míg a világoskék jelöli a változáscsatorna már olvasási megváltozik a. A gazdagépek a bérletek gyűjteményének használni egy "folytatási" érték az aktuális olvasó pozíció minden felhasználóhoz nyomon követéséhez. 

![Processor host használatával az Azure Cosmos DB-módosítási hírcsatorna](./media/change-feed/changefeedprocessornew.png)

### <a name="working-with-the-change-feed-processor-library"></a>A változáscsatorna feldolgozói kódtára használata

Mielőtt telepítése a módosítási hírcsatorna processzor NuGet-csomagot, először telepítenie: 

* Microsoft.Azure.DocumentDB, a legújabb verzióra.
* Newtonsoft.Json, legújabb verzió

Majd telepítse a [Microsoft.Azure.DocumentDB.ChangeFeedProcessor Nuget-csomag](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/) és a egy hivatkozásként.

A változáscsatorna feldolgozói könyvtárával be kell megvalósítani a következő:

1. Alkalmazzon egy **DocumentFeedObserver** objektum, amely megvalósítja **IChangeFeedObserver**.
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

2. Alkalmazzon egy **DocumentFeedObserverFactory**, mely valósít meg egy **IChangeFeedObserverFactory**.
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

3. Definiálása *CancellationTokenSource* és *ChangeFeedProcessorBuilder*

    ```csharp
    private readonly CancellationTokenSource cancellationTokenSource = new CancellationTokenSource();
    private readonly ChangeFeedProcessorBuilder builder = new ChangeFeedProcessorBuilder();
    ```

5. hozhat létre a **ChangeFeedProcessorBuilder** a kívánt objektumok meghatározása után 

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
       
            ChangeFeedProcessorOptions feedProcessorOptions = new ChangeFeedProcessorOptions();

            // ie. customizing lease renewal interval to 15 seconds
            // can customize LeaseRenewInterval, LeaseAcquireInterval, LeaseExpirationInterval, FeedPollDelay 
            feedProcessorOptions.LeaseRenewInterval = TimeSpan.FromSeconds(15);
            feedProcessorOptions.StartFromBeginning = true;

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

Ennyi az egész. Ezeket a lépéseket néhány dokumentumok jelenik meg elindul a **DocumentFeedObserver.ProcessChangesAsync** metódust.

Kód felett van ábra célra különböző típusú objektumok és azok interakció megjelenítéséhez. Megfelelő változókat határozhat meg, és indítsa el őket a megfelelő értékekkel kell. Ebben a cikkben használt teljes kódot kap a [GitHub-adattárat](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessorV2).

> [!NOTE]
> Soha nem kell egy főkulcsot a kódban, vagy a konfigurációs fájlban a fenti kód látható módon. Lásd: [a kulcsok használata a Key-Vault beolvasni](https://sarosh.wordpress.com/2017/11/23/cosmos-db-and-key-vault/).


## <a name="faq"></a>GYIK

### <a name="what-are-the-different-ways-you-can-read-change-feed-and-when-to-use-each-method"></a>Mik azok a különböző módokon tudja olvasni a módosítási hírcsatorna? és mikor érdemes használni az egyes módszerek?

Módosítási hírcsatorna olvasásra három lehetőség áll rendelkezésre:

* **[Az Azure Cosmos DB SQL API .NET SDK használatával](#sql-sdk)**
   
   Ezzel a módszerrel kap alacsony mértékű a módosítási hírcsatorna. Az ellenőrzőpont kezelheti, elérheti, hogy egy adott partíció kulcsok stb. Ha több olvasók, használhat [ChangeFeedOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.changefeedoptions?view=azure-dotnet) a olvasási terheléselosztásban, különböző szálakon vagy különböző ügyfelek részére. .

* **[Használatával az Azure Cosmos DB-módosítási hírcsatorna feldolgozói kódtára](#change-feed-processor)**

   Ha azt szeretné, a módosítási hírcsatorna összetettsége rengeteg kiszervezik a változáscsatorna feldolgozói könyvtárával használhatja. Ez a kódtár elrejti összetettséget rengeteg, de továbbra is lehetővé teszi a teljes felügyeletet módosításcsatornáját. Ez a kódtár egy [megfigyelő minta](https://en.wikipedia.org/wiki/Observer_pattern), az SDK-t hívja meg a feldolgozó függvényhez. 

   Ha egy nagy átviteli sebességű változáscsatorna, olvassa el a módosítási hírcsatorna több ügyféllel példányosítható. Mivel "módosítása a változáscsatorna feldolgozói könyvtárával" használ, automatikusan felosztja a terhelést különböző ügyfelek között. Nem kell tennie semmit. Az összes összetettsége SDK kezeli. Azonban ha azt szeretné, hogy a saját terheléselosztóját, majd valósíthat meg IParitionLoadBalancingStrategy egyéni partíció stratégiához. Megvalósítása IPartitionProcessor – egyéni feldolgozási módosítások partíción. Az SDK-val, azonban egy partíciótartomány dolgozhatja fel, de ha szeretne feldolgozni egy adott partíciókulcs akkor rendelkezik az SQL API-hoz az SDK használatához.

* **[Az Azure Functions használatával](#azure-functions)** 
   
   Az Azure-függvény utolsó dolog, a legegyszerűbb lehetőség. Ez a beállítás használatát javasoljuk. Ha az Azure Functions alkalmazásokban hoz létre egy Azure Cosmos DB-eseményindítóval, választja, az Azure Cosmos DB-gyűjtemények való csatlakozáshoz, és a függvény akkor aktiválódik, amikor a gyűjteménybe módosításakor. Tekintse meg a [képernyő cast](https://www.youtube.com/watch?v=Mnq0O91i-0s&t=14s) , az Azure-függvényt, és a módosítási hírcsatornáról

   Eseményindítók hozható létre az Azure Functions portálon az Azure Cosmos DB Portalon vagy programozott módon. A Visual Studio és a VS Code Azure-függvény írása nagyszerű támogatással rendelkezik. Írási és a kódok hibakeresése az Ön asztalán, és telepíteni a függvény egyetlen kattintással. További információkért lásd: [Azure Cosmos DB: az Azure Functions szolgáltatással kiszolgáló nélküli adatbázis-használat](serverless-computing-database.md) cikk.

### <a name="what-is-the-sort-order-of-documents-in-change-feed"></a>Mi az a rendezési sorrend – csatorna módosítása a dokumentumok?

A változáscsatorna dokumentumok sorrendjét, a módosítás ideje érhető el. A rendezési sorrend garantáltan partíciónként csak.

### <a name="for-a-multi-region-account-what-happens-to-the-change-feed-when-the-write-region-fails-over-does-the-change-feed-also-failover-would-the-change-feed-still-appear-contiguous-or-would-the-fail-over-cause-change-feed-to-reset"></a>Egy többrégiós fiókhoz mi történik, ha az írási régió nem sikerül feladatátvételi a változáscsatorna? Nem a módosítási hírcsatorna is feladatátvételi? A változáscsatorna továbbra is jelenjen meg összefüggő, vagy megváltozna a feladatátvétel ok új adatcsatorna?

Igen, módosítási hírcsatorna a manuális feladatátvételt művelet között fog működni, és összefüggő lesz.

### <a name="how-long-change-feed-persist-the-changed-data-if-i-set-the-ttl-time-to-live-property-for-the-document-to--1"></a>Módosítási hírcsatorna mennyi ideig megőrizni a módosított adatokat, ha megadom a dokumentum TTL (Time élettartama) tulajdonsága a-1?

Módosítási hírcsatorna a végtelen megmaradnak. Adatok nem törlődnek, ha a módosítási hírcsatorna megmarad.

### <a name="how-can-i-configure-azure-functions-to-read-from-a-particular-region-as-change-feed-is-available-in-all-the-read-regions-by-default"></a>Hogyan konfigurálhatok régiót beolvasni az Azure functions, a olvasási régiók alapértelmezés szerint a módosítási hírcsatorna elérhető?

Ez jelenleg nem konfigurálhatja az Azure Functions szolgáltatást egy adott régió olvasni. Állítsa be a preferált régiók bármilyen Azure Cosmos DB-kötés és az eseményindító az Azure Functions-adattárat a githubon szerepel.

Az Azure Functions az alapértelmezett kapcsolat házirendet használja. Beállíthatja a kapcsolati módot az Azure Functions szolgáltatásban, és alapértelmezés szerint, olvas az írási régió, ezért érdemes közös elhelyezése az Azure Functions az ugyanabban a régióban.

### <a name="what-is-the-default-size-of-batches-in-azure-functions"></a>Mi az az alapértelmezett méret az Azure Functions kötegek?

az Azure Functions minden hívás 100 dokumentumok. Azonban ez a szám nem konfigurálható a function.json fájlban. Itt van a teljes [konfigurációs listájának](../azure-functions/functions-run-local.md). Ha helyileg fejleszt, belüli alkalmazás-beállításainak frissítése a [local.settings.json](../azure-functions/functions-run-local.md) fájlt.

### <a name="i-am-monitoring-a-collection-and-reading-its-change-feed-however-i-see-i-am-not-getting-all-the-inserted-document-some-documents-are-missing-what-is-going-on-here"></a>Általam figyelt gyűjtemény és olvasása a módosítási hírcsatorna, azonban láthatók nem érkeznek meg hozzám a beszúrt dokumentum, egyes dokumentumok hiányoznak. Mi történik Itt?

Győződjön meg arról, hogy nincs-e más függvény. ugyanabban a gyűjteményben az ugyanazon a bérletek gyűjteményének beolvasása. Történt, a velem, és később vettem a hiányzó dokumentumok dolgozza fel saját más az Azure functions, amely is használ ugyanabban a bérlet.

Ezért létrehozásakor olvassa el a több Azure Functions ugyanaz a módosítási hírcsatornáról akkor kell használni a különböző a bérletek gyűjteményének vagy a "leasePrefix" konfiguráció használata ugyanabban a gyűjteményben. Azonban a változáscsatorna feldolgozói könyvtárával használatakor a függvény több példányát is elindítható, és SDK felosztja a dokumentumok automatikusan az Ön számára különböző példányai között.

### <a name="my-document-is-updated-every-second-and-i-am-not-getting-all-the-changes-in-azure-functions-listening-to-change-feed"></a>A dokumentum másodpercenként frissül, és nem érkeznek meg hozzám a módosításokat az Azure Functions módosításcsatornáját figyeli.

Az Azure Functions szavazások változás hírcsatornáját 5 másodpercenként, 5 másodperc között végrehajtott módosítások elvesznek. Az Azure Cosmos DB tárolja csak egyetlen verziója 5 másodpercenként, 5. a módosítás megjelenik a dokumentum. Azonban ha azt szeretné, 5 másodperc alatt, és szeretné lekérdezni a változáscsatorna másodpercenként, akkor is konfigurálja a lekérdezési idő "feedPollDelay", lásd: [Azure Cosmos DB-kötéseket](../azure-functions/functions-bindings-cosmosdb.md#trigger---configuration). Az alapértelmezett 5000-es érték ezredmásodpercben van definiálva. 1 másodperc alatt a lehetőség lehetséges, de nem javasolt, több Processzor írás indul.

### <a name="i-inserted-a-document-in-the-mongo-api-collection-but-when-i-get-the-document-in-change-feed-it-shows-a-different-id-value-what-is-wrong-here"></a>A Mongo-API-gyűjtemény beszúrt egy dokumentumot, de a dokumentum a módosítási hírcsatorna kapok, ha egy másik azonosító értéket mutatja. Miért nem megfelelő Itt?

A gyűjtemény olyan Mongo API gyűjteménye. Ne feledje, módosítási hírcsatorna olvasható az SQL-ügyfél használatával, és szerializálja elemek JSON formátumba. A JSON formázását, MongoDB ügyfelek tapasztalható miatt egyeznek a BSON, formázott dokumentumok és a JSON formátumú – csatorna módosítása. Jelennek meg a BSON dokumentum JSON-ábrázolása. A bináris attribútumok egy Mongo-fiókokat használ, ha azok konvertálja JSON.

### <a name="is-there-a-way-to-control-change-feed-for-updates-only-and-not-inserts"></a>Van mód a frissítések csak változáscsatorna szabályozására, és nem szúr be?

Nincs még ma de ez a funkció a terveink között. Napjainkban a dokumentum a frissítések egy helyreállítható jelölő is hozzáadhat.

### <a name="is-there-a-way-to-get-deletes-in-change-feed"></a>Van mód a törlések beolvasása a módosítási hírcsatorna?

Jelenleg a módosítási hírcsatorna jelentkezzen nem törli. Módosítási hírcsatorna is folyamatosan javítja, és ez a funkció terveink között. Még ma a dokumentum a törlés helyreállítható jelölő is hozzáadhat. Attribútum hozzáadása a dokumentum "törölt" nevű és "true" értékűre, és állíthat be a dokumentum TTL így automatikusan törli.

### <a name="can-i-read-change-feed-for-historic-documentsfor-example-documents-that-were-added-5-years-back-"></a>Olvashatja a változáscsatorna történelmi dokumentumok (például dokumentumok 5 éves vissza hozzáadott)?

Igen, ha a dokumentum törlése nem érheti el, a módosítás adatcsatorna lehetőség a gyűjtemény forrása.

### <a name="can-i-read-change-feed-using-javascript"></a>Olvashatja a módosítási hírcsatorna JavaScript használatával?

Igen, a Node.js SDK kezdeti támogatás a módosítási hírcsatorna nemrégiben hozzáadott. Ahogyan az a következő példában adjon aktuális verzióra frissítés documentdb modult a kód futtatása előtt használható:

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

### <a name="can-i-read-change-feed-using-java"></a>Olvashatja a módosítási hírcsatorna Java használatával?

Olvassa el a módosítási hírcsatorna a Java-kódtár érhető el a [Github-adattár](https://github.com/Azure/azure-documentdb-changefeedprocessor-java). Azonban jelenleg Java-kódtár, néhány verzió .NET-kódtár mögött. Mindkét a kódtárakat hamarosan szinkronizálva lesz.

### <a name="can-i-use-etag-lsn-or-ts-for-internal-bookkeeping-which-i-get-in-response"></a>Használható _etag, _lsn vagy _ts a belső könyvelés, amely válasz jelenik meg?

belső _etag formátum, és meg nem függ attól, hogy (nem elemezhető,), mert bármikor módosíthatja.
_ts létrehozásának vagy módosításának időbélyeg. Időrendi összehasonlító _ts is használhat.
_lsn egy kötegazonosítót, csak a módosítási hírcsatorna hozzáadott, a azt a tranzakcióazonosító jelöli az áruházból... Előfordulhat, hogy hány dokumentum ugyanazon _lsn.
Egy dolog, vegye figyelembe, FeedResponse az ETag nem egyezik a _etag akkor jelenik meg a dokumentumot. _etag belső azonosító és egyidejűségi, a verziójának használatát a dokumentum ismerteti, az ETag pedig az alkalmazás-előkészítés a hírcsatornát.

### <a name="does-reading-change-feed-add-any-additional-cost-"></a>Nem, olvasási, módosítási hírcsatorna hozzá többletköltség nélkül?

A Kérelemegység felhasznált, azaz az Azure Cosmos DB-gyűjtemények kimenő és bemenő bármikor felhasználhatja a RU díjkötelesek. Felhasználók által a bérletek gyűjteményének felhasznált RU kell fizetnie.

### <a name="can-multiple-azure-functions-read-one-collections-change-feed"></a>Olvashatja a több Azure Functions egy gyűjtemény módosítási hírcsatorna?

Igen. Több Azure Functions ugyanabba a gyűjteménybe módosítási hírcsatorna olvashatja. Azonban az Azure Functions kell egy külön leaseCollectionPrefix definiálva van.

### <a name="should-the-lease-collection-be-partitioned"></a>A bérletek gyűjteményének particionált?

Nem, a bérletek gyűjteményének lehet meghatározni. A particionált a bérletek gyűjteményének nincs szükség, és jelenleg nem támogatja.

### <a name="can-i-read-change-feed-from-spark"></a>Olvastam is módosítási hírcsatorna a Spark?

Igen. Lásd: [Azure Cosmos DB Spark-összekötő](spark-connector.md). Íme egy [képernyő cast](https://www.youtube.com/watch?v=P9Qz4pwKm_0&t=1519s) látható, hogyan tud feldolgozni egy strukturált stream, csatorna módosítása.

### <a name="if-i-am-processing-change-feed-by-using-azure-functions-say-a-batch-of-10-documents-and-i-get-an-error-at-7th-document-in-that-case-the-last-three-documents-are-not-processed-how-can-i-start-processing-from-the-failed-documentie-7th-document-in-my-next-feed"></a>I vagyok feldolgozása az Azure Functions szolgáltatással csatorna módosítása, ha 10 dokumentumok kötegelt tegyük fel, és hibaüzenetet kapok, 7. a dokumentum. Ebben az esetben az utolsó három dokumentumok feldolgozása nem Hogyan tudok indíthatja feldolgozása a hibás dokumentum (vagyis) a dokumentum 7.) a következő hírcsatornában?

Kezelni a hibát, a javasolt egyik burkolhatja a kód a try-catch blokkban. A hiba tényleges és helyezze a dokumentum egy üzenetsorban (kézbesíthetetlen levelek), és megadhatja, logikai dokumentumokat állítja elő a hiba kezelésére. Ezzel a módszerrel van egy 200-dokumentum batch, és nem sikerült, csak egyetlen dokumentumot nem rendelkezik, az egész batch dobja.

Abban az esetben, ha hiba meg kell nem Visszatekerés az ellenőrzőpont vissza a kezdő más meg fog is továbbra is megkapja ezeket a dokumentumokat a módosítási hírcsatorna. Ne feledje, hogy a dokumentum az előző pillanatképet elveszítheti a módosítási hírcsatorna tartja a dokumentumokat, ezt, mert az utolsó végső beépülő képernyőképe. módosítási hírcsatorna tartja a dokumentum csak egy előző verzióját, és a köztes más folyamatok származnak, és módosítani a dokumentumot.

A kód tartsa kijavítása, hamarosan található dokumentumok a kézbesítetlen levelek várólistájára vonatkozik.
Módosítási hírcsatorna rendszer automatikusan hívja meg az Azure Functions és a check point stb belsőleg munkaterheléseire Azure-függvény. Ha azt szeretné, állítsa vissza a a check point és minden szempontját, érdemes lehet processzor SDK használatával módosítási hírcsatorna.


## <a name="next-steps"></a>További lépések

Azure Cosmos DB használatával az Azure Functions használatával kapcsolatos további információk: [Azure Cosmos DB: az Azure Functions szolgáltatással kiszolgáló nélküli adatbázis-használat](serverless-computing-database.md).

További információ a változáscsatorna feldolgozói kódtára használatával használja az alábbi forrásanyagokat:

* [Adatai lap](sql-api-sdk-dotnet-changefeed.md) 
* [Nuget-csomag](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)
* [1 – 6. lépéseket bemutató mintakód](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor)
* [További példák a Githubon](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)

További információ a változáscsatorna keresztül az SDK használatával használja az alábbi forrásanyagokat:

* [SDK-t adatai lap](sql-api-sdk-dotnet.md)
