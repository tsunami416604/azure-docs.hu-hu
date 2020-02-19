---
title: Események küldése vagy fogadása az Azure Event Hubs .NET használatával (legújabb)
description: Ez a cikk egy olyan .NET Core-alkalmazás létrehozását ismerteti, amely az Azure-Event Hubs a legújabb Azure. Messaging. EventHubs csomag használatával küld/fogad eseményeket.
services: event-hubs
documentationcenter: na
author: spelluru
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: d7d697e3ea4b1b683275d53f6e407396f474b37b
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462020"
---
# <a name="send-events-to-and-receive-events-from-azure-event-hubs---net-core-azuremessagingeventhubs"></a>Események küldése és fogadása az Azure Event Hubs-.NET Core-ból (Azure. Messaging. EventHubs) 
Ez a rövid útmutató bemutatja, hogyan lehet eseményeket küldeni és fogadni az Event hub eseményeiről az **Azure. Messaging. EventHubs** .net Core könyvtár használatával. 

> [!IMPORTANT]
> Ez a rövid útmutató az új **Azure. Messaging. EventHubs** könyvtárat használja. A régi **Microsoft. Azure. EventHubs** könyvtárat használó gyors útmutató: [események küldése és fogadása a Microsoft. Azure. EventHubs könyvtár használatával](event-hubs-dotnet-standard-getstarted-send.md). 



## <a name="prerequisites"></a>Előfeltételek
Ha még nem ismeri az Azure Event Hubs-t, a rövid útmutató elvégzése előtt tekintse meg a [Event Hubs áttekintése](event-hubs-about.md) című témakört. 

A rövid útmutató elvégzéséhez a következő előfeltételek szükségesek:

- **Microsoft Azure előfizetés**. Az Azure-szolgáltatások, például az Azure Event Hubs használatához előfizetésre van szükség.  Ha még nem rendelkezik Azure-fiókkal, regisztrálhat az [ingyenes próbaverzióra](https://azure.microsoft.com/free/) , vagy a [fiók létrehozásakor](https://azure.microsoft.com)használhatja az MSDN-előfizetői előnyeit.
- **Microsoft Visual Studio 2019**. Az Azure Event Hubs ügyféloldali kódtár a 8,0-es C# verzióban bevezetett új funkciókat használja.  Továbbra is használhatja a könyvtárat a régebbi verzióival C#, de néhány funkciója nem érhető el.  Ezen funkciók engedélyezéséhez a .NET Core 3,0-es verzióját kell [megcéloznia](/dotnet/standard/frameworks#how-to-specify-target-frameworks) , vagy [meg kell adnia a használni kívánt nyelvi verziót](/dotnet/csharp/language-reference/configure-language-version#override-a-default) (8,0 vagy újabb). Ha a Visual studiót használja, a Visual Studio 2019 előtti verziói nem kompatibilisek a 8,0-projektek létrehozásához C# szükséges eszközökkel. A Visual Studio 2019, beleértve az ingyenes Community Editiont is, [itt](https://visualstudio.microsoft.com/vs/) tölthető le
- **Hozzon létre egy Event Hubs névteret és egy Event hubot**. Első lépésként az [Azure Portalon](https://portal.azure.com) hozzon létre egy Event Hubs típusú névteret, és szerezze be az alkalmazása és az eseményközpont közötti kommunikációhoz szükséges felügyeleti hitelesítő adatokat. A névtér és az Event hub létrehozásához kövesse az [ebben a cikkben](event-hubs-create.md)ismertetett eljárást. Ezután szerezze be a **Event Hubs névtérhez tartozó kapcsolatok karakterláncot** a cikk utasításait követve: a [kapcsolatok karakterláncának beolvasása](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). A rövid útmutató későbbi részében használja a kapcsolatok karakterláncát.

## <a name="send-events"></a>Események küldése 
Ebből a szakaszból megtudhatja, hogyan hozhat létre olyan .NET Core Console-alkalmazást, amely eseményeket küld az Event hub-nak. 

### <a name="create-a-console-application"></a>Konzolalkalmazás létrehozása

1. Indítsa el a Visual Studio 2019 alkalmazást. 
1. Válassza **az új projekt létrehozása**lehetőséget. 
1. A **create a New Project (új projekt létrehozása** ) párbeszédpanelen hajtsa végre a következő lépéseket: Ha nem látja ezt a párbeszédpanelt, válassza a **fájl** lehetőséget a menüben, majd válassza az **új**, majd a **projekt**elemet. 
    1. Válassza **C#** ki a programozási nyelvet.
    1. Válassza ki a **konzolt** az alkalmazás típusához. 
    1. Válassza ki a **Console app (.net Core)** elemet az eredmények listából. 
    1. Ezután válassza a **Tovább** lehetőséget. 

        ![A New project (Új projekt) párbeszédpanel](./media/getstarted-dotnet-standard-send-v2/new-send-project.png)    
1. Adja meg a **EventHubsSender** a projekt neve mezőben, a **EventHubsQuickStart** , majd kattintson az **OK** gombra a projekt létrehozásához. 

    ![C#> Console-alkalmazás](./media/getstarted-dotnet-standard-send-v2/project-solution-names.png)

### <a name="add-the-event-hubs-nuget-package"></a>Az Event Hubs NuGet-csomag hozzáadása

1. Válassza az **eszközök** > **NuGet Package Manager** > **csomagkezelő konzol** lehetőséget a menüből. 
1. Futtassa az alábbi parancsot az **Azure. Messaging. EventHubs** NuGet-csomag telepítéséhez:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```


### <a name="write-code-to-send-messages-to-the-event-hub"></a>Kód írása az üzenetek eseményközpontba való küldésére

1. Adja hozzá a következő `using` utasításokat a **program.cs** fájl elejéhez:

    ```csharp
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Producer;
    ```

2. Adja hozzá az állandókat a `Program` osztályhoz a Event Hubs kapcsolati karakterlánc és az Event hub neve között. Cserélje le a zárójelben lévő helyőrzőket az Event hub létrehozásakor kapott megfelelő értékekre. Győződjön meg arról, hogy az `{Event Hubs namespace connection string}` a névtérszintű kapcsolati sztring, és nem az eseményközpont sztringje. 

    ```csharp
    private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
    private const string eventHubName = "<EVENT HUB NAME>";
    ```

3. Cserélje le a `Main` metódust a következő `async Main` metódusra. A részletekért tekintse meg a kód megjegyzéseit. 

    ```csharp
        static async Task Main()
        {
            // Create a producer client that you can use to send events to an event hub
            await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
            {
                // Create a batch of events 
                using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

                // Add events to the batch. An event is a represented by a collection of bytes and metadata. 
                eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First event")));
                eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second event")));
                eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Third event")));

                // Use the producer client to send the batch of events to the event hub
                await producerClient.SendAsync(eventBatch);
                Console.WriteLine("A batch of 3 events has been published.");
            }
        }
    ```
5. Hozza létre a projektet, és ellenőrizze, hogy nincsenek-e hibák.
6. Futtassa a programot, és várjon a megerősítő üzenetre. 
7. A Azure Portalon ellenőrizheti, hogy az Event hub fogadta-e az üzeneteket. Váltson az **üzenetek** nézetre a **metrikák** szakaszban. Frissítse a lapot a diagram frissítéséhez. Eltarthat néhány másodpercig, amíg meg nem jelenik az üzenetek fogadása. 

    [![annak ellenőrzése, hogy az Event hub fogadta-e az üzeneteket](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > A teljes forráskódot további tájékoztató megjegyzésekkel tekintheti [meg a githubon](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample03_PublishAnEventBatch.cs) .

## <a name="receive-events"></a>Események fogadása
Ez a szakasz bemutatja, hogyan írhat olyan .NET Core Console-alkalmazást, amely az Event hub-ból származó üzeneteket fogad egy eseményvezérelt processzor használatával. Az esemény-feldolgozó leegyszerűsíti az események fogadását az Event hubokból az állandó ellenőrzőpontok és a párhuzamos fogadások kezelésével az adott esemény hubokból. Egy esemény-feldolgozó társítva van egy adott Event hubhoz és egy fogyasztói csoporthoz. Az Event hub több partíciójának eseményeit fogadja el, amelyek az Ön által megadott kóddal való feldolgozásra szolgálnak. 


### <a name="create-an-azure-storage-and-a-blob-container"></a>Azure Storage és blob-tároló létrehozása
Ebben a rövid útmutatóban az Azure Storage-t használja ellenőrzőpont-tárolóként. Egy Azure Storage-fiók létrehozásához kövesse az alábbi lépéseket. 

1. [Azure Storage-fiók létrehozása](/azure/storage/common/storage-account-create?tabs=azure-portal)
2. [BLOB-tároló létrehozása](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [A Storage-fiókhoz tartozó kapcsolódási karakterlánc lekérése](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

    Jegyezze fel a kapcsolatok sztringjét és a tároló nevét. Ezeket a fogadási kódban fogja használni. 


### <a name="create-a-project-for-the-receiver"></a>Projekt létrehozása a fogadó számára

1. A Megoldáskezelő ablakban kattintson a jobb gombbal a **EventHubQuickStart** -megoldásra, mutasson a **Hozzáadás**elemre, majd válassza az **új projekt**lehetőséget. 
1. Válassza ki a **Console app (.net Core)** elemet, majd kattintson a **Tovább gombra**. 
1. Adja meg a **EventHubsReceiver** a **projekt neveként**, majd válassza a **Létrehozás**lehetőséget. 

### <a name="add-the-event-hubs-nuget-package"></a>Az Event Hubs NuGet-csomag hozzáadása

1. Válassza az **eszközök** > **NuGet Package Manager** > **csomagkezelő konzol** lehetőséget a menüből. 
1. Futtassa az alábbi parancsot az **Azure. Messaging. EventHubs** NuGet-csomag telepítéséhez:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
1. Futtassa az alábbi parancsot az **Azure. Messaging. EventHubs. Processor** NuGet csomag telepítéséhez:

    ```cmd
    Install-Package Azure.Messaging.EventHubs.Processor
    ```    

### <a name="update-the-main-method"></a>A Main metódus frissítése 

1. Adja hozzá a következő `using` utasításokat a **program.cs** fájl elejéhez.

    ```csharp
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Storage.Blobs;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Consumer;
    using Azure.Messaging.EventHubs.Processor;
    ```
1. Adja hozzá az állandókat a `Program` osztályhoz a Event Hubs kapcsolati karakterlánc és az Event hub neve között. Cserélje le a zárójelben lévő helyőrzőket az Event hub létrehozásakor kapott megfelelő értékekre. Cserélje le a zárójelben lévő helyőrzőket az Event hub és a Storage-fiók létrehozásakor kapott megfelelő értékekre (hozzáférési kulcsok – elsődleges kapcsolati sztring). Győződjön meg arról, hogy az `{Event Hubs namespace connection string}` a névtérszintű kapcsolati sztring, és nem az eseményközpont sztringje.

    ```csharp
        private const string ehubNamespaceConnectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
        private const string eventHubName = "<EVENT HUB NAME>";
        private const string blobStorageConnectionString = "<AZURE STORAGE CONNECTION STRING>";
        private const string blobContainerName = "<BLOB CONTAINER NAME>";
    ```
3. Cserélje le a `Main` metódust a következő `async Main` metódusra. A részletekért tekintse meg a kód megjegyzéseit. 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;

            // Create a blob container client that the event processor will use 
            BlobContainerClient storageClient = new BlobContainerClient(blobStorageConnectionString, blobContainerName);

            // Create an event processor client to process events in the event hub
            EventProcessorClient processor = new EventProcessorClient(storageClient, consumerGroup, ehubNamespaceConnectionString, eventHubName);

            // Register handlers for processing events and handling errors
            processor.ProcessEventAsync += ProcessEventHandler;
            processor.ProcessErrorAsync += ProcessErrorHandler;

            // Start the processing
            await processor.StartProcessingAsync();

            // Wait for 10 seconds for the events to be processed
            await Task.Delay(TimeSpan.FromSeconds(10));

            // Stop the processing
            await processor.StopProcessingAsync();
        }    
    ```
1. Most adja hozzá a következő eseményt és hibakezelő metódusokat a osztályhoz. 

    ```csharp
        static Task ProcessEventHandler(ProcessEventArgs eventArgs)
        { 
            // Write the body of the event to the console window
            Console.WriteLine("\tRecevied event: {0}", Encoding.UTF8.GetString(eventArgs.Data.Body.ToArray())); 
            return Task.CompletedTask; 
        }

        static Task ProcessErrorHandler(ProcessErrorEventArgs eventArgs)
        {
            // Write details about the error to the console window
            Console.WriteLine($"\tPartition '{ eventArgs.PartitionId}': an unhandled exception was encountered. This was not expected to happen.");
            Console.WriteLine(eventArgs.Exception.Message);
            return Task.CompletedTask;
        }    
    ```
1. Hozza létre a projektet, és ellenőrizze, hogy nincsenek-e hibák.

    > [!NOTE]
    > A teljes forráskódot további tájékoztató megjegyzésekkel tekintheti [meg a githubon](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample01_HelloWorld.cs).
6. Futtassa a fogadó alkalmazást. 
1. Ekkor megjelenik egy üzenet, amely szerint az esemény érkezett. 

    ![Esemény érkezett](./media/getstarted-dotnet-standard-send-v2/event-received.png)

    Ezek az események a küldő program futtatásával korábban a következő három eseményt küldik el az Event hub számára. 


## <a name="next-steps"></a>Következő lépések
Tekintse meg a mintákat a GitHubon. 

- [Event Hubs minták a GitHubon](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [Event Processor-minták a GitHubon](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples)
- [Szerepköralapú hozzáférés-vezérlés (RBAC) minta](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
