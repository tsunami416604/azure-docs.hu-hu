---
title: Oktatóanyag – Kereskedelmi leltárválaszték frissítése közzétételi/előfizetési csatornák és témakörszűrők használatával az Azure PowerShell-lel | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan küldhet és fogadhat üzeneteket egy témakörből vagy előfizetésből, és miként adhat hozzá és használhat szűrési szabályokat az Azure PowerShell felületről
services: service-bus-messaging
author: spelluru
manager: timlt
ms.author: spelluru
ms.date: 09/22/2018
ms.topic: tutorial
ms.service: service-bus-messaging
ms.custom: mvc
ms.openlocfilehash: 845fc32d527158258304a92c6855017c9d8c0492
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59049557"
---
# <a name="tutorial-update-inventory-using-powershell-and-topicssubscriptions"></a>Oktatóanyag: A PowerShell és a témakörök/előfizetések használatával készlet frissítése

A Microsoft Azure Service Bus egy több-bérlős felhőalapú üzenetkezelési szolgáltatás, amely információkat küld alkalmazások és szolgáltatók között. Az aszinkron műveletek rugalmas, közvetítőalapú üzenettovábbítást, valamint strukturált, érkezési sorrendben történő üzenetkiküldést tesznek lehetővé. Emellett közzétételi vagy előfizetési lehetőségeket is biztosítanak. 

Ebből az oktatóanyagból megismerheti, hogyan küldhet és fogadhat üzeneteket egy Service Bus-üzenetsorból a PowerShell használatával egy üzenetküldési névtér és egy azon belüli üzenetsor létrehozásához, valamint az adott névtér hitelesítő adatainak lekéréséhez. Az eljárás ezután bemutatja, hogyan küldhet és fogadhat üzeneteket az üzenetsorból a [.NET Standard-kódtárral](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Service Bus-témakör és egy vagy több hozzá tartozó előfizetés létrehozása az Azure PowerShell használatával
> * Témakörszűrők hozzáadása a PowerShell használatával
> * Két különböző tartalmú üzenet létrehozása
> * Az üzenetek elküldése és annak ellenőrzése, hogy megérkeztek-e a megfelelő előfizetésekbe
> * Üzenet fogadása az előfizetésektől

Erre a forgatókönyvre egy példa, amikor több kiskereskedelmi üzletben frissül a leltárválaszték. Ebben az esetben minden egyes üzlet vagy üzletcsoport megkapja a neki szánt üzeneteket a választéka frissítéséről. Ez az oktatóanyag bemutatja, hogyan valósítható meg a forgatókönyv előfizetések és szűrők használatával. Először hozzon létre egy témakört 3 előfizetéssel, adjon hozzá szabályokat és szűrőket, majd küldjön és fogadjon üzeneteket a témaköröktől és az előfizetésektől.

![témakör](./media/service-bus-tutorial-topics-subscriptions-powershell/about-service-bus-topic.png)

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot][] a feladatok megkezdése előtt.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy telepítette a következőket:

1. [Visual Studio 2017 3-as frissítés (verziószám: 15.3, 26730.01)](https://www.visualstudio.com/vs) vagy újabb.
2. [NET Core SDK](https://www.microsoft.com/net/download/windows), 2.0-s vagy újabb verzió.

Ehhez az oktatóanyaghoz az Azure PowerShell legújabb verzióját kell futtatnia. Ha telepíteni vagy frissíteni szeretne: [Az Azure PowerShell telepítése és konfigurálása][].

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

A következő parancsok futtatásával jelentkezzen be az Azure-ba. Ezek a lépések nem szükségesek, ha a PowerShell-parancsokat a Cloud Shellben futtatja: 

1. Telepítse a Service Bus PowerShell-modult:

   ```azurepowershell-interactive
   Install-Module Az.ServiceBus
   ```

2. Az alábbi parancs futtatásával jelentkezzen be az Azure-ba:

   ```azurepowershell-interactive
   Login-AzAccount
   ```

4. Állítsa be az aktuális előfizetési környezetet vagy tekintse meg a jelenleg aktív előfizetést:

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "MyAzureSubName" 
   Get-AzContext
   ```

## <a name="provision-resources"></a>Erőforrások kiosztása

Miután bejelentkezett az Azure-ba, hajtsa végre az alábbi parancsokat a Service Bus-erőforrások üzembe helyezéséhez. Ne felejtse el lecserélni az összes helyőrzőt a megfelelő értékre:

```azurepowershell-interactive
# Create a resource group 
New-AzResourceGroup –Name my-resourcegroup –Location westus2

# Create a Messaging namespace
New-AzServiceBusNamespace -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Location westus2

# Create a queue 
New-AzServiceBusQueue -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Name queue-name -EnablePartitioning $False

# Get primary connection string (required in next step)
Get-AzServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
```

A `Get-AzServiceBusKey` parancsmag futtatása után másolja ki és illessze be a kapcsolati sztringet és a kiválasztott üzenetsor nevét egy átmeneti helyre, például a Jegyzettömbbe. A következő lépésben szüksége lesz ezekre.

## <a name="send-and-receive-messages"></a>Üzenetek küldése és fogadása

Miután létrehozta a névteret és az üzenetsort, valamint beszerezte a szükséges hitelesítő adatokat, készen áll az üzenetek küldésére és fogadására. A kódot [ebben a GitHub-mintamappában](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveQuickStart) vizsgálhatja meg.

A kód futtatásához tegye a következőt:

1. Klónozza a [Service Bus GitHub-adattárát](https://github.com/Azure/azure-service-bus/) a következő paranccsal:

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. Nyisson meg egy PowerShell-parancssort.

3. Lépjen a következő mintamappához: `azure-service-bus\samples\DotNet\GettingStarted\BasicSendReceiveQuickStart\BasicSendReceiveQuickStart`.

4. Ha még nem tette meg, szerezze be a kapcsolati sztringet a következő PowerShell-parancsmaggal. Ne felejtse el a `my-resourcegroup` és `namespace-name` értéket a saját értékeire cserélni: 

   ```azurepowershell-interactive
   Get-AzServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
   ```
5. A PowerShell-parancssorba írja be a következő parancsot:

   ```shell
   dotnet build
   ```
6. Navigáljon a `\bin\Debug\netcoreapp2.0` mappához.
7. Írja be a következő parancsot a program futtatásához. Ne felejtse el kicserélni a `myConnectionString` kifejezést az előzőleg beszerzett értékre, a `myQueueName` kifejezést pedig az imént létrehozott üzenetsor nevére:

   ```shell
   dotnet BasicSendReceiveQuickStart.dll -ConnectionString "myConnectionString" -QueueName "myQueueName"
   ``` 
8. Figyeljen meg 10 üzenetet, amelyeket a rendszer elküld az üzenetsornak, majd onnan fogadja őket:

   ![a program kimenete](./media/service-bus-quickstart-powershell/dotnet.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az alábbi paranccsal eltávolítható az erőforráscsoport, a névtér és az összes kapcsolódó erőforrás:

```powershell-interactive
Remove-AzResourceGroup -Name my-resourcegroup
```

## <a name="understand-the-sample-code"></a>A mintakód értelmezése

Ez a szakasz a mintakód működésének további részleteit ismerteti. 

### <a name="get-connection-string-and-queue"></a>A kapcsolati sztring és az üzenetsor lekérése

A rendszer parancssori argumentumként adja át a kapcsolati sztringet és az üzenetsor nevét a `Main()` metódusnak. `Main()` deklarálja a két karakterlánc típusú változót az értékek tárolásához:

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

A `MainAsync()` metódus a parancssori argumentumokkal létrehoz egy üzenetsorügyfelet, meghív egy `RegisterOnMessageHandlerAndReceiveMessages()` nevű fogadó üzenetkezelőt, és elküldi az üzenetek készletét:

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

A `RegisterOnMessageHandlerAndReceiveMessages()` metódus megad néhány üzenetkezelő-beállítást, majd meghívja az üzenetsor-ügyfél `RegisterMessageHandler()` metódusát, amely elindítja a fogadást:

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

Ebben az oktatóanyagban erőforrásokat osztott ki az Azure PowerShell használatával, majd üzeneteket küldött és fogadott egy Service Bus-témakörből és annak előfizetéseiből. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Service Bus-témakör és egy vagy több hozzá tartozó előfizetés létrehozása az Azure Portal használatával
> * Témakörszűrők hozzáadása .NET-kód használatával
> * Két különböző tartalmú üzenet létrehozása
> * Az üzenetek elküldése és annak ellenőrzése, hogy megérkeztek-e a megfelelő előfizetésekbe
> * Üzenet fogadása az előfizetésektől

Az üzenetküldéssel és -fogadással kapcsolatos további példákért tekintse meg a [Service Bus-mintákat a GitHubon](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted).

Folytassa a következő oktatóanyaggal, ha szeretne többet megtudni a Service Bus közzétételi/előfizetési funkcióiról.

> [!div class="nextstepaction"]
> [A PowerShell és a témakörök/előfizetések használatával készlet frissítése](service-bus-tutorial-topics-subscriptions-cli.md)

[ingyenes fiók]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Az Azure PowerShell telepítése és konfigurálása]: /powershell/azure/install-Az-ps
