---
title: Változáscsatorna feldolgozása az Azure Blob Storage-ban (előzetes verzió) | Microsoft dokumentumok
description: Információ a módosítási hírcsatorna-naplók feldolgozásához .NET ügyfélalkalmazásban
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 75995eeb3f8255cb4c60d5be267f9c343edfea89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74111859"
---
# <a name="process-change-feed-in-azure-blob-storage-preview"></a>Módosítási hírcsatorna feldolgozása az Azure Blob Storage-ban (előzetes verzió)

A változáscsatorna tranzakciós naplókat biztosít a blobok és a tárfiókblob-metaadatok változásairól. Ez a cikk bemutatja, hogyan olvashatja a módosítási hírcsatorna-rekordokat a blob változáscsatorna-processzorkódtár használatával.

Ha többet szeretne megtudni a módosítási hírcsatornáról, olvassa el a [Hírcsatorna módosítása az Azure Blob Storage (előzetes verzióban) című témakört.](storage-blob-change-feed.md)

> [!NOTE]
> A módosítási hírcsatorna nyilvános előzetes verzióban érhető el, és a **westcentralus** és a **westus2** régiókban érhető el. Ha többet szeretne megtudni erről a funkcióról az ismert problémákkal és korlátozásokkal együtt, olvassa el a [Hírcsatorna támogatásának módosítása az Azure Blob Storage szolgáltatásban című témakört.](storage-blob-change-feed.md) A módosítási hírcsatorna-processzorkönyvtár mostantól a könyvtár általános elérhetővé adandóná.

## <a name="get-the-blob-change-feed-processor-library"></a>A blobmódosítási hírcsatorna-folyamattár beszereznie

1. A Visual Studióban `https://azuresdkartifacts.blob.core.windows.net/azuresdkpartnerdrops/index.json` adja hozzá az URL-címet a NuGet csomagforrásokhoz. 

   Ebből megtudhatja, hogyan, lásd: [csomagforrások](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#package-sources).

2. A NuGet csomagkezelőben keresse meg a **Microsoft.Azure.Storage.Changefeed** csomagot, és telepítse azt a projektre. 

   Ebből megtudhatja, hogyan, olvassa el a Csomag keresése és telepítése című [témakört.](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package)

## <a name="connect-to-the-storage-account"></a>Csatlakozás a tárfiókhoz

Elemezje a kapcsolati karakterláncot a [CloudStorageAccount.TryParse](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.tryparse) metódus hívásával. 

Ezután hozzon létre egy objektumot, amely a Blob Storage-ot képviseli a tárfiókban a [CloudStorageAccount.CreateCloudBlobClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobaccountextensions.createcloudblobclient) metódus hívásával.

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

## <a name="initialize-the-change-feed"></a>A módosítási hírcsatorna inicializálása

Adja hozzá a következőket a kódfájl tetejéhez. 

```csharp
using Avro.Generic;
using ChangeFeedClient;
```

Ezután hozzon létre egy példányt a **ChangeFeed** osztály hívja meg a **GetContainerReference** metódust. Adja át a változáscsatorna-tároló nevét.

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

## <a name="reading-records"></a>Rekordok olvasása

> [!NOTE]
> A változáscsatorna egy nem módosítható és csak olvasható entitás a tárfiókban. Tetszőleges számú alkalmazás képes egyszerre és függetlenül olvasni és feldolgozni a módosítási hírcsatornát saját kényelmük szerint. A rekordok nem törlődnek a módosítási hírcsatornából, amikor egy alkalmazás beolvassa őket. Az egyes elfogyasztott olvasó olvasási vagy iterációs állapota független, és csak az alkalmazás tartja karban.

A rekordok olvasásának legegyszerűbb módja a **ChangeFeedReader** osztály egy példányának létrehozása. 

Ez a példa végighalad a módosítási hírcsatorna összes rekordján, majd kinyomtatja a konzolra az egyes rekordok néhány értékét. 
 
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

## <a name="resuming-reading-records-from-a-saved-position"></a>Rekordok olvasásának folytatása mentett helyről

Dönthet úgy, hogy menti az olvasási pozíciót a módosítási hírcsatornában, és egy későbbi időpontban folytatja a rekordok iterációját. A **ChangeFeedReader.SerializeState()** metódussal bármikor mentheti a módosítási hírcsatorna iterációjának állapotát. Az állapot egy **karakterlánc,** és az alkalmazás mentheti az adott állapot alapján az alkalmazás design (Például: egy adatbázisvagy egy fájl).

```csharp
    string currentReadState = processor.SerializeState();
```

A **ChangeFeedReader** **createTheAPasserAsync** metódussal folytathatja a szerkesztést az utolsó állapot rekordjaiközött.

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

## <a name="stream-processing-of-records"></a>A nyilvántartások feldolgozásának streamelése

Beállíthatja, hogy a módosítási hírcsatorna-bejegyzések et érkezésükkor feldolgozza. Lásd: [Műszaki adatok](storage-blob-change-feed.md#specifications).

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

## <a name="reading-records-within-a-time-range"></a>Rekordok olvasása egy időtartományon belül

A módosítási hírcsatorna a változási esemény időpontja alapján óránkénti szegmensekbe van rendezve. Lásd: [Műszaki adatok](storage-blob-change-feed.md#specifications). Az adott időtartományba tartozó változáscsatorna-szegmensek rekordjait olvashatja.

Ez a példa az összes szegmens kezdési idejét kapja meg. Ezután végighalad a listán, amíg a kezdési idő túl nem lesz az utolsó fogyóeszköz-szegmens idején, vagy túl nem a kívánt tartomány befejezési ideje. 

### <a name="selecting-segments-for-a-time-range"></a>Szegmensek kijelölése egy időtartományhoz

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

### <a name="reading-records-in-a-segment"></a>Rekordok olvasása egy szegmensben

Az egyes szegmensekből vagy szegmenstartományokból származó rekordokat olvashat.

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

## <a name="read-records-starting-from-a-time"></a>Rekordok olvasása egy időponttól kezdve

A változáscsatorna rekordjait a kezdő szegmenstől a végéig olvashatja el. Az időtartományon belüli rekordok olvasásához hasonlóan felsorolhatja a szegmenseket, és kiválaszthatja azt a szegmenst, amelyből el szeretné kezdeni az iterációt.

Ez a példa az első feldolgozandó szegmens [DateTimeOffset értékét](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) kapja.

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

Ez a példa egy kezdő szegmens [DateTimeOffset dátum-eltolásától](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) kezdődően dolgozza fel a hírcsatorna-rekordok módosítását.

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
> A szegmens egy vagy több *adattömbfilePath*módosítási naplóilehetnek. Több *chunkFilePath* esetén a rendszer belsőleg particionált a rekordokat több szegmensre a közzétételi átviteli szint kezeléséhez. Garantált, hogy a szegmens minden partíciója tartalmazni fogja a kölcsönösen kizáró blobok módosításait, és egymástól függetlenül feldolgozható a rendezés megsértése nélkül. Használhatja a **ChangeFeedSegmentShardReader** osztály iterálni a rekordok a szegmens szintjén, ha ez a leghatékonyabb a forgatókönyv.

## <a name="next-steps"></a>További lépések

További információ a hírcsatorna-naplók módosításáról. Lásd: [Hírcsatorna módosítása az Azure Blob Storage-ban (előzetes verzió)](storage-blob-change-feed.md)
