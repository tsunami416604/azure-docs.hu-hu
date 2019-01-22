---
title: Rövid útmutató – Azure PowerShell használatával hozzon létre egy Service Bus-üzenetsorba |} A Microsoft Docs
description: Ez a rövid útmutatóban megismerheti, hogyan Azure PowerShell használatával creat egy Service Bus-üzenetsorba. Ezután használja a mintaalkalmazás az üzenetek küldéséhez és üzenetek fogadása az üzenetsorból.
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/12/2019
ms.author: spelluru
ms.openlocfilehash: 36f1b39ca4cef33a8cfcdb295b5c4d59e1128a0c
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54426845"
---
# <a name="quickstart-use-azure-powershell-to-create-a-service-bus-queue"></a>Gyors útmutató: Az Azure PowerShell használatával hozzon létre egy Service Bus-üzenetsorba
A Microsoft Azure Service Bus egy vállalati integrációs üzenetközvetítő, amely biztonságos üzenetküldést és teljes mértékben megbízható szolgáltatást nyújt. Egy jellemző Service Bus-forgatókönyv általában két vagy több alkalmazás, szolgáltatás vagy folyamat egymástól való szétválasztását, továbbá az állapot- vagy adatváltozások továbbítását foglalja magában. Ilyen esetekben előfordulhat, hogy több kötegelt feladatokat kell üzemeznie más alkalmazásokban vagy szolgáltatásokban, vagy pedig ki kell váltani a megrendelések teljesítését. Például egy kereskedelmi vállalat elküldheti az értékesítési pontokra vonatkozó adatait egy háttérrendszernek vagy egy regionális terjesztőközpontnak feltöltés vagy leltárfrissítés céljából. Ebben a forgatókönyvben az ügyfélalkalmazás üzeneteket küld és fogad egy Service Bus-üzenetsorból.

![üzenetsor](./media/service-bus-quickstart-powershell/quick-start-queue.png)

Ebből a rövid útmutatóból megismerheti, hogyan küldhet és fogadhat üzeneteket egy Service Bus-üzenetsorból a PowerShell használatával egy üzenetküldési névtér és egy azon belüli üzenetsor létrehozásához, valamint az adott névtér hitelesítő adatainak lekéréséhez. Az eljárás ezután bemutatja, hogyan küldhet és fogadhat üzeneteket az üzenetsorból a [.NET Standard-kódtárral](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot][] a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy telepítette a következőket:

- [Visual Studio 2017 3-as frissítés (verziószám: 15.3, 26730.01)](https://www.visualstudio.com/vs) vagy újabb.
- [NET Core SDK](https://www.microsoft.com/net/download/windows), 2.0-s vagy újabb verzió.

Ehhez a rövid útmutatóhoz az Azure PowerShell legújabb verzióját kell futtatnia. Ha telepíteni vagy frissíteni szeretne: [Az Azure PowerShell telepítése és konfigurálása][].

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

1. Ha még nem tette meg, először telepítse a Service Bus PowerShell-modult:

   ```azurepowershell-interactive
   Install-Module AzureRM.ServiceBus
   ```

2. Az alábbi parancs futtatásával jelentkezzen be az Azure-ba:

   ```azurepowershell-interactive
   Login-AzureRmAccount
   ```

3. Adja ki a következő parancsokat az aktuális előfizetési környezet beállításához vagy a jelenleg aktív előfizetés megtekintéséhez:

   ```azurepowershell-interactive
   Select-AzureRmSubscription -SubscriptionName "MyAzureSubName" 
   Get-AzureRmContext
   ```

## <a name="provision-resources"></a>Erőforrások kiosztása

A PowerShell-parancssorból hajtsa végre az alábbi parancsokat a Service Bus-erőforrások kiépítéséhez. Ne felejtse el lecserélni az összes helyőrzőt a megfelelő értékre:

```azurepowershell-interactive
# Create a resource group 
New-AzureRmResourceGroup –Name my-resourcegroup –Location eastus

# Create a Messaging namespace
New-AzureRmServiceBusNamespace -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Location eastus

# Create a queue 
New-AzureRmServiceBusQueue -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Name queue-name -EnablePartitioning $False

# Get primary connection string (required in next step)
Get-AzureRmServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
```

A `Get-AzureRmServiceBusKey` parancsmag futtatása után másolja ki és illessze be a kapcsolati sztringet és a kiválasztott üzenetsor nevét egy átmeneti helyre, például a Jegyzettömbbe. A következő lépésben szüksége lesz ezekre.

## <a name="send-and-receive-messages"></a>Üzenetek küldése és fogadása

Miután létrehozta a névteret és az üzenetsort, valamint beszerezte a szükséges hitelesítő adatokat, készen áll az üzenetek küldésére és fogadására. A kódot [ebben a GitHub-mintamappában](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveQuickStart) vizsgálhatja meg.

A kód futtatásához tegye a következőt:

1. Klónozza a [Service Bus GitHub-adattárát](https://github.com/Azure/azure-service-bus/) a következő paranccsal:

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

3. Lépjen a következő mintamappához: `azure-service-bus\samples\DotNet\GettingStarted\BasicSendReceiveQuickStart\BasicSendReceiveQuickStart`.

4. Ha még nem tette meg, szerezze be a kapcsolati sztringet a következő PowerShell-parancsmaggal. Ne felejtse el a `my-resourcegroup`  és a  `namespace-name` értéket a saját értékeire cserélni: 

   ```azurepowershell-interactive
   Get-AzureRmServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
   ```

5.  A PowerShell-parancssorba írja be a következő parancsot:

   ```shell
   dotnet build
   ```

6.  Navigáljon a `bin\Debug\netcoreapp2.0` mappához.

7.  Írja be a következő parancsot a program futtatásához. Ne felejtse el kicserélni a `myConnectionString` kifejezést az előzőleg beszerzett értékre, a `myQueueName` kifejezést pedig az imént létrehozott üzenetsor nevére:

   ```shell
   dotnet BasicSendReceiveQuickStart.dll -ConnectionString "myConnectionString" -QueueName "myQueueName"
   ``` 

8. Figyeljen meg 10 üzenetet, amelyeket a rendszer elküld az üzenetsornak, majd onnan fogadja őket:

   ![a program kimenete](./media/service-bus-quickstart-powershell/dotnet.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az alábbi paranccsal eltávolítható az erőforráscsoport, a névtér és az összes kapcsolódó erőforrás:

```powershell-interactive
Remove-AzureRmResourceGroup -Name my-resourcegroup
```

## <a name="understand-the-sample-code"></a>A mintakód értelmezése

Ez a szakasz a mintakód működésének további részleteit ismerteti. 

### <a name="get-connection-string-and-queue"></a>A kapcsolati sztring és az üzenetsor lekérése

A rendszer parancssori argumentumként adja át a kapcsolati sztringet és az üzenetsor nevét a `Main()` metódusnak. A `Main()` metódus két sztringváltozót jelöl ki az értékek tárolásához:

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

A MainAsync() metódus a parancssori argumentumokkal létrehoz egy üzenetsorügyfelet, meghív egy `RegisterOnMessageHandlerAndReceiveMessages()` nevű fogadó üzenetkezelőt, és elküldi az üzenetek készletét:

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

Ebben a cikkben egy Service Bus-névteret és az üzenetsorba történő üzenetküldéshez és -fogadáshoz szükséges egyéb erőforrásokat hozott létre. Ha többet szeretne megtudni az üzenetfogadásra és -küldésre szolgáló kódok írásáról, lépjen tovább a Service Busról szóló oktatóanyagra:

> [!div class="nextstepaction"]
> [Leltár frissítése az Azure PowerShell használatával](./service-bus-tutorial-topics-subscriptions-powershell.md)

[ingyenes fiókot]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Az Azure PowerShell telepítése és konfigurálása]: /powershell/azure/azurerm/install-azurerm-ps