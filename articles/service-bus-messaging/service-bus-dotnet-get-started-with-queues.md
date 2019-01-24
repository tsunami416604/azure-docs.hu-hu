---
title: Bevezetés az Azure Service Bus által kezelt üzenetsorok használatába | Microsoft Docs
description: Írjon egy Service Bus üzenetkezelési sorokat használó C# konzolalkalmazást.
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 68a34c00-5600-43f6-bbcc-fea599d500da
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 81bc37a4bbd63beda122d7254409e2b16f0e3cb1
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54852159"
---
# <a name="get-started-with-service-bus-queues"></a>Bevezetés a Service Bus által kezelt üzenetsorok használatába

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Ez az oktatóanyag a következő lépéseken vezet végig:

1. Service Bus-névtér létrehozása az Azure Portal használatával.
2. Service Bus-üzenetsor létrehozása az Azure Portal használatával.
3. Írjon egy .NET Core-konzolalkalmazást, hogy elküldjön egy üzenetkészletet az üzenetsorba.
4. Írjon egy .NET Core-konzolalkalmazást, hogy üzeneteket tudjon fogadni az üzenetsorból.

## <a name="prerequisites"></a>Előfeltételek

1. [Visual Studio 2017 3-as frissítés (verziószám: 15.3, 26730.01)](https://www.visualstudio.com/vs) vagy újabb.
2. [NET Core SDK](https://www.microsoft.com/net/download/windows), 2.0-s vagy újabb verzió.
2. Azure-előfizetés.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="create-a-namespace-using-the-azure-portal"></a>Névtér létrehozása az Azure Portal használatával

> [!NOTE] 
> A [PowerShell](/powershell/azure/get-started-azureps) használatával Service Bus-névtér és üzenetküldési entitások létrehozására is lehetőség van. További információkért lásd [a Service Bus-erőforrások PowerShell-lel történő kezelésével](service-bus-manage-with-ps.md) foglalkozó témakört.

Ha már létrehozta a Service Bus Messaging-névteret, lépjen az [Üzensor létrehozása az Azure Portal használatával](#2-create-a-queue-using-the-azure-portal) szakaszra.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-a-queue-using-the-azure-portal"></a>Üzenetsor létrehozása az Azure Portal használatával

Ha a Service Bus-üzenetsort már létrehozta, ugorjon az [Üzenetek küldése az üzenetsorba](#3-send-messages-to-the-queue) szakaszra.

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="send-messages-to-the-queue"></a>Üzenetek küldése az üzenetsorba

A Visual Studio használatával írjon C# konzolalkalmazást az üzenetek üzenetsorba való küldéséhez.

### <a name="create-a-console-application"></a>Konzolalkalmazás létrehozása

Indítsa el a Visual Studiót, majd hozzon létre egy új **Konzolalkalmazás (.NET Core)** projektet.

### <a name="add-the-service-bus-nuget-package"></a>A Service Bus NuGet-csomag hozzáadása

1. Kattintson a jobb gombbal az újonnan létrehozott projektre, és válassza a **Manage Nuget Packages** (NuGet-csomagok kezelése) lehetőséget.
2. Kattintson a **Browse** (Tallózás) fülre, keressen rá a **[Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)** kifejezésre, majd válassza ki a **Microsoft.Azure.ServiceBus** elemet. Kattintson a **Telepítés** gombra a telepítés befejezéséhez, majd zárja be a párbeszédpanelt.
   
    ![NuGet-csomag kiválasztása][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-queue"></a>Kód írása az üzenetek üzenetsorba való küldéséhez

1. A Program.cs fájlban adja hozzá a következő `using` utasításokat a névtér-definíció elejéhez, az osztálydeklaráció elé:
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. A `Program` osztályban deklarálja az alábbi változókat. A `ServiceBusConnectionString` változó értékének állítsa be a névtér létrehozásakor kapott kapcsolati sztringet, a `QueueName` értékének pedig az üzenetsor létrehozásakor használt nevet:
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ``` 

3. Cserélje le a `Main()` tartalmát a következő kódsorral:

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```

4. Közvetlenül a `Main()` után adja hozzá a következő aszinkron `MainAsync()` metódust az üzenetküldési metódus meghívásához:

    ```csharp
    static async Task MainAsync()
    {
        const int numberOfMessages = 10;
        queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after sending all the messages.");
        Console.WriteLine("======================================================");

        // Send messages.
        await SendMessagesAsync(numberOfMessages);

        Console.ReadKey();

        await queueClient.CloseAsync();
    }
    ```

5. Közvetlenül a `MainAsync()` metódus után adja hozzá a következő `SendMessagesAsync()` metódust a `numberOfMessagesToSend` által megadott számú (aktuális értéke: 10) üzenet elküldéséhez:

    ```csharp
    static async Task SendMessagesAsync(int numberOfMessagesToSend)
    {
        try
        {
            for (var i = 0; i < numberOfMessagesToSend; i++)
            {
                // Create a new message to send to the queue.
                string messageBody = $"Message {i}";
                var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                // Write the body of the message to the console.
                Console.WriteLine($"Sending message: {messageBody}");

                // Send the message to the queue.
                await queueClient.SendAsync(message);
            }
        }
        catch (Exception exception)
        {
            Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
        }
    }
    ```
   
6. A Program.cs fájlnak így kell kinéznie.
   
    ```csharp
    namespace CoreSenderApp
    {
        using System;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.ServiceBus;

        class Program
        {
            // Connection String for the namespace can be obtained from the Azure portal under the 
            // 'Shared Access policies' section.
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string QueueName = "<your_queue_name>";
            static IQueueClient queueClient;

            static void Main(string[] args)
            {
                MainAsync().GetAwaiter().GetResult();
            }

            static async Task MainAsync()
            {
                const int numberOfMessages = 10;
                queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after sending all the messages.");
                Console.WriteLine("======================================================");

                // Send Messages
                await SendMessagesAsync(numberOfMessages);
                        
                Console.ReadKey();

                await queueClient.CloseAsync();
            }

            static async Task SendMessagesAsync(int numberOfMessagesToSend)
            {
                try
                {
                    for (var i = 0; i < numberOfMessagesToSend; i++)
                    {
                        // Create a new message to send to the queue
                        string messageBody = $"Message {i}";
                        var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                        // Write the body of the message to the console
                        Console.WriteLine($"Sending message: {messageBody}");

                        // Send the message to the queue
                        await queueClient.SendAsync(message);
                    }
                }
                catch (Exception exception)
                {
                    Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
                }
            }
        }
    }
    ```

7. Futtassa a programot, és ellenőrizze az Azure Portalon: kattintson az üzenetsor nevére a névtér **Áttekintés** ablakában. Megjelenik az üzenetsor **Alapok** képernyője. Vegye figyelembe, hogy az üzenetsor **Aktív üzenetek száma** értéke ekkor **10**. Valahányszor a küldő alkalmazást az üzenetek (a következő szakaszban leírt módon történő) fogadása nélkül futtatja, ez az érték 10-zel növekszik. Azt is vegye figyelembe, hogy az üzenetsor aktuális mérete minden alkalommal megnöveli az **Alapok** ablak **Jelenlegi** értékét, amikor az alkalmazás üzeneteket ad hozzá az üzenetsorhoz.
   
      ![Üzenet mérete][queue-message]

## <a name="receive-messages-from-the-queue"></a>Üzenet fogadása az üzenetsorból

Az imént elküldött üzenetek fogadásához hozzon létre egy másik .NET Core-konzolalkalmazást, majd telepítse a **Microsoft.Azure.ServiceBus** NuGet-csomagot, az előző küldő alkalmazáshoz hasonlóan.

### <a name="write-code-to-receive-messages-from-the-queue"></a>Kód írása az üzenetek üzenetsorból történő fogadásához

1. A Program.cs fájlban adja hozzá a következő `using` utasításokat a névtér-definíció elejéhez, az osztálydeklaráció elé:
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. A `Program` osztályban deklarálja az alábbi változókat. A `ServiceBusConnectionString` változó értékének állítsa be a névtér létrehozásakor kapott kapcsolati sztringet, a `QueueName` értékének pedig az üzenetsor létrehozásakor használt nevet:
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ```

3. Cserélje le a `Main()` tartalmát a következő kódsorral:

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```

4. Közvetlenül a `Main()` után adja hozzá a következő aszinkron `MainAsync()` metódust a `RegisterOnMessageHandlerAndReceiveMessages()` metódus meghívásához:

    ```csharp
    static async Task MainAsync()
    {
        queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
        Console.WriteLine("======================================================");

        // Register the queue message handler and receive messages in a loop
        RegisterOnMessageHandlerAndReceiveMessages();

        Console.ReadKey();

        await queueClient.CloseAsync();
    }
    ```

5. Közvetlenül a `MainAsync()` metódus után adja hozzá a következő metódust az üzenetkezelő regisztrálásához és a küldő alkalmazás által elküldött üzenetek fogadásához:

    ```csharp
    static void RegisterOnMessageHandlerAndReceiveMessages()
    {
        // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
        var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
        {
            // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
            // Set it according to how many messages the application wants to process in parallel.
            MaxConcurrentCalls = 1,

            // Indicates whether the message pump should automatically complete the messages after returning from user callback.
            // False below indicates the complete operation is handled by the user callback as in ProcessMessagesAsync().
            AutoComplete = false
        };

        // Register the function that processes messages.
        queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
    }
    ```

6. Közvetlenül az előző metódus után adja hozzá a következő `ProcessMessagesAsync()` metódust a fogadott üzenetek feldolgozásához:
 
    ```csharp
    static async Task ProcessMessagesAsync(Message message, CancellationToken token)
    {
        // Process the message.
        Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

        // Complete the message so that it is not received again.
        // This can be done only if the queue Client is created in ReceiveMode.PeekLock mode (which is the default).
        await queueClient.CompleteAsync(message.SystemProperties.LockToken);

        // Note: Use the cancellationToken passed as necessary to determine if the queueClient has already been closed.
        // If queueClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
        // to avoid unnecessary exceptions.
    }
    ```

7. Végül adja hozzá a következő metódust az esetlegesen előforduló kivételek kezeléséhez:
 
    ```csharp
    // Use this handler to examine the exceptions received on the message pump.
    static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
    {
        Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
        var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
        Console.WriteLine("Exception context for troubleshooting:");
        Console.WriteLine($"- Endpoint: {context.Endpoint}");
        Console.WriteLine($"- Entity Path: {context.EntityPath}");
        Console.WriteLine($"- Executing Action: {context.Action}");
        return Task.CompletedTask;
    }    
    ```    
   
8. A Program.cs fájlnak így kell kinéznie:
   
    ```csharp
    namespace CoreReceiverApp
    {
        using System;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.ServiceBus;

        class Program
        {
            // Connection String for the namespace can be obtained from the Azure portal under the 
            // 'Shared Access policies' section.
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string QueueName = "<your_queue_name>";
            static IQueueClient queueClient;

            static void Main(string[] args)
            {
                MainAsync().GetAwaiter().GetResult();
            }

            static async Task MainAsync()
            {
                queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
                Console.WriteLine("======================================================");

                // Register QueueClient's MessageHandler and receive messages in a loop
                RegisterOnMessageHandlerAndReceiveMessages();
                    
                Console.ReadKey();

                await queueClient.CloseAsync();
            }

            static void RegisterOnMessageHandlerAndReceiveMessages()
            {
                // Configure the MessageHandler Options in terms of exception handling, number of concurrent messages to deliver etc.
                var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
                {
                    // Maximum number of Concurrent calls to the callback `ProcessMessagesAsync`, set to 1 for simplicity.
                    // Set it according to how many messages the application wants to process in parallel.
                    MaxConcurrentCalls = 1,

                    // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
                    // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
                    AutoComplete = false
                };

                // Register the function that will process messages
                queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
            }

            static async Task ProcessMessagesAsync(Message message, CancellationToken token)
            {
                // Process the message
                Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

                // Complete the message so that it is not received again.
                // This can be done only if the queueClient is created in ReceiveMode.PeekLock mode (which is default).
                await queueClient.CompleteAsync(message.SystemProperties.LockToken);

                // Note: Use the cancellationToken passed as necessary to determine if the queueClient has already been closed.
                // If queueClient has already been Closed, you may chose to not call CompleteAsync() or AbandonAsync() etc. calls 
               // to avoid unnecessary exceptions.
            }

            static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
            {
                Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
                var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
                Console.WriteLine("Exception context for troubleshooting:");
                Console.WriteLine($"- Endpoint: {context.Endpoint}");
                Console.WriteLine($"- Entity Path: {context.EntityPath}");
                Console.WriteLine($"- Executing Action: {context.Action}");
                return Task.CompletedTask;
            }
        }
    }
    ```
4. Futtassa a programot, majd ellenőrizze ismét a portálon. Figyelje meg, hogy az **Aktív üzenetek száma** és a **Jelenlegi** értéke most **0**.
   
    ![Várólista hossza][queue-message-receive]

Gratulálunk! Ezzel létrehozott egy üzenetsort, elküldött egy üzenetkészletet az üzenetsornak, és fogadta is tőle az üzeneteket.

## <a name="next-steps"></a>További lépések

Tekintse meg a [GitHub-tárunkat, ahol további példákat talál](https://github.com/Azure/azure-service-bus/tree/master/samples), amelyek a Service Bus üzenetkezelési szolgáltatásának néhány speciális funkcióját mutatják be.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/queue-message.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive.png

