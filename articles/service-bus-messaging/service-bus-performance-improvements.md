---
title: Ajánlott eljárások a teljesítmény javításához a Azure Service Bus használatával
description: Ismerteti, hogyan optimalizálható a teljesítmény a Service Bus használatával a felügyelt üzenetek cseréjekor.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 03/12/2020
ms.author: aschhab
ms.openlocfilehash: b864f433c67d47b4b92a1d4b98693ebd42806dd3
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79259460"
---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Ajánlott eljárások a teljesítmény növeléséhez Service Bus üzenetkezelés használatával

Ez a cikk azt ismerteti, hogyan optimalizálható a teljesítmény a felügyelt üzenetek cseréjekor a Azure Service Bus használatával. A cikk első része a teljesítmény növelését segítő különböző mechanizmusokat ismerteti. A második rész útmutatást nyújt arról, hogyan használhatók a Service Bus olyan módon, hogy a lehető legjobb teljesítményt nyújtsa egy adott forgatókönyvben.

Ebben a cikkben az "ügyfél" kifejezés minden olyan entitásra hivatkozik, amely hozzáfér Service Bushoz. Egy ügyfél elvégezheti a küldő vagy fogadó szerepkörét. A "Sender" kifejezés olyan Service Bus üzenetsor vagy témakör-ügyfél esetében használatos, amely üzeneteket küld egy Service Bus üzenetsor vagy témakör-előfizetés számára. A "fogadó" kifejezés egy Service Bus üzenetsor vagy egy előfizetési ügyfél, amely üzeneteket fogad egy Service Bus sorból vagy előfizetésből.

Ezek a lépések számos olyan fogalom bevezetését ismertetik, amelyeket a Service Bus a teljesítmény növelése érdekében használ.

## <a name="protocols"></a>Protokollok

Service Bus lehetővé teszi az ügyfelek számára az üzenetek küldését és fogadását a következő három protokoll egyikének használatával:

1. Advanced Message Queueing Protocol (AMQP)
2. Service Bus üzenetküldési protokoll (SBMP)
3. Hypertext Transfer Protocol (HTTP)

A AMQP a leghatékonyabb, mert a Service Bus kapcsolatot tart fenn. Emellett a kötegelt és a beolvasási művelet is megvalósítható. Hacsak nem kifejezetten említettük, az ebben a cikkben szereplő összes tartalom a AMQP vagy a SBMP használatát feltételezi.

> [!IMPORTANT]
> A SBMP csak a .NET-keretrendszerhez érhető el. A AMQP a .NET Standard alapértelmezett értéke.

## <a name="choosing-the-appropriate-service-bus-net-sdk"></a>A megfelelő Service Bus .NET SDK kiválasztása

Két támogatott .NET SDK-Azure Service Bus létezik. Az API-k nagyon hasonlóak, és zavaró lehet, hogy melyiket érdemes választani. A döntés meghozatalához tekintse meg a következő táblázatot. Javasoljuk, hogy használja a Microsoft. Azure. ServiceBus SDK-t, mint a modern, a teljesítmény és a platformfüggetlen kompatibilitás. Emellett támogatja a AMQP-t a websocketeken keresztül, és része a nyílt forráskódú projektek Azure .NET SDK-gyűjteményének.

| NuGet-csomag | Elsődleges névtér (ek) | Minimális platform (ok) | Protokoll (ok) |
|---------------|----------------------|---------------------|-------------|
| <a href="https://www.nuget.org/packages/Microsoft.Azure.ServiceBus" target="_blank">Microsoft. Azure. ServiceBus<span class="docon docon-navigate-external x-hidden-focus"></span></a> | `Microsoft.Azure.ServiceBus`<br>`Microsoft.Azure.ServiceBus.Management` | .NET Core 2.0<br>.NET-keretrendszer 4.6.1<br>Monó 5,4<br>Xamarin. iOS 10,14<br>Xamarin. Mac 3,8<br>Xamarin. Android 8,0<br>Univerzális Windows-platform 10.0.16299 | AMQP<br>HTTP |
| <a href="https://www.nuget.org/packages/WindowsAzure.ServiceBus" target="_blank">WindowsAzure. ServiceBus<span class="docon docon-navigate-external x-hidden-focus"></span></a> | `Microsoft.ServiceBus`<br>`Microsoft.ServiceBus.Messaging` | .NET-keretrendszer 4.6.1 | AMQP<br>SBMP<br>HTTP |

További információ a .NET Standard szintű platform támogatásáról: [.net-implementáció támogatása](https://docs.microsoft.com/dotnet/standard/net-standard#net-implementation-support).

## <a name="reusing-factories-and-clients"></a>Üzemek és ügyfelek újrafelhasználása

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft. Azure. ServiceBus SDK](#tab/net-standard-sdk)

Service Bus az ügyfélalkalmazások, például [`IQueueClient`][QueueClient] vagy [`IMessageSender`][MessageSender]megvalósítását, regisztrálni kell a függőségi injektáláshoz (vagy egyszeres és megosztott). Azt javasoljuk, hogy az üzenet elküldése után ne zárjunk be üzenetküldési gyárakat vagy üzenetsor-kezelést, témakört és előfizetési ügyfelet, majd hozza létre újra a következő üzenet elküldésekor. Az üzenetküldési gyár bezárása törli a Service Bus szolgáltatással létesített kapcsolódást, és a gyár újbóli létrehozásakor létrejön egy új csatlakozás. A kapcsolatok létrehozása költséges művelet, amellyel elkerülhető, hogy a több művelethez ugyanazt a gyári és ügyféloldali objektumot használja. Ezeket az ügyféloldali objektumokat biztonságosan használhatja egyidejű aszinkron műveletekhez és több szálból is.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. ServiceBus SDK](#tab/net-framework-sdk)

Service Bus-objektumok, például `QueueClient` vagy `MessageSender`, egy [MessagingFactory][MessagingFactory] objektumon keresztül jönnek létre, amely a kapcsolatok belső felügyeletét is biztosítja. Azt javasoljuk, hogy az üzenet elküldése után ne zárjunk be üzenetküldési gyárakat vagy üzenetsor-kezelést, témakört és előfizetési ügyfelet, majd hozza létre újra a következő üzenet elküldésekor. Az üzenetküldési gyár bezárása törli a Service Bus szolgáltatással létesített kapcsolódást, és a gyár újbóli létrehozásakor létrejön egy új csatlakozás. A kapcsolatok létrehozása költséges művelet, amellyel elkerülhető, hogy a több művelethez ugyanazt a gyári és ügyféloldali objektumot használja. Ezeket az ügyféloldali objektumokat biztonságosan használhatja egyidejű aszinkron műveletekhez és több szálból is.

---

## <a name="concurrent-operations"></a>Egyidejű műveletek

Egy művelet végrehajtása (küldés, fogadás, törlés stb.) eltarthat egy ideig. Ez az idő magában foglalja a műveletnek a Service Bus szolgáltatás általi feldolgozását, valamint a kérés késését és a választ. A műveletek másodpercenkénti számának növeléséhez a műveleteknek egyidejűleg kell futniuk.

Az ügyfél aszinkron műveletek végrehajtásával ütemezhet egyidejű műveleteket. A következő kérelem az előző kérelem befejeződése előtt indul el. A következő kódrészlet példa egy aszinkron küldési műveletre:

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft. Azure. ServiceBus SDK](#tab/net-standard-sdk)

```csharp
var messageOne = new Message(body);
var messageTwo = new Message(body);

var sendFirstMessageTask =
    queueClient.SendAsync(messageOne).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #1");
    });
var sendSecondMessageTask =
    queueClient.SendAsync(messageTwo).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #2");
    });

await Task.WhenAll(sendFirstMessageTask, sendSecondMessageTask);
Console.WriteLine("All messages sent");
```

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. ServiceBus SDK](#tab/net-framework-sdk)

```csharp
var messageOne = new BrokeredMessage(body);
var messageTwo = new BrokeredMessage(body);

var sendFirstMessageTask =
    queueClient.SendAsync(messageOne).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #1");
    });
var sendSecondMessageTask =
    queueClient.SendAsync(messageTwo).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #2");
    });

await Task.WhenAll(sendFirstMessageTask, sendSecondMessageTask);
Console.WriteLine("All messages sent");
```

---

Az alábbi kód egy aszinkron fogadási műveletre mutat példát.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft. Azure. ServiceBus SDK](#tab/net-standard-sdk)

A teljes <a href="https://github.com/Azure/azure-service-bus/blob/master/samples/DotNet/Microsoft.Azure.ServiceBus/SendersReceiversWithQueues" target="_blank">forráskódra vonatkozó példákat <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>a GitHub-tárházban tekintheti meg:

```csharp
var receiver = new MessageReceiver(connectionString, queueName, ReceiveMode.PeekLock);

static Task LogErrorAsync(Exception exception)
{
    Console.WriteLine(exception);
    return Task.CompletedTask;
};

receiver.RegisterMessageHandler(
    async (message, cancellationToken) =>
    {
        Console.WriteLine("Handle message");
        await receiver.CompleteAsync(message.SystemProperties.LockToken);
    },
    new MessageHandlerOptions(e => LogErrorAsync(e.Exception))
    {
        AutoComplete = false,
        MaxConcurrentCalls = 1
    });
```

A `MessageReceiver` objektum a (z) és a (z) kapcsolatok karakterláncával, a várólista nevével és a betekintéses fogadás módjával jön létre. Ezután a `receiver`-példány az üzenetkezelő regisztrálására szolgál.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. ServiceBus SDK](#tab/net-framework-sdk)

A teljes <a href="https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/SendersReceiversWithQueues" target="_blank">forráskódra vonatkozó példákat <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>a GitHub-tárházban tekintheti meg:

```csharp
var factory = MessagingFactory.CreateFromConnectionString(connectionString);
var receiver = await factory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);

// Register the handler to receive messages asynchronously
receiver.OnMessageAsync(
    async message =>
    {
        Console.WriteLine("Handle message");
        await message.CompleteAsync();
    },
    new OnMessageOptions
    {
        AutoComplete = false,
        MaxConcurrentCalls = 1
    });
```

A `MessagingFactory` létrehoz egy `factory` objektumot a kapcsolatok sztringből. A `factory`-példánnyal a rendszer egy `MessageReceiver` példányt hoz létre. Ezt követően a `receiver`-példány az üzenetkezelő kezelő regisztrálására szolgál.

---

## <a name="receive-mode"></a>Fogadási mód

Üzenetsor vagy előfizetési ügyfél létrehozásakor megadhat egy fogadási módot: *betekintés zárolás* vagy *fogadás és törlés*. Az alapértelmezett fogadási mód a `PeekLock`. Ha az alapértelmezett módban működik, az ügyfél kérést küld, hogy üzenetet kapjon Service Busról. Miután az ügyfél megkapta az üzenetet, elküld egy kérést az üzenet végrehajtásához.

A fogadási mód `ReceiveAndDelete`értékre állításakor mindkét lépés egyetlen kérelemben van egyesítve. Ezek a lépések csökkentik a műveletek teljes számát, és javítják az üzenetek teljes átviteli sebességét. Ez a teljesítmény az üzenetek elvesztésének kockázatával jár.

A Service Bus nem támogatja a fogadási és törlési műveletek tranzakcióit. Emellett a betekintés-zárolás szemantikai követelménye minden olyan esetben szükséges, amikor az ügyfél késleltetni vagy [kézbesíteni](service-bus-dead-letter-queues.md) kívánja az üzenetet.

## <a name="client-side-batching"></a>Ügyféloldali kötegek

Az ügyféloldali kötegek lehetővé teszik egy üzenetsor vagy egy témakör-ügyfél számára, hogy egy adott időtartamon belül késleltetni lehessen az üzenet küldését. Ha az ügyfél további üzeneteket küld ezen időszakon belül, a rendszer egyetlen kötegben továbbítja ezen üzeneteket. Az ügyféloldali kötegek azt is eredményezik, hogy egy üzenetsor vagy egy előfizetési ügyfél több **teljes** kérelmet küld egyetlen kérelembe. A kötegelt feldolgozás csak aszinkron **küldési** és **befejezési** műveletekhez érhető el. A szinkron műveleteket a rendszer azonnal elküldi a Service Bus szolgáltatásnak. A betekintési és fogadási műveletekre nem kerül sor, és a kötegelt feldolgozás nem történik meg az ügyfeleken.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft. Azure. ServiceBus SDK](#tab/net-standard-sdk)

A .NET Standard SDK batching funkciója még nem tesz elérhetővé egy tulajdonságot a kezeléshez.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. ServiceBus SDK](#tab/net-framework-sdk)

Alapértelmezés szerint az ügyfél 20 ms-os batch-intervallumot használ. A Batch-intervallum módosításához állítsa a [BatchFlushInterval][BatchFlushInterval] tulajdonságot az üzenetküldési gyár létrehozása előtt. Ez a beállítás a gyár által létrehozott összes ügyfelet érinti.

A kötegelt feldolgozás letiltásához állítsa a [BatchFlushInterval][BatchFlushInterval] tulajdonságot **TimeSpan. Zero**értékre. Például:

```csharp
var settings = new MessagingFactorySettings
{
    NetMessagingTransportSettings =
    {
        BatchFlushInterval = TimeSpan.Zero
    }
};
var factory = MessagingFactory.Create(namespaceUri, settings);
```

A kötegelt feldolgozás nem befolyásolja a számlázható üzenetkezelési műveletek számát, és csak a [Microsoft. ServiceBus. Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) függvénytár használatával érhető el a Service Bus ügyfél protokollja számára. A HTTP protokoll nem támogatja a kötegelt feldolgozást.

> [!NOTE]
> A `BatchFlushInterval` beállítása biztosítja, hogy a kötegelt feldolgozás implicit legyen az alkalmazás szemszögéből. i.e. az alkalmazás `SendAsync` és `CompleteAsync` hívásokat végez, és nem hajt végre konkrét batch-hívásokat.
>
> A explicit ügyféloldali kötegelt feldolgozást az alábbi metódus hívásával lehet megvalósítani:
> ```csharp
> Task SendBatchAsync(IEnumerable<BrokeredMessage> messages);
> ```
> Itt az üzenetek összesített méretének kisebbnek kell lennie az árképzési szinten támogatott maximális méretnél.

---

## <a name="batching-store-access"></a>Tároló-hozzáférés feldolgozása

Egy üzenetsor, témakör vagy előfizetés átviteli sebességének növeléséhez Service Bus batchs több üzenetet, amikor a belső tárolóba ír. Ha engedélyezve van egy várólistán vagy témakörön, a rendszer az üzeneteket az áruházba írja. Ha egy várólistán vagy előfizetésen engedélyezve van, az üzenetek az áruházból való törlése kötegelt feldolgozással történik. Ha a kötegek tárolóhoz való hozzáférése engedélyezve van egy entitáshoz, a Service Bus akár 20 MS-ra késlelteti az adott entitásra vonatkozó tárolási írási műveletet.

> [!NOTE]
> A kötegelt feldolgozással nem lehet az üzenetek elvesztésének kockázata, még akkor is, ha a 20ms-köteg intervallumának végén Service Bus hiba történt.

Az ezen intervallumban megjelenő további tárolási műveletek hozzáadódnak a köteghez. A kötegelt tárolók hozzáférése csak a **küldési** és a **befejezési** műveleteket érinti; a fogadási műveletek nem érintettek. A kötegelt tár hozzáférése egy entitás egyik tulajdonsága. A kötegelt tárolás az összes olyan entitáson megtörténik, amelyek engedélyezik a Batch-tárolók elérését.

Új üzenetsor, témakör vagy előfizetés létrehozásakor a kötegelt tároló-hozzáférés alapértelmezés szerint engedélyezve van.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft. Azure. ServiceBus SDK](#tab/net-standard-sdk)

A kötegelt tárolás hozzáférésének letiltásához szüksége lesz egy `ManagementClient`egy példányára. Hozzon létre egy várólistát egy üzenetsor leírásában, amely a `EnableBatchedOperations` tulajdonságot `false`re állítja.

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = await managementClient.CreateQueueAsync(queueDescription);
```

További információt a következő témakörben talál:
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.queuedescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.QueueDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.subscriptiondescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.SubscriptionDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.topicdescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.TopicDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. ServiceBus SDK](#tab/net-framework-sdk)

A kötegelt tárolás hozzáférésének letiltásához szüksége lesz egy `NamespaceManager`egy példányára. Hozzon létre egy várólistát egy üzenetsor leírásában, amely a `EnableBatchedOperations` tulajdonságot `false`re állítja.

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = namespaceManager.CreateQueue(queueDescription);
```

További információt a következő témakörben talál:
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.QueueDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.SubscriptionDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.topicdescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.TopicDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

---

A batchd Store-hozzáférés nem befolyásolja a számlázható üzenetkezelési műveletek számát, és egy üzenetsor, témakör vagy előfizetés tulajdonsága. Független a fogadási módtól és az ügyfél és a Service Bus szolgáltatás között használt protokolltól.

## <a name="prefetching"></a>Prefetching

A [beolvasás](service-bus-prefetch.md) lehetővé teszi, hogy a várólista vagy az előfizetési ügyfél további üzeneteket töltsön be a szolgáltatásból, amikor fogadási műveletet hajt végre. Az ügyfél helyi gyorsítótárban tárolja ezeket az üzeneteket. A gyorsítótár méretét a `QueueClient.PrefetchCount` vagy `SubscriptionClient.PrefetchCount` tulajdonságai határozzák meg. Minden olyan ügyfél, amely lehetővé teszi, hogy az előhívások saját gyorsítótárat tároljanak. A rendszer nem osztja meg a gyorsítótárat az ügyfelek között. Ha az ügyfél fogadási műveletet kezdeményez, és a gyorsítótára üres, a szolgáltatás egy köteg üzenetet küld. A köteg mérete megegyezik a gyorsítótár vagy a 256 KB-os méretével, attól függően, hogy melyik a kisebb. Ha az ügyfél fogadási műveletet kezdeményez, és a gyorsítótár egy üzenetet tartalmaz, a rendszer az üzenetet a gyorsítótárból veszi át.

Egy üzenet előhívásakor a szolgáltatás zárolja az előhívott üzenetet. A zárolással az előhívott üzenetet nem lehet egy másik fogadótól fogadni. Ha a fogadó nem tudja befejezni az üzenetet a zárolás lejárta előtt, az üzenet elérhetővé válik más fogadók számára. Az üzenet előre beolvasott másolata a gyorsítótárban marad. A lejárt gyorsítótárazott példányt használó fogadó kivételt fog kapni, amikor megpróbálja befejezni az üzenetet. Alapértelmezés szerint az üzenet zárolása 60 másodperc után lejár. Ez az érték 5 percre bővíthető. A lejárt üzenetek felhasználásának megakadályozásához a gyorsítótár méretének mindig kisebbnek kell lennie, mint az ügyfél által a zárolási időkorlát intervallumában felhasználható üzenetek száma.

A 60 másodperces alapértelmezett zárolás lejáratának használatakor a `PrefetchCount` megfelelő értéke 20 alkalommal a gyári vevőkészülékek maximális feldolgozási sebessége. Egy gyár például három fogadót hoz létre, és az egyes fogadók másodpercenként akár 10 üzenetet is feldolgozhatnak. A kihívások száma nem haladhatja meg a 20 X 3 X 10 = 600. Alapértelmezés szerint a `PrefetchCount` 0 értékre van állítva, ami azt jelenti, hogy a szolgáltatás nem olvas be további üzeneteket.

Az üzenetek előhívása növeli a várólista vagy előfizetés teljes átviteli sebességét, mivel csökkenti az üzenetküldési műveletek teljes számát vagy az oda-és visszaútt. Az első üzenet beolvasása azonban hosszabb időt vesz igénybe (az üzenet méretének növekedése miatt). Az előre beolvasott üzenetek fogadása gyorsabb lesz, mert az ügyfél már letöltötte ezeket az üzeneteket.

Az üzenet élettartam (TTL) tulajdonságát a kiszolgáló ellenőrzi, amikor a kiszolgáló elküldi az üzenetet az ügyfélnek. Az üzenet fogadásakor az ügyfél nem vizsgálja meg az üzenet TTL tulajdonságát. Ehelyett az üzenet akkor is megjelenhet, ha az üzenet TTL-értéke megtelt, miközben az ügyfél gyorsítótárazta az üzenetet.

Az előolvasás nem befolyásolja a számlázható üzenetkezelési műveletek számát, és csak az Service Bus ügyfél-protokoll esetében érhető el. A HTTP protokoll nem támogatja a beolvasást. Az előhívás a szinkron és aszinkron fogadási műveletekhez is elérhető.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft. Azure. ServiceBus SDK](#tab/net-standard-sdk)

További információkért tekintse meg a következő `PrefetchCount` tulajdonságokat:

* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.servicebus.subscriptionclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. ServiceBus SDK](#tab/net-framework-sdk)

További információkért tekintse meg a következő `PrefetchCount` tulajdonságokat:

* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.queueclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

---

## <a name="prefetching-and-receivebatch"></a>Előhívás és ReceiveBatch

> [!NOTE]
> Ez a szakasz csak a WindowsAzure. ServiceBus SDK-ra vonatkozik, mivel a Microsoft. Azure. ServiceBus SDK nem tesz elérhetővé batch-függvényeket.

Habár a több üzenet előhívásának fogalmai hasonló szemantikai feladattal rendelkeznek egy kötegben (`ReceiveBatch`) lévő üzenetek feldolgozásához, néhány kisebb eltérést kell szem előtt tartania, amikor ezeket együtt használják.

A kiküldés egy konfiguráció (vagy mód) az ügyfélen (`QueueClient` és `SubscriptionClient`), és a `ReceiveBatch` egy művelet (amely a kérés-válasz szemantikagal rendelkezik).

Ha ezeket együtt használja, vegye figyelembe a következő eseteket:

* A prefektus nem lehet nagyobb, mint a `ReceiveBatch`tól kapott üzenetek száma.
* A kiállítók akár n/3-szor is lehetnek a feldolgozott üzenetek másodpercenkénti száma, ahol n az alapértelmezett zárolási időtartam.

Vannak olyan kihívások, amelyek kapzsi megközelítéssel rendelkeznek (azaz a visszahívások száma nagyon magas), mert ez azt jelenti, hogy az üzenet egy adott fogadó számára van zárolva. A javaslat célja, hogy kipróbálja a fenti küszöbértékek közötti kiugró értékeket, és empirikusan azonosítsa, hogy mi illik hozzá.

## <a name="multiple-queues"></a>Több várólista

Ha a várt terhelést egyetlen üzenetsor vagy témakör nem tudja kezelni, több üzenetkezelési entitást kell használnia. Ha több entitást használ, hozzon létre egy dedikált ügyfelet az egyes entitásokhoz ahelyett, hogy ugyanazt az ügyfelet használja az összes entitáshoz.

## <a name="development-and-testing-features"></a>Fejlesztési és tesztelési funkciók

> [!NOTE]
> Ez a szakasz csak a WindowsAzure. ServiceBus SDK-ra vonatkozik, mivel a Microsoft. Azure. ServiceBus SDK nem teszi elérhetővé ezt a funkciót.

Service Bus rendelkezik egy, a fejlesztéshez használt funkcióval, amelyet **soha nem használhat éles konfigurációkban**: [`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering].

Ha új szabályok vagy szűrők lettek hozzáadva a témakörhöz, a [`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering] segítségével ellenőrizheti, hogy az új szűrő kifejezés a várt módon működik-e.

## <a name="scenarios"></a>Forgatókönyvek

A következő szakaszok ismertetik a tipikus üzenetkezelési forgatókönyveket, és körvonalazzák az előnyben részesített Service Bus beállításait. Az átviteli sebességet kisméretű (kevesebb, mint 1 üzenet/másodperc), mérsékelt (1 üzenet/másodperc vagy nagyobb, de kevesebb mint 100 üzenet/másodperc) és magas (100 üzenet/másodperc vagy nagyobb) értékre sorolják be. Az ügyfelek száma kisméretű (5 vagy kevesebb), mérsékelt (több mint 5, de legfeljebb 20) és nagy (több mint 20).

### <a name="high-throughput-queue"></a>Nagy átviteli sebességű üzenetsor

Cél: egyetlen üzenetsor átviteli sebességének maximalizálása. A küldők és a fogadók száma kicsi.

* A teljes küldési aránynak a várólistára való növeléséhez használjon több Message Factoryt a küldők létrehozásához. Mindegyik feladó esetében használjon aszinkron műveleteket vagy több szálat.
* A várólista általános fogadási sebességének növeléséhez használjon több Message Factoryt a fogadók létrehozásához.
* Aszinkron műveletek használatával kihasználhatja az ügyféloldali kötegek előnyeit.
* Állítsa a batching intervallumot 50 MS értékre, hogy csökkentse a Service Bus ügyfél-protokollok átvitelének számát. Ha több feladót használ, növelje a kötegelt intervallumot 100 MS-ra.
* A kötegelt tárolók elérésének engedélyezése engedélyezve. Ez a hozzáférés növeli az üzenetek várólistába írásának teljes mértékét.
* Állítsa be a visszahívások számát a gyári összes fogadójának maximális feldolgozási sebességének 20-szor értékére. Ez a szám csökkenti a Service Bus ügyfél-protokollok átvitelét.

### <a name="multiple-high-throughput-queues"></a>Több nagy átviteli sebességű várólista

Cél: több várólista teljes átviteli sebességének maximalizálása. Az egyes várólisták átviteli sebessége közepes vagy magas.

A maximális átviteli sebesség több várólistára történő beszerzéséhez használja a következő beállításokat, hogy maximalizálja egy adott várólista átviteli sebességét. Emellett különböző gyárakat használhat a különböző várólistákból küldött vagy fogadott ügyfelek létrehozásához.

### <a name="low-latency-queue"></a>Kis késleltetésű várólista

Cél: a várólista vagy témakör végpontok közötti késésének csökkentése. A küldők és a fogadók száma kicsi. A várólista átviteli sebessége kicsi vagy mérsékelt.

* Tiltsa le az ügyféloldali kötegeket. Az ügyfél azonnal üzenetet küld.
* A kötegelt tárolók elérésének letiltása. A szolgáltatás azonnal beírja az üzenetet az áruházba.
* Ha egyetlen ügyfelet használ, állítsa be a visszahívások számát a fogadó feldolgozási sebessége szerint 20-szor. Ha egyszerre több üzenet érkezik a várólistára, a Service Bus ügyfél-protokoll egyszerre továbbítja őket. Amikor az ügyfél megkapja a következő üzenetet, az üzenet már szerepel a helyi gyorsítótárban. A gyorsítótárnak kicsinek kell lennie.
* Ha több ügyfelet használ, állítsa a prefektusok száma értéket 0-ra. A szám beállításával a második ügyfél a második üzenetet is megkapja, amikor az első ügyfél még dolgozza fel az első üzenetet.

### <a name="queue-with-a-large-number-of-senders"></a>Üzenetsor nagy számú küldővel

Cél: egy üzenetsor vagy témakör maximális átviteli sebessége nagy számú küldővel. Mindegyik feladó mérsékelt sebességgel küld üzeneteket. A fogadók száma kicsi.

Service Bus lehetővé teszi akár 1000 egyidejű kapcsolatot egy üzenetküldési entitással (vagy 5000 a AMQP használatával). Ez a korlát a névtér szintjén van kikényszerítve, és a várólisták/témakörök/előfizetések maximális száma a névtér egyidejű kapcsolatainak korlátja. A várólisták esetében ez a szám a küldők és a fogadók között van megosztva. Ha az összes 1000-kapcsolat szükséges a küldők számára, cserélje le a várólistát egy témakörre és egy előfizetésre. A témakörök legfeljebb 1000 egyidejű kapcsolatot fogadnak el a küldők között, míg az előfizetés további 1000 egyidejű kapcsolatokat fogad el a fogadók között. Ha több mint 1000 egyidejű küldőre van szükség, a küldőknek HTTP-n keresztül kell üzeneteket küldeniük a Service Bus protokollnak.

Az átviteli sebesség maximalizálása érdekében hajtsa végre a következő lépéseket:

* Ha az egyes küldők egy másik folyamatban vannak, a folyamaton belül csak egyetlen gyárat használjon.
* Aszinkron műveletek használatával kihasználhatja az ügyféloldali kötegek előnyeit.
* A 20 MS alapértelmezett batch-intervallum használatával csökkentse a Service Bus az ügyfél-protokollok átvitelének számát.
* A kötegelt tárolók elérésének engedélyezése engedélyezve. Ez a hozzáférés növeli az üzenetek üzenetsor vagy témakörbe való írásának általános sebességét.
* Állítsa be a visszahívások számát a gyári összes fogadójának maximális feldolgozási sebességének 20-szor értékére. Ez a szám csökkenti a Service Bus ügyfél-protokollok átvitelét.

### <a name="queue-with-a-large-number-of-receivers"></a>Üzenetsor nagy számú fogadóval

Cél: a várólista vagy előfizetés fogadási sebességének maximalizálása nagy számú fogadóval. Minden fogadó mérsékelt sebességgel fogad üzeneteket. A küldők száma kicsi.

A Service Bus akár 1000 egyidejű kapcsolatot tesz lehetővé egy entitással. Ha egy üzenetsor több mint 1000 fogadót igényel, cserélje le a várólistát egy témakörre és több előfizetésre. Az egyes előfizetések akár 1000 egyidejű kapcsolatot is támogatnak. Másik lehetőségként a fogadók a HTTP protokollon keresztül érhetik el a várólistát.

Az átviteli sebesség maximalizálása érdekében tegye a következőket:

* Ha az egyes fogadók egy másik folyamaton belül találhatók, a folyamathoz csak egyetlen gyárat használjon.
* A fogadók szinkron vagy aszinkron műveleteket is használhatnak. Az egyes fogadók mérsékelt fogadási sebessége miatt a teljes kérelem ügyféloldali kötegelt feldolgozása nem befolyásolja a fogadó átviteli sebességét.
* A kötegelt tárolók elérésének engedélyezése engedélyezve. Ez a hozzáférés csökkenti az entitás teljes terhelését. Emellett csökkenti az üzenetek üzenetsor vagy témakörbe való írásának általános sebességét is.
* Állítsa be a prefektusi számot egy kis értékre (például PrefetchCount = 10). Ez a szám megakadályozza, hogy a fogadók tétlenek legyenek, míg a többi fogadó nagy számú üzenetet gyorsítótárazott.

### <a name="topic-with-a-small-number-of-subscriptions"></a>Témakör kis számú előfizetéssel

Cél: egy adott témakör átviteli sebességének maximalizálása kis számú előfizetéssel. Számos előfizetés fogad egy üzenetet, ami azt jelenti, hogy az összes előfizetéshez tartozó összesített fogadási arány nagyobb a küldési aránynál. A küldők száma kicsi. Az előfizetéshez tartozó fogadók száma kicsi.

Az átviteli sebesség maximalizálása érdekében tegye a következőket:

* A teljes küldési aránynak a témakörben való növeléséhez használjon több Message Factoryt a küldők létrehozásához. Mindegyik feladó esetében használjon aszinkron műveleteket vagy több szálat.
* Az előfizetés teljes fogadási arányának növeléséhez használjon több Message Factoryt a fogadók létrehozásához. Minden egyes fogadó esetében használjon aszinkron műveleteket vagy több szálat.
* Aszinkron műveletek használatával kihasználhatja az ügyféloldali kötegek előnyeit.
* A 20 MS alapértelmezett batch-intervallum használatával csökkentse a Service Bus az ügyfél-protokollok átvitelének számát.
* A kötegelt tárolók elérésének engedélyezése engedélyezve. Ez a hozzáférés növeli a témakörbe írt üzenetek összesített arányát.
* Állítsa be a visszahívások számát a gyári összes fogadójának maximális feldolgozási sebességének 20-szor értékére. Ez a szám csökkenti a Service Bus ügyfél-protokollok átvitelét.

### <a name="topic-with-a-large-number-of-subscriptions"></a>Számos előfizetéssel rendelkező témakör

Cél: nagy mennyiségű előfizetéssel maximalizálhatja a témakör átviteli sebességét. Számos előfizetés fogad egy üzenetet, ami azt jelenti, hogy az összes előfizetéshez tartozó összesített fogadási arány sokkal nagyobb, mint a küldési sebesség. A küldők száma kicsi. Az előfizetéshez tartozó fogadók száma kicsi.

A nagy számú előfizetéssel rendelkező témakörök általában alacsony teljes átviteli sebességet tesznek elérhetővé, ha az összes üzenet az összes előfizetéshez van irányítva. Az alacsony átviteli sebesség oka az, hogy az egyes üzenetek többször is érkeznek, és a témakörben szereplő összes üzenet és az összes előfizetése ugyanabban a tárolóban van tárolva. Feltételezzük, hogy a küldők száma és az előfizetéshez tartozó fogadók száma kicsi. A Service Bus egy témakörben legfeljebb 2 000 előfizetést támogat.

Az átviteli sebesség maximalizálása érdekében próbálkozzon az alábbi lépésekkel:

* Aszinkron műveletek használatával kihasználhatja az ügyféloldali kötegek előnyeit.
* A 20 MS alapértelmezett batch-intervallum használatával csökkentse a Service Bus az ügyfél-protokollok átvitelének számát.
* A kötegelt tárolók elérésének engedélyezése engedélyezve. Ez a hozzáférés növeli a témakörbe írt üzenetek összesített arányát.
* Állítsa be a visszahívások darabszámát a várt fogadási sebesség (másodperc) értékének 20-szor. Ez a szám csökkenti a Service Bus ügyfél-protokollok átvitelét.

<!-- .NET Standard SDK, Microsoft.Azure.ServiceBus -->
[QueueClient]: /dotnet/api/microsoft.azure.servicebus.queueclient
[MessageSender]: /dotnet/api/microsoft.azure.servicebus.core.messagesender

<!-- .NET Framework SDK, Microsoft.Azure.ServiceBus -->
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[BatchFlushInterval]: /dotnet/api/microsoft.servicebus.messaging.messagesender.batchflushinterval
[ForcePersistence]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.forcepersistence
[EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnableFiltering]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablefilteringmessagesbeforepublishing

<!-- Local links -->
[Partitioned messaging entities]: service-bus-partitioning.md