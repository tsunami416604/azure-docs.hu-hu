---
title: Események küldése és fogadása az Azure Event Hubs-tól a .NET használatával (legújabb)
description: Ez a cikk egy forgatókönyvet biztosít egy .NET Core alkalmazás létrehozásához, amely eseményeket küld/fogad az Azure Event Hubs-ba/a legújabb Azure.Messaging.EventHubs csomag használatával.
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
ms.openlocfilehash: 40d291ee17f1fdaf819d70daade735e152df8f71
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548520"
---
# <a name="send-events-to-and-receive-events-from-azure-event-hubs---net-core-azuremessagingeventhubs"></a>Események küldése és események fogadása az Azure Event Hubs-ból – .NET Core (Azure.Messaging.EventHubs) 
Ez a rövid útmutató bemutatja, hogyan küldhet eseményeket egy eseményközpontba, és hogyan fogadhat eseményeket az **Azure.Messaging.EventHubs** .NET Core függvénytár használatával. 

> [!IMPORTANT]
> Ez a rövid útmutató az új **Azure.Messaging.EventHubs-könyvtárat** használja. A régi **Microsoft.Azure.EventHubs-könyvtárat** használó rövid útmutatót az [Események küldése és fogadása a Microsoft.Azure.EventHubs-könyvtár használatával című témakörben](event-hubs-dotnet-standard-getstarted-send.md)található. 



## <a name="prerequisites"></a>Előfeltételek
Ha most írja be az Azure Event Hubs szolgáltatást, olvassa el [az Eseményközpontok áttekintése című témakört,](event-hubs-about.md) mielőtt ezt a rövid útmutatót elvégezné. 

A rövid útmutató végrehajtásához a következő előfeltételekre van szükség:

- **Microsoft Azure-előfizetés**. Az Azure-szolgáltatások, köztük az Azure Event Hubs használatához előfizetésre van szüksége.  Ha nem rendelkezik meglévő Azure-fiókkal, regisztrálhat egy [ingyenes próbaverzióra,](https://azure.microsoft.com/free/) vagy használhatja az MSDN-előfizetői előnyöket [a fiók létrehozásakor.](https://azure.microsoft.com)
- **Microsoft Visual Studio 2019**. Az Azure Event Hubs ügyfélkódtár a C 8.0-s számban bevezetett új funkciókat használja.  A könyvtár továbbra is használható a C# régebbi verzióival, de néhány funkciója nem lesz elérhető.  A szolgáltatások engedélyezéséhez [a .NET Core 3.0-s verzióját kell megcéloznia,](/dotnet/standard/frameworks#how-to-specify-target-frameworks) vagy meg kell adnia a használni kívánt [nyelvi verziót](/dotnet/csharp/language-reference/configure-language-version#override-a-default) (8.0 vagy újabb). Ha a Visual Studio-t használja, a Visual Studio 2019 előtti verziók nem kompatibilisek a C# 8.0-s projektek létrehozásához szükséges eszközökkel. A Visual Studio 2019, beleértve az ingyenes közösségi kiadást, [itt](https://visualstudio.microsoft.com/vs/) tölthető le
- **Hozzon létre egy Eseményközpontok névterét és egy eseményközpontot.** Az első lépés [az,](https://portal.azure.com) hogy az Azure Portal használatával hozzon létre egy Event Hubs típusú névteret, és szerezze be az alkalmazás által az eseményközponttal való kommunikációhoz szükséges felügyeleti hitelesítő adatokat. Névtér és eseményközpont létrehozásához kövesse a [cikkben](event-hubs-create.md)található eljárást. Ezután az **Event Hubs névtér kapcsolati karakterláncát a** cikk utasításainak követésével kapja [meg: Kapcsolati karakterlánc beszereznie](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). A kapcsolati karakterláncot később használja ebben a rövid útmutatóban.

## <a name="send-events"></a>Események küldése 
Ez a szakasz bemutatja, hogyan hozhat létre .NET Core konzolalkalmazást események et egy eseményközpontba. 

### <a name="create-a-console-application"></a>Konzolalkalmazás létrehozása

1. Indítsa el a Visual Studio 2019-et. 
1. Válassza **az Új projekt létrehozása**lehetőséget. 
1. Az **Új projekt létrehozása** párbeszédpanelen tegye a következő lépéseket: Ha ez a párbeszédpanel nem jelenik meg, válassza a menü **Fájl parancsát,** válassza az **Új**lehetőséget, majd a **Project**lehetőséget. 
    1. Válassza a **C#** lehetőséget a programozási nyelvhez.
    1. Válassza a **Konzol** lehetőséget az alkalmazás típusához. 
    1. Válassza a **Konzolalkalmazás (.NET Core)** elemet az eredménylistából. 
    1. Ezután válassza a **Tovább gombot.** 

        ![A New project (Új projekt) párbeszédpanel](./media/getstarted-dotnet-standard-send-v2/new-send-project.png)    
1. Írja be az **EventHubsSender** értéket a projekt nevéhez, az **EventHubsQuickStart** nevet a megoldás nevéhez, majd válassza az **OK** gombot a projekt létrehozásához. 

    ![C# > konzolalkalmazás](./media/getstarted-dotnet-standard-send-v2/project-solution-names.png)

### <a name="add-the-event-hubs-nuget-package"></a>Az Event Hubs NuGet-csomag hozzáadása

1. A menüből válassza az **Eszközök** > **NuGet csomagkezelő** > **csomagkezelő konzolját.** 
1. Futtassa a következő parancsot az **Azure.Messaging.EventHubs** NuGet csomag telepítéséhez:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```


### <a name="write-code-to-send-messages-to-the-event-hub"></a>Kód írása az üzenetek eseményközpontba való küldésére

1. Adja hozzá `using` a következő állításokat a **Program.cs** fájl tetejéhez:

    ```csharp
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Producer;
    ```

2. Adja hozzá az `Program` állandókat az Eseményközpontok kapcsolati karakterláncának és az eseményközpont nevének osztályához. Cserélje le a helyőrzőket zárójelben az eseményközpont létrehozásakor kapott megfelelő értékekre. Győződjön meg arról, hogy az `{Event Hubs namespace connection string}` a névtérszintű kapcsolati sztring, és nem az eseményközpont sztringje. 

    ```csharp
    private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
    private const string eventHubName = "<EVENT HUB NAME>";
    ```

3. Cserélje `Main` le a `async Main` módszert a következő módszerrel. A részleteket lásd a kódmegjegyzésekben. 

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
5. Építse fel a projektet, és győződjön meg arról, hogy nincsenek hibák.
6. Futtassa a programot, és várja meg a megerősítő üzenetet. 
7. Az Azure Portalon ellenőrizheti, hogy az eseményközpont megkapta-e az üzeneteket. Váltson **az Üzenetek** nézetre a **Metrikák** szakaszban. A lap frissítése a diagram frissítéséhez. Eltarthat néhány másodpercig, amíg megis szállhat meg, hogy az üzenetek érkeztek. 

    [![Annak ellenőrzése, hogy az eseményközpont megkapta-e az üzeneteket](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > A teljes forráskódot további tájékoztató megjegyzésekkel együtt tekintse meg [ezt a fájlt a GitHubon](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample03_PublishAnEventBatch.cs)

## <a name="receive-events"></a>Események fogadása
Ez a szakasz azt mutatja be, hogyan írható meg egy .NET Core konzolalkalmazás, amely eseményközpontból fogad üzeneteket egy eseményprocesszor használatával. Az eseményfeldolgozó leegyszerűsíti az események fogadását az eseményközpontokból az állandó ellenőrzőpontok és az ilyen eseményközpontokból érkező párhuzamos fogadások kezelésével. Egy eseményfeldolgozó egy adott eseményközponthoz és egy fogyasztói csoporthoz van társítva. Az eseményközpontban több partícióról is fogad eseményeket, és átadja azokat egy kezelődelegáltnak a megadott kód használatával történő feldolgozáshoz. 


> [!NOTE]
> Ha az Azure Stack Hubon fut, akkor előfordulhat, hogy a storage blob SDK egy másik verzióját támogatja, mint az Azure-ban általában elérhető. Ha például az [Azure Stack Hub 2002-es verzióján](https://docs.microsoft.com/azure-stack/user/event-hubs-overview)fut, a Storage szolgáltatás legmagasabb elérhető verziója a 2017-11-09-es verzió. Ebben az esetben az ebben a szakaszban ismertetett lépések mellett kódot is hozzá kell adnia a Storage service API 2017-11-09-es verziójának célzásához. Egy adott Storage API-verzió célzásával kapcsolatos példát tekintse meg [ezt a mintát a GitHubon.](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs) Az Azure Stack Hubon támogatott Azure Storage-szolgáltatásverzióiról az [Azure Stack Hub storage: Különbségek és szempontok](https://docs.microsoft.com/azure-stack/user/azure-stack-acs-differences)című további információkért tekintse meg.

### <a name="create-an-azure-storage-and-a-blob-container"></a>Hozzon létre egy Azure Storage-tárolót és egy blobtárolót
Ebben a rövid útmutatóban az Azure Storage-t használja ellenőrzőpont-tárolóként. Az Azure Storage-fiók létrehozásához kövesse az alábbi lépéseket. 

1. [Azure Storage-fiók létrehozása](/azure/storage/common/storage-account-create?tabs=azure-portal)
2. [Blobtároló létrehozása](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [A kapcsolati karakterlánc beszereznie a tárfiókhoz](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

    Jegyezze fel a kapcsolati karakterláncot és a tároló nevét. A fogadási kódban fogja használni őket. 


### <a name="create-a-project-for-the-receiver"></a>Projekt létrehozása a fogadó számára

1. A Megoldáskezelő ablakban kattintson a jobb gombbal az **EventHubQuickStart** megoldásra, mutasson a **Hozzáadás**pontra, és válassza az **Új projekt**parancsot. 
1. Válassza a **Console App (.NET Core)** lehetőséget, majd a **Tovább**gombot. 
1. Írja be az **EventHubsReceiver értéket** a **projekt nevéhez,** és válassza a **Létrehozás gombot.** 

### <a name="add-the-event-hubs-nuget-package"></a>Az Event Hubs NuGet-csomag hozzáadása

1. A menüből válassza az **Eszközök** > **NuGet csomagkezelő** > **csomagkezelő konzolját.** 
1. Futtassa a következő parancsot az **Azure.Messaging.EventHubs** NuGet csomag telepítéséhez:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
1. Futtassa a következő parancsot az **Azure.Messaging.EventHubs.Processor** NuGet csomag telepítéséhez:

    ```cmd
    Install-Package Azure.Messaging.EventHubs.Processor
    ```    

### <a name="update-the-main-method"></a>A fő módszer frissítése 

1. Adja hozzá `using` a következő állításokat a **Program.cs** fájl tetején.

    ```csharp
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Storage.Blobs;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Consumer;
    using Azure.Messaging.EventHubs.Processor;
    ```
1. Adja hozzá az `Program` állandókat az Eseményközpontok kapcsolati karakterláncának és az eseményközpont nevének osztályához. Cserélje le a helyőrzőket zárójelben az eseményközpont létrehozásakor kapott megfelelő értékekre. Cserélje le a helyőrzőket zárójelben az eseményközpont és a tárfiók (hozzáférési kulcsok - elsődleges kapcsolati karakterlánc) létrehozásakor kapott megfelelő értékekre. Győződjön meg arról, hogy az `{Event Hubs namespace connection string}` a névtérszintű kapcsolati sztring, és nem az eseményközpont sztringje.

    ```csharp
        private const string ehubNamespaceConnectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
        private const string eventHubName = "<EVENT HUB NAME>";
        private const string blobStorageConnectionString = "<AZURE STORAGE CONNECTION STRING>";
        private const string blobContainerName = "<BLOB CONTAINER NAME>";
    ```
3. Cserélje `Main` le a `async Main` módszert a következő módszerrel. A részleteket lásd a kódmegjegyzésekben. 

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
1. Most adja hozzá a következő esemény- és hibakezelő metódusokat az osztályhoz. 

    ```csharp
        static Task ProcessEventHandler(ProcessEventArgs eventArgs)
        { 
            // Write the body of the event to the console window
            Console.WriteLine("\tReceived event: {0}", Encoding.UTF8.GetString(eventArgs.Data.Body.ToArray())); 
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
1. Építse fel a projektet, és győződjön meg arról, hogy nincsenek hibák.

    > [!NOTE]
    > A teljes forráskódot további tájékoztató megjegyzésekkel együtt tekintse meg [ezt a fájlt a GitHubon.](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample01_HelloWorld.cs)
6. Futtassa a fogadóalkalmazást. 
1. Meg kell jelennie egy üzenetnek, hogy az esemény megérkezett. 

    ![Esemény érkezett](./media/getstarted-dotnet-standard-send-v2/event-received.png)

    Ezek az események azt a három eseményt, amelyet korábban küldött az eseményközpontnak a feladó program futtatásával. 


## <a name="next-steps"></a>További lépések
Tekintse meg a mintákat a GitHubon. 

- [Az Event Hubs-minták a GitHubon](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [Eseményprocesszor-minták a GitHubon](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples)
- [Szerepköralapú hozzáférés-vezérlési (RBAC) minta](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
