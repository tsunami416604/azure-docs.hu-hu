---
title: A várólista-tárolás használatának első lépései a Visual Studio használatával (felhőszolgáltatások)
description: Az Azure Queue storage használatának első lépései egy felhőalapú szolgáltatásprojektben a Visual Studióban, miután a Visual Studio csatlakoztatott szolgáltatásait használva egy tárfiókhoz csatlakoztak
services: storage
author: ghogen
manager: jillfra
ms.assetid: da587aac-5e64-4e9a-8405-44cc1924881d
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 603bb2b9a862ad4ed2cbde63e2d82b9a82fbeaa1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298781"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Ismerkedés az Azure Queue Storage-hez és a Visual Studióhoz kapcsolódó szolgáltatásokkal (felhőszolgáltatás-projektek)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Áttekintés
Ez a cikk azt ismerteti, hogyan kezdheti el használni az Azure Queue storage-t a Visual Studióban, miután létrehozott vagy hivatkozott egy Azure-tárfiókra egy felhőszolgáltatási projektben a Visual Studio **Csatlakoztatott szolgáltatások hozzáadása** párbeszédpanelen.

Megmutatjuk, hogyan hozhat létre egy várólistát a kódban. Azt is bemutatjuk, hogyan hajthatja végre az alapvető várólista-műveleteket, például a várólista-üzenetek hozzáadását, módosítását, olvasását és eltávolítását. A minták C# kódban vannak megírva, és a [Microsoft Azure Storage Client Library for .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)programot használják.

A **Csatlakoztatott szolgáltatások hozzáadása** művelet telepíti a megfelelő NuGet-csomagokat az Azure storage eléréséhez a projektben, és hozzáadja a tárfiók kapcsolati karakterláncát a projekt konfigurációs fájljaihoz.

* A várólisták kódban történő kezelésére vonatkozó további információkért olvassa el az [Azure Queue storage használatával a .NET használatával](../storage/queues/storage-dotnet-how-to-use-queues.md) című témakört.
* Az Azure Storage szolgáltatással kapcsolatos általános tudnivalókat a [Storage dokumentációjában](https://azure.microsoft.com/documentation/services/storage/) találja.
* Az Azure felhőszolgáltatásaival kapcsolatos általános tudnivalókat a [Cloud Services dokumentációjában](https://azure.microsoft.com/documentation/services/cloud-services/) találja.
* Az [ASP.NET](https://www.asp.net) alkalmazások programozásáról ASP.NET programozásáról ASP.NET című témakörben talál további információt.

Az Azure Queue Storage szolgáltatás üzenetek nagy számban történő tárolására szolgál, amelyek HTTP- vagy HTTPS-kapcsolattal, hitelesített hívásokon keresztül a világon bárhonnan elérhetők. Egyetlen üzenetsor akár 64 KB méretű is lehet, és a tárfiók maximális kapacitásán belül több millió üzenetet tartalmazhat.

## <a name="access-queues-in-code"></a>Hozzáférési várólisták kódban
A Visual Studio Cloud Services-projektek várólistáinak eléréséhez a következő elemeket kell megadnia minden Olyan C# forrásfájlhoz, amely hozzáfér az Azure Queue storage-hoz.

1. Győződjön meg arról, hogy a C# fájl tetején lévő névtérdeklarációk tartalmazzák ezeket a utasítások **használatával.**
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;
2. Szerezzen be egy **CloudStorageAccount** objektumot, amely a tárfiók adatait jelöli. A következő kód használatával lejuthat a tárolási kapcsolat ihúr- és tárfiók-adataihoz az Azure szolgáltatáskonfigurációjából.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
3. A storage-fiókban lévő várólista-objektumokra való hivatkozáshoz szerezzen be egy **CloudQueueClient** objektumot.  
   
        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
4. CloudQueue **CloudQueue** objektum beszereznie egy adott várólistára való hivatkozáshoz.
   
        // Get a reference to a queue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");

**MEGJEGYZÉS:** Használja az összes fenti kódot a kód előtt a következő mintákban.

## <a name="create-a-queue-in-code"></a>Várólista létrehozása a kódban
A várólista kódjának létrehozásához egyszerűen adjon hozzá egy hívást a **CreateIfNotExists**.

    // Create the CloudQueue if it does not exist
    messageQueue.CreateIfNotExists();

## <a name="add-a-message-to-a-queue"></a>Üzenet hozzáadása várólistához
Ha egy üzenetet szeretne beszúrni egy meglévő várólistába, hozzon létre egy új **CloudQueueMessage** objektumot, majd hívja meg az **AddMessage** metódust.

A **CloudQueueMessage** objektum létrehozható karakterláncból (UTF-8 formátumban) vagy bájttömbből.

Íme egy példa, amely beilleszti a "Hello, World" üzenetet.

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    messageQueue.AddMessage(message);

## <a name="read-a-message-in-a-queue"></a>Üzenet olvasása a várólistában
Egy üzenetsor elején található üzenetbe anélkül is bepillanthat, hogy eltávolítaná az üzenetsorból. Ehhez hívja meg a **PeekMessage** módszert.

    // Peek at the next message
    CloudQueueMessage peekedMessage = messageQueue.PeekMessage();

## <a name="read-and-remove-a-message-in-a-queue"></a>Üzenet olvasása és eltávolítása a várólistában
A kód két lépésben eltávolíthatja (várólistán maradhat) egy üzenetet.

1. Hívja meg a **GetMessage-et,** hogy megkapja a következő üzenetet a várólistában. A **GetMessage** módszerrel lekért üzenet láthatatlanná válik az adott üzenetsorban található üzeneteket olvasó többi kód számára. Alapértelmezés szerint az üzenet 30 másodpercig marad láthatatlan.
2. Az üzenet várólistából való eltávolításának befejezéséhez hívja meg a **DeleteMessage**.

Az üzenetek kétlépéses eltávolítása lehetővé teszi, hogy ha a kód hardver- vagy szoftverhiba miatt nem tud feldolgozni egy üzenetet, a kód egy másik példánya megkaphassa ugyanazt az üzenetet, és újra megpróbálkozhasson a feldolgozásával. A következő kód az üzenet feldolgozása után azonnal meghívja a **DeleteMessage-et.**

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

    // Process the message in less than 30 seconds

    // Then delete the message.
    await messageQueue.DeleteMessage(retrievedMessage);


## <a name="use-additional-options-to-process-and-remove-queue-messages"></a>Várólista-üzenetek feldolgozása és eltávolítása további beállításokkal
Két módon szabhatja testre az üzenetek lekérését egy üzenetsorból.

* Egy köteg üzenetet (legfeljebb 32- ig) kaphat.
* Hosszabb vagy rövidebb láthatatlansági időhosszabbítást is beállíthat, így a kódnak többé-kevésbé időre van szüksége az egyes üzenetek teljes feldolgozásához. Az alábbi példakód a **GetMessages** módszer segítségével egyszerre 20 üzenetet kér le. Ezután minden üzenetet feldolgoz egy **foreach** hurok segítségével. Mindemellett a láthatatlansági időkorlátot minden üzenethez öt percre állítja be. Vegye figyelembe, hogy az 5 perc minden üzenetnél ugyanakkor kezdődik, tehát 5 perccel a **GetMessages** hívása után a nem törölt üzenetek újra láthatóvá válnak.

Például:

    foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.

        // Then delete the message after processing
        messageQueue.DeleteMessage(message);

    }

## <a name="get-the-queue-length"></a>Az üzenetsor hosszának lekérése
Megbecsülheti egy üzenetsorban található üzenetek számát. A **FetchAttributes** módszer lekéri a Queue szolgáltatásból az üzenetsorra vonatkozó attribútumokat, amelyek között megtalálható az üzenetek száma is. Az **ApproximateMethodCount** tulajdonság a **FetchAttributes** metódus által beolvasott utolsó értéket adja vissza, a Várólista szolgáltatás hívása nélkül.

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="use-the-async-await-pattern-with-common-azure-queue-apis"></a>Az Async-Await minta használata gyakori Azure-várólista API-kkal
Ez a példa bemutatja, hogyan használhatja az Async-Await mintát a közös Azure Queue API-kkal. A minta meghívja az egyes metódusok aszinkron verzióját, ezt az egyes módszerek **Async** utáni javítása is láthatja. Aszinkron metódus használata esetén az async-await minta felfüggeszti a helyi végrehajtást, amíg a hívás befejeződik. Ez a viselkedés lehetővé teszi, hogy az aktuális szál más munkát, amely segít elkerülni a teljesítmény szűk keresztmetszetek, és javítja az általános válaszkészséget az alkalmazás. További információk az Async-Await mintázat használatáról .NET-keretrendszerben: [Async and Await (C# and Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx) (Async és Await (C# és Visual Basic)).

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

