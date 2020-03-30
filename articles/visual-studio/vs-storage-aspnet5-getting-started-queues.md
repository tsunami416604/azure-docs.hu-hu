---
title: A várólista-tárolás első lépései a Visual Studio (ASP.NET Core) használatával
description: Az Azure-várólista-tárolás használatának első lépései egy ASP.NET Core projektben a Visual Studióban
services: storage
author: ghogen
manager: jillfra
ms.assetid: 04977069-5b2d-4cba-84ae-9fb2f5eb1006
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: article
ms.date: 11/14/2017
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5cdf6f2644788674df91b533c9444fc88ab30b09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72300026"
---
# <a name="get-started-with-queue-storage-and-visual-studio-connected-services-aspnet-core"></a>Első lépések a várólista-tárolás és a Visual Studio-hoz csatlakoztatott szolgáltatások (ASP.NET Core) szolgáltatással

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

Ez a cikk bemutatja, hogyan kezdheti el használni az Azure Queue storage-t a Visual Studióban, miután létrehozott vagy hivatkozott egy Azure storage-fiókra egy ASP.NET Core projektben a Visual Studio **Connected Services** szolgáltatás használatával. A **Connected Services** művelet telepíti a megfelelő NuGet csomagokat az Azure storage eléréséhez a projektben, és hozzáadja a tárfiók kapcsolati karakterláncát a projekt konfigurációs fájljaihoz. (Az Azure Storage szolgáltatással kapcsolatos általános tudnivalókat a [Storage dokumentációjában](https://azure.microsoft.com/documentation/services/storage/) találja.)

Az Azure-várólista-tárolás olyan szolgáltatás, amely a világ bármely pontjáról http-n vagy HTTPS-en keresztül elérhető üzenetek nagy számának tárolására szolgál. Egy üzenetsor-üzenet mérete legfeljebb 64 kilobájt (KB) lehet, és egy várólista több millió üzenetet tartalmazhat, a tárfiók teljes kapacitáskorlátjáig. Az Azure Queue storage használatával a .NET használatával történő [használatának első lépései](../storage/queues/storage-dotnet-how-to-use-queues.md) a várólisták programos antropológiai kezelésére vonatkozó részletekről is.

Első lépésekhez hozzon létre egy Azure-várólistát a tárfiókban. Ez a cikk bemutatja, hogyan hozhat létre egy várólistát a C# nyelven, és hogyan hajthatja végre az alapvető várólista-műveleteket, például a várólista-üzenetek hozzáadását, módosítását, olvasását és eltávolítását.  A kód az Azure Storage ügyfélkódtárját használja a .NET számára. A ASP.NET ASP.NET a ASP.NET című témakörben [talál](https://www.asp.net)további információt.

Az Azure Storage API-k egy része aszinkron, és a jelen cikkben szereplő kód feltételezi, hogy aszinkron metódusok használatosak. További információt az [Aszinkron programozás](https://docs.microsoft.com/dotnet/csharp/async) című témakörben talál.

## <a name="access-queues-in-code"></a>Hozzáférési várólisták kódban

A ASP.NET Core-projektek várólistáinak eléréséhez adja meg a következő elemeket bármely C# forrásfájlban, amely hozzáfér az Azure-várólista-tárolóhoz. Használja az összes ezt a kódot a következő szakaszokban lévő kód előtt.

1. Adja hozzá `using` a szükséges állításokat:
    ```cs
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Queue;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```

1. Szerezzen `CloudStorageAccount` be egy objektumot, amely a tárfiók adatait jelöli. A következő kód segítségével lejuthat a tárolási kapcsolat ihúrjának és a tárfiók adatainak az Azure szolgáltatáskonfigurációjából:

    ```cs
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. A `CloudQueueClient` tárfiókban lévő várólista-objektumokra hivatkozó objektum beszereznie:

    ```cs
    // Create the CloudQueueClient object for the storage account.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Adott `CloudQueue` várólistára hivatkozó objektum beszerezni:

    ```cs
    // Get a reference to the CloudQueue named "messagequeue"
    CloudQueue messageQueue = queueClient.GetQueueReference("messagequeue");
    ```

### <a name="create-a-queue-in-code"></a>Várólista létrehozása a kódban

Az Azure-várólista kódban való létrehozásához hívja meg a következőt: `CreateIfNotExistsAsync`

```cs
// Create the CloudQueue if it does not exist.
await messageQueue.CreateIfNotExistsAsync();
```

## <a name="add-a-message-to-a-queue"></a>Üzenet hozzáadása várólistához

Ha üzenetet szeretne beszúrni egy meglévő `CloudQueueMessage` várólistába, `AddMessageAsync` hozzon létre egy új objektumot, majd hívja meg a metódust. Az `CloudQueueMessage` objektum létrehozható karakterláncból (UTF-8 formátumban) vagy bájttömbből.

```cs
// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await messageQueue.AddMessageAsync(message);
```

## <a name="read-a-message-in-a-queue"></a>Üzenet olvasása a várólistában

A várólista elején lévő üzenetbe anélkül tekinthet meg, hogy `PeekMessageAsync` eltávolítaná a várólistából a metódus hívásával:

```cs
// Peek the next message in the queue.
CloudQueueMessage peekedMessage = await messageQueue.PeekMessageAsync();
```

## <a name="read-and-remove-a-message-in-a-queue"></a>Üzenet olvasása és eltávolítása a várólistában

A kód két lépésben eltávolíthatja (megszüntetheti) az üzeneteket a várólistából.

1. Hívás `GetMessageAsync` a várólista következő üzenetének beállásához. A várólistából `GetMessageAsync` érkező üzenetek láthatatlanná válnak a várólistából érkező üzeneteket olvasó bármely más kód számára. Alapértelmezés szerint az üzenet 30 másodpercig marad láthatatlan.
1. Ha be szeretné fejezni az üzenet `DeleteMessageAsync`eltávolítását a várólistából, hívja meg a .

Az üzenetek kétlépéses eltávolítása lehetővé teszi, hogy ha a kód hardver- vagy szoftverhiba miatt nem tud feldolgozni egy üzenetet, a kód egy másik példánya megkaphassa ugyanazt az üzenetet, és újra megpróbálkozhasson a feldolgozásával. Az üzenet `DeleteMessageAsync` feldolgozása után azonnal a következő kód hívásokat kezdeményez:

```cs
// Get the next message in the queue.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();

// Process the message in less than 30 seconds.

// Then delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
```

## <a name="additional-options-for-dequeuing-messages"></a>Az üzenetek sorba oltó dinamtálásának további lehetőségei

A várólistából kétféleképpen szabhatja testre az üzenetek beolvasását. Az első lehetőség az üzenetkötegek (legfeljebb 32) lekérése. A második lehetőség az, hogy beállít egy hosszabb vagy rövidebb láthatatlansági időkorlátot, így a kódnak lehetősége van hosszabb vagy rövidebb idő alatt teljesen feldolgozni az egyes üzeneteket. A következő kódpélda `GetMessages` a módszer rel 20 üzenetet kap egy hívásban. Ezután feldolgozza az `foreach` egyes üzeneteket egy hurok használatával. Mindemellett a láthatatlansági időkorlátot minden üzenethez öt percre állítja be. Ne feledje, hogy az öt perces időzítő egyszerre indul el az összes üzenethez, így öt perc elteltével a nem törölt üzenetek ismét láthatóvá válnak.

```cs
// Retrieve 20 messages at a time, keeping those messages invisible for 5 minutes, 
//   delete each message after processing.

foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

## <a name="get-the-queue-length"></a>Az üzenetsor hosszának lekérése

Megbecsülheti egy üzenetsorban található üzenetek számát. A `FetchAttributes` metódus arra kéri a várólista-szolgáltatást, hogy olvassa be a várólista-attribútumokat, beleértve az üzenetek számát is. A `ApproximateMethodCount` tulajdonság a `FetchAttributes` metódus által beolvasott utolsó értéket adja vissza a várólista-szolgáltatás hívása nélkül.

```cs
// Fetch the queue attributes.
messageQueue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = messageQueue.ApproximateMessageCount;

// Display the number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="use-the-async-await-pattern-with-common-queue-apis"></a>Az Async-Await minta használata közös várólista API-kkal

Ez a példa bemutatja, hogyan használható az async-await minta a közös várólista API-k végződésével. `Async` Aszinkron metódus használatos, az async-await minta felfüggeszti a helyi végrehajtást, amíg a hívás befejeződik. Ez a viselkedés lehetővé teszi, hogy az aktuális szál más munkát, amely segít elkerülni a teljesítmény szűk keresztmetszetek, és javítja az általános válaszkészséget az alkalmazás.

```cs
// Create a message to add to the queue.
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message.
await messageQueue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

## <a name="delete-a-queue"></a>Üzenetsor törlése

Egy várólista és a benne lévő `Delete` összes üzenet törléséhez hívja meg a metódust a várólista-objektumon:

```cs
// Delete the queue.
messageQueue.Delete();
```

## <a name="next-steps"></a>További lépések

[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
