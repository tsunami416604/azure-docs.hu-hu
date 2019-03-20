---
title: Bevezetés a queue storage és a Visual Studio csatlakoztatott szolgáltatásainak (a Webjobs projekteket) |} A Microsoft Docs
description: Ismerkedés az Azure Queue storage használata a webjobs-feladat projektben egy tárfiókot, a Visual Studio használatával való csatlakozást követően csatlakoztatott szolgáltatások.
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
ms.openlocfilehash: f6f1a3a7f0a406e1dbb40f4bfc6a358da7ac68fa
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57999549"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-webjob-projects"></a>Ismerkedés az Azure Queue storage és a Visual Studio csatlakoztatott szolgáltatásainak (webjobs-feladat projektek)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Áttekintés
Ez a cikk azt ismerteti, hogyan használatának első lépései az Azure Queue storage a projekt a Visual Studio Azure webjobs-feladat létrehozása vagy a Visual Studio használatával Azure storage-fiók hivatkozott után **csatlakoztatott szolgáltatás hozzáadása** párbeszédpanel bezárásához. Hozzáadásakor egy tárfiókot, a webjobs-feladat projekt a Visual Studio használatával **csatlakoztatott szolgáltatás hozzáadása** párbeszédpanelen a megfelelő Azure Storage NuGet-csomagok telepítése, a megfelelő .NET hivatkozások hozzáadódnak a projektet, és a storage-fiókhoz tartozó kapcsolati karakterláncok frissülnek az App.config fájlban.  

Ez a cikk a C#-mintakódot biztosít, amelyek bemutatják az Azure WebJobs SDK-verzió használatához az Azure Queue storage szolgáltatással 1.x.

Az Azure Queue Storage szolgáltatás üzenetek nagy számban történő tárolására szolgál, amelyek HTTP- vagy HTTPS-kapcsolattal, hitelesített hívásokon keresztül a világon bárhonnan elérhetők. Egyetlen üzenetsor akár 64 KB méretű is lehet, és a tárfiók maximális kapacitásán belül több millió üzenetet tartalmazhat. Lásd: [Ismerkedés az Azure Queue Storage .NET-tel](../storage/queues/storage-dotnet-how-to-use-queues.md) további információt. Az ASP.NET kapcsolatos további információkért lásd: [ASP.NET](https://www.asp.net).

## <a name="how-to-trigger-a-function-when-a-queue-message-is-received"></a>Hogyan aktiválja a függvényt, üzenetsori üzenet fogadásakor.
Függvény, amely a WebJobs SDK meghívja az üzenetsori üzenet fogadásakor. használja a **QueueTrigger** attribútum. Az attribútum konstruktorának paramétereként egy karakterlánc-paramétert, amely meghatározza a lekérdezni kívánt üzenetsor neve. Az üzenetsor neve dinamikusan beállítása megtekintéséhez tekintse meg [konfigurációs beállításainak megadása](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Karakterlánc-üzenetsorbeli üzenetek esetén
A következő példában a várólistát tartalmaz egy karakterlánc üzenetet, így **QueueTrigger** egy karakterlánc-paramétert nevű alkalmazott **logMessage** tartalmazó az üzenetsorban található üzenet tartalma. A függvény [log üzenetet ír az irányítópult](#how-to-write-logs).

```csharp
public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
{
    logger.WriteLine(logMessage);
}
```

Mellett **karakterlánc**, az lehet, hogy egy bájttömböt egy **CloudQueueMessage** objektumot, vagy egy Ön által meghatározott POCO.

### <a name="poco-plain-old-clr-objecthttpsenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO [(egyszerű régi CLR-beli objektum](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) várólista-üzenetek
A következő példában az üzenetsorban található üzenet a JSON-t tartalmaz egy **BlobInformation** objektum, amely tartalmaz egy **BlobName** tulajdonság. Az SDK automatikusan deserializes az objektumot.

```csharp
public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
{
    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
}
```

Az SDK-t használ a [Newtonsoft.Json NuGet-csomag](https://www.nuget.org/packages/Newtonsoft.Json) szerializálható és deszerializálható üzeneteket. Ha üzenetsorbeli üzenetek számára hoz létre egy programot, amely nem használja a WebJobs SDK-val, írhat kódot, amely az SDK szolgáltatás elemezni tudja POCO üzenetsori üzenet létrehozására, az alábbi példához hasonlóan.

```csharp
BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
logQueue.AddMessage(queueMessage);
```

### <a name="async-functions"></a>Az aszinkron funkciók
A következő aszinkron függvény [ír az Irányítópulton egy log](#how-to-write-logs).

```csharp
public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
{
    await logger.WriteLineAsync(logMessage);
}
```

Az aszinkron funkciókat is igénybe vehet egy [megszakítás jogkivonat](https://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken), ahogyan az a következő példának, amely egy blobot másol. (Ismertetése a **queueTrigger** helyőrző, tekintse meg a [Blobok](#how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message) szakaszban.)

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

## <a name="types-the-queuetrigger-attribute-works-with"></a>Együttműködik a QueueTrigger attribútum típusa
Használhat **QueueTrigger** alábbi típusait:

* **string**
* Egy POCO típus szerializált JSON-fájlként
* **byte[]**
* **CloudQueueMessage**

## <a name="polling-algorithm"></a>Lekérdezési algoritmus
Az SDK-t implementál egy véletlenszerű exponenciális visszatartási algoritmus üresjárati várólista tárolási tranzakciós költségeket a lekérdezési hatásának csökkentése érdekében.  Amikor egy üzenet található, az SDK két másodpercet vár, és csak azután ellenőrzi egy másik üzenet; Ha található nincs üzenet arra vár körülbelül négy másodpercig az újrapróbálkozás előtt. Későbbi a sikertelen kísérletek üzenetsori üzenetek beolvasásához, miután a várakozási idő továbbra is növelje addig a maximális várakozási idő, alapértelmezett értéke egy perc alatt. [A maximális várakozási időtartam](#how-to-set-configuration-options).

## <a name="multiple-instances"></a>Több példány
A webes alkalmazás több példánya fut, ha egy folyamatos webjobs-feladatok futtatása az összes olyan számítógépen, és minden gép fog várja meg, eseményindítók és a functions futtatására tett kísérlet. Bizonyos esetekben ez bizonyos funkciók, ugyanazokat az adatokat kétszer feldolgozása eredményezhet ezért funkciók idempotensnek kell lennie (írt, hogy ismételten hívása az ugyanazon bemeneti adatokkal nem eredményez az ismétlődések).  

## <a name="parallel-execution"></a>Párhuzamos végrehajtás
Ha több funkciók külön üzenetsorokra figyel, az SDK fogja hívni őket párhuzamosan fogadása egyszerre.

Ugyanez igaz akkor, ha több üzenet érkezik egy egyetlen sor. Alapértelmezés szerint az SDK 16 üzenetsorbeli üzenetek kötegelt lekérdezi egy időben, és végrehajtja a függvényt, amely párhuzamosan dolgozza fel. [A Köteg mérete nem konfigurálható](#how-to-set-configuration-options). A feldolgozott számát a Köteg mérete felének lekérdezi, amikor az SDK lekérdezi egy másik köteg, és elindítja a feldolgozási ezeket az üzeneteket. Ezért a függvény feldolgozott egyidejű üzenetek maximális száma esetében egy másfélszerese a Köteg mérete. Ezt a korlátot vonatkozik külön-külön mindegyik függvény, amely rendelkezik egy **QueueTrigger** attribútum. Ha nem szeretné egy üzenetsorban fogadott üzenetek párhuzamos futtatáshoz, állítsa a köteg méretének 1.

## <a name="get-queue-or-queue-message-metadata"></a>Üzenetsor vagy üzenetsor üzenetet metaadatainak lekérése
Az alábbi üzenet tulajdonságait kaphat a metódus aláírásához paraméterek hozzáadásával:

* **DateTimeOffset** expirationTime
* **DateTimeOffset** insertionTime
* **DateTimeOffset** nextVisibleTime
* **karakterlánc** queueTrigger (tartalmazza a üzenet szövege)
* **karakterlánc** azonosítója
* **karakterlánc** popReceipt
* **int** dequeueCount

Ha az ügyfelek közvetlenül az Azure storage API-t szeretne, hozzáadhatja egy **CloudStorageAccount** paraméter.

Az alábbi példa egy INFO napló összes ezeket a metaadatokat ír. A példában logMessage és queueTrigger is tartalmaz az üzenetsorban található üzenet tartalma.

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

A következő példakód által írt mintanapló:

        logMessage=Hello world!
        expirationTime=10/14/2014 10:31:04 PM +00:00
        insertionTime=10/7/2014 10:31:04 PM +00:00
        nextVisibleTime=10/7/2014 10:41:23 PM +00:00
        id=262e49cd-26d3-4303-ae88-33baf8796d91
        popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
        dequeueCount=1
        queue endpoint=https://contosoads.queue.core.windows.net/
        queueTrigger=Hello world!

## <a name="graceful-shutdown"></a>Biztonságos leállításának
Elfogadhatja a függvény, amely egy folyamatos webjobs-feladatot futtat egy **CancellationToken** paramétert, amely lehetővé teszi, hogy az operációs rendszer, a függvény értesítése, ha a webjobs-feladat arra készül, hogy állítható le. Használhatja ezt az értesítést, hogy a funkció váratlanul leáll nem úgy, hogy az adatok inkonzisztens állapotban hagyja.

Az alábbi példa bemutatja, hogyan ellenőrizheti a függvény közelgő webjobs-feladat leállítására.

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

**Megjegyzés:** Az irányítópult nem megfelelően jelenik meg a állapota és kimenete függvények, amelyek le lett állítva.

További információkért lásd: [WebJobs leállításának](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR).   

## <a name="how-to-create-a-queue-message-while-processing-a-queue-message"></a>Hogyan hozhat létre üzenetsori üzenet üzenetsori üzenet feldolgozása közben
Függvény, amely létrehoz egy új üzenetsor üzenetet ír, használja a **várólista** attribútum. Például **QueueTrigger**, az üzenetsor neve karakterláncként adja át, vagy Ön is [dinamikusan állítsa be az üzenetsor neve](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Karakterlánc-üzenetsorbeli üzenetek esetén
A következő az aszinkron kódminta az üzenetsorban, az üzenetsorban található üzenet a várólistában "inputqueue" nevű kapott az azonos tartalmú "outputqueue" nevű hoz létre egy új üzenetsor üzenetet. (Aszinkron funkciók használata **IAsyncCollector<T>**  , ahogyan az ebben a szakaszban később.)

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    [Queue("outputqueue")] out string outputQueueMessage )
{
    outputQueueMessage = queueMessage;
}
```

### <a name="poco-plain-old-clr-objecthttpsenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO [(egyszerű régi CLR-beli objektum](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) várólista-üzenetek
Amely tartalmazza a karakterláncot, hanem egy POCO üzenetsori üzenet létrehozására, át kell adnia a POCO típus a kimeneti paraméterként a **várólista** attribútumok konstruktorában.

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
{
    blobInfoOutput = blobInfoInput;
}
```

Az SDK automatikusan szerializálja a JSON-objektum. Egy üzenetsor-üzenetet mindig létrejön, még akkor is, ha az objektum null értékű.

### <a name="create-multiple-messages-or-in-async-functions"></a>Hozzon létre több üzenetet, vagy aszinkron funkciók
Hozzon létre több üzeneteket, győződjön meg arról, a kimeneti várólista paramétertípusa **ICollector<T>**  vagy **IAsyncCollector<T>**, az alábbi példában látható módon.

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

Azonnal jön létre minden egyes üzenetsor során a **Hozzáadás** módszert hívja meg.

### <a name="types-that-the-queue-attribute-works-with"></a>Az üzenetsor attribútum-mel együttműködő típusok
Használhatja a **várólista** attribútum a következő paraméter típusa:

* **karakterlánc ki** (hoz létre üzenetsori üzenet, ha a paraméter értéke nem null értékű amikor befejeződik a függvény)
* **ki byte []** (működik, mint az **karakterlánc**)
* **ki CloudQueueMessage** (működik, mint az **karakterlánc**)
* **ki POCO** (egy szerializálható típust hoz létre egy üzenetet objektem s hodnotou null, ha a paraméter null értékű, amikor befejeződik a függvény)
* **ICollector**
* **IAsyncCollector**
* **CloudQueue** (az üzeneteket, manuálisan használatával közvetlenül az Azure Storage API létrehozása)

### <a name="use-webjobs-sdk-attributes-in-the-body-of-a-function"></a>A WebJobs SDK attribútumok függvény törzsében.
Ha néhány dolgot a függvényben a WebJobs SDK-val attribútum például használata előtt végrehajtandó **várólista**, **Blob**, vagy **tábla**, használhatja a **IBinder**felületet.

Az alábbi példa egy bemeneti üzenetsor vesz igénybe, és egy új üzenetet hoz létre ugyanahhoz a tartalomhoz egy kimeneti várólista. A kimeneti várólista neve kódot a függvény törzsében állítja be.

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

A **IBinder** felület is használható a **tábla** és **Blob** attribútumok.

## <a name="how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message"></a>Hogyan lehet olvasni és írni a blobok és táblák üzenetsori üzenet feldolgozása közben
A **Blob** és **tábla** attribútumok engedélyezése, hogy a blobok és táblák olvasása és írása. Ebben a szakaszban a minták blobok vonatkoznak. Kód a minták azt mutatják, hogy miként indítható folyamatokat, ha a blobok létrehozott vagy frissített, lásd: [az Azure blob storage használata a WebJobs SDK-val](https://github.com/Azure/azure-webjobs-sdk/wiki).
<!-- , and for code samples that read and write tables, see [How to use Azure table storage with the WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-storage-tables-how-to.md). -->

### <a name="string-queue-messages-triggering-blob-operations"></a>Karakterlánc-üzenetsorbeli üzenetek blob műveletek indítása
Egy üzenetsor üzenet, amely tartalmazza a karakterláncot **queueTrigger** használhatja a helyőrző a **Blob** attribútum **blobPath** paraméter, amely tartalmazza a tartalmát a az üzenet.

Az alábbi példában **Stream** objektumok olvasása és írása a blobokat. Az üzenetsorban található üzenet a textblobs tárolóban található blobok neve. A blob-egy példányát "– új" hozzáfűzi neve jön létre ugyanabban a tárolóban.

```csharp
public static void ProcessQueueMessage(
    [QueueTrigger("blobcopyqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

A **Blob** attribútum konstruktorban vesz igénybe egy **blobPath** paraméter, amely meghatározza a tároló és blobnév nevét. A helyőrző kapcsolatos további információkért lásd: [az Azure blob storage használata a WebJobs SDK-val](https://github.com/Azure/azure-webjobs-sdk/wiki).

Ha az attribútum decorates egy **Stream** objektumot, egy másik konstruktor paraméter adja meg a **FileAccess** olvasási, írási vagy olvasási/írási módban.

Az alábbi példában egy **CloudBlockBlob** egy blobot a törölni kívánt objektum. Az üzenetsorban található üzenet a blob neve.

```csharp
public static void DeleteBlob(
    [QueueTrigger("deleteblobqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
{
    blobToDelete.Delete();
}
```

### <a name="poco-plain-old-clr-objecthttpsenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO [(egyszerű régi CLR-beli objektum](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) várólista-üzenetek
Egy POCO az üzenetsorban található üzenet JSON-fájlként tárolja, használhatja ezt a nevet az objektum tulajdonságainak helyőrzőket a **várólista** attribútum **blobPath** paraméter. Üzenetsor metaadatai a tulajdonságnevek helyőrzőket is használhatja. Lásd: [várólista vagy üzenetsor üzenetet metaadatok lekérése](#get-queue-or-queue-message-metadata).

Az alábbi példában egy blobot egy másik kiterjesztésű új blob másolja. Az üzenetsorban található üzenet van egy **BlobInformation** , amely tartalmazza az objektum **BlobName** és **BlobNameWithoutExtension** tulajdonságait. A tulajdonságnevek helyőrzőként a blob elérési útja a **Blob** attribútumok.

```csharp
public static void CopyBlobPOCO(
    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

Az SDK-t használ a [Newtonsoft.Json NuGet-csomag](https://www.nuget.org/packages/Newtonsoft.Json) szerializálható és deszerializálható üzeneteket. Ha üzenetsorbeli üzenetek számára hoz létre egy programot, amely nem használja a WebJobs SDK-val, írhat kódot, amely az SDK szolgáltatás elemezni tudja POCO üzenetsori üzenet létrehozására, az alábbi példához hasonlóan.

```csharp
BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
logQueue.AddMessage(queueMessage);
```

Ha ehhez néhány dolgot a függvényben előtt kötés egy blob egy objektumhoz van szüksége, az attribútum a függvény törzsében használhatja, ahogyan az [használata a WebJobs SDK-val attribútumok függvény törzsében](#use-webjobs-sdk-attributes-in-the-body-of-a-function).

### <a name="types-you-can-use-the-blob-attribute-with"></a>A Blob attribútumot is használhatja típusok
A **Blob** attribútum is használható a következők:

* **Stream** (olvasás vagy írás, a FileAccess konstruktorparamétert használatával)
* **TextReader**
* **TextWriter**
* **karakterlánc** (olvasás)
* **karakterlánc ki** (írás, egy blobot hoz létre, csak akkor, ha a karakterlánc-paraméter esetén nem null értékű függvény)
* POCO (read)
* POCO meg (írási; mindig létrehoz egy blobot, NULL értékű objektum hoz létre, ha POCO paraméter null értékű, ha a visszatérési érték)
* **CloudBlobStream** (write)
* **ICloudBlob** (olvasása vagy írása)
* **CloudBlockBlob** (olvasása vagy írása)
* **CloudPageBlob** (olvasása vagy írása)

## <a name="how-to-handle-poison-messages"></a>Ártalmas üzenetek kezelése
Üzenetek, amelynek tartalma következtében sikertelen függvény nevezzük *ártalmas üzeneteket*. Ha a függvény nem sikerül, az üzenetsorban található üzenet nincs törölve, és végül a mértékének ismét meg kell ismételni a ciklus miatt. Az SDK automatikusan megszakíthatja a ciklus ismétléseinek korlátozott számú után, vagy manuálisan is megteheti.

### <a name="automatic-poison-message-handling"></a>Automatikus ártalmas üzenetek kezelése
Az SDK meghívja függvény legfeljebb 5-ször üzenetsori üzenetek feldolgozásához. Az ötödik próbálkozzon nem sikerül, ha az üzenet ártalmas várólistára került. Láthatja, hogy a próbálkozások maximális számának konfigurálása [konfigurációs beállításainak megadása](#how-to-set-configuration-options).

Az ártalmas üzenetsor neve *{originalqueuename}*-ártalmas. Írhat a naplózásukhoz vagy egy értesítést küld, manuális beavatkozást ártalmas várólistában lévő üzenetek feldolgozásával függvény van szükség.

Az alábbi példában a **CopyBlob** függvény sikertelen lesz, amikor egy üzenetsor-üzenetet, amely nem létezik blob nevét tartalmazza. Ha ez történik, az üzenet átkerül az copyblobqueue üzenetsorból a copyblobqueue-poison várólista. A **ProcessPoisonMessage** majd bejelentkezik az ártalmas üzenetek.

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

A következő ábrán ezek a függvények konzolkimenete ártalmas üzenetek feldolgozása során.

![Konzol kimenete az ártalmas üzenetek kezelése](./media/vs-storage-webjobs-getting-started-queues/poison.png)

### <a name="manual-poison-message-handling"></a>Manuális ártalmas üzenetek kezelése
Feldolgozási hányszor üzenet lett kiválasztva beszerezheti a hozzáadásával egy **int** nevű paraméter **dequeueCount** a függvényt. Ezután ellenőrizze a függvény kódját eltávolítása onnan száma, és hajtsa végre a saját ártalmas üzenetek kezelése, ha a szám meghaladja a küszöbértéket, az alábbi példában látható módon.

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

## <a name="how-to-set-configuration-options"></a>Konfigurációs beállításainak megadása
Használhatja a **JobHostConfiguration** írja be a következő konfigurációs beállításainak megadása:

* Az SDK-kapcsolati karakterláncok beállítása a kódban.
* Konfigurálása **QueueTrigger** beállítások, például a maximális darabszám eltávolítása a sorból.
* Üzenetsornevek első konfigurációjából.

### <a name="set-sdk-connection-strings-in-code"></a>SDK-kapcsolati karakterláncok beállítása a code-ban
Az SDK-t a kapcsolati sztringek beállítása a code-ban lehetővé teszi a saját kapcsolati karakterlánc nevét a konfigurációs fájlok vagy a környezeti változók, használja az alábbi példában látható módon.

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

### <a name="configure-queuetrigger--settings"></a>QueueTrigger beállításainak konfigurálása
A várólista-üzenet feldolgozása a következő beállításokat konfigurálhatja:

* Által mértékének egyidejűleg futtatandó párhuzamosan üzenetsori üzenetek maximális száma (alapértelmezés szerint a 16).
* Előtt egy üzenetsor-üzenetet küld egy ártalmas sorba az újrapróbálkozások maximális száma (az alapértelmezett érték 5).
* A maximális várakozási idő lekérdezések újra, amikor a várólista nem üres (alapértelmezett érték 1 perc).

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

### <a name="set-values-for-webjobs-sdk-constructor-parameters-in-code"></a>Értékek a WebJobs SDK-val konstruktor paraméterek beállítása a code-ban
Néha szeretne hozzáadni egy üzenetsor neve, a blob nevét vagy a tároló, vagy egy Táblanév rögzítse szoftveresen helyett a kód azt. Például előfordulhat, hogy az üzenetsor nevének megadásához szeretné **QueueTrigger** egy konfigurációs fájlban vagy környezeti változóban.

Úgy teheti meg, amely megadásának egy **NameResolver** az objektum a **JobHostConfiguration** típusa. Ön százalékjel (%) foglalt speciális helyőrzőket tartalmaznak bejelentkezik a WebJobs SDK-val attribútum konstruktor paramétereket, és a **NameResolver** kód határozza meg a tényleges értékek ezeket a helyőrzők helyett használható.

Tegyük fel például, egy üzenetsorba, a tesztelési környezetben logqueuetest és a egy elnevezett logqueueprod éles környezetben használni kívánt. A paraméterfájlokban várólista neve helyett adja meg a nevét, egy bejegyzés szeretné a **appSettings** gyűjteményt, amely a tényleges üzenetsor neve lenne. Ha a **appSettings** kulcs logqueue, a függvény az alábbi példához hasonlóan rákeresve.

```csharp
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

A **NameResolver** osztály majd sikerült beolvasni az üzenetsor neve, a **appSettings** az alábbi példában látható módon:

```csharp
public class QueueNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

Át kell adnia a **NameResolver** az osztály a **JobHost** objektum a következő példában látható módon.

```csharp
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new QueueNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

**Megjegyzés:** Üzenetsor, tábla és a blob nevének feloldása minden alkalommal, amikor egy függvény neve, de a blob-tároló nevének feloldása csak akkor, amikor az alkalmazás elindul. Blobtároló neve nem módosítható, amíg a feladat fut-e.

## <a name="how-to-trigger-a-function-manually"></a>Hogyan függvény manuális aktiválása
Egy függvény manuális aktiválásához használhatja az **hívja** vagy **CallAsync** metódust a **JobHost** objektum és a **NoAutomaticTrigger** attribútum a függvény, az alábbi példában látható módon.

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
Az irányítópult megjeleníti a naplók két helyen lehet: az oldalon, a webjobs-feladat, és a egy adott webjobs-feladat meghíváshoz az oldal.

![Webjobs-feladat oldal-naplók](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

![Függvény meghívási lapon-naplók](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

Konzol módszer, amely a függvény hívása, vagy a kimenete a **Main()** metódus az irányítópult-oldalon, a webjobs-feladat, nem egy adott metódus meghívásának oldalán megjelenik. TextWriter objektumot fog kapni a paramétert a podpis metody kimenete egy metódus meghívásának irányítópult-oldalon jelenik meg.

Konzolkimenet egy adott metódus meghívásának nem csatolható, mert a konzol egyszálas, miközben számos feladat funkciók esetleg fut egyszerre. Ezért az SDK-t biztosít a saját egyedi napló az író objektum minden egyes függvény meghívási.

Írhat [alkalmazás nyomkövetési naplók](../app-service/troubleshoot-dotnet-visual-studio.md#logsoverview), használja **Console.Out** (hoz létre a naplók adatai megjelölve) és **Console.Error** (a hiba megjelölve naplókat hoz létre). A másik lehetőség az használandó [nyomkövetési vagy TraceSource](https://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx), részletes, figyelmeztetés, és a kritikus adatok és a hiba mellett szintjeit. Alkalmazás nyomkövetési naplók jelennek meg a webes alkalmazások naplófájljainak, Azure-táblák, illetve attól függően, hogy hogyan konfigurálhat az Azure-webalkalmazás Azure-blobok. Az összes konzolkimenet igaz, a legutóbbi 100 alkalmazásnaplókat is megjelennek az irányítópult-oldalon a webjobs-feladat, nem egy függvény meghívási lapját.

Konzolkimenet jelenik meg, csak akkor, ha a program fut az Azure webjobs-feladat, nem, ha helyben fut a program az irányítópulton vagy más környezetben.

Naplózási letilthatja az irányítópult kapcsolati karakterlánc beállítása null. További információkért lásd: [konfigurációs beállításainak megadása](#how-to-set-configuration-options).

Az alábbi példa bemutatja a naplóznak számos módon:

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

A WebJobs SDK irányítópultján kimenete a **TextWriter** függvény meghívási mikor lépjen az egy adott oldal jelenik meg, és válassza ki az objektum **váltógomb kimeneti**:

![Meghívási hivatkozás](./media/vs-storage-webjobs-getting-started-queues/dashboardinvocations.png)

![Függvény meghívási lapon-naplók](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

A WebJobs SDK irányítópultján, a legutóbbi 100 sor konzol kimeneti show fel a webjobs-feladat (és nem az a függvény meghívási) lépjen a lapra, és válassza ki **váltógomb kimeneti**.

![Kimeneti be-vagy kikapcsolása](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

Egy folyamatos webjobs-feladatot, az alkalmazásnaplókat megjelenjen a/data/feladatok/folyamatos/*{webjobname}*/job_log.txt a webes alkalmazáshoz fájlrendszerben.

        [09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
        [09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
        [09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

Az Azure-ban az alkalmazás naplókban ez blob: 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write – Hello world!, 2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error – Hello world!, 2014-09-26T21 : 01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out – Hello world!,

És a egy Azure-táblában a **Console.Out** és **Console.Error** naplók néznek ki:

![Tábla adatai napló](./media/vs-storage-webjobs-getting-started-queues/tableinfo.png)

![Tábla hibanapló](./media/vs-storage-webjobs-getting-started-queues/tableerror.png)

## <a name="next-steps"></a>További lépések
Ebben a cikkben nyújtott kódmintákkal, amelyek bemutatják, hogyan kezelje az Azure-üzenetsorok használata gyakori forgatókönyvei. Azure WebJobs és WebJobs SDK használatával kapcsolatos további információkért lásd: [Azure WebJobs-dokumentáció erőforrások](https://go.microsoft.com/fwlink/?linkid=390226).

