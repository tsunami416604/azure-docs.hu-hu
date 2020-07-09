---
title: Adatváltozási hírcsatorna feldolgozása az Azure Blob Storage (előzetes verzió) | Microsoft Docs
description: Megtudhatja, hogyan dolgozhatja fel az adatcsatorna-naplókat egy .NET-ügyfélalkalmazás esetében
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 75995eeb3f8255cb4c60d5be267f9c343edfea89
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "74111859"
---
# <a name="process-change-feed-in-azure-blob-storage-preview"></a>Adatváltozási hírcsatorna feldolgozása az Azure Blob Storage (előzetes verzió)

A módosítási hírcsatorna tranzakciós naplókat biztosít a blobok és a blob metaadatainak a Storage-fiókban történt változásairól. Ebből a cikkből megtudhatja, hogyan olvashatja el a módosítási hírcsatorna rekordjait a blob Change feed Processor Library használatával.

További információ a változási csatornáról: a [hírcsatorna módosítása az Azure Blob Storage (előzetes verzió)](storage-blob-change-feed.md).

> [!NOTE]
> A módosítási hírcsatorna nyilvános előzetes verzióban érhető el, és a **westcentralus** és **westus2** régiókban is elérhető. Ha többet szeretne megtudni erről a szolgáltatásról, valamint az ismert problémákról és korlátozásokról, tekintse meg a [hírcsatorna-támogatás módosítása az Azure Blob Storageban](storage-blob-change-feed.md)című témakört. A módosítási hírcsatorna-feldolgozó függvénytárának változása mostantól változhat, és a tár általánosan elérhetővé válik.

## <a name="get-the-blob-change-feed-processor-library"></a>A blob Change feed Processor Library letöltése

1. A Visual Studióban adja hozzá a `https://azuresdkartifacts.blob.core.windows.net/azuresdkpartnerdrops/index.json` NuGet-csomag forrásaihoz tartozó URL-címet. 

   További információ: [Package sources](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#package-sources).

2. A NuGet csomagkezelő eszközben keresse meg a **Microsoft. Azure. Storage. Changefeed** csomagot, és telepítse a projektbe. 

   További információt a [csomagok keresése és telepítése](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package)című témakörben talál.

## <a name="connect-to-the-storage-account"></a>Kapcsolódás a Storage-fiókhoz

A [CloudStorageAccount. TryParse](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.tryparse) metódus meghívásával elemezheti a kapcsolatok karakterláncát. 

Ezután hozzon létre egy objektumot, amely a Storage-fiókban lévő Blob Storage jelképezi, és hívja meg a [CloudStorageAccount. CreateCloudBlobClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobaccountextensions.createcloudblobclient) metódust.

```cs
public bool GetBlobClient(ref CloudBlobClient cloudBlobClient, string storageConnectionString)
{
    if (CloudStorageAccount.TryParse
        (storageConnectionString, out CloudStorageAccount storageAccount))
        {
            cloudBlobClient = storageAccount.CreateCloudBlobClient();

            return true;
        }
        else
        {
            return false;
        }
    }
}
```

## <a name="initialize-the-change-feed"></a>A változási csatorna inicializálása

Adja hozzá a következő using utasításokat a fájl elejéhez. 

```csharp
using Avro.Generic;
using ChangeFeedClient;
```

Ezután hozza létre a **ChangeFeed** osztály egy példányát a **GetContainerReference** metódus meghívásával. Adja át a változási hírcsatorna tárolójának nevét.

```csharp
public async Task<ChangeFeed> GetChangeFeed(CloudBlobClient cloudBlobClient)
{
    CloudBlobContainer changeFeedContainer =
        cloudBlobClient.GetContainerReference("$blobchangefeed");

    ChangeFeed changeFeed = new ChangeFeed(changeFeedContainer);
    await changeFeed.InitializeAsync();

    return changeFeed;
}
```

## <a name="reading-records"></a>Rekordok beolvasása

> [!NOTE]
> A módosítási hírcsatorna egy nem módosítható és csak olvasható entitás a Storage-fiókban. Tetszőleges számú alkalmazás olvashatja és feldolgozhatja a változási csatornát párhuzamosan és önállóan, a saját kényelmében. A rendszer nem távolítja el a rekordokat a változási hírcsatornából, amikor egy alkalmazás beolvassa őket. Az egyes fogyasztási olvasók olvasási vagy iterációs állapota független, és csak az alkalmazás tartja karban.

A rekordok olvasásának legegyszerűbb módja, ha létrehozza a **ChangeFeedReader** osztály egy példányát. 

Ez a példa a változási hírcsatorna összes rekordját megismétli, majd a-konzolra a rekordok néhány értékét kinyomtatja. 
 
```csharp
public async Task ProcessRecords(ChangeFeed changeFeed)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderAsync();

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}
```

## <a name="resuming-reading-records-from-a-saved-position"></a>Rekordok olvasásának folytatása mentett pozícióból

Dönthet úgy, hogy elmenti az olvasási pozícióját a változási hírcsatornában, és folytatja a rekordok későbbi megismétlését. A módosítási hírcsatorna iterációjának állapotát bármikor mentheti a **ChangeFeedReader. SerializeState ()** metódus használatával. Az állapot egy **karakterlánc** , és az alkalmazás a terv alapján mentheti az adott állapotot (például egy adatbázisba vagy egy fájlba).

```csharp
    string currentReadState = processor.SerializeState();
```

A **ChangeFeedReader** a **CreateChangeFeedReaderFromPointerAsync** metódus használatával folytathatja az utolsó állapotú rekordok megismétlését.

```csharp
public async Task ProcessRecordsFromLastPosition(ChangeFeed changeFeed, string lastReadState)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderFromPointerAsync(lastReadState);

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}

```

## <a name="stream-processing-of-records"></a>Rekordok adatfolyam-feldolgozása

Megadhatja, hogy a megérkezéskor feldolgozza-e a változási adatcsatornákat. Lásd a [specifikációkat](storage-blob-change-feed.md#specifications).

```csharp
public async Task ProcessRecordsStream(ChangeFeed changeFeed, int waitTimeMs)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderAsync();

    ChangeFeedRecord currentRecord = null;
    while (true)
    {
        do
        {
            currentRecord = await processor.GetNextItemAsync();

            if (currentRecord != null)
            {
                string subject = currentRecord.record["subject"].ToString();
                string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
                string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

                Console.WriteLine("Subject: " + subject + "\n" +
                    "Event Type: " + eventType + "\n" +
                    "Api: " + api);
            }

        } while (currentRecord != null);

        await Task.Delay(waitTimeMs);
    }
}
```

## <a name="reading-records-within-a-time-range"></a>Rekordok beolvasása egy időtartományon belül

A változási csatornát óránkénti szegmensekre rendezi a változási esemény időpontja alapján. Lásd a [specifikációkat](storage-blob-change-feed.md#specifications). Az adott időtartományon belül eső változási hírcsatorna-szegmensek rekordjait is elolvashatja.

Ez a példa az összes szegmens kezdési idejét kéri le. Ezután ezt a listát ismétli meg addig, amíg a kezdési időpont nem haladja meg az utolsó felhasználó szegmens időpontját vagy a kívánt tartomány befejezési időpontját. 

### <a name="selecting-segments-for-a-time-range"></a>Szegmensek kiválasztása időtartományhoz

```csharp
public async Task<List<DateTimeOffset>> GetChangeFeedSegmentRefsForTimeRange
    (ChangeFeed changeFeed, DateTimeOffset startTime, DateTimeOffset endTime)
{
    List<DateTimeOffset> result = new List<DateTimeOffset>();

    DateTimeOffset stAdj = startTime.AddMinutes(-15);
    DateTimeOffset enAdj = endTime.AddMinutes(15);

    DateTimeOffset lastConsumable = (DateTimeOffset)changeFeed.LastConsumable;

    List<DateTimeOffset> segments = 
        (await changeFeed.ListAvailableSegmentTimesAsync()).ToList();

    foreach (var segmentStart in segments)
    {
        if (lastConsumable.CompareTo(segmentStart) < 0)
        {
            break;
        }

        if (enAdj.CompareTo(segmentStart) < 0)
        {
            break;
        }

        DateTimeOffset segmentEnd = segmentStart.AddMinutes(60);

        bool overlaps = stAdj.CompareTo(segmentEnd) < 0 && 
            segmentStart.CompareTo(enAdj) < 0;

        if (overlaps)
        {
            result.Add(segmentStart);
        }
    }

    return result;
}
```

### <a name="reading-records-in-a-segment"></a>Rekordok beolvasása egy szegmensben

Az egyes szegmensek és szegmensek rekordjait is elolvashatja.

```csharp
public async Task ProcessRecordsInSegment(ChangeFeed changeFeed, DateTimeOffset segmentOffset)
{
    ChangeFeedSegment segment = new ChangeFeedSegment(segmentOffset, changeFeed);
    await segment.InitializeAsync();

    ChangeFeedSegmentReader processor = await segment.CreateChangeFeedSegmentReaderAsync();

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}
```

## <a name="read-records-starting-from-a-time"></a>Rekordok beolvasása egy adott időpontból

A változási hírcsatorna rekordjait egy kezdő szegmensből tekintheti meg, egészen a végéig. A rekordok időtartományon belüli olvasásához hasonlóan kilistázhatja a szegmenseket, és kiválaszthat egy szegmenst, amellyel megkezdheti az iterációt.

Ez a példa beolvassa a feldolgozandó első szegmens [DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) .

```csharp
public async Task<DateTimeOffset> GetChangeFeedSegmentRefAfterTime
    (ChangeFeed changeFeed, DateTimeOffset timestamp)
{
    DateTimeOffset result = new DateTimeOffset();

    DateTimeOffset lastConsumable = (DateTimeOffset)changeFeed.LastConsumable;
    DateTimeOffset lastConsumableEnd = lastConsumable.AddMinutes(60);

    DateTimeOffset timestampAdj = timestamp.AddMinutes(-15);

    if (lastConsumableEnd.CompareTo(timestampAdj) < 0)
    {
        return result;
    }

    List<DateTimeOffset> segments = (await changeFeed.ListAvailableSegmentTimesAsync()).ToList();
    foreach (var segmentStart in segments)
    {
        DateTimeOffset segmentEnd = segmentStart.AddMinutes(60);
        if (timestampAdj.CompareTo(segmentEnd) <= 0)
        {
            result = segmentStart;
            break;
        }
    }

    return result;
}
```

Ez a példa a kezdő szegmens [DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) kezdődő adatcsatorna-rekordok módosítását dolgozza fel.

```csharp
public async Task ProcessRecordsStartingFromSegment(ChangeFeed changeFeed, DateTimeOffset segmentStart)
{
    TimeSpan waitTime = new TimeSpan(60 * 1000);

    ChangeFeedSegment segment = new ChangeFeedSegment(segmentStart, changeFeed);

    await segment.InitializeAsync();

    while (true)
    {
        while (!await IsSegmentConsumableAsync(changeFeed, segment))
        {
            await Task.Delay(waitTime);
        }

        ChangeFeedSegmentReader reader = await segment.CreateChangeFeedSegmentReaderAsync();

        do
        {
            await reader.CheckForFinalizationAsync();

            ChangeFeedRecord currentItem = null;
            do
            {
                currentItem = await reader.GetNextItemAsync();
                if (currentItem != null)
                {
                    string subject = currentItem.record["subject"].ToString();
                    string eventType = ((GenericEnum)currentItem.record["eventType"]).Value;
                    string api = ((GenericEnum)((GenericRecord)currentItem.record["data"])["api"]).Value;

                    Console.WriteLine("Subject: " + subject + "\n" +
                        "Event Type: " + eventType + "\n" +
                        "Api: " + api);
                }
            } while (currentItem != null);

            if (segment.timeWindowStatus != ChangefeedSegmentStatus.Finalized)
            {
                await Task.Delay(waitTime);
            }
        } while (segment.timeWindowStatus != ChangefeedSegmentStatus.Finalized);

        segment = await segment.GetNextSegmentAsync(); // TODO: What if next window doesn't yet exist?
        await segment.InitializeAsync(); // Should update status, shard list.
    }
}

private async Task<bool> IsSegmentConsumableAsync(ChangeFeed changeFeed, ChangeFeedSegment segment)
{
    if (changeFeed.LastConsumable >= segment.startTime)
    {
        return true;
    }
    await changeFeed.InitializeAsync();
    return changeFeed.LastConsumable >= segment.startTime;
}
```

>[!TIP]
> Az lehet, hogy a szegmensek egy vagy több *chunkFilePath*módosíthatják a hírcsatorna naplóit. Több *chunkFilePath* esetén a rendszeren belül több szegmensre particionálta a rekordokat a közzétételi teljesítmény kezeléséhez. Garantáljuk, hogy a szegmens minden partíciója tartalmazni fogja a kölcsönösen kizáró Blobok módosításait, és a rendelés megszegése nélkül is feldolgozhatók egymástól függetlenül. A **ChangeFeedSegmentShardReader** osztály segítségével megismételheti a rekordokat a szegmens szintjén, ha ez a leghatékonyabb megoldás a forgatókönyvhöz.

## <a name="next-steps"></a>További lépések

További információ a hírcsatorna-naplók változásáról. Lásd: [hírcsatorna módosítása az Azure Blob Storage (előzetes verzió)](storage-blob-change-feed.md)
