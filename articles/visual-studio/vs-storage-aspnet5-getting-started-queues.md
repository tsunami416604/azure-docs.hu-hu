---
title: Ismerkedés a queue storage és a Visual Studio csatlakoztatott szolgáltatásainak (az ASP.NET Core) |} A Microsoft Docs
description: Hogyan lehet egy ASP.NET Core-projektet a Visual Studióban az Azure queue storage használatának első lépései
services: storage
author: ghogen
manager: douge
ms.assetid: 04977069-5b2d-4cba-84ae-9fb2f5eb1006
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: article
ms.date: 11/14/2017
ms.author: ghogen
ms.openlocfilehash: 237294c1bc603402c349f7a56f20c34ed8d210fe
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42055532"
---
# <a name="get-started-with-queue-storage-and-visual-studio-connected-services-aspnet-core"></a>Ismerkedés a queue storage és a Visual Studio csatlakoztatott szolgáltatásainak (ASP.NET-mag)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

Ez a cikk azt ismerteti, hogyan kezdheti el az Azure Queue storage használata a Visual Studióban létrehozott vagy a hivatkozott Azure storage-fiók egy ASP.NET Core-projektet a Visual Studio használatával **csatlakoztatott szolgáltatás** funkció. A **csatlakoztatott szolgáltatás** művelet telepíti a megfelelő NuGet-csomagokat a projekthez az Azure storage eléréséhez, és a projekt konfigurációs fájlokat ad hozzá a tárfiók kapcsolati karakterláncát. (Lásd: [Storage-dokumentáció](https://azure.microsoft.com/documentation/services/storage/) Azure Storage kapcsolatos általános információkhoz.)

Az Azure queue storage szolgáltatása nagy számú, amelyek segítségével bárhonnan elérhetők HTTP vagy HTTPS PROTOKOLLT használó hitelesített hívásokon keresztül a világon üzenetek tárolásához. Egyetlen üzenetsor akár 64 kilobájt (KB méretű) lehet, és a egy üzenetsor több millió üzenetet a storage-fiók összesített kapacitásán belül is tartalmazhat. További tájékoztatás [.NET használatával az Azure Queue storage használatának első lépései](../storage/queues/storage-dotnet-how-to-use-queues.md) programozott módon kezelésére szolgáló üzenetsorok részleteiért.

Első lépésként hozzon létre egy Azure-üzenetsorba a storage-fiókban. Ez a cikk megjeleníti a várólista létrehozása a C#-ban és hogyan hajthat végre egyszerű üzenetsor-műveletek, például a hozzáadása, módosítása, olvasó és üzenetsorbeli üzenetek eltávolítása.  A kódot használja az Azure Storage ügyféloldali kódtára a .NET-hez. Az ASP.NET kapcsolatos további információkért lásd: [ASP.NET](http://www.asp.net).

Az Azure Storage API-k némelyike aszinkron, és ebben a cikkben a kód azt feltételezi, hogy az aszinkron módszereket használ. Lásd: [aszinkron programozás](https://docs.microsoft.com/dotnet/csharp/async) további információt.

## <a name="access-queues-in-code"></a>Hozzáférés üzenetsorok a code-ban

Üzenetsorok, ASP.NET Core-projekt eléréséhez adathordozófájlba felvenni a következő elemek bármely C# forrás, amely hozzáfér az Azure queue storage. Az alábbi szakaszok a felhasználja az összes ezt a kódot, a kód elé.

1. Adja hozzá a szükséges `using` utasításokat:
    ```cs
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Queue;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```

1. Get- `CloudStorageAccount` objektum, amely a storage-fiók adatait jelöli. A tárolási kapcsolati karakterlánccal és a tárfiókjával kapcsolatos információkat kaphat az Azure-szolgáltatás konfigurációs használja a következő kódot:

    ```cs
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Get- `CloudQueueClient` való hivatkozáshoz a várólista-objektumok a tárfiókban lévő objektumok:

    ```cs
    // Create the CloudQueueClient object for the storage account.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Get- `CloudQueue` való hivatkozáshoz egy konkrét várólistába objektum:

    ```cs
    // Get a reference to the CloudQueue named "messagequeue"
    CloudQueue messageQueue = queueClient.GetQueueReference("messagequeue");
    ```

### <a name="create-a-queue-in-code"></a>Várólista létrehozása a code-ban

Az Azure üzenetsor létrehozása a kódban, hívja meg: "% CreateIfNotExistsAsync":

```cs
// Create the CloudQueue if it does not exist.
await messageQueue.CreateIfNotExistsAsync();
```

## <a name="add-a-message-to-a-queue"></a>Adjon meg egy üzenetet egy üzenetsorba

Üzenet beszúrása egy létező üzenetsorba, hozzon létre egy új `CloudQueueMessage` objektumot, majd hívja a `AddMessageAsync` metódust. A `CloudQueueMessage` objektum vagy egy karakterláncból (UTF-8 formátumban), vagy egy bájttömböt hozható létre.

```cs
// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await messageQueue.AddMessageAsync(message);
```

## <a name="read-a-message-in-a-queue"></a>Egy üzenetsorban lévő üzenet olvasása

Anélkül, hogy eltávolítaná az üzenetsorból meghívásával is bepillanthat egy üzenetsorban található üzenet a `PeekMessageAsync` módszer:

```cs
// Peek the next message in the queue.
CloudQueueMessage peekedMessage = await messageQueue.PeekMessageAsync();
```

## <a name="read-and-remove-a-message-in-a-queue"></a>Olvassa el, és a egy üzenetsorban lévő üzenet eltávolítása

A kód távolíthatja el (eltávolítása a sorból) egy üzenetet egy üzenetsorból két lépésben.

1. Hívás `GetMessageAsync` , a következő üzenet jelenik meg egy üzenetsorban. Lekért üzenet `GetMessageAsync` válik az adott üzenetsorban üzeneteket olvasó többi kód számára. Alapértelmezés szerint az üzenet 30 másodpercig marad láthatatlan.
1. Végzett az üzenet eltávolítása a sorból, hívja meg `DeleteMessageAsync`.

Az üzenetek kétlépéses eltávolítása lehetővé teszi, hogy ha a kód hardver- vagy szoftverhiba miatt nem tud feldolgozni egy üzenetet, a kód egy másik példánya megkaphassa ugyanazt az üzenetet, és újra megpróbálkozhasson a feldolgozásával. A következő kód hívások `DeleteMessageAsync` jobb gombbal az üzenet feldolgozása után:

```cs
// Get the next message in the queue.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();

// Process the message in less than 30 seconds.

// Then delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
```

## <a name="additional-options-for-dequeuing-messages"></a>További beállítások üzenetmozgatót üzenetek

Két módon szabhatja testre az üzenetek lekérését egy üzenetsorból. Az első lehetőség az üzenetkötegek (legfeljebb 32) lekérése. A második lehetőség az, hogy beállít egy hosszabb vagy rövidebb láthatatlansági időkorlátot, így a kódnak lehetősége van hosszabb vagy rövidebb idő alatt teljesen feldolgozni az egyes üzeneteket. Az alábbi példakód a `GetMessages` metódus lekéréséhez 20 üzenetet egyetlen hívásával. Ezután minden üzenetet használatával feldolgozza a `foreach` ciklus. Mindemellett a láthatatlansági időkorlátot minden üzenethez öt percre állítja be. Vegye figyelembe, hogy az ötperces időzítő elindítja az összes üzenetet egy időben, ezért után öt perc alatt teljesül, az üzenetek nem lett törölve, láthatóvá újra.

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

Megbecsülheti egy üzenetsorban található üzenetek számát. A `FetchAttributes` módszert kéri a queue szolgáltatás lekérdezni az attribútumokat, az üzenetek száma. A `ApproximateMethodCount` tulajdonság által lekért legutóbbi értéket adja vissza a `FetchAttributes` metódus, a queue szolgáltatás hívása nélkül.

```cs
// Fetch the queue attributes.
messageQueue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = messageQueue.ApproximateMessageCount;

// Display the number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="use-the-async-await-pattern-with-common-queue-apis"></a>Az Async-Await mintázat használata közös queue API-k

Ez a példa bemutatja, hogyan használható az async-await mintázat a közös API-k, a befejezési várólistára `Async`. Ha async módszert használja, az async-await mintázat felfüggeszti a helyi végrehajtást a hívás befejeződéséig. Ez a viselkedés lehetővé teszi, hogy az aktuális szál más munkát, amely segít a teljesítmény szűk keresztmetszetek elkerülése és az alkalmazás általános válaszkészsége növeli.

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

Egy üzenetsor és a benne tárolt összes üzenet törléséhez hívja a `Delete` a várólista-objektum metódust:

```cs
// Delete the queue.
messageQueue.Delete();
```

## <a name="next-steps"></a>További lépések

[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
