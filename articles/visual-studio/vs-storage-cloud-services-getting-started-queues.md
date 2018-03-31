---
title: Ismerkedjen meg a queue storage és a Visual Studio services (felhőszolgáltatások) kapcsolódó |} Microsoft Docs
description: Ismerkedés az Azure Queue storage használata a Visual Studio felhőszolgáltatás-projekt egy tárfiókot, a Visual Studio használatával történő kapcsolódás után kapcsolódó szolgáltatások
services: storage
documentationcenter: ''
author: ghogen
manager: douge
editor: ''
ms.assetid: da587aac-5e64-4e9a-8405-44cc1924881d
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: ee4228db093b840940152fcad6dfba793dbc4772
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Ismerkedés az Azure Queue storage és a Visual Studio kapcsolódó szolgáltatások (felhőalapú szolgáltatások projektek)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Áttekintés
Ez a cikk ismerteti az első lépések az Azure Queue storage segítségével a Visual Studio után készített vagy a Visual Studio használatával Azure-tárfiók felhőalapú szolgáltatások projektben hivatkozott **kapcsolódó szolgáltatások hozzáadása** párbeszédpanel.

Mutat be, hogyan várólista létrehozása a kódban. Is mutat be, hogyan hajthat végre alapszintű várólista műveletek, például a hozzáadása, módosítása, olvasási és eltávolítása az üzenetsor-üzeneteket. A mintákat a C#-kódban és -felhasználási nyelven íródtak a [Microsoft Azure Storage ügyféloldali kódtára a .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

A **kapcsolódó szolgáltatások hozzáadása** műveletet a megfelelő NuGet-csomagok az Azure storage a projekt eléréséhez telepíti, és a tárfiók kapcsolati karakterlánca ad hozzá a projekt konfigurációs fájlokat.

* Lásd: [Ismerkedés az Azure Queue storage használatának .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) további információt a kódban várólisták kezelésére.
* Lásd: [Storage-dokumentációt](https://azure.microsoft.com/documentation/services/storage/) Azure Storage kapcsolatos általános információkat.
* Lásd: [felhőalapú szolgáltatások dokumentációja](https://azure.microsoft.com/documentation/services/cloud-services/) Azure felhőszolgáltatások kapcsolatos általános információkat.
* Lásd: [ASP.NET](http://www.asp.net) ASP.NET-alkalmazások programozásával kapcsolatos további információt.

Az Azure Queue Storage szolgáltatás üzenetek nagy számban történő tárolására szolgál, amelyek HTTP- vagy HTTPS-kapcsolattal, hitelesített hívásokon keresztül a világon bárhonnan elérhetők. Egyetlen üzenetsor akár 64 KB méretű is lehet, és a tárfiók maximális kapacitásán belül több millió üzenetet tartalmazhat.

## <a name="access-queues-in-code"></a>A kód várólisták elérése
A Visual Studio Felhőszolgáltatás-projektek várólisták fér hozzá, a következő elemek bármely C# forrásfájl elérhető Azure Queue storage is kell.

1. Győződjön meg arról, hogy a névtér-deklarációk, a C# fájlban tetején az ezen **használatával** utasításokat.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;
2. Első egy **CloudStorageAccount** objektum, amely a tárfiók adatait jelöli. A következő kód segítségével beolvasása a a tárolási kapcsolati karakterlánc és a tárfiók adatait az Azure-szolgáltatások konfigurációjából.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
3. Első egy **CloudQueueClient** való hivatkozáshoz a várólista-objektumok a tárfiókban lévő objektum.  
   
        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
4. Első egy **CloudQueue** objektum való hivatkozáshoz egy konkrét várólistába helyezi.
   
        // Get a reference to a queue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");

**Megjegyzés:** összes a fenti kódot a kód előtt használja a következő mintában.

## <a name="create-a-queue-in-code"></a>Várólista létrehozása a kódban
A várólista létrehozása a kódban, csak adjon hozzá egy **CreateIfNotExists**.

    // Create the CloudQueue if it does not exist
    messageQueue.CreateIfNotExists();

## <a name="add-a-message-to-a-queue"></a>A várólista üzenet hozzáadása
Üzenet beszúrása egy létező üzenetsorba, hozzon létre egy új **CloudQueueMessage** objektumot, majd hívja a **AddMessage** metódust.

A **CloudQueueMessage** objektum vagy egy karakterláncból (UTF-8 formátumban), vagy egy bájttömböt hozható létre.

Íme egy példa, amely beszúrja a "Hello, World" üzenet.

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    messageQueue.AddMessage(message);

## <a name="read-a-message-in-a-queue"></a>Olvassa el egy üzenetet a várólistában egyszerre várakozó
Egy üzenetsor elején található üzenetbe anélkül is bepillanthat, hogy eltávolítaná az üzenetsorból. Ehhez hívja meg a **PeekMessage** módszert.

    // Peek at the next message
    CloudQueueMessage peekedMessage = messageQueue.PeekMessage();

## <a name="read-and-remove-a-message-in-a-queue"></a>Olvassa el, és távolítsa el az üzenet a várólistában lévő
A kód eltávolítása (kivétele az üzenetsorból) egy üzenetet az üzenetsorból két lépésben.

1. Hívás **GetMessage** számára a következő üzenet jelenik meg a sorhoz. A **GetMessage** módszerrel lekért üzenet láthatatlanná válik az adott üzenetsorban található üzeneteket olvasó többi kód számára. Alapértelmezés szerint az üzenet 30 másodpercig marad láthatatlan.
2. Szeretné távolítani az üzenetet az üzenetsorból, hívja **DeleteMessage**.

Az üzenetek kétlépéses eltávolítása lehetővé teszi, hogy ha a kód hardver- vagy szoftverhiba miatt nem tud feldolgozni egy üzenetet, a kód egy másik példánya megkaphassa ugyanazt az üzenetet, és újra megpróbálkozhasson a feldolgozásával. A következő kódot a hívások **DeleteMessage** jobb gombbal az üzenet feldolgozása után.

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

    // Process the message in less than 30 seconds

    // Then delete the message.
    await messageQueue.DeleteMessage(retrievedMessage);


## <a name="use-additional-options-to-process-and-remove-queue-messages"></a>További beállítások segítségével feldolgozni, és távolítsa el a várólista-üzenetek
Két módon szabhatja testre az üzenetek lekérését egy üzenetsorból.

* Az üzenetkötegek (legfeljebb 32) kérheti le.
* Beállíthat egy hosszabb vagy rövidebb láthatatlansági időkorlátot, így a kódnak hosszabb vagy rövidebb idő teljesen feldolgozni az egyes üzeneteket. Az alábbi példakód a **GetMessages** módszer segítségével egyszerre 20 üzenetet kér le. Ezután minden üzenetet feldolgoz egy **foreach** hurok segítségével. Mindemellett a láthatatlansági időkorlátot minden üzenethez öt percre állítja be. Vegye figyelembe, hogy az 5 perc minden üzenetnél ugyanakkor kezdődik, tehát 5 perccel a **GetMessages** hívása után a nem törölt üzenetek újra láthatóvá válnak.

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

## <a name="use-the-async-await-pattern-with-common-azure-queue-apis"></a>Az Async-Await mintázat használata közös Azure várólista API-k
Ez a példa bemutatja, hogyan használható az Async-Await mintázat a közös Azure várólista API-k. A minta meghívja az adott módszerek aszinkron verzióját, ez látható által a **aszinkron** az egyes módszerek utáni javítás. Egy aszinkron metódus használatakor az async-await mintázat felfüggeszti a helyi végrehajtást a hívás befejeződéséig. Ez a viselkedés lehetővé teszi, hogy az aktuális szál más a munkáját, amely segít a elkerülhetők a szűk keresztmetszetek, és növeli az alkalmazás általános válaszkészsége. További információk az Async-Await mintázat használatáról .NET-keretrendszerben: [Async and Await (C# and Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx) (Async és Await (C# és Visual Basic)).

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

