---
title: Üzenetek küldése Azure Service Bus témakörökbe az Azure-Messaging-servicebus használatával
description: Ez a rövid útmutató bemutatja, hogyan küldhet üzeneteket Azure Service Bus témakörökbe az Azure-Messaging-servicebus csomag használatával.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 11/13/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 60504bcf9e2c3f9460eee9a2e72d18767c0cfa71
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/21/2021
ms.locfileid: "98631674"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-net"></a>Üzenetek küldése egy Azure Service Bus témakörnek, és üzenetek fogadása az előfizetésből a témakörbe (.NET)
Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre olyan .NET Core Console-alkalmazást, amely üzeneteket küld egy Service Bus témakörnek, és üzeneteket fogad a témakör előfizetéséről. 

> [!Important]
> Ez a rövid útmutató az új **Azure. Messaging. ServiceBus** csomagot használja. A régi Microsoft. Azure. ServiceBus csomagot használó gyors útmutató: [üzenetek küldése és fogadása a Microsoft. Azure. ServiceBus csomag használatával](service-bus-dotnet-how-to-use-topics-subscriptions-legacy.md).

## <a name="prerequisites"></a>Előfeltételek

- [Visual Studio 2019](https://www.visualstudio.com/vs)
- Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja [Visual Studio-vagy MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) , vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Kövesse a rövid útmutató lépéseit [: a Azure Portal használatával hozzon létre egy Service Bus témakört és előfizetéseket a témakörbe](service-bus-quickstart-topics-subscriptions-portal.md). Jegyezze fel a kapcsolatok karakterláncát, a témakör nevét és az előfizetés nevét. Ehhez a rövid útmutatóhoz csak egy előfizetést fog használni. 

## <a name="send-messages-to-a-topic"></a>Üzenetek küldése egy üzenettémakörbe
Ebben a szakaszban létrehoz egy .NET Core Console-alkalmazást a Visual Studióban, kód hozzáadásával üzeneteket küld a létrehozott témakörnek. 

### <a name="create-a-console-application"></a>Konzolalkalmazás létrehozása
Indítsa el a Visual studiót, és hozzon létre egy új **Console app (.net Core)** projektet a C# nyelvhez. 

### <a name="add-the-service-bus-nuget-package"></a>A Service Bus NuGet-csomag hozzáadása

1. Kattintson a jobb gombbal az újonnan létrehozott projektre, és válassza a **Manage Nuget Packages** (NuGet-csomagok kezelése) lehetőséget.
1. Válassza a **Tallózás** lehetőséget. Keresse meg és válassza ki az **[Azure. Messaging. ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/)** elemet.
1. A telepítés befejezéséhez válassza a **telepítés** lehetőséget, majd a NuGet csomagkezelő elemet.

### <a name="add-code-to-send-messages-to-the-topic"></a>Kód hozzáadása az üzenetek a témakörbe való küldéséhez 

1. A Program.cs fájlban adja hozzá a következő `using` utasításokat a névtér-definíció elejéhez, az osztálydeklaráció elé:
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    ```
1. A `Program` osztályban deklarálja a következő változókat:

    ```csharp
        static string connectionString = "<NAMESPACE CONNECTION STRING>";
        static string topicName = "<TOPIC NAME>";
        static string subscriptionName = "<SUBSCRIPTION NAME>";
    ```

    Cserélje le a következő értékeket:
    - `<NAMESPACE CONNECTION STRING>` a Service Bus névtérhez tartozó kapcsolódási karakterlánccal
    - `<TOPIC NAME>` a témakör nevével
    - `<SUBSCRIPTION NAME>` az előfizetés nevével
2. Adjon hozzá egy nevű metódust `SendMessageToTopicAsync` , amely egy üzenetet küld a témakörnek. 

    ```csharp
        static async Task SendMessageToTopicAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a sender for the topic
                ServiceBusSender sender = client.CreateSender(topicName);
                await sender.SendMessageAsync(new ServiceBusMessage("Hello, World!"));
                Console.WriteLine($"Sent a single message to the topic: {topicName}");
            }
        }
    ```
1. Adjon hozzá egy nevű metódust `CreateMessages` egy üzenetsor (.net-várólista) az osztályhoz való létrehozásához `Program` . Ezeket az üzeneteket általában az alkalmazás különböző részeiről szerezheti be. Itt hozzuk létre a mintaadatok várólistáját.

    ```csharp
        static Queue<ServiceBusMessage> CreateMessages()
        {
            // create a queue containing the messages and return it to the caller
            Queue<ServiceBusMessage> messages = new Queue<ServiceBusMessage>();
            messages.Enqueue(new ServiceBusMessage("First message"));
            messages.Enqueue(new ServiceBusMessage("Second message"));
            messages.Enqueue(new ServiceBusMessage("Third message"));
            return messages;
        }
    ```
1. Adjon hozzá egy nevű metódust `SendMessageBatchAsync` a `Program` osztályhoz, és adja hozzá a következő kódot. Ez a metódus az üzenetek várólistáját veszi fel, és előkészíti egy vagy több köteg küldését az Service Bus témakörbe. 

    ```csharp
        static async Task SendMessageBatchToTopicAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {

                // create a sender for the topic 
                ServiceBusSender sender = client.CreateSender(topicName);

                // get the messages to be sent to the Service Bus topic
                Queue<ServiceBusMessage> messages = CreateMessages();

                // total number of messages to be sent to the Service Bus topic
                int messageCount = messages.Count;

                // while all messages are not sent to the Service Bus topic
                while (messages.Count > 0)
                {
                    // start a new batch 
                    using ServiceBusMessageBatch messageBatch = await sender.CreateMessageBatchAsync();

                    // add the first message to the batch
                    if (messageBatch.TryAddMessage(messages.Peek()))
                    {
                        // dequeue the message from the .NET queue once the message is added to the batch
                        messages.Dequeue();
                    }
                    else
                    {
                        // if the first message can't fit, then it is too large for the batch
                        throw new Exception($"Message {messageCount - messages.Count} is too large and cannot be sent.");
                    }

                    // add as many messages as possible to the current batch
                    while (messages.Count > 0 && messageBatch.TryAddMessage(messages.Peek()))
                    {
                        // dequeue the message from the .NET queue as it has been added to the batch
                        messages.Dequeue();
                    }

                    // now, send the batch
                    await sender.SendMessagesAsync(messageBatch);

                    // if there are any remaining messages in the .NET queue, the while loop repeats 
                }

                Console.WriteLine($"Sent a batch of {messageCount} messages to the topic: {topicName}");
            }
        }
    ```
1. Cserélje le a `Main()` metódust a következő **aszinkron** `Main` metódusra. Meghívja a küldési metódusokat is, hogy egyetlen üzenetet és egy köteget küldjön a témakörnek.  

    ```csharp
        static async Task Main()
        {
            // send a message to the topic
            await SendMessageToTopicAsync();

            // send a batch of messages to the topic
            await SendMessageBatchToTopicAsync();
        }
    ```
5. Futtassa az alkalmazást. A következő kimenetnek kell megjelennie:

    ```console
    Sent a single message to the topic: mytopic
    Sent a batch of 3 messages to the topic: mytopic
    ```
1. A Azure Portal hajtsa végre az alábbi lépéseket:
    1. Navigáljon a Service Bus névtérhez. 
    1. Az **Áttekintés** lap alsó középső paneljén váltson a **témakörök** lapra, és válassza ki a Service Bus témakört. Az alábbi példában ez a következő: `mytopic` .
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/select-topic.png" alt-text="Témakör kiválasztása":::
    1. A **Service Bus témakör** lap alsó **mérőszámok** szakaszában található **üzenetek** diagramon láthatja, hogy négy bejövő üzenet van a témakörhöz. Ha nem látja az értéket, várjon néhány percet, és frissítse az oldalt a frissített diagram megjelenítéséhez. 

        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png" alt-text="A témakörbe küldött üzenetek" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png":::
    4. Válassza ki az előfizetést az alsó ablaktáblán. A következő példában ez az **S1**. Az **Service Bus-előfizetés** lapon az **aktív üzenetek száma** **4**. Az előfizetés a témakörbe küldött négy üzenetet fogadta, de még nem vette át a fogadót. 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png" alt-text="Az előfizetésben fogadott üzenetek" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png":::
    

## <a name="receive-messages-from-a-subscription"></a>Üzenetek fogadása egy előfizetésből

1. Adja hozzá a következő metódusokat a `Program` osztályhoz, amely kezeli az üzeneteket és az esetleges hibákat. 

    ```csharp
        static async Task MessageHandler(ProcessMessageEventArgs args)
        {
            string body = args.Message.Body.ToString();
            Console.WriteLine($"Received: {body} from subscription: {subscriptionName}");

            // complete the message. messages is deleted from the queue. 
            await args.CompleteMessageAsync(args.Message);
        }

        static Task ErrorHandler(ProcessErrorEventArgs args)
        {
            Console.WriteLine(args.Exception.ToString());
            return Task.CompletedTask;
        }
    ```
1. Adja hozzá a következő metódust `ReceiveMessagesFromSubscriptionAsync` a `Program` osztályhoz.

    ```csharp
        static async Task ReceiveMessagesFromSubscriptionAsync()
        {
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a processor that we can use to process the messages
                ServiceBusProcessor processor = client.CreateProcessor(topicName, subscriptionName, new ServiceBusProcessorOptions());

                // add handler to process messages
                processor.ProcessMessageAsync += MessageHandler;

                // add handler to process any errors
                processor.ProcessErrorAsync += ErrorHandler;

                // start processing 
                await processor.StartProcessingAsync();

                Console.WriteLine("Wait for a minute and then press any key to end the processing");
                Console.ReadKey();

                // stop processing 
                Console.WriteLine("\nStopping the receiver...");
                await processor.StopProcessingAsync();
                Console.WriteLine("Stopped receiving messages");
            }
        }
    ```
1. Adjon hozzá egy hívást a metódushoz `ReceiveMessagesFromSubscriptionAsync` a `Main` metódushoz. `SendMessagesToTopicAsync`Ha csak az üzenetek fogadását szeretné tesztelni, tegye megjegyzésbe a metódust. Ha nem, akkor a témakörbe további négy üzenetet is láthat. 

    ```csharp
        static async Task Main()
        {
            // send a message to the topic
            await SendMessageToTopicAsync();

            // send a batch of messages to the topic
            await SendMessageBatchToTopicAsync();

            // receive messages from the subscription
            await ReceiveMessagesFromSubscriptionAsync();
        }
    ```
## <a name="run-the-app"></a>Az alkalmazás futtatása
Futtassa az alkalmazást. Várjon egy percet, majd nyomja le az egyik billentyűt az üzenetek fogadásának leállításához. A következő kimenetnek kell megjelennie (szóköz a kulcshoz). 

```console
Sent a single message to the topic: mytopic
Sent a batch of 3 messages to the topic: mytopic
Wait for a minute and then press any key to end the processing
Received: Hello, World! from subscription: mysub
Received: First message from subscription: mysub
Received: Second message from subscription: mysub
Received: Third message from subscription: mysub
Received: Hello, World! from subscription: mysub
Received: First message from subscription: mysub
Received: Second message from subscription: mysub
Received: Third message from subscription: mysub

Stopping the receiver...
Stopped receiving messages
```

Próbálkozzon újra a portálon. 

- A **Service Bus témakör** lap **üzenetek** diagramján nyolc bejövő üzenet és nyolc kimenő üzenet jelenik meg. Ha nem látja ezeket a számokat, várjon néhány percet, és frissítse a lapot a frissített diagram megjelenítéséhez. 

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png" alt-text="Küldött és fogadott üzenetek" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png":::
- Az **Service Bus-előfizetés** lapon az **aktív üzenetek száma** nullaként jelenik meg. Ez azért van, mert a fogadó üzenetet kapott az előfizetésből, és befejezte az üzeneteket. 

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png" alt-text="Az aktív üzenetek száma az előfizetés végén" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png":::
    


## <a name="next-steps"></a>Következő lépések
Tekintse meg a következő dokumentációt és mintákat:

- [A .NET-hez készült ügyféloldali kódtár Azure Service Bus – readme](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)
- [Példák a GitHubon](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [.NET API-referencia](/dotnet/api/azure.messaging.servicebus?preserve-view=true)