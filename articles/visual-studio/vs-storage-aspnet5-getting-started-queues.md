---
title: "Ismerkedés a queue storage és a Visual Studio a kapcsolódó szolgáltatások (az ASP.NET Core) |} Microsoft Docs"
description: "Ismerkedés az Azure üzenetsor-kezelési tárolási az ASP.NET Core projekt, a Visual Studio használatával"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 04977069-5b2d-4cba-84ae-9fb2f5eb1006
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: kraigb
ms.openlocfilehash: 394344c0e126472b97c2e8f721c8c8d6514a17dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-queue-storage-and-visual-studio-connected-services-aspnet-core"></a>Ismerkedés a queue storage és a Visual Studio a kapcsolódó szolgáltatások (ASP.NET mag)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Áttekintés
Ez a cikk ismerteti az első lépések az Azure Queue storage segítségével a Visual Studio után készített vagy a Visual Studio használatával Azure-tárfiók az ASP.NET Core projekt hivatkozik **kapcsolódó szolgáltatások hozzáadása** párbeszédpanel. A **kapcsolódó szolgáltatások hozzáadása** műveletet a megfelelő NuGet-csomagok az Azure storage a projekt eléréséhez telepíti, és a tárfiók kapcsolati karakterlánca ad hozzá a projekt konfigurációs fájlokat.

Azure a queue storage egy olyan szolgáltatás, amely elérhető bárhol a világon HTTP vagy HTTPS PROTOKOLLT használ, hitelesített hívásokon keresztül üzenetek nagy számban tárolásához. Egyetlen üzenetsor akár 64 kilobájt (KB) méretű lehet, és több millió üzenetet a tárfiók maximális kapacitásán tartalmazhat.

A kezdéshez, először hozzon létre egy Azure-üzenetsorba tárfiókba. Mutat be, hogyan várólista létrehozása a kódban. Is mutat be, hogyan hajthat végre alapszintű várólista műveletek, például a hozzáadása, módosítása, olvasása, és eltávolítása a üzenetsor-üzeneteket. A Kódminták C nyelven íródtak\# code, és használja az Azure Storage ügyféloldali kódtára a .NET-hez. ASP.NET kapcsolatos további információkért lásd: [ASP.NET](http://www.asp.net).

**Megjegyzés:** az ASP.NET Core hajtsa végre az Azure storage hívásainak API-k aszinkron. Lásd: [aszinkron programozás az Async és Await](http://msdn.microsoft.com/library/hh191443.aspx) további információt. Az alábbi kódot azt feltételezi, hogy aszinkron programozási módszerek vannak használatban.

* Lásd: [Ismerkedés az Azure Queue storage használatának .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) további információt a sorok programozott módon kezelésére.
* Lásd: [Storage-dokumentációt](https://azure.microsoft.com/documentation/services/storage/) Azure Storage kapcsolatos általános információkat.
* Lásd: [felhőalapú szolgáltatások dokumentációja](https://azure.microsoft.com/documentation/services/cloud-services/) Azure felhőszolgáltatások kapcsolatos általános információkat.
* Lásd: [ASP.NET](http://www.asp.net) ASP.NET-alkalmazások programozásával kapcsolatos további információt.

## <a name="access-queues-in-code"></a>A kód várólisták elérése
Fér hozzá az ASP.NET Core projektek várólistákat, is minden C# forrásfájlhoz, aki hozzáfér az Azure a queue storage a következő elemeket kell.

1. Győződjön meg arról, hogy a névtér-deklarációk, a C# fájlban tetején az ezen **használatával** utasításokat.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. Első egy **CloudStorageAccount** objektum, amely a tárfiók adatait jelöli. A következő kód segítségével beolvasása a a tárolási kapcsolati karakterlánc és a tárfiók adatait az Azure-szolgáltatások konfigurációjából.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
3. Első egy **CloudQueueClient** való hivatkozáshoz a várólista-objektumok a tárfiókban lévő objektum.  
   
        // Create the CloudQueueClient object for the storage account.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
4. Első egy **CloudQueue** objektum való hivatkozáshoz egy konkrét várólistába helyezi.
   
        // Get a reference to the CloudQueue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");

**Megjegyzés:** összes a fenti kódot a kód előtt használja a következő mintában.

### <a name="create-a-queue-in-code"></a>Várólista létrehozása a kódban
Az Azure üzenetsor-kezelési létrehozása a kódban, csak adjon hozzá egy **CreateIfNotExistsAsync**.

    // Create the CloudQueue if it does not exist.
    await messageQueue.CreateIfNotExistsAsync();

## <a name="add-a-message-to-a-queue"></a>A várólista üzenet hozzáadása
Üzenet beszúrása egy létező üzenetsorba, hozzon létre egy új **CloudQueueMessage** objektumot, majd hívja a **AddMessageAsync** metódust.

A **CloudQueueMessage** objektum vagy egy karakterláncból (UTF-8 formátumban), vagy egy bájttömböt hozható létre.

Íme egy példa, amely beszúrja a "Hello, World" üzenet.

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    await messageQueue.AddMessageAsync(message);

## <a name="read-a-message-in-a-queue"></a>Olvassa el egy üzenetet a várólistában egyszerre várakozó
Is bepillanthat, hogy egy sor elején található üzenetbe anélkül, hogy eltávolítaná az üzenetsorból meghívásával a **PeekMessageAsync** metódust.

    // Peek the next message in the queue. 
    CloudQueueMessage peekedMessage = await messageQueue.PeekMessageAsync();


## <a name="read-and-remove-a-message-in-a-queue"></a>Olvassa el, és távolítsa el az üzenet a várólistában lévő
A kód eltávolítása (created) egy üzenetet az üzenetsorból két lépésben.

1. Hívás **GetMessageAsync** számára a következő üzenet jelenik meg a sorhoz. Az üzenet **GetMessageAsync** ebből a várólistából üzeneteket olvasó többi kód láthatatlanná válik. Alapértelmezés szerint az üzenet 30 másodpercig marad láthatatlan.
2. Szeretné távolítani az üzenetet az üzenetsorból, hívja **DeleteMessageAsync**.

Az üzenetek kétlépéses eltávolítása lehetővé teszi, hogy ha a kód hardver- vagy szoftverhiba miatt nem tud feldolgozni egy üzenetet, a kód egy másik példánya megkaphassa ugyanazt az üzenetet, és újra megpróbálkozhasson a feldolgozásával. A következő kódot a hívások **DeleteMessageAsync** jobb gombbal az üzenet feldolgozása után.

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();

    // Process the message in less than 30 seconds.

    // Then delete the message.
    await messageQueue.DeleteMessageAsync(retrievedMessage);

## <a name="leverage-additional-options-for-dequeuing-messages"></a>Egyéb lehetőségek az üzenetek üzenetmozgatót
Két módon szabhatja testre az üzenetek lekérését egy üzenetsorból.
Az első lehetőség az üzenetkötegek (legfeljebb 32) lekérése. A második lehetőség az, hogy beállít egy hosszabb vagy rövidebb láthatatlansági időkorlátot, így a kódnak lehetősége van hosszabb vagy rövidebb idő alatt teljesen feldolgozni az egyes üzeneteket. Az alábbi példakód a **GetMessages** módszer segítségével egyszerre 20 üzenetet kér le. Ezután minden üzenetet feldolgoz egy **foreach** hurok segítségével. Azt is állítja be a láthatatlansági időkorlátot 5 perc minden egyes üzenet esetében. Vegye figyelembe, hogy minden üzenet egyszerre, indítsa el a 5 perc, miután 5 perccel átadott hívása után **GetMessages**, nem törölt üzenetek újra lesz látható.

    // Retrieve 20 messages at a time, keeping those messages invisible for 5 minutes, 
    //   delete each message after processing.

    foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

## <a name="get-the-queue-length"></a>Az üzenetsor hosszának lekérése
Megbecsülheti egy üzenetsorban található üzenetek számát. A **FetchAttributes** módszert kéri a queue szolgáltatás olvashatók be a várólista attribútumai, az üzenetek száma. A **ApproximateMethodCount** tulajdonság által lekért legutóbbi értéket adja vissza a **FetchAttributes** metódus, a queue szolgáltatás hívása nélkül.

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display the number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="use-the-async-await-pattern-with-common-queue-apis"></a>Az Async-Await mintázat használata közös várólista API-k
Ez a példa bemutatja, hogyan használható az Async-Await mintázat a közös várólista API-k. A minta meghívja az adott módszerek aszinkron verzióját. Ez az aszinkron utáni javítás az egyes módszerek által látható. Egy aszinkron metódus használata esetén az Async-Await mintázat felfüggeszti helyi a hívás befejeződéséig. Ez a viselkedés lehetővé teszi, hogy az aktuális szál más a munkáját, amely segít a elkerülhetők a szűk keresztmetszetek, és növeli az alkalmazás általános válaszkészsége. Az Async-Await mintázat használatáról .NET a további részletekért lásd: [Async és Await (C# és Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx)

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
## <a name="delete-a-queue"></a>Üzenetsor törlése
Egy üzenetsor és az összes benne foglalt üzenet törléséhez hívja meg a **Delete** módszert az üzenetsor-objektumhoz.

    // Delete the queue.
    messageQueue.Delete();


## <a name="next-steps"></a>Következő lépések
[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]

