---
title: Ismerkedés a queue storage és a Visual Studio csatlakoztatott szolgáltatásainak (felhőszolgáltatások) |} A Microsoft Docs
description: Ismerkedés az Azure Queue storage használatával egy felhőszolgáltatás-projektet a Visual Studióban egy tárfiókot, a Visual Studio használatával való csatlakozást követően kapcsolódó szolgáltatások
services: storage
author: ghogen
manager: douge
ms.assetid: da587aac-5e64-4e9a-8405-44cc1924881d
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: eb924bcfe3e2545cf6666a19bbb3494c11bc3a48
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42060840"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Ismerkedés az Azure Queue storage és a Visual Studio kapcsolódó szolgáltatásokkal (felhőszolgáltatás-projektek)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Áttekintés
Ez a cikk azt ismerteti, hogyan kezdheti el az Azure Queue storage használata a Visual Studióban, hivatkozott Azure storage-fiók egy felhőszolgáltatási projekt a Visual studióval vagy létrehozása után **csatlakoztatott szolgáltatás hozzáadása** párbeszédpanel.

Bemutatjuk, hogyan várólista létrehozása a code-ban. Azt is megmutatjuk, hogyan hajthat végre alapszintű üzenetsor-műveletek, például hozzáadása, módosítása, olvasó és üzenetsorbeli üzenetek eltávolítása. A Kódminták C#-kódot, és használja a írt a [a Microsoft Azure Storage ügyféloldali kódtára a .NET-hez](https://msdn.microsoft.com/library/azure/dn261237.aspx).

A **csatlakoztatott szolgáltatás hozzáadása** művelet telepíti a megfelelő NuGet-csomagokat a projekthez az Azure storage eléréséhez, és a projekt konfigurációs fájlokat ad hozzá a tárfiók kapcsolati karakterláncát.

* Lásd: [.NET használatával az Azure Queue storage használatának első lépései](../storage/queues/storage-dotnet-how-to-use-queues.md) üzenetsorok a kód módosítása további tájékoztatást.
* Lásd: [Storage-dokumentáció](https://azure.microsoft.com/documentation/services/storage/) Azure Storage szolgáltatással kapcsolatos általános információkat.
* Lásd: [Cloud Services – dokumentáció](https://azure.microsoft.com/documentation/services/cloud-services/) az Azure cloud services kapcsolatos általános információkat.
* Lásd: [ASP.NET](http://www.asp.net) ASP.NET-alkalmazások programozása további információt.

Az Azure Queue Storage szolgáltatás üzenetek nagy számban történő tárolására szolgál, amelyek HTTP- vagy HTTPS-kapcsolattal, hitelesített hívásokon keresztül a világon bárhonnan elérhetők. Egyetlen üzenetsor akár 64 KB méretű is lehet, és a tárfiók maximális kapacitásán belül több millió üzenetet tartalmazhat.

## <a name="access-queues-in-code"></a>Hozzáférés üzenetsorok a code-ban
A Visual Studio Felhőszolgáltatás-projektek várólisták elérése, foglalja bele a következő elemeket minden olyan C# forrásfájl elérhető az Azure Queue storage kell.

1. Ellenőrizze, hogy a névtér-deklarációk, a C# fájlban tetején az ezen **használatával** utasításokat.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;
2. Get- **CloudStorageAccount** objektum, amely a storage-fiók adatait jelöli. Az alábbi kód használatával az a tárolási kapcsolati karakterlánccal és a tárfiók-információ az Azure-szolgáltatás konfigurációjából.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
3. Get- **CloudQueueClient** objektum, melyet a storage-fiókban a várólista objektumokra hivatkozzanak.  
   
        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
4. Get- **CloudQueue** való hivatkozáshoz egy konkrét várólistába objektum.
   
        // Get a reference to a queue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");

**Megjegyzés:** használata előtt a kódot a fenti kódja a következő minták.

## <a name="create-a-queue-in-code"></a>Várólista létrehozása a code-ban
A várólista létrehozása a kódban, adja hozzá a hívás **CreateIfNotExists**.

    // Create the CloudQueue if it does not exist
    messageQueue.CreateIfNotExists();

## <a name="add-a-message-to-a-queue"></a>Adjon meg egy üzenetet egy üzenetsorba
Üzenet beszúrása egy létező üzenetsorba, hozzon létre egy új **CloudQueueMessage** objektumot, majd hívja a **AddMessage** metódust.

A **CloudQueueMessage** objektum vagy egy karakterláncból (UTF-8 formátumban), vagy egy bájttömböt hozható létre.

Íme egy példa, amely beszúrja a "Hello, World" üzenetet.

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    messageQueue.AddMessage(message);

## <a name="read-a-message-in-a-queue"></a>Egy üzenetsorban lévő üzenet olvasása
Egy üzenetsor elején található üzenetbe anélkül is bepillanthat, hogy eltávolítaná az üzenetsorból. Ehhez hívja meg a **PeekMessage** módszert.

    // Peek at the next message
    CloudQueueMessage peekedMessage = messageQueue.PeekMessage();

## <a name="read-and-remove-a-message-in-a-queue"></a>Olvassa el, és a egy üzenetsorban lévő üzenet eltávolítása
A kód távolíthatja el (kivétele az üzenetsorból) egy üzenetet egy üzenetsorból két lépésben.

1. Hívás **GetMessage** , a következő üzenet jelenik meg egy üzenetsorban. A **GetMessage** módszerrel lekért üzenet láthatatlanná válik az adott üzenetsorban található üzeneteket olvasó többi kód számára. Alapértelmezés szerint az üzenet 30 másodpercig marad láthatatlan.
2. Végzett az üzenet eltávolítása a sorból, hívja meg **DeleteMessage**.

Az üzenetek kétlépéses eltávolítása lehetővé teszi, hogy ha a kód hardver- vagy szoftverhiba miatt nem tud feldolgozni egy üzenetet, a kód egy másik példánya megkaphassa ugyanazt az üzenetet, és újra megpróbálkozhasson a feldolgozásával. A következő kód hívások **DeleteMessage** jobb gombbal az üzenet feldolgozása után.

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

    // Process the message in less than 30 seconds

    // Then delete the message.
    await messageQueue.DeleteMessage(retrievedMessage);


## <a name="use-additional-options-to-process-and-remove-queue-messages"></a>További beállítások segítségével dolgozza fel, és távolítsa el a üzenetsorbeli üzenetek esetén
Két módon szabhatja testre az üzenetek lekérését egy üzenetsorból.

* Üzenetkötegek (legfeljebb 32) kérheti le.
* Beállíthat egy hosszabb vagy rövidebb láthatatlansági időkorlátot hosszabb vagy rövidebb idő teljesen feldolgozni az egyes üzenet, amely lehetővé teszi, hogy a kód. Az alábbi példakód a **GetMessages** módszer segítségével egyszerre 20 üzenetet kér le. Ezután minden üzenetet feldolgoz egy **foreach** hurok segítségével. Mindemellett a láthatatlansági időkorlátot minden üzenethez öt percre állítja be. Vegye figyelembe, hogy az 5 perc minden üzenetnél ugyanakkor kezdődik, tehát 5 perccel a **GetMessages** hívása után a nem törölt üzenetek újra láthatóvá válnak.

Például:

    foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.

        // Then delete the message after processing
        messageQueue.DeleteMessage(message);

    }

## <a name="get-the-queue-length"></a>Az üzenetsor hosszának lekérése
Megbecsülheti egy üzenetsorban található üzenetek számát. A **FetchAttributes** módszer lekéri a Queue szolgáltatásból az üzenetsorra vonatkozó attribútumokat, amelyek között megtalálható az üzenetek száma is. A **ApproximateMethodCount** tulajdonság által lekért legutóbbi értéket adja vissza a **FetchAttributes** metódus, a Queue szolgáltatás hívása nélkül.

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="use-the-async-await-pattern-with-common-azure-queue-apis"></a>Az Async-Await mintázat használata közös Azure üzenetsor API-kkal
Ez a példa bemutatja az Async-Await mintázat használata közös Azure-üzenetsor API-kkal. A minta meghívja az adott módszerek aszinkron verzióját, ez látható által a **aszinkron** utáni javítás az egyes módszerek. Ha async módszert használja az async-await mintázat felfüggeszti a helyi végrehajtást a hívás befejeződéséig. Ez a viselkedés lehetővé teszi, hogy az aktuális szál más munkát, amely segít a teljesítmény szűk keresztmetszetek elkerülése és az alkalmazás általános válaszkészsége növeli. További információk az Async-Await mintázat használatáról .NET-keretrendszerben: [Async and Await (C# and Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx) (Async és Await (C# és Visual Basic)).

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Add the message asynchronously
    await messageQueue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Delete the message asynchronously
    await messageQueue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");

## <a name="delete-a-queue"></a>Üzenetsor törlése
Egy üzenetsor és az összes benne foglalt üzenet törléséhez hívja meg a **Delete** módszert az üzenetsor-objektumhoz.

    // Delete the queue.
    messageQueue.Delete();

## <a name="next-steps"></a>További lépések
[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]

