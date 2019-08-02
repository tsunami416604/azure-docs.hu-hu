---
title: Ismerkedés a üzenetsor-tárolással és a Visual Studio csatlakoztatott szolgáltatásaival (Webjobs-projektek) | Microsoft Docs
description: Az Azure üzenetsor-tárolás használatának első lépései egy Webjobs-projektben a Visual Studio csatlakoztatott szolgáltatásainak használatával a Storage-fiókhoz való csatlakozás után.
services: storage
author: ghogen
manager: douge
ms.assetid: 5c3ef267-2a67-44e9-ab4a-1edd7015034f
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 44206f1826fc25407d9dec3f832b70881091e187
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68248961"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-webjob-projects"></a>Ismerkedés az Azure üzenetsor Storage és a Visual Studio csatlakoztatott szolgáltatásaival (Webjobs-projektek)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Áttekintés
Ez a cikk azt ismerteti, hogyan kezdheti el az Azure üzenetsor-tárolás használatát egy Visual Studio Azure Webjobs-projektben, miután létrehozta vagy hivatkozott egy Azure Storage-fiókot a Visual Studio **csatlakoztatott szolgáltatások hozzáadása** párbeszédpanel használatával. Amikor egy Webjobs-projekthez hozzáadja a Storage-fiókot a Visual Studio **csatlakoztatott szolgáltatások hozzáadása** párbeszédpanel használatával, a megfelelő Azure Storage NuGet-csomagok települnek, a megfelelő .net-hivatkozások hozzáadódnak a projekthez, és a kapcsolati karakterláncok a következőhöz: a Storage-fiók az app. config fájlban frissül.  

Ez a cikk C# a Azure WebJobs SDK 1. x verziójának Azure üzenetsor-tárolási szolgáltatással való használatát bemutató kódrészleteket tartalmaz.

Az Azure Queue Storage szolgáltatás üzenetek nagy számban történő tárolására szolgál, amelyek HTTP- vagy HTTPS-kapcsolattal, hitelesített hívásokon keresztül a világon bárhonnan elérhetők. Egyetlen üzenetsor akár 64 KB méretű is lehet, és a tárfiók maximális kapacitásán belül több millió üzenetet tartalmazhat. További információ: az [Azure Queue Storage használatának első lépései a .NET használatával](../storage/queues/storage-dotnet-how-to-use-queues.md) . További információ a ASP.NET: [ASP.net](https://www.asp.net).

## <a name="how-to-trigger-a-function-when-a-queue-message-is-received"></a>Függvény elindítása üzenetsor-üzenet érkezésekor
Ha olyan függvényt szeretne írni, amelyet a webjobs SDK hív meg egy üzenetsor-üzenet fogadásakor, használja a **QueueTrigger** attribútumot. Az attribútum konstruktora egy olyan karakterlánc-paramétert használ, amely megadja a lekérdezési várólista nevét. Ha szeretné megtudni, hogyan kell dinamikusan beállítani a várólista nevét, tekintse meg a [konfigurációs beállítások megadása](#how-to-set-configuration-options)című témakört.

### <a name="string-queue-messages"></a>Karakterlánc-Várólista üzenetei
A következő példában a várólista egy karakterlánc-üzenetet tartalmaz, így a **QueueTrigger** egy **logMessage** nevű karakterlánc-paraméterre lesz alkalmazva, amely tartalmazza az üzenetsor-üzenet tartalmát. A függvény [egy naplófájlt ír az irányítópultra](#how-to-write-logs).

```csharp
public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
{
    logger.WriteLine(logMessage);
}
```

A **karakterlánc**mellett a paraméter lehet egy byte Array, egy **CloudQueueMessage** objektum vagy egy Ön által meghatározott poco.

### <a name="poco-plain-old-clr-objecthttpsenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO [(régi CLR-objektum](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) üzenetsor-üzenetei
A következő példában az üzenetsor-üzenet JSON-t tartalmaz egy **BlobInformation** objektumhoz, amely tartalmaz egy **BlobName** tulajdonságot. Az SDK automatikusan deszerializálja az objektumot.

```csharp
public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
{
    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
}
```

Az SDK az [Newtonsoft. JSON NuGet-csomagot](https://www.nuget.org/packages/Newtonsoft.Json) használja az üzenetek szerializálásához és deszerializálásához. Ha olyan programban hoz létre üzenetsor-üzeneteket, amely nem a webjobs SDK-t használja, a következő példához hasonló kódot írhat az SDK által elemezhető POCO üzenetsor-üzenet létrehozásához.

```csharp
BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
logQueue.AddMessage(queueMessage);
```

### <a name="async-functions"></a>Aszinkron függvények
A következő aszinkron függvény [naplót ír az irányítópultra](#how-to-write-logs).

```csharp
public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
{
    await logger.WriteLineAsync(logMessage);
}
```

Az aszinkron függvények lemondási [tokent](https://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken)is igénybe vehetnek, ahogy azt az alábbi példában is látható, amely egy blobot másol. (A **queueTrigger** helyőrző magyarázatát lásd a Blobok szakaszban [](#how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message) .)

```csharp
public async static Task ProcessQueueMessageAsyncCancellationToken(
    [QueueTrigger("blobcopyqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
    CancellationToken token)
{
    await blobInput.CopyToAsync(blobOutput, 4096, token);
}
```

## <a name="types-the-queuetrigger-attribute-works-with"></a>A QueueTrigger attribútum típusa
A **QueueTrigger** a következő típusokkal végezhető el:

* **string**
* Egy olyan POCO típus, amely JSON-ként van szerializálva
* **byte[]**
* **CloudQueueMessage**

## <a name="polling-algorithm"></a>Lekérdezési algoritmus
Az SDK egy véletlenszerű exponenciális visszakapcsolási algoritmust valósít meg, amely csökkenti az üresjárati üzenetsor lekérdezésének hatását a tárolási tranzakciós költségekre.  Ha egy üzenet található, az SDK két másodpercet vár, majd egy másik üzenetet keres. Ha nem talál üzenetet, a rendszer körülbelül négy másodpercig vár, mielőtt újra próbálkozik. A várakozási sor üzenetének későbbi sikertelen próbálkozásai után a várakozási idő továbbra is növekszik, amíg el nem éri a maximális várakozási időt, ami alapértelmezés szerint egy percet mutat. [A maximális várakozási idő konfigurálható](#how-to-set-configuration-options).

## <a name="multiple-instances"></a>Több példány
Ha a webalkalmazás több példányon fut, a folyamatos webjobs minden gépen fut, és minden gép megvárja az eseményindítókat, és megkísérli futtatni a függvényeket. Bizonyos helyzetekben ez néhány, ugyanazon adatokat feldolgozó függvényt is eredményezhet, ezért a függvényeket idempotens kell (írni kell, hogy az azonos bemeneti adatok ismételt meghívása ne hozzon létre ismétlődő eredményeket).  

## <a name="parallel-execution"></a>Párhuzamos végrehajtás
Ha több függvény is figyeli a különböző várólistákat, az SDK párhuzamosan hívja őket, ha az üzenetek egyidejűleg érkeznek.

Ugyanez igaz, ha egyetlen várólistához több üzenet érkezik. Alapértelmezés szerint az SDK egyszerre 16 üzenetsor-üzenetet kap, és végrehajtja azt a függvényt, amely párhuzamosan dolgozza fel őket. [A köteg mérete konfigurálható](#how-to-set-configuration-options). Ha a feldolgozás alatt álló szám a Batch-méret felére kerül, az SDK beolvas egy másik köteget, és elindítja az üzenetek feldolgozását. Ezért a függvény által feldolgozott egyidejű üzenetek maximális száma egy és fél alkalommal a köteg méretének. Ez a korlát külön vonatkozik a **QueueTrigger** attribútummal rendelkező mindegyik függvényre. Ha nem szeretne párhuzamos végrehajtást végrehajtani az egyik várólistán fogadott üzenetekhez, állítsa a Batch méretét 1-re.

## <a name="get-queue-or-queue-message-metadata"></a>Üzenetsor vagy üzenetsor-üzenetek metaadatainak beolvasása
A következő üzenet-tulajdonságokat a metódus-aláírás paramétereinek hozzáadásával érheti el:

* **DateTimeOffset** expirationtime tulajdonságok
* **DateTimeOffset** insertionTime
* **DateTimeOffset** nextVisibleTime
* **karakterlánc** queueTrigger (szöveges üzenet)
* **karakterlánc** -azonosító
* **karakterlánc** popReceipt
* **int** dequeueCount

Ha közvetlenül az Azure Storage API-val szeretne dolgozni, **CloudStorageAccount** paramétert is hozzáadhat.

Az alábbi példa a metaadatokat egy INFO-alkalmazás naplójába írja. A példában mind a logMessage, mind a queueTrigger tartalmazza az üzenetsor-üzenet tartalmát.

```csharp
public static void WriteLog([QueueTrigger("logqueue")] string logMessage,
    DateTimeOffset expirationTime,
    DateTimeOffset insertionTime,
    DateTimeOffset nextVisibleTime,
    string id,
    string popReceipt,
    int dequeueCount,
    string queueTrigger,
    CloudStorageAccount cloudStorageAccount,
    TextWriter logger)
{
    logger.WriteLine(
        "logMessage={0}\n" +
        "expirationTime={1}\ninsertionTime={2}\n" +
        "nextVisibleTime={3}\n" +
        "id={4}\npopReceipt={5}\ndequeueCount={6}\n" +
        "queue endpoint={7} queueTrigger={8}",
        logMessage, expirationTime,
        insertionTime,
        nextVisibleTime, id,
        popReceipt, dequeueCount,
        cloudStorageAccount.QueueEndpoint,
        queueTrigger);
}
```

Itt látható a mintakód által írt minta napló:

        logMessage=Hello world!
        expirationTime=10/14/2014 10:31:04 PM +00:00
        insertionTime=10/7/2014 10:31:04 PM +00:00
        nextVisibleTime=10/7/2014 10:41:23 PM +00:00
        id=262e49cd-26d3-4303-ae88-33baf8796d91
        popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
        dequeueCount=1
        queue endpoint=https://contosoads.queue.core.windows.net/
        queueTrigger=Hello world!

## <a name="graceful-shutdown"></a>Biztonságos leállítás
A folytonos Webjobs futó függvények elfogadják a **CancellationToken** paramétert, amely lehetővé teszi, hogy az operációs rendszer értesítse a függvényt, amikor a webjobs hamarosan leáll. Ezzel az értesítéssel meggyőződhet arról, hogy a függvény váratlanul leáll olyan módon, amely inkonzisztens állapotban hagyja az adatvesztést.

Az alábbi példa azt szemlélteti, hogyan ellenőrizhető a közelgő Webjobs megszakítása egy függvényben.

```csharp
public static void GracefulShutdownDemo(
            [QueueTrigger("inputqueue")] string inputText,
            TextWriter logger,
            CancellationToken token)
{
    for (int i = 0; i < 100; i++)
    {
        if (token.IsCancellationRequested)
        {
            logger.WriteLine("Function was cancelled at iteration {0}", i);
            break;
        }
        Thread.Sleep(1000);
        logger.WriteLine("Normal processing for queue message={0}", inputText);
    }
}
```

**Megjegyzés:** Előfordulhat, hogy az irányítópulton nem jelenik meg megfelelően a leállított függvények állapota és kimenete.

További információkért lásd: [webjobs](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR)-leállítási funkció.   

## <a name="how-to-create-a-queue-message-while-processing-a-queue-message"></a>Üzenetsor-üzenet létrehozása üzenetsor-üzenet feldolgozásakor
Új üzenetsor-üzenetet létrehozó függvény írásához használja az **üzenetsor** attribútumot. A **QueueTrigger**hasonlóan karakterláncként adja át a várólista nevét, vagy beállíthatja [a várólista nevét dinamikusan](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Karakterlánc-Várólista üzenetei
A következő nem aszinkron kód minta egy új üzenetsor-üzenetet hoz létre a "outputqueue" nevű várólistában ugyanazzal a tartalommal, mint a "inputqueue" nevű várólistában kapott üzenetsor-üzenet. (Az aszinkron függvények a szakasz későbbi részében látható **\<IAsyncCollector T >** használják.)

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    [Queue("outputqueue")] out string outputQueueMessage )
{
    outputQueueMessage = queueMessage;
}
```

### <a name="poco-plain-old-clr-objecthttpsenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO [(régi CLR-objektum](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) üzenetsor-üzenetei
Ha nem karakterlánc helyett egy POCOt tartalmazó üzenetsor-üzenetet szeretne létrehozni, adja át a POCO típusát kimeneti paraméterként a **várólista** -attribútum konstruktorának.

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
{
    blobInfoOutput = blobInfoInput;
}
```

Az SDK automatikusan a JSON-ra szerializálja az objektumot. A várólista-üzenet mindig létrejön, még akkor is, ha az objektum null értékű.

### <a name="create-multiple-messages-or-in-async-functions"></a>Több üzenet vagy aszinkron függvények létrehozása
Több üzenet létrehozásához adja meg a **\<ICollector t >** vagy **\<IAsyncCollector t >** kimeneti várólistához a következő példában látható módon.

```csharp
public static void CreateQueueMessages(
    [QueueTrigger("inputqueue")] string queueMessage,
    [Queue("outputqueue")] ICollector<string> outputQueueMessage,
    TextWriter logger)
{
    logger.WriteLine("Creating 2 messages in outputqueue");
    outputQueueMessage.Add(queueMessage + "1");
    outputQueueMessage.Add(queueMessage + "2");
}
```

Minden üzenetsor-üzenet azonnal létrejön az **Add** metódus hívásakor.

### <a name="types-that-the-queue-attribute-works-with"></a>A várólista-attribútum által használható típusok
A **várólista** attribútum a következő paraméterek típusainál használható:

* **kimenő sztring** (Üzenetsor létrehozása, ha a paraméter értéke nem null, ha a függvény véget ér)
* **kimenő bájt []** (működik, mint a **String**)
* **kimenő CloudQueueMessage** (működik, mint a **String**)
* a **poco** (szerializálható típus esetén a null objektummal rendelkező üzenetet hoz létre, ha a paraméter értéke null, ha a függvény véget ér)
* **ICollector**
* **IAsyncCollector**
* **CloudQueue** (üzenetek manuális létrehozása az Azure Storage API közvetlen használatával)

### <a name="use-webjobs-sdk-attributes-in-the-body-of-a-function"></a>Webjobs SDK-attribútumok használata függvények törzsében
Ha egy webjobs SDK-attribútum (például **üzenetsor**, **blob**vagy **tábla**) használata előtt végre kell hajtania néhány munkát a függvényben, használhatja a **IBinder** felületet.

A következő példa egy bemeneti üzenetsor-üzenetet hoz létre, és létrehoz egy új üzenetet ugyanazzal a tartalommal egy kimeneti várólistában. A kimeneti várólista nevét a függvény törzsében található kód állítja be.

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
}
```

A **IBinder** felületet a **tábla** -és **blob** -attribútumokkal is használhatja.

## <a name="how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message"></a>Blobok és táblák olvasása és írása üzenetsor-üzenet feldolgozásakor
A **blob** és a **tábla** attribútumai lehetővé teszik a blobok és táblák olvasását és írását. Az ebben a szakaszban szereplő minták a blobokra vonatkoznak. A Blobok létrehozásakor vagy frissítésekor a folyamatok indításának módját bemutató mintakód: [Az Azure Blob Storage használata a Webjobs SDK-val](https://github.com/Azure/azure-webjobs-sdk/wiki).
<!-- , and for code samples that read and write tables, see [How to use Azure table storage with the WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-storage-tables-how-to.md). -->

### <a name="string-queue-messages-triggering-blob-operations"></a>A blob-műveleteket kiváltó karakterlánc-Várólista üzenetei
Karakterláncot tartalmazó üzenetsor-üzenet esetén a **queueTrigger** egy helyőrző, amelyet a **blob** attribútum **blobPath** paramétere tartalmaz, amely az üzenet tartalmát tartalmazza.

A következő példa **stream** -objektumokat használ a Blobok olvasására és írására. Az üzenetsor-üzenet a textblobs tárolóban található blob neve. A blob egy példánya, amely az "-New" utótaggal van hozzáfűzve a névben, ugyanabban a tárolóban jön létre.

```csharp
public static void ProcessQueueMessage(
    [QueueTrigger("blobcopyqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

A **blob** attribútum konstruktora egy **blobPath** paramétert hoz létre, amely meghatározza a tárolót és a blob nevét. További információ erről a helyőrzőről: [Az Azure Blob Storage használata a Webjobs SDK-val](https://github.com/Azure/azure-webjobs-sdk/wiki).

Ha az attribútum egy **stream** objektumot díszít, egy másik konstruktor paraméter adja meg a **fileaccess** módot olvasási, írási vagy olvasási/írási értékként.

A következő példa egy **CloudBlockBlob** objektumot használ a Blobok törléséhez. A várólista-üzenet a blob neve.

```csharp
public static void DeleteBlob(
    [QueueTrigger("deleteblobqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
{
    blobToDelete.Delete();
}
```

### <a name="poco-plain-old-clr-objecthttpsenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO [(régi CLR-objektum](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) üzenetsor-üzenetei
A várólista-üzenetben JSON-ként tárolt POCO esetében használhatja a **várólista** -attribútum **blobPath** paraméterében található objektum nevét tartalmazó helyőrzőket. A várólista metaadatainak tulajdonságainak neve helyőrzőként is használható. Lásd: üzenetsor [vagy üzenetsor-üzenetek metaadatainak](#get-queue-or-queue-message-metadata)beolvasása.

A következő példa egy blobot másol egy másik kiterjesztésű új blobra. Az üzenetsor-üzenet egy **BlobInformation** objektum, amely **BlobName** -és **BlobNameWithoutExtension** -tulajdonságokat is tartalmaz. A tulajdonságok nevei helyőrzőként használatosak a blob **-attribútumok blob** -elérési útjában.

```csharp
public static void CopyBlobPOCO(
    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

Az SDK az [Newtonsoft. JSON NuGet-csomagot](https://www.nuget.org/packages/Newtonsoft.Json) használja az üzenetek szerializálásához és deszerializálásához. Ha olyan programban hoz létre üzenetsor-üzeneteket, amely nem a webjobs SDK-t használja, a következő példához hasonló kódot írhat az SDK által elemezhető POCO üzenetsor-üzenet létrehozásához.

```csharp
BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
logQueue.AddMessage(queueMessage);
```

Ha a függvényben némi munkát kell végrehajtania, mielőtt a blobot egy objektumhoz köti, használhatja a függvény törzsében található attribútumot, ahogyan az a [függvény törzsében a Webjobs SDK-attribútumok használata](#use-webjobs-sdk-attributes-in-the-body-of-a-function)című részben látható.

### <a name="types-you-can-use-the-blob-attribute-with"></a>A blob attribútumot használhatja a
A **blob** attribútum a következő típusokkal használható:

* **Stream** (olvasás vagy írás, a FileAccess konstruktor paraméter használatával megadva)
* **TextReader**
* **TextWriter**
* **karakterlánc** olvasni
* **kimenő sztring** (írás; csak akkor hoz létre blobot, ha a függvény visszatérése esetén a karakterlánc paraméter értéke nem null.)
* POCO (olvasás)
* a POCO (írás; mindig létrehoz egy blobot, és null objektumként jön létre, ha a POCO paraméter értéke null, ha a függvény visszatér)
* **CloudBlobStream** írni
* **ICloudBlob** (olvasás vagy írás)
* **CloudBlockBlob** (olvasás vagy írás)
* **CloudPageBlob** (olvasás vagy írás)

## <a name="how-to-handle-poison-messages"></a>A mérgezett üzenetek kezelése
Azok az üzenetek, amelyeknek a tartalma a függvény ** meghibásodását okozza, megmérgezi üzenetnek nevezzük. Ha a függvény meghibásodik, a várólista-üzenet nem törlődik, és végül újra bekerül, így a ciklus megismétlődik. Az SDK csak korlátozott számú iteráció után képes automatikusan megszakítani a ciklust, vagy manuálisan is elvégezheti.

### <a name="automatic-poison-message-handling"></a>Üzenetek automatikus megmérgezés általi kezelésére
Az SDK a várólista-üzenetek feldolgozásához legfeljebb 5 alkalommal hívja meg a függvényt. Ha az ötödik próbálkozás sikertelen, az üzenet átkerül egy mérgezett várólistára. Megtudhatja, hogyan állíthatja be az újrapróbálkozások maximális számát a [konfigurációs beállítások](#how-to-set-configuration-options)megadásával.

A méreg üzenetsor neve *{originalqueuename}* -Poison. Írhat egy függvényt, amely az üzenetek törlését végzi a méreg-várólistából úgy, hogy naplózza azokat, vagy értesítést küld, amely manuális beavatkozást igényel.

A következő példában a **CopyBlob** függvény sikertelen lesz, ha egy üzenetsor-üzenet tartalmazza a nem létező blob nevét. Ebben az esetben a rendszer áthelyezi az üzenetet a copyblobqueue-várólistáról a copyblobqueue-Poison várólistára. A **ProcessPoisonMessage** ezután naplózza a Megmérgező üzenetet.

```csharp
public static void CopyBlob(
    [QueueTrigger("copyblobqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}

public static void ProcessPoisonMessage(
    [QueueTrigger("copyblobqueue-poison")] string blobName, TextWriter logger)
{
    logger.WriteLine("Failed to copy blob, name=" + blobName);
}
```

Az alábbi ábrán a függvények konzoljának kimenete látható a méreg üzenet feldolgozásakor.

![Konzol kimenete a megmérgezett üzenetek kezelésére](./media/vs-storage-webjobs-getting-started-queues/poison.png)

### <a name="manual-poison-message-handling"></a>Az üzenetek kézi megmérgezésének manuális feldolgozása
Lekérheti, hogy az üzenet hányszor lett feldolgozva feldolgozásra egy **dequeueCount** nevű **int** paraméter hozzáadásával a függvényhez. Ezután a függvény kódjában megtekintheti a várólista-várólistát, és elvégezheti a saját Megmérgező üzenetek kezelését, ha a szám meghaladja a küszöbértéket, ahogy az az alábbi példában is látható.

```csharp
public static void CopyBlob(
    [QueueTrigger("copyblobqueue")] string blobName, int dequeueCount,
    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput,
    TextWriter logger)
{
    if (dequeueCount > 3)
    {
        logger.WriteLine("Failed to copy blob, name=" + blobName);
    }
    else
    {
        blobInput.CopyTo(blobOutput, 4096);
    }
}
```

## <a name="how-to-set-configuration-options"></a>Konfigurációs beállítások megadása
A **JobHostConfiguration** típusa a következő konfigurációs beállítások megadására használható:

* Állítsa be az SDK-kapcsolatok karakterláncait a kódban.
* Konfigurálja a **QueueTrigger** beállításait, például a várólista maximális száma értéket.
* Várólisták nevének beolvasása a konfigurációból.

### <a name="set-sdk-connection-strings-in-code"></a>SDK-beli kapcsolatok karakterláncának beállítása a kódban
Az SDK-kapcsolati karakterláncok a kódban való beállítása lehetővé teszi a saját kapcsolati karakterláncok nevének használatát a konfigurációs fájlokban vagy környezeti változókban, az alábbi példában látható módon.

```csharp
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    var _dashboardConn = ConfigurationManager
        .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    var _serviceBusConn = ConfigurationManager
        .ConnectionStrings["MyServiceBusConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    config.DashboardConnectionString = _dashboardConn;
    config.ServiceBusConnectionString = _serviceBusConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="configure-queuetrigger--settings"></a>QueueTrigger-beállítások konfigurálása
A várólista-üzenetek feldolgozására vonatkozó következő beállításokat állíthatja be:

* A párhuzamosan végrehajtandó üzenetsor-üzenetek maximális száma (alapértelmezés szerint 16).
* Az újrapróbálkozások maximális száma, mielőtt a várólista-üzenetet elküldjék egy Megmérgező várólistára (az alapértelmezett érték 5).
* Az a várakozási idő, ameddig a várólista üres, és a lekérdezés megkezdése előtt (az alapértelmezett érték 1 perc).

Az alábbi példa bemutatja, hogyan konfigurálhatja ezeket a beállításokat:

```csharp
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.Queues.BatchSize = 8;
    config.Queues.MaxDequeueCount = 4;
    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="set-values-for-webjobs-sdk-constructor-parameters-in-code"></a>A webjobs SDK-konstruktor paramétereinek értékeinek beállítása a kódban
Esetenként meg kell adnia egy üzenetsor nevét, egy blob nevét vagy tárolóját, vagy egy kódot a kódban a kód helyett. Előfordulhat például, hogy egy konfigurációs fájlban vagy környezeti változóban szeretné megadni a **QueueTrigger** várólistájának nevét.

Ezt úgy teheti meg, hogy egy **NameResolver** objektumot továbbít a **JobHostConfiguration** típusának. A speciális helyőrzőket százalékban (%) kell megadnia aláírja a webjobs SDK-attribútumok konstruktorának paramétereit, és a **NameResolver** -kód meghatározza a helyőrzők helyett használandó tényleges értékeket.

Tegyük fel például, hogy egy logqueuetest nevű várólistát szeretne használni a tesztkörnyezetben és egy logqueueprod az éles környezetben. A rögzített üzenetsor neve helyett egy olyan bejegyzés nevét szeretné megadni a **appSettings** gyűjteményben, amelynek a neve megegyezik a tényleges üzenetsor nevével. Ha a **appSettings** kulcs logqueue, a függvény az alábbi példához hasonlóan fog kinézni.

```csharp
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

A **NameResolver** osztály az alábbi példában látható módon kérheti le az üzenetsor nevét a **appSettings** -ből:

```csharp
public class QueueNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

A **NameResolver** osztályt a következő példában látható módon adja át a **JobHost** objektumnak.

```csharp
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new QueueNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

**Megjegyzés:** A rendszer minden alkalommal feloldja a várólista-, tábla-és Blobok nevét, de a blob-tárolók nevei csak az alkalmazás indításakor lesznek feloldva. A blob-tároló neve nem módosítható a feladatok futása közben.

## <a name="how-to-trigger-a-function-manually"></a>Függvények manuális elindítása
A függvények manuális elindításához használja a **JobHost** objektum **hívás** vagy **CallAsync** metódusát, valamint a függvény **NoAutomaticTrigger** attribútumát az alábbi példában látható módon.

```csharp
public class Program
{
    static void Main(string[] args)
    {
        JobHost host = new JobHost();
        host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
    }

    [NoAutomaticTrigger]
    public static void CreateQueueMessage(
        TextWriter logger,
        string value,
        [Queue("outputqueue")] out string message)
    {
        message = value;
        logger.WriteLine("Creating queue message: ", message);
    }
}
```

## <a name="how-to-write-logs"></a>Naplók írása
Az irányítópult két helyen jeleníti meg a naplókat: a Webjobs lapja, valamint egy adott Webjobs-hívás lapja.

![Naplók a Webjobs oldalon](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

![Naplók a függvény meghívása lapon](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

Az adott függvényben vagy a **Main ()** metódusban meghívott konzolos metódusok kimenete a webjobs irányítópult lapján jelenik meg, nem pedig az adott metódus meghívásának oldalán. A metódus-aláírás egyik paramétere által beolvasott TextWriter objektum kimenete megjelenik az irányítópult lapon a metódus meghívásához.

A konzol kimenete nem csatolható egy adott metódushoz, mert a konzol egyetlen szálból áll, míg a feladatok egyszerre több funkciója is futhat. Ezért az SDK az egyes függvényeket a saját egyedi napló-írói objektumával látja el.

Az [alkalmazás-nyomkövetési naplók](../app-service/troubleshoot-dotnet-visual-studio.md#logsoverview)írásához használja a **Console. out** (információként megjelölt naplók létrehozása) és a **konzol. hiba** (a naplókat a következőként jelölte meg: hiba). Egy másik lehetőség, hogy [nyomkövetési vagy TraceSource](https://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)használjon, amely részletes, figyelmeztetési és kritikus szinteket biztosít az információk és a hibák mellett. Az alkalmazás-nyomkövetési naplók a webalkalmazás naplófájljaiban, az Azure-táblákban vagy az Azure-blobokban jelennek meg attól függően, hogyan konfigurálja az Azure-webalkalmazást. Ahogy az az összes konzol kimenete igaz, a legutóbbi 100-es alkalmazási naplók is megjelennek a Webjobs irányítópult lapján, nem pedig a függvény meghívásának lapja.

A konzol kimenete csak akkor jelenik meg az irányítópulton, ha a program egy Azure-Webjobs fut, nem, ha a program helyi vagy más környezetben fut.

A naplózás letiltásához állítsa az irányítópult-kapcsolódási karakterláncot NULL értékre. További információ: [a konfigurációs beállítások megadása](#how-to-set-configuration-options).

A következő példa számos módszert mutat be a naplók írásához:

```csharp
public static void WriteLog(
    [QueueTrigger("logqueue")] string logMessage,
    TextWriter logger)
{
    Console.WriteLine("Console.Write - " + logMessage);
    Console.Out.WriteLine("Console.Out - " + logMessage);
    Console.Error.WriteLine("Console.Error - " + logMessage);
    logger.WriteLine("TextWriter - " + logMessage);
}
```

A webjobs SDK irányítópultján megjelenik a **TextWriter** objektum kimenete, amikor egy adott függvény meghívásának lapjára lép, és kiválasztja a **kimenet váltása**lehetőséget:

![Meghívási hivatkozás](./media/vs-storage-webjobs-getting-started-queues/dashboardinvocations.png)

![Naplók a függvény meghívása lapon](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

A webjobs SDK irányítópultján a konzol kimenetének legutóbbi 100 sora jelenik meg, amikor a Webjobs (nem a függvény meghívásához) oldalára mutat, és kiválasztja a **kimenet váltása**elemet.

![Kimenet váltása](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

Folyamatos Webjobs az alkalmazások naplói a/Data/Jobs/Continuous/ *{webjobname}* /job_log.txt jelennek meg a webalkalmazás fájlrendszerében.

        [09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
        [09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
        [09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

Egy Azure-blobban az alkalmazás naplói így néznek ki: 2014-09-26T21:01:13, Information, contosoadsnew, 491e54, 635473620738373502, 0, 17404, 17, konzol. Write-Helló világ!, 2014-09-26T21:01:13, hiba, contosoadsnew, 491e54, 635473620738373502, 0, 17404, 19, konzol. Error-Helló World!, 2014-09-26T21 : 01:13, információ, contosoadsnew, 491e54, 635473620738529920, 0, 17404, 17, konzol. out-Helló világ!,

És egy Azure-táblázatban a **Console. out** és a **Console. Error** naplók a következőképpen néznek ki:

![Információs napló a táblában](./media/vs-storage-webjobs-getting-started-queues/tableinfo.png)

![Hibanapló a táblában](./media/vs-storage-webjobs-getting-started-queues/tableerror.png)

## <a name="next-steps"></a>További lépések
Ez a cikk az Azure Queues használatának gyakori forgatókönyveit bemutató kódrészleteket tartalmaz. További információ a Azure WebJobs és a webjobs SDK használatáról: [Azure WebJobs dokumentációs erőforrások](https://go.microsoft.com/fwlink/?linkid=390226).

