---
title: A várólista-tárolás első lépései a Visual Studio használatával (WebJob-projektek)
description: Az Azure Queue storage használatának első lépései egy WebJob-projektben, miután a Visual Studio csatlakoztatott szolgáltatásaival csatlakozott egy tárfiókhoz.
services: storage
author: ghogen
manager: jillfra
ms.assetid: 5c3ef267-2a67-44e9-ab4a-1edd7015034f
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ffba203bafaf3837cd2d7fc1a6fd962a6926b186
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298752"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-webjob-projects"></a>Az Azure Queue storage és a Visual Studio csatlakoztatott szolgáltatásainak (WebJob-projektek) első lépései
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Áttekintés
Ez a cikk bemutatja, hogyan kezdheti el használni az Azure Queue storage-t egy Visual Studio Azure WebJob-projektben, miután létrehozott vagy hivatkozott egy Azure-tárfiókra a Visual Studio **Csatlakoztatott szolgáltatások hozzáadása** párbeszédpanelen. Ha a Visual Studio **Csatlakoztatott szolgáltatások hozzáadása** párbeszédpanelen hozzáad egy tárfiókot egy WebJob-projekthez, a megfelelő Azure Storage NuGet csomagok települnek, a megfelelő .NET-hivatkozások hozzáadódnak a projekthez, és a tárfiók kapcsolati karakterláncai frissülnek az App.config fájlban.  

Ez a cikk C# kódmintákat tartalmaz, amelyek bemutatják, hogyan használhatja az Azure WebJobs SDK 1.x-es verzióját az Azure Queue storage szolgáltatással.

Az Azure Queue Storage szolgáltatás üzenetek nagy számban történő tárolására szolgál, amelyek HTTP- vagy HTTPS-kapcsolattal, hitelesített hívásokon keresztül a világon bárhonnan elérhetők. Egyetlen üzenetsor akár 64 KB méretű is lehet, és a tárfiók maximális kapacitásán belül több millió üzenetet tartalmazhat. További információ: Az Azure Queue Storage használatával a [.NET használatával](../storage/queues/storage-dotnet-how-to-use-queues.md) című témakörben talál. A ASP.NET ASP.NET a ASP.NET című témakörben [talál](https://www.asp.net)további információt.

## <a name="how-to-trigger-a-function-when-a-queue-message-is-received"></a>Funkció aktiválása üzenetsorüzenet érkezésekor
Ha olyan függvényt szeretne írni, amelyet a WebJobs SDK hív meg a várólista-üzenet fogadásakor, használja a **QueueTrigger** attribútumot. Az attribútumkonstruktor egy karakterlánc-paramétert vesz igénybe, amely megadja a lehívni kívánt várólista nevét. A várólista nevének dinamikus beállításáról a [Konfigurációs beállítások megadása című](#how-to-set-configuration-options)témakörben található.

### <a name="string-queue-messages"></a>Sorlistaüzenetek karakterlánc-üzenetei
A következő példában a várólista egy karakterlánc-üzenetet tartalmaz, így a **QueueTrigger** egy **logMessage** nevű karakterlánc-paraméterre lesz alkalmazva, amely a várólistaüzenet tartalmát tartalmazza. A függvény [naplóüzenetet küld az irányítópultnak.](#how-to-write-logs)

```csharp
public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
{
    logger.WriteLine(logMessage);
}
```

A **karakterlánc**mellett a paraméter lehet bájttömb, **CloudQueueMessage** objektum vagy ön által megadott POCO.

### <a name="poco-plain-old-clr-object-queue-messages"></a>POCO [(Plain Old CLR Object)](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)várólista-üzenetek
A következő példában a várólista-üzenet JSON-t tartalmaz egy **BlobInformation** objektumhoz, amely **blobname** tulajdonságot tartalmaz. Az SDK automatikusan deszerializálja az objektumot.

```csharp
public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
{
    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
}
```

Az SDK a [Newtonsoft.Json NuGet csomagot](https://www.nuget.org/packages/Newtonsoft.Json) használja az üzenetek szerializálására és deszerializálására. Ha olyan programban hoz létre várólistaüzeneteket, amelyek nem a WebJobs SDK-t használják, a következő példához hasonló kódot írhat egy POCO-várólista-üzenet létrehozásához, amelyet az SDK elemezhet.

```csharp
BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
logQueue.AddMessage(queueMessage);
```

### <a name="async-functions"></a>Aszinkron függvények
A következő aszinkron függvény [naplót ír az irányítópultra.](#how-to-write-logs)

```csharp
public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
{
    await logger.WriteLineAsync(logMessage);
}
```

Az Async függvények [egy érvénytelenítési jogkivonatot](https://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken)vehetnek igénybe, ahogy az a következő példában is látható, amely egy blobot másol. (A **queueTrigger** helyőrző magyarázatát a [Blobok](#how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message) szakaszban található.)

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

## <a name="types-the-queuetrigger-attribute-works-with"></a>Az QueueTrigger attribútum típusai
A **QueueTrigger** a következő típusokkal használható:

* **sztring**
* JSON-ként szerializált POCO-típus
* **bájt[]**
* **CloudQueueMessage üzenet**

## <a name="polling-algorithm"></a>Lekérdezési algoritmus
Az SDK egy véletlenszerű, exponenciális visszakeresési algoritmust valósít meg, hogy csökkentse az automatikus várólista-lekérdezés hatását a tárolási tranzakciós költségekre.  Ha egy üzenet található, az SDK két másodpercet vár, majd egy másik üzenetet keres; ha nem található üzenet, körülbelül négy másodpercet vár, mielőtt újra próbálkozna. A várólista-üzenet lehívására tett későbbi sikertelen próbálkozások után a várakozási idő tovább növekszik, amíg el nem éri a maximális várakozási időt, amely alapértelmezés szerint egy perc. [A maximális várakozási idő konfigurálható](#how-to-set-configuration-options).

## <a name="multiple-instances"></a>Több példány
Ha a webalkalmazás több példányon fut, egy folyamatos WebJobs fut minden gépen, és minden gép megvárja az eseményindítókat, és megpróbálja futtatni a függvényeket. Bizonyos esetekben ez azt eredményezheti, hogy egyes függvények kétszer dolgozzák fel ugyanazokat az adatokat, ezért a függvényeknek idempotensnek kell lenniük (úgy kell írni, hogy az azonos bemeneti adatokkal történő ismételt hívás a duplikált eredményeket ne eredményezze.  

## <a name="parallel-execution"></a>Párhuzamos végrehajtás
Ha több funkció figyeli a különböző várólistákat, az SDK párhuzamosan hívja őket, amikor üzeneteket fogadnak egyszerre.

Ugyanez igaz akkor is, ha egy várólistához több üzenet érkezik. Alapértelmezés szerint az SDK egyszerre 16 várólista-üzenetet kap, és végrehajtja a függvényt, amely párhuzamosan dolgozza fel őket. [A kötegmérete konfigurálható.](#how-to-set-configuration-options) Amikor a feldolgozott szám a kötegméret felére csökken, az SDK egy másik köteget kap, és megkezdi az üzenetek feldolgozását. Ezért a függvényenként feldolgozott egyidejű üzenetek maximális száma a kötegméret másfélszerese. Ez a korlát külön-külön vonatkozik minden olyan függvényre, amely nek **queueTrigger** attribútuma van. Ha nem szeretné, hogy az egyik várólistán fogadott üzenetek párhuzamos végrehajtása, állítsa a köteg méretét 1-re.

## <a name="get-queue-or-queue-message-metadata"></a>Várólista- vagy várólistaüzenet-metaadatok beküldése
A következő üzenettulajdonságokat kaphatja meg, ha paramétereket ad a metódus aláírásához:

* **DateTimeOffset** elévülési ideje
* **DateTimeOffset** beszúrástime
* **DateTimeOffset** nextVisibleTime
* **string** queueTrigger (üzenetszöveget tartalmaz)
* **karakterlánc-azonosító**
* **karakterlánc** popReceipt
* **int** dequeueCount

Ha közvetlenül az Azure storage API-val szeretne dolgozni, hozzáadhat egy **CloudStorageAccount** paramétert is.

A következő példa az összes metaadatot beírja egy INFO alkalmazásnaplóba. A példában a logMessage és a queueTrigger is tartalmazza a várólista-üzenet tartalmát.

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

Itt van egy minta napló írta a minta kódot:

        logMessage=Hello world!
        expirationTime=10/14/2014 10:31:04 PM +00:00
        insertionTime=10/7/2014 10:31:04 PM +00:00
        nextVisibleTime=10/7/2014 10:41:23 PM +00:00
        id=262e49cd-26d3-4303-ae88-33baf8796d91
        popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
        dequeueCount=1
        queue endpoint=https://contosoads.queue.core.windows.net/
        queueTrigger=Hello world!

## <a name="graceful-shutdown"></a>Kecses leállítás
A folyamatos WebJob ban futó függvény elfogadhatja az **CancellationToken** paramétert, amely lehetővé teszi, hogy az operációs rendszer értesítse a függvényt, amikor a WebJob leáll. Ezzel az értesítéssel biztosíthatja, hogy a függvény ne fejeződjön be váratlanul úgy, hogy az adatok inkonzisztens állapotban maradnak.

A következő példa bemutatja, hogyan ellenőrizheti a közelgő WebJob megszűnését egy függvényben.

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

**Megjegyzés:** Előfordulhat, hogy az irányítópult nem megfelelően jeleníti meg a leállított függvények állapotát és kimenetét.

További információ: [WebJobs Graceful Shutdown](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR).   

## <a name="how-to-create-a-queue-message-while-processing-a-queue-message"></a>Várólista-üzenet létrehozása üzenet létrehozása üzenetsor-üzenet feldolgozása közben
Új várólista-üzenetet létrehozó függvény írásához használja a **Várólista** attribútumot. A **QueueTrigger hez**hasonlóan a várólista nevét karakterláncként adja meg, vagy [dinamikusan is beállíthatja a várólista nevét.](#how-to-set-configuration-options)

### <a name="string-queue-messages"></a>Sorlistaüzenetek karakterlánc-üzenetei
A következő nem aszinkron kódminta létrehoz egy új várólista-üzenetet a "outputqueue" nevű várólistában, amelynek tartalma megegyezik a "inputqueue" nevű várólistában fogadott várólistaüzenettartalmával. (Az async függvények hez használja **az\<IAsyncCollector T>** ahogy az a szakasz későbbi részében látható.)

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    [Queue("outputqueue")] out string outputQueueMessage )
{
    outputQueueMessage = queueMessage;
}
```

### <a name="poco-plain-old-clr-object-queue-messages"></a>POCO [(Plain Old CLR Object)](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)várólista-üzenetek
Ha olyan várólista-üzenetet szeretne létrehozni, amely nem karakterláncot, hanem POCO-t tartalmaz, adja át a POCO-típust kimeneti paraméterként a **Várólista** attribútumkonstruktornak.

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
{
    blobInfoOutput = blobInfoInput;
}
```

Az SDK automatikusan szerializálja az objektumot a JSON-ra. A várólista-üzenet mindig létrejön, még akkor is, ha az objektum null értékű.

### <a name="create-multiple-messages-or-in-async-functions"></a>Több üzenet vagy aszinkron függvény ek létrehozása
Több üzenet létrehozásához hozza létre az **ICollector\<T>** vagy az **\<IAsyncCollector T>** kimeneti várólista paramétertípusát, ahogy az a következő példában látható.

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

Minden várólista-üzenet azonnal létrejön, amikor az **Add** metódust hívják.

### <a name="types-that-the-queue-attribute-works-with"></a>Azon típusok, amelyekkel a Queue attribútum működik
**A Várólista** attribútum ot a következő paramétertípusokon használhatja:

* **kimenő karakterlánc** (várólista-üzenetet hoz létre, ha a paraméter értéke nem null értékű a függvény végekor)
* **out byte[]** (úgy működik, mint **a karakterlánc)**
* **out CloudQueueMessage** (úgy működik, mint **a karakterlánc)**
* **ki POCO** (egy szerializálható típus, létrehoz egy üzenetet null objektummal, ha a paraméter null értékű, amikor a függvény véget ér)
* **ICollector (ikoló**
* **IAsyncCollector**
* **CloudQueue** (az Üzenetek manuális létrehozásához az Azure Storage API-val közvetlenül)

### <a name="use-webjobs-sdk-attributes-in-the-body-of-a-function"></a>WebJobs SDK-attribútumok használata egy függvény törzsében
Ha a WebJobs SDK attribútum (például **Queue,** **Blob** **)** használata előtt el kell végeznie a munkát a függvényben, használhatja az **IBinder** felületet.

A következő példa egy bemeneti várólista-üzenetet vesz fel, és létrehoz egy új üzenetet ugyanazzal a tartalommal egy kimeneti várólistában. A kimeneti várólista nevét a függvény törzsében lévő kód állítja be.

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

Az **IBinder** felület a **Table** és a **Blob** attribútumokkal is használható.

## <a name="how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message"></a>Blobok és táblák olvasása és írása várólistaüzenet feldolgozása közben
A **Blob** és **a Table** attribútumok lehetővé teszik a blobok és táblák olvasását és írását. Az ebben a szakaszban található minták blobokra vonatkoznak. A blobok létrehozásakor vagy frissítésekor a folyamatok indítását bemutató kódmintákért olvassa el [az Azure blobstorage használata a WebJobs SDK-val című témakört.](https://github.com/Azure/azure-webjobs-sdk/wiki)
<!-- , and for code samples that read and write tables, see [How to use Azure table storage with the WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-storage-tables-how-to.md). -->

### <a name="string-queue-messages-triggering-blob-operations"></a>Blob-műveleteket kiváltó karakterlánc-várólista-üzenetek
Egy karakterláncot tartalmazó várólista-üzenet esetén az **queueTrigger** olyan helyőrző, amelyet a **Blob** attribútum **blobPath-paraméterében** használhat, amely az üzenet tartalmát tartalmazza.

A következő példa **streamobjektumokat** használ a blobok olvasásához és írásához. A várólista-üzenet a textblobok tárolóban található blob neve. A blob egy példányát az "-új" hozzáfűzve a név ugyanabban a tárolóban jön létre.

```csharp
public static void ProcessQueueMessage(
    [QueueTrigger("blobcopyqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

A **Blob** attribútum konstruktor vesz egy **blobPath** paraméter, amely meghatározza a tároló és a blob nevét. A helyőrzőről további információt az [Azure blob storage használata a WebJobs SDK-val című témakörben talál.](https://github.com/Azure/azure-webjobs-sdk/wiki)

Amikor az attribútum egy **Stream** objektumot díszít, egy másik konstruktorparaméter a **FileAccess** módot olvasási, írási vagy írási/írási módként adja meg.

A következő példa egy **CloudBlockBlob** objektumot használ egy blob törléséhez. A várólista-üzenet a blob neve.

```csharp
public static void DeleteBlob(
    [QueueTrigger("deleteblobqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
{
    blobToDelete.Delete();
}
```

### <a name="poco-plain-old-clr-object-queue-messages"></a>POCO [(Plain Old CLR Object)](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)várólista-üzenetek
A várólista-üzenetben JSON-ként tárolt POCO-k esetében olyan helyőrzőket használhat, amelyek az objektum tulajdonságait a **Queue** attribútum **blobPath-paraméterében** nevezik el. Helyőrzőként a várólista metaadat-tulajdonságneveit is használhatja. Lásd: [Várólista- vagy üzenetüzenet-metaadatok beküldése](#get-queue-or-queue-message-metadata).

A következő példa egy blobot másol egy másik kiterjesztéssel rendelkező új blobba. A várólista-üzenet egy **BlobInformation** objektum, amely tartalmazza **a BlobName** és **a BlobNameWithoutExtension** tulajdonságokat. A tulajdonságnevek helyőrzőkként használatosak **Blob** a Blob-attribútumok blobelérési útvonalában.

```csharp
public static void CopyBlobPOCO(
    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

Az SDK a [Newtonsoft.Json NuGet csomagot](https://www.nuget.org/packages/Newtonsoft.Json) használja az üzenetek szerializálására és deszerializálására. Ha olyan programban hoz létre várólistaüzeneteket, amelyek nem a WebJobs SDK-t használják, a következő példához hasonló kódot írhat egy POCO-várólista-üzenet létrehozásához, amelyet az SDK elemezhet.

```csharp
BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
logQueue.AddMessage(queueMessage);
```

Ha a blob objektumhoz való kötése előtt el kell végeznie a munkát a függvényben, használhatja az attribútumot a függvény törzsében, ahogy [az a Függvény törzsében látható: A WebJobs SDK-attribútumok használata](#use-webjobs-sdk-attributes-in-the-body-of-a-function).

### <a name="types-you-can-use-the-blob-attribute-with"></a>A Blob attribútum ot használni lehet
A **Blob** attribútum a következő típusokkal használható:

* **Adatfolyam** (olvasás vagy írás, a FileAccess konstruktor paraméterrel megadva)
* **TextReader (Szövegolvasó)**
* **TextWriter**
* **karakterlánc** (olvasás)
* **kimenő karakterlánc** (írás; blobot csak akkor hoz létre, ha a karakterlánc-paraméter nem null értékű, amikor a függvény visszatér)
* POCO (olvasás)
* ki POCO (írás; mindig létrehoz egy blobot, null objektumként hoz létre, ha a POCO paraméter null értékű, amikor a függvény visszatér)
* **CloudBlobStream** (írás)
* **ICloudBlob** (olvasás vagy írás)
* **CloudBlockBlob** (olvasás vagy írás)
* **CloudPageBlob** (olvasás vagy írás)

## <a name="how-to-handle-poison-messages"></a>Hogyan kezeljük a méregüzeneteket?
Azokat az üzeneteket, amelyek tartalma függvényt eredményez, *mérgező üzeneteknek*nevezzük. Ha a függvény meghibásodik, a várólista-üzenet nem törlődik, és végül újra felveszi, ami a ciklus megismétlődését okozza. Az SDK korlátozott számú ismétlés után automatikusan megszakíthatja a ciklust, vagy manuálisan is megteheti.

### <a name="automatic-poison-message-handling"></a>Automatikus méregüzenet-kezelés
Az SDK legfeljebb 5 alkalommal hív meg egy függvényt a várólista-üzenet feldolgozásához. Ha az ötödik próbálkozás sikertelen, az üzenet egy méregvárólistába kerül. A [konfigurációs beállítások megadása](#how-to-set-configuration-options)című témakörben láthatja, hogyan állíthatja be az újrapróbálkozások maximális számát.

A méregvárólista neve *{originalqueuename}*-poison. Írhat egy függvényt a méregvárólistából érkező üzenetek feldolgozásához, ha naplózza őket, vagy értesítést küld arról, hogy manuális figyelemre van szükség.

A következő példában a **CopyBlob** függvény sikertelen lesz, ha egy várólista-üzenet tartalmazza egy blob nevét, amely nem létezik. Ebben az esetben az üzenet a copyblobqueue várólistából a copyblobqueue-poison várólistába kerül. A **ProcessPoisonMessage** ezután naplózza a méreg üzenetet.

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

A következő ábrán a konzol kimenete ezekből a függvényekből, amikor egy méreg üzenet feldolgozása.

![Konzol kimenet a mérgező üzenetek kezeléséhez](./media/vs-storage-webjobs-getting-started-queues/poison.png)

### <a name="manual-poison-message-handling"></a>Kézi méregüzenet-kezelés
A függvényhez **dequeueCount** nevű **int** paraméter hozzáadásával lekaphatja, hogy hányszor vették fel az üzenetet feldolgozásra. Ezután ellenőrizheti a dequeue száma a függvénykódban, és végrehajthatja a saját méregüzenet-kezelést, ha a szám túllép egy küszöbértéket, ahogy az a következő példában látható.

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

## <a name="how-to-set-configuration-options"></a>A konfigurációs beállítások megadása
A **JobHostConfiguration** típussal a következő konfigurációs beállításokat állíthatja be:

* Állítsa be az SDK-kapcsolati karakterláncokat a kódban.
* **Konfigurálja a QueueTrigger** beállításait, például a várólista maximális számát.
* Várólistanevek beszereznie a konfigurációból.

### <a name="set-sdk-connection-strings-in-code"></a>SDK-kapcsolati karakterláncok beállítása a kódban
Az SDK-kapcsolati karakterláncok kódban történő beállítása lehetővé teszi, hogy saját kapcsolati karakterláncneveket használjon konfigurációs fájlokban vagy környezeti változókban, ahogy az a következő példában látható.

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

### <a name="configure-queuetrigger--settings"></a>A Várólista-trigger beállításainak konfigurálása
A várólistaüzenetek feldolgozására a következő beállításokat állíthatja be:

* A párhuzamosan végrehajtandó várólistán lévő üzenetek maximális száma (alapértelmezett érték: 16).
* Az újrapróbálkozások maximális száma, mielőtt a rendszer elküldi a várólista-üzenetet a mérgező várólistába (alapértelmezett érték 5).
* A maximális várakozási idő a lekérdezés előtt, ha egy várólista üres (az alapértelmezett érték 1 perc).

A következő példa bemutatja, hogyan konfigurálhatók ezek a beállítások:

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

### <a name="set-values-for-webjobs-sdk-constructor-parameters-in-code"></a>A WebJobs SDK konstruktorparamétereinek értékei a kódban
Néha meg szeretne adni egy várólista nevét, egy blob nevét vagy tárolóját, vagy egy táblanevet a kódban, nem pedig a kódba. Előfordulhat például, hogy meg szeretné adni az **QueueTrigger** várólistanevét egy konfigurációs fájlban vagy környezeti változóban.

Ezt úgy teheti meg, hogy egy **NameResolver** objektumot a **JobHostConfiguration** típusnak ad át. A speciális helyőrzőket százalékkal (%) veszi körül. a WebJobs SDK-attribútumkonstruktor paramétereiben lévő jelek, a **NameResolver-kód** pedig a helyőrzők helyett használandó tényleges értékeket adja meg.

Tegyük fel például, hogy a tesztkörnyezetben egy logqueuetest nevű várólistát és egy logqueueprod nevű várólistát szeretne használni éles környezetben. A kódolt várólista név helyett meg szeretné adni egy bejegyzés nevét az **appSettings** gyűjteményben, amelynek tényleges várólista neve lenne. Ha az **appSettings** kulcs logqueue, a függvény a következő példához hasonlólehet.

```csharp
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

A **NameResolver** osztály ezután lekaphatja a várólista nevét az **appSettings-ból,** ahogy az a következő példában látható:

```csharp
public class QueueNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

Adja át a **NameResolver** osztályt a **JobHost** objektumnak, ahogy az a következő példában látható.

```csharp
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new QueueNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

**Megjegyzés:** A várólista-, tábla- és blobnevek feloldása minden alkalommal, amikor egy függvényt hív, de a blobtároló-nevek csak az alkalmazás indításakor kerülnek feloldásra. A blobtároló neve nem módosítható, amíg a feladat fut.

## <a name="how-to-trigger-a-function-manually"></a>Funkció manuális aktiválása
Függvény manuális indításához használja a **Hívás** vagy **callAsync** metódust a **JobHost** objektumon és a **NoAutomaticTrigger** attribútumot a függvényen, ahogy az a következő példában látható.

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
Az irányítópult két helyen jeleníti meg a naplókat: a WebJob lapját és egy adott WebJob-meghívás lapját.

![Naplók a WebJob lapon](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

![Naplók a függvény meghívási lapján](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

A függvényben vagy a **Main()** metódusban megnevezett konzolmetódusok kimenete a WebJob Irányítópult lapján jelenik meg, nem pedig egy adott metódus meghívásának lapján. A metódus aláírási paraméteréből kapott Szövegíró objektum kimenete megjelenik az Irányítópult lapon egy metódus meghívásához.

A konzol kimenete nem kapcsolható egy adott metódus meghívásához, mert a konzol egyszálas, miközben előfordulhat, hogy számos feladatfunkció fut egyszerre. Ezért az SDK biztosítja az egyes függvények meghívása saját egyedi naplóíró objektum.

Az [alkalmazásnyomkövetési naplók](../app-service/troubleshoot-dotnet-visual-studio.md#logsoverview)írásához használja **a Console.Out** (INFO jelölésű naplókat) és **a Console.Error** (hibaként megjelölt naplókat hoz létre). Egy másik lehetőség a [Nyomkövetés vagy a TraceSource](https://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)használata, amely az Információ és a Hiba mellett részletes, figyelmeztetési és kritikus szinteket is biztosít. Az alkalmazások nyomkövetési naplói az Azure-webalkalmazás konfigurálásától függően megjelennek a webalkalmazás naplófájljaiban, az Azure-táblákban vagy az Azure-blobokban. A konzol összes kimenetéhez, a legutóbbi 100 alkalmazásnaplók is megjelennek a WebJob Irányítópult lapján, nem pedig egy függvénymeghívás lapján.

A konzolkimenet csak akkor jelenik meg az irányítópulton, ha a program egy Azure WebJob ban fut, nem akkor, ha a program helyileg vagy más környezetben fut.

A naplózás letiltásához az Irányítópult kapcsolati karakterláncának null értékre állításával. További információt a [Konfigurációs beállítások megadása című](#how-to-set-configuration-options)témakörben talál.

A következő példa a naplók írásának számos módját mutatja be:

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

A WebJobs SDK irányítópulton a **TextWriter** objektum kimenete megjelenik, amikor egy adott függvénymeghíváshoz a lapra lép, és a Kimenet váltása lehetőséget **választja:**

![Meghívási hivatkozás](./media/vs-storage-webjobs-getting-started-queues/dashboardinvocations.png)

![Naplók a függvény meghívási lapján](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

A WebJobs SDK irányítópultján a konzol kimenetének legutóbbi 100 sora jelenik meg, amikor a WebJob lapjára lép (nem a függvény meghívásához), és válassza a **Kimenet váltása**lehetőséget.

![Kimenet be- és be- és bekapcsolása](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

Folyamatos webfeladatesetén az alkalmazásnaplók a webalkalmazás fájlrendszerében a /data/jobs/continuous/*{webjobname}*/job_log.txt fájlrendszerben jelennek meg.

        [09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
        [09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
        [09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

Egy Azure blobban az alkalmazásnaplók így néznek ki: 2014-09-26T21:01:13,Információ,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world!, 2014-09-26T21:01:13,Hiba,contosoadsnew,491e54, 635473620738373502,0,17404,19,Console.Error - Hello world!, 2014-09-26T21:01:13,Információ,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello world!,

Egy Azure-táblában a **Console.Out** és **a Console.Error** naplók a következőkre néznek ki:

![Információs napló tábla](./media/vs-storage-webjobs-getting-started-queues/tableinfo.png)

![Hiba naplótábla](./media/vs-storage-webjobs-getting-started-queues/tableerror.png)

## <a name="next-steps"></a>További lépések
Ez a cikk olyan kódmintákat tartalmaz, amelyek bemutatják, hogyan kezelhetők az Azure-várólisták gyakori forgatókönyvei. Az Azure WebJobs és a WebJobs SDK használatáról az [Azure WebJobs dokumentációs erőforrásaiban](https://go.microsoft.com/fwlink/?linkid=390226)olvashat bővebben.

