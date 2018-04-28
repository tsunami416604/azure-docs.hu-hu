---
title: Ismerkedés a queue storage és a Visual Studio a kapcsolódó szolgáltatások (az ASP.NET Core) |} Microsoft Docs
description: Ismerkedés az Azure üzenetsor-kezelési tárolási az ASP.NET Core projekt, a Visual Studio használatával
services: storage
author: ghogen
manager: douge
ms.assetid: 04977069-5b2d-4cba-84ae-9fb2f5eb1006
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: article
ms.date: 11/14/2017
ms.author: ghogen
ms.openlocfilehash: e56f79334aa85d9a0c81bed4f00664fee5789676
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="get-started-with-queue-storage-and-visual-studio-connected-services-aspnet-core"></a>Ismerkedés a queue storage és a Visual Studio a kapcsolódó szolgáltatások (ASP.NET mag)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

Ez a cikk ismerteti az első lépések az Azure Queue storage segítségével a Visual Studio után készített vagy a Visual Studio használatával Azure-tárfiók az ASP.NET Core projekt hivatkozik **kapcsolódó szolgáltatások** szolgáltatás. A **kapcsolódó szolgáltatások** műveletet a megfelelő NuGet-csomagok az Azure storage a projekt eléréséhez telepíti, és a tárfiók kapcsolati karakterlánca ad hozzá a projekt konfigurációs fájlokat. (Lásd: [Storage-dokumentációt](https://azure.microsoft.com/documentation/services/storage/) Azure Storage kapcsolatos általános információkhoz.)

Azure a queue storage egy olyan szolgáltatás, amely elérhető bárhol a világon HTTP vagy HTTPS PROTOKOLLT használ, hitelesített hívásokon keresztül üzenetek nagy számban tárolásához. Egyetlen üzenetsor akár 64 kilobájt (KB) méretű lehet, és több millió üzenetet a tárfiók maximális kapacitásán tartalmazhat. Lásd még: [Ismerkedés az Azure Queue storage használatának .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) részleteket a sorok programozott módon kezelésére.

Első lépésként hozzon létre egy Azure-üzenetsorba tárfiókba. Ez a cikk megjeleníti a várólista létrehozása a C# és hogyan hajthat végre alapszintű várólista műveletek, például a hozzáadása, módosítása, olvasása, és eltávolítása a üzenetsor-üzeneteket.  A kód az Azure Storage ügyféloldali kódtára a .NET-hez használja. ASP.NET kapcsolatos további információkért lásd: [ASP.NET](http://www.asp.net).

Az Azure Storage API-k között aszinkron, és ebben a cikkben a kód azt feltételezi, hogy aszinkron metódusok használatban van. Lásd: [aszinkron programozás](https://docs.microsoft.com/dotnet/csharp/async) további információt.

## <a name="access-queues-in-code"></a>A kód várólisták elérése

Az ASP.NET Core projektek várólisták szeretne használni, a C# forrásfájlban, aki hozzáfér az Azure üzenetsor-kezelési tárolási az alábbiakat tartalmazza. Az összes ezt a kódot, a kód előtt használja a következő szakaszok.

1. Adja hozzá a szükséges `using` utasításokat:
    ```cs
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Queue;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```

1. Első egy `CloudStorageAccount` objektum, amely a tárfiók adatait jelöli. A tárolási kapcsolati karakterlánc és tárfiókadatok beolvasása az Azure szolgáltatás konfigurációs használja a következő kódot:

    ```cs
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Első egy `CloudQueueClient` objektum a várólista-objektumok a tárfiókban lévő hivatkozni:

    ```cs
    // Create the CloudQueueClient object for the storage account.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Első egy `CloudQueue` objektum egy konkrét várólistába helyezi hivatkozni:

    ```cs
    // Get a reference to the CloudQueue named "messagequeue"
    CloudQueue messageQueue = queueClient.GetQueueReference("messagequeue");
    ```

### <a name="create-a-queue-in-code"></a>Várólista létrehozása a kódban

Az Azure várólista létrehozása a kódban, hívja meg a "CreateIfNotExistsAsync":

```cs
// Create the CloudQueue if it does not exist.
await messageQueue.CreateIfNotExistsAsync();
```

## <a name="add-a-message-to-a-queue"></a>A várólista üzenet hozzáadása

Üzenet beszúrása egy létező üzenetsorba, hozzon létre egy új `CloudQueueMessage` objektumot, majd hívja a `AddMessageAsync` metódust. A `CloudQueueMessage` objektum vagy egy karakterláncból (UTF-8 formátumban), vagy egy bájttömböt hozható létre.

```cs
// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await messageQueue.AddMessageAsync(message);
```

## <a name="read-a-message-in-a-queue"></a>Olvassa el egy üzenetet a várólistában egyszerre várakozó

Is bepillanthat, hogy egy sor elején található üzenetbe anélkül, hogy eltávolítaná az üzenetsorból meghívásával a `PeekMessageAsync` módszert:

```cs
// Peek the next message in the queue.
CloudQueueMessage peekedMessage = await messageQueue.PeekMessageAsync();
```

## <a name="read-and-remove-a-message-in-a-queue"></a>Olvassa el, és távolítsa el az üzenet a várólistában lévő

A kód eltávolítása (created) egy üzenetet az üzenetsorból két lépésben.

1. Hívás `GetMessageAsync` számára a következő üzenet jelenik meg a sorhoz. Az üzenet `GetMessageAsync` ebből a várólistából üzeneteket olvasó többi kód láthatatlanná válik. Alapértelmezés szerint az üzenet 30 másodpercig marad láthatatlan.
1. Szeretné távolítani az üzenetet az üzenetsorból, hívja `DeleteMessageAsync`.

Az üzenetek kétlépéses eltávolítása lehetővé teszi, hogy ha a kód hardver- vagy szoftverhiba miatt nem tud feldolgozni egy üzenetet, a kód egy másik példánya megkaphassa ugyanazt az üzenetet, és újra megpróbálkozhasson a feldolgozásával. A következő kódot a hívások `DeleteMessageAsync` jobb gombbal az üzenet feldolgozása után:

```cs
// Get the next message in the queue.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();

// Process the message in less than 30 seconds.

// Then delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
```

## <a name="additional-options-for-dequeuing-messages"></a>További beállítások üzenetmozgatót üzenetek

Két módon lehet testre szabhatja az üzenetek lekérését egy üzenetsorból. Az első lehetőség az üzenetkötegek (legfeljebb 32) lekérése. A második lehetőség az, hogy beállít egy hosszabb vagy rövidebb láthatatlansági időkorlátot, így a kódnak lehetősége van hosszabb vagy rövidebb idő alatt teljesen feldolgozni az egyes üzeneteket. Az alábbi példakód a `GetMessages` metódus használatával kérje le a 20 üzenetet egy hívásban. Ezután minden üzenetet használatával feldolgozza a `foreach` hurok. Mindemellett a láthatatlansági időkorlátot minden üzenethez öt percre állítja be. Vegye figyelembe, hogy a ötperces felszabadításakor kezdődik összes üzenet egyszerre, így miután öt perc telt, az üzenetek, hogy nem törölték láthatóvá válnak újra.

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

Megbecsülheti egy üzenetsorban található üzenetek számát. A `FetchAttributes` metódus kéri a queue szolgáltatás olvashatók be a várólista attribútumai, az üzenetek száma. A `ApproximateMethodCount` tulajdonság által lekért legutóbbi értéket adja vissza a `FetchAttributes` metódus, a queue szolgáltatás hívása nélkül.

```cs
// Fetch the queue attributes.
messageQueue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = messageQueue.ApproximateMessageCount;

// Display the number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="use-the-async-await-pattern-with-common-queue-apis"></a>Az Async-Await mintázat használata közös várólista API-k

A példa bemutatja, hogyan használható az async-await mintázat a közös API-k végződő várólistára `Async`. Egy aszinkron metódus használatakor, az async-await mintázat felfüggeszti a helyi végrehajtást a hívás befejeződéséig. Ez a viselkedés lehetővé teszi, hogy az aktuális szál más célra, amely segít a elkerülhetők a szűk keresztmetszetek, és növeli az alkalmazás általános válaszkészsége.

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

Egy üzenetsor és a benne tárolt összes üzenet törléséhez hívja meg a `Delete` metódus olyan objektumon, sor:

```cs
// Delete the queue.
messageQueue.Delete();
```

## <a name="next-steps"></a>További lépések

[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
