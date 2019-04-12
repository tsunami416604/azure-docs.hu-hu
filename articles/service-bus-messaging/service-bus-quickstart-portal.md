---
title: Rövid útmutató – az Azure portal segítségével hozza létre a Service Bus-üzenetsorba |} A Microsoft Docs
description: Ebben a rövid útmutatóban megismerheti, hogyan hozhat létre a Service Bus-üzenetsorba az Azure portal használatával. Ezután használja a ügyfélalkalmazásra, üzenetek küldéséhez és üzenetek fogadása az üzenetsorból.
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/10/2019
ms.author: spelluru
ms.openlocfilehash: 700b58723073e425c0587665ccd884932837dcbf
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59501076"
---
# <a name="quickstart-use-azure-portal-to-create-a-service-bus-queue"></a>Gyors útmutató: A Service Bus-üzenetsor létrehozása az Azure portal használatával
Ebből a rövid útmutatóból megismerheti, hogyan használhatja az [Azure Portalt][Azure portal] egy Service Bus-üzenetsorral való üzenetváltásra egy üzenetküldési névtér és egy azon belüli üzenetsor létrehozásával, valamint az adott névsor hitelesítő adatainak lekérésével. Az eljárás ezután bemutatja, hogyan küldhet és fogadhat üzeneteket az üzenetsorból a [.NET Standard-kódtárral](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus).

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy telepítette a következőket:

- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, első lépésként létrehozhat egy [ingyenes fiókot][].
- [Visual Studio 2017 3-as frissítés (verziószám: 15.3, 26730.01)](https://www.visualstudio.com/vs) vagy újabb. A Visual Studio használatával hozhat létre, amely üzeneteket küld, és üzenetet fogad egy mintát. A minta, hogy tesztelni a PowerShell használatával létrehozott üzenetsorba. 
- [NET Core SDK](https://www.microsoft.com/net/download/windows), 2.0-s vagy újabb verzió.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="send-and-receive-messages"></a>Üzenetek küldése és fogadása

Miután kiépítette a névteret és az üzenetsort, valamint beszerezte a szükséges hitelesítő adatokat, készen áll az üzenetek küldésére és fogadására. A kódot [ebben a GitHub-mintamappában](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/TopicFilters) vizsgálhatja meg.

A kód futtatásához tegye a következőt:

1. Klónozza a [Service Bus GitHub-adattárát](https://github.com/Azure/azure-service-bus/) a következő paranccsal:

   ```
   git clone https://github.com/Azure/azure-service-bus.git
   ```
3. Lépjen a következő mintamappához: `azure-service-bus\samples\DotNet\GettingStarted\BasicSendReceiveQuickStart\BasicSendReceiveQuickStart`.
4. Másolja a kapcsolati karakterláncot, és az üzenetsor neve beolvasott a beszerzése a felügyeleti hitelesítő adatok szakaszban.
5.  Írja be a parancssorba a következő parancsot:

    ```
    dotnet build
    ```
6.  Navigáljon a `bin\Debug\netcoreapp2.0` mappához.
7.  Írja be a következő parancsot a program futtatásához. Ne felejtse el kicserélni a `myConnectionString` kifejezést az előzőleg beszerzett értékre, a `myQueueName` kifejezést pedig az imént létrehozott üzenetsor nevére:

    ```shell
    dotnet BasicSendReceiveQuickStart.dll -ConnectionString "myConnectionString" -QueueName "myQueueName"
    ``` 
8. Figyeljen meg 10 üzenetet, amelyeket a rendszer elküld az üzenetsornak, majd onnan fogadja őket:

   ![a program kimenete](./media/service-bus-quickstart-portal/dotnet.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A portálon eltávolíthatja az erőforráscsoportot, a névteret és az üzenetsort.

## <a name="understand-the-sample-code"></a>A mintakód értelmezése

Ez a szakasz a mintakód működésének további részleteit ismerteti. 

### <a name="get-connection-string-and-queue"></a>A kapcsolati sztring és az üzenetsor lekérése

A kapcsolati karakterlánc és a várólista nevét lesznek átadva a `Main()` parancssori argumentumok módját. `Main()` deklarálja a két karakterlánc típusú változót az értékek tárolásához:

```csharp
static void Main(string[] args)
{
    string ServiceBusConnectionString = "";
    string QueueName = "";

    for (int i = 0; i < args.Length; i++)
    {
        var p = new Program();
        if (args[i] == "-ConnectionString")
        {
            Console.WriteLine($"ConnectionString: {args[i+1]}");
            ServiceBusConnectionString = args[i + 1]; 
        }
        else if(args[i] == "-QueueName")
        {
            Console.WriteLine($"QueueName: {args[i+1]}");
            QueueName = args[i + 1];
        }                
    }

    if (ServiceBusConnectionString != "" && QueueName != "")
        MainAsync(ServiceBusConnectionString, QueueName).GetAwaiter().GetResult();
    else
    {
        Console.WriteLine("Specify -Connectionstring and -QueueName to execute the example.");
        Console.ReadKey();
    }                            
}
```
 
A `Main()` metódus ezután elindítja a `MainAsync()` aszinkron üzenethurkot.

### <a name="message-loop"></a>Üzenethurok

A MainAsync() metódus várólista ügyfél hoz létre a parancssori argumentumokat, meghívja a nevű fogadó üzenetkezelőként `RegisterOnMessageHandlerAndReceiveMessages()`, és a üzenetkészletet küld:

```csharp
static async Task MainAsync(string ServiceBusConnectionString, string QueueName)
{
    const int numberOfMessages = 10;
    queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

    Console.WriteLine("======================================================");
    Console.WriteLine("Press any key to exit after receiving all the messages.");
    Console.WriteLine("======================================================");

    // Register QueueClient's MessageHandler and receive messages in a loop
    RegisterOnMessageHandlerAndReceiveMessages();

    // Send Messages
    await SendMessagesAsync(numberOfMessages);

    Console.ReadKey();

    await queueClient.CloseAsync();
}
```

A `RegisterOnMessageHandlerAndReceiveMessages()` metódus egyszerűen megad néhány üzenetkezelő-beállítást, majd meghívja az üzenetsorügyfél `RegisterMessageHandler()` metódusát, amely elindítja a fogadást:

```csharp
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
```

### <a name="send-messages"></a>Üzenetek küldése

Az üzenetlétrehozási és -küldési műveletek a `SendMessagesAsync()` metódusban mennek végbe:

```csharp
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
```

### <a name="process-messages"></a>Üzenetek feldolgozása

A `ProcessMessagesAsync()` metódus nyugtázza, feldolgozza és befejezi az üzenetek fogadását:

```csharp
static async Task ProcessMessagesAsync(Message message, CancellationToken token)
{
    // Process the message
    Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

    // Complete the message so that it is not received again.
    await queueClient.CompleteAsync(message.SystemProperties.LockToken);
}
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy Service Bus-névteret és az üzenetsorba történő üzenetküldéshez és -fogadáshoz szükséges egyéb erőforrásokat hozott létre. Üzenetek küldése és fogadása, kódírás kapcsolatos további információkért folytassa szereplő oktatóanyagok a **üzenetek küldése és fogadása** szakaszban. 

> [!div class="nextstepaction"]
> [Üzenetek küldése és fogadása](service-bus-dotnet-get-started-with-queues.md)


[ingyenes fiók]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Azure portal]: https://portal.azure.com/

[service-bus-flow]: ./media/service-bus-quickstart-portal/service-bus-flow.png
