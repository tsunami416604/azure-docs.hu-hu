---
title: Ismerkedés a várólista-tárolóval és a Visual Studio csatlakoztatott szolgáltatásaival (ASP.NET Core) | Microsoft Docs
description: Az Azure üzenetsor-tárolás használatának első lépései egy ASP.NET Core-projektben a Visual Studióban
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
ms.openlocfilehash: d8e370c6f7c59da8522bb4fb1403b6107a9c9c41
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510980"
---
# <a name="get-started-with-queue-storage-and-visual-studio-connected-services-aspnet-core"></a>Ismerkedés a várólista-tárolóval és a Visual Studio csatlakoztatott szolgáltatásaival (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

Ez a cikk azt ismerteti, hogyan kezdheti el az Azure üzenetsor-tárolás használatát a Visual Studióban, miután létrehozta vagy hivatkozott egy Azure Storage-fiókot egy ASP.NET Core-projektben a Visual Studio **csatlakoztatott szolgáltatások** funkciójának használatával. A **csatlakoztatott szolgáltatások** művelet telepíti a megfelelő NuGet-csomagokat az Azure Storage-ban a projektben, és hozzáadja a Storage-fiókhoz tartozó kapcsolati karakterláncot a projekt konfigurációs fájljaihoz. (Lásd az Azure Storage szolgáltatással kapcsolatos általános információk [tárolási dokumentációját](https://azure.microsoft.com/documentation/services/storage/) .)

Az Azure üzenetsor-tároló szolgáltatás olyan nagy mennyiségű üzenet tárolására szolgál, amely bárhonnan elérhető a világ bármely pontjáról a HTTP vagy HTTPS protokollt használó hitelesített hívásokon keresztül. A várólista-üzenetek mérete 64 kilobájt (KB) lehet, és a várólista több millió üzenetet is tartalmazhat, akár egy Storage-fiók teljes kapacitási korlátját. Lásd még: [Az Azure Queue Storage használatának első lépései a .NET használatával](../storage/queues/storage-dotnet-how-to-use-queues.md) a várólisták programozott kezelésével kapcsolatos részletekért.

Első lépésként hozzon létre egy Azure-várólistát a Storage-fiókban. Ebből a cikkből megtudhatja, hogyan hozhat C# létre üzenetsor-kezelést, és hogyan hajthat végre olyan alapszintű üzenetsor-műveleteket, mint például a üzenetsor-üzenetek hozzáadása, módosítása, olvasása és eltávolítása.  A kód a .NET-hez készült Azure Storage ügyféloldali kódtárat használja. További információ a ASP.NET: [ASP.net](https://www.asp.net).

Egyes Azure Storage API-k aszinkron módon működnek, és a cikkben szereplő kód feltételezi az aszinkron metódusok használatát. További információ: [aszinkron programozás](https://docs.microsoft.com/dotnet/csharp/async) .

## <a name="access-queues-in-code"></a>Hozzáférési sorok a kódban

Ha ASP.NET Core-projektekben szeretné elérni a várólistákat, a következő C# elemeket minden olyan forrásfájlban adja meg, amely hozzáfér az Azure Queue Storage szolgáltatáshoz. A következő szakaszokban a kód előtt használja ezt a kódot.

1. Adja hozzá a `using` szükséges utasításokat:
    ```cs
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Queue;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```

1. Szerezzen `CloudStorageAccount` be egy objektumot, amely a Storage-fiók adatait jelöli. A következő kód használatával szerezheti be a Storage-kapcsolódási karakterlánc és a Storage-fiók adatait az Azure szolgáltatás konfigurációjában:

    ```cs
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. A Storage-fiókban található üzenetsor-objektumokra hivatkozó objektumlekérése:`CloudQueueClient`

    ```cs
    // Create the CloudQueueClient object for the storage account.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. `CloudQueue` Objektum lekérése egy adott várólistára való hivatkozáshoz:

    ```cs
    // Get a reference to the CloudQueue named "messagequeue"
    CloudQueue messageQueue = queueClient.GetQueueReference("messagequeue");
    ```

### <a name="create-a-queue-in-code"></a>Várólista létrehozása a kódban

Az Azure-várólista kódban való létrehozásához hívja `CreateIfNotExistsAsync`a következőt:

```cs
// Create the CloudQueue if it does not exist.
await messageQueue.CreateIfNotExistsAsync();
```

## <a name="add-a-message-to-a-queue"></a>Üzenet hozzáadása egy várólistához

Ha egy üzenetet egy meglévő várólistába szeretne beszúrni, hozzon létre egy új `CloudQueueMessage` objektumot, majd hívja meg a `AddMessageAsync` metódust. Egy `CloudQueueMessage` objektum hozható létre egy sztringből (UTF-8 formátumban) vagy egy bájtos tömbből.

```cs
// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await messageQueue.AddMessageAsync(message);
```

## <a name="read-a-message-in-a-queue"></a>Üzenet elolvasása egy várólistában

A várólista elején lévő üzenetbe való betekintés nélkül is betekintést nyerhet a várólistába `PeekMessageAsync` :

```cs
// Peek the next message in the queue.
CloudQueueMessage peekedMessage = await messageQueue.PeekMessageAsync();
```

## <a name="read-and-remove-a-message-in-a-queue"></a>Üzenetsor beolvasása és eltávolítása

A kód két lépésben képes eltávolítani (elválasztani) egy üzenetsor üzenetét.

1. Hívja `GetMessageAsync` meg a következő üzenet beszerzését egy várólistában. A visszaadott `GetMessageAsync` üzenet a várólistából beolvasott más kódokba is láthatatlanná válik. Alapértelmezés szerint az üzenet 30 másodpercig marad láthatatlan.
1. Az üzenet várólistából való eltávolításának befejezéséhez hívja `DeleteMessageAsync`meg a következőt:.

Az üzenetek kétlépéses eltávolítása lehetővé teszi, hogy ha a kód hardver- vagy szoftverhiba miatt nem tud feldolgozni egy üzenetet, a kód egy másik példánya megkaphassa ugyanazt az üzenetet, és újra megpróbálkozhasson a feldolgozásával. Az alábbi kód az `DeleteMessageAsync` üzenet feldolgozását követően azonnal meghívja a következő kódot:

```cs
// Get the next message in the queue.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();

// Process the message in less than 30 seconds.

// Then delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
```

## <a name="additional-options-for-dequeuing-messages"></a>További beállítások a dequeuing üzenetek számára

Az üzenetek várólistából való lekérését kétféleképpen lehet testre szabni. Az első lehetőség az üzenetkötegek (legfeljebb 32) lekérése. A második lehetőség az, hogy beállít egy hosszabb vagy rövidebb láthatatlansági időkorlátot, így a kódnak lehetősége van hosszabb vagy rövidebb idő alatt teljesen feldolgozni az egyes üzeneteket. A következő példában a `GetMessages` metódus használatával 20 üzenetet kap egy hívásban. Ezután az összes üzenetet feldolgozza `foreach` egy hurok használatával. Mindemellett a láthatatlansági időkorlátot minden üzenethez öt percre állítja be. Vegye figyelembe, hogy az öt perces időzítő egyszerre indul el az összes üzenethez, így öt perc elteltével az összes nem törölt üzenet ismét láthatóvá válik.

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

Megbecsülheti egy üzenetsorban található üzenetek számát. A `FetchAttributes` metódus arra kéri a várólista-szolgáltatást, hogy lekérje a várólista attribútumait, beleértve az üzenetek darabszámát is. A `ApproximateMethodCount` tulajdonság a `FetchAttributes` metódus által lekért utolsó értéket adja vissza a várólista-szolgáltatás hívása nélkül.

```cs
// Fetch the queue attributes.
messageQueue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = messageQueue.ApproximateMessageCount;

// Display the number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="use-the-async-await-pattern-with-common-queue-apis"></a>Az aszinkron várakozási minta használata közös üzenetsor-API-kkal

Ez a példa azt mutatja be, hogyan használható az aszinkron várakozási minta a közös üzenetsor `Async`-API-kkal. Aszinkron módszer használata esetén az aszinkron várakozási minta a hívás befejezéséig felfüggeszti a helyi végrehajtást. Ez a viselkedés lehetővé teszi, hogy az aktuális szál más olyan munkát végezzen, amely segít elkerülni a teljesítmény szűk keresztmetszetét, és javítja az alkalmazás általános reagálását.

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

Ha törölni szeretne egy várólistát és a benne található összes üzenetet, hívja `Delete` meg a metódust a várólista-objektumon:

```cs
// Delete the queue.
messageQueue.Delete();
```

## <a name="next-steps"></a>További lépések

[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
