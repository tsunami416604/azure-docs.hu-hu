---
title: Gyakorlati tanácsok a teljesítmény növeléséhez az Azure Service Bus használatával
description: Ez a témakör azt ismerteti, hogyan használhatja a Service Bus a teljesítmény optimalizálása a közvetített üzenetek cseréjekor.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 03/12/2020
ms.author: aschhab
ms.openlocfilehash: 267965ee41280a677050d1676285dda8734bc044
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606058"
---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Ajánlott eljárások a teljesítmény javításához a Service Bus-üzenetkezelés használatával

Ez a cikk ismerteti, hogyan használhatja az Azure Service Bus a teljesítmény optimalizálása a közvetített üzenetek cseréjekor. A cikk első része ismerteti a különböző mechanizmusokat, amelyek a teljesítmény növelése érdekében. A második rész útmutatást nyújt a Service Bus olyan módon történő használatához, amely egy adott forgatókönyvben a legjobb teljesítményt nyújtja.

Ebben a cikkben az "ügyfél" kifejezés minden olyan entitásra vonatkozik, amely hozzáfér a Service Bus szolgáltatáshoz. Az ügyfél elveheti a feladó vagy a fogadó szerepét. A "feladó" kifejezés egy Service Bus-várólistára vagy témakör-ügyfélre használatos, amely üzeneteket küld egy Service Bus-várólistába vagy témakör-előfizetésbe. A "fogadó" kifejezés egy Service Bus-várólistára vagy előfizetési ügyfélre utal, amely üzeneteket fogad egy Service Bus-várólistából vagy -előfizetésből.

Ezek a szakaszok számos olyan fogalmat vezetnek be, amelyeket a Service Bus a teljesítmény növelése érdekében használ.

## <a name="protocols"></a>Protokollok

A Service Bus lehetővé teszi az ügyfelek számára, hogy üzeneteket küldjenek és fogadjanak a következő három protokoll egyikén:

1. Advanced Message Queueing Protocol (AMQP)
2. Service Bus Messaging Protocol (SBMP)
3. Hypertext Transfer Protocol (HTTP)

Az AMQP a leghatékonyabb, mert fenntartja a kapcsolatot a Service Bus-szal. Azt is megvalósítja kötegelés és prefetching. Hacsak kifejezetten nem említik, a cikkben szereplő összes tartalom az AMQP vagy az SBMP használatát feltételezi.

> [!IMPORTANT]
> Az SBMP csak a . Az AMQP a .NET Standard alapértelmezett beállítása.

## <a name="choosing-the-appropriate-service-bus-net-sdk"></a>A megfelelő service bus .NET SDK kiválasztása

Két támogatott Azure Service Bus .NET SDK van. Az API-k nagyon hasonlóak, és zavaró lehet, hogy melyiket válassza. Az alábbi táblázat ban segíthet a döntés irányításában. Javasoljuk, hogy a Microsoft.Azure.ServiceBus SDK, mert modernebb, teljesítmény, és a platformfüggetlen kompatibilis. Emellett támogatja az AMQP websocketeken keresztül, és része az Azure .NET SDK nyílt forráskódú projektek gyűjteménye.

| NuGet csomag | Elsődleges névtér(ek) | Minimális platform(ok) | Protokoll(ok) |
|---------------|----------------------|---------------------|-------------|
| <a href="https://www.nuget.org/packages/Microsoft.Azure.ServiceBus" target="_blank">Microsoft.Azure.ServiceBus<span class="docon docon-navigate-external x-hidden-focus"></span></a> | `Microsoft.Azure.ServiceBus`<br>`Microsoft.Azure.ServiceBus.Management` | .NET Core 2.0<br>.NET keretrendszer 4.6.1<br>Monó 5,4<br>Xamarin.iOS 10.14<br>Xamarin.Mac 3,8<br>Xamarin.Android 8.0<br>Univerzális Windows platform 10.0.16299 | AMQP<br>HTTP |
| <a href="https://www.nuget.org/packages/WindowsAzure.ServiceBus" target="_blank">WindowsAzure.ServiceBus<span class="docon docon-navigate-external x-hidden-focus"></span></a> | `Microsoft.ServiceBus`<br>`Microsoft.ServiceBus.Messaging` | .NET keretrendszer 4.6.1 | AMQP<br>SBMP<br>HTTP |

A minimális .NET Standard platform támogatásról a [.NET implementáció támogatása című](https://docs.microsoft.com/dotnet/standard/net-standard#net-implementation-support)témakörben talál további információt.

## <a name="reusing-factories-and-clients"></a>Gyárak és ügyfelek újrafelhasználása

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

A Service Bus ügyfélobjektumait, [`IMessageSender`][MessageSender]például a vagy implementációit [`IQueueClient`][QueueClient] regisztrálni kell a függőségi injektáláshoz singletons (vagy egyszer példányosított és megosztott) néven. Javasoljuk, hogy ne zárja be az üzenetgyárakat vagy a várólista-, témakör- és előfizetési ügyfeleket az üzenet elküldése után, majd hozza létre újra őket a következő üzenet elküldésekor. Az üzenetkezelő gyár bezárása törli a kapcsolatot a Service Bus szolgáltatással, és új kapcsolat jön létre a gyár újbóli létrehozásakor. A kapcsolat létrehozása költséges művelet, amelyet elkerülhet, ha ugyanazt a gyári és ügyfélobjektumot több művelethez is újra felhasználja. Ezeket az ügyfélobjektumokat biztonságosan használhatja egyidejű aszinkron műveletekhez és több szálból.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

A Service Bus ügyfélobjektumai, például `QueueClient` vagy `MessageSender`a létrehozása egy [MessagingFactory][MessagingFactory] objektumon keresztül jön létre, amely a kapcsolatok belső felügyeletét is biztosítja. Javasoljuk, hogy ne zárja be az üzenetgyárakat vagy a várólista-, témakör- és előfizetési ügyfeleket az üzenet elküldése után, majd hozza létre újra őket a következő üzenet elküldésekor. Az üzenetkezelő gyár bezárása törli a kapcsolatot a Service Bus szolgáltatással, és új kapcsolat jön létre a gyár újbóli létrehozásakor. A kapcsolat létrehozása költséges művelet, amelyet elkerülhet, ha ugyanazt a gyári és ügyfélobjektumot több művelethez is újra felhasználja. Ezeket az ügyfélobjektumokat biztonságosan használhatja egyidejű aszinkron műveletekhez és több szálból.

---

## <a name="concurrent-operations"></a>Egyidejű műveletek

Egy művelet végrehajtása (küldés, fogadás, törlés stb.) némi időt vesz igénybe. Ez az idő magában foglalja a művelet feldolgozása a Service Bus szolgáltatás mellett a kérelem és a válasz késése mellett. Az idővel végrehajtott műveletek számának növeléséhez a műveleteket egyidejűleg kell végrehajtani.

Az ügyfél egyidejű műveleteket ütemez aszinkron műveletek végrehajtásával. A következő kérelem az előző kérelem befejezése előtt indul el. A következő kódrészlet egy aszinkron küldési művelet példája:

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

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

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

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

A következő kód egy aszinkron fogadási művelet példája.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

Tekintse meg a GitHub-tárházban a teljes <a href="https://github.com/Azure/azure-service-bus/blob/master/samples/DotNet/Microsoft.Azure.ServiceBus/SendersReceiversWithQueues" target="_blank">forráskód példákat: <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>

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
        MaxConcurrentCalls = 20
    });
```

Az `MessageReceiver` objektum a kapcsolati karakterláncmal, a várólista nevével és a betekintő-megjelenés fogadási móddal van elhívva. Ezután `receiver` a példány az üzenetkezelő regisztrálására szolgál.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Tekintse meg a GitHub-tárházban a teljes <a href="https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/SendersReceiversWithQueues" target="_blank">forráskód példákat: <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>

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
        MaxConcurrentCalls = 20
    });
```

A `MessagingFactory` létrehoz `factory` egy objektumot a kapcsolati karakterláncból. A `factory` példával `MessageReceiver` a példány példányos. Ezután `receiver` a példány az üzenetkezelő regisztrálására szolgál.

---

## <a name="receive-mode"></a>Fogadási mód

Várólista- vagy előfizetés-ügyfél létrehozásakor megadhat egy fogadási módot: *Betekintés* zárolás vagy *Fogadás és törlés*. Az alapértelmezett fogadási `PeekLock`mód a . Ha az alapértelmezett módban működik, az ügyfél üzenetet küld a Service Bus-tól. Miután az ügyfél megkapta az üzenetet, az üzenet befejezésére vonatkozó kérést küld.

Ha a fogadási `ReceiveAndDelete`módot a beállítására állítja, mindkét lépés egyetlen kérelemben egyesül. Ezek a lépések csökkentik a műveletek teljes számát, és javíthatják a teljes üzenetátviteli. Ez a teljesítménynövekedés az üzenetek elvesztésének kockázatával jár.

A Service Bus nem támogatja a fogadási és törlési műveletek tranzakcióit. Emellett a betekintés-zárolás szemantikája szükséges minden olyan forgatókönyv esetén, amelyben az ügyfél el akarja halasztani vagy [kézbesíteni](service-bus-dead-letter-queues.md) szeretne egy üzenetet.

## <a name="client-side-batching"></a>Ügyféloldali kötegelés

Az ügyféloldali kötegelés lehetővé teszi, hogy egy várólista- vagy témakör-ügyfél egy bizonyos ideig késleltesse az üzenet küldését. Ha az ügyfél további üzeneteket küld ezen időszakon belül, a rendszer egyetlen kötegben továbbítja ezen üzeneteket. Ügyféloldali kötegelés is okoz egy várólista vagy előfizetés-ügyfél kötegelt több **Complete** kérelmek egyetlen kérelembe. Kötegelés csak az aszinkron **küldési** és **teljes** műveletekhez érhető el. A szinkron műveletek azonnal elküldésre kerülnek a Service Bus szolgáltatásba. Kötegelés nem fordul elő betekintési vagy fogadási műveletek, és nem kötegelés fordul elő az ügyfelek között.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

A .NET Standard SDK kötegelési funkciója még nem teszi elérhetővé a tulajdonság ot.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Alapértelmezés szerint az ügyfél 20 ms kötegelt időközt használ. A kötegelt időköz módosításával a [BatchFlushInterval][BatchFlushInterval] tulajdonságot az üzenetkezelő gyár létrehozása előtt módosíthatja. Ez a beállítás a gyár által létrehozott összes ügyfelet érinti.

A kötegelés letiltásához állítsa a [BatchFlushInterval][BatchFlushInterval] tulajdonságot **TimeSpan.Zero**értékre. Például:

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

A kötegelés nincs hatással a számlázható üzenetküldési műveletek számára, és csak a Service Bus ügyfélprotokollhoz érhető el a [Microsoft.ServiceBus.Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) függvény használatával. A HTTP protokoll nem támogatja a kötegelést.

> [!NOTE]
> A `BatchFlushInterval` beállítás biztosítja, hogy a kötegelés implicit az alkalmazás szempontjából. azaz; az alkalmazás `SendAsync` `CompleteAsync` kezdeményez és hív, és nem kezdeményez konkrét Batch hívásokat.
>
> Explicit ügyféloldali kötegelés lehet végrehajtani felhasználásával az alábbi módszer hívás:
> ```csharp
> Task SendBatchAsync(IEnumerable<BrokeredMessage> messages);
> ```
> Itt az üzenetek együttes méretének kisebbnek kell lennie, mint a tarifacsomag által támogatott maximális méret.

---

## <a name="batching-store-access"></a>Tároló hozzáférés kötegelése

Egy várólista, témakör vagy előfizetés átviteli mertsék, a Service Bus több üzenetet kötegel, amikor a belső tárolóba ír. Ha engedélyezve van egy várólistán vagy témakörben, az üzenetek írása az üzletbe kötegelt lesz. Ha engedélyezve van egy várólistán vagy előfizetésben, az üzenetek tárolóból való törlése kötegelésre kerül. Ha a kötegelt tárolóhozzáférés engedélyezve van egy entitáshoz, a Service Bus legfeljebb 20 ms-ig késlelteti az adott entitással kapcsolatos üzletírási műveletet.

> [!NOTE]
> Nem áll fenn az üzenetek elvesztésének veszélye kötegeléssel, még akkor sem, ha egy 20 ms-os kötegelési időköz végén szolgáltatásbusz-hiba áll fenn.

Az ezen időköz alatt előforduló további tárolási műveletek hozzáadódnak a köteghez. A kötegelt tárolóhoz való hozzáférés csak a **Küldés** i és **a teljes** műveletekre van hatással; nem érinti a fogadási műveleteket. A kötegelt üzlethozzáférés egy entitás tulajdonsága. Kötegelés történik az összes olyan entitások, amelyek lehetővé teszik a kötegelt tároló hozzáférést.

Új várólista, témakör vagy előfizetés létrehozásakor a kötegelt tárhoz való hozzáférés alapértelmezés szerint engedélyezve van.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

A kötegelt tárolóhozzáférés letiltásához szüksége `ManagementClient`lesz egy példányra. Várólista létrehozása a várólista leírásából, amely a `EnableBatchedOperations` tulajdonságot a-ra állítja. `false`

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = await managementClient.CreateQueueAsync(queueDescription);
```

További információkat a következő cikkekben talál:
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.queuedescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.QueueDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.subscriptiondescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.SubscriptionDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.topicdescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.TopicDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

A kötegelt tárolóhozzáférés letiltásához szüksége `NamespaceManager`lesz egy példányra. Várólista létrehozása a várólista leírásából, amely a `EnableBatchedOperations` tulajdonságot a-ra állítja. `false`

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = namespaceManager.CreateQueue(queueDescription);
```

További információkat a következő cikkekben talál:
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.QueueDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.SubscriptionDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.topicdescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.TopicDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

A kötegelt tárhoz való hozzáférés nincs hatással a számlázható üzenetküldési műveletek számára, és egy várólista, témakör vagy előfizetés tulajdonsága. Független az ügyfél és a Service Bus szolgáltatás között használt fogadási módtól és protokolltól.

## <a name="prefetching"></a>Prefetching

[Az előzetes betöltés](service-bus-prefetch.md) lehetővé teszi, hogy a várólista- vagy előfizetés-ügyfél további üzeneteket töltsön be a szolgáltatásból, amikor fogadási műveletet hajt végre. Az ügyfél ezeket az üzeneteket a helyi gyorsítótárban tárolja. A gyorsítótár méretét a vagy `QueueClient.PrefetchCount` `SubscriptionClient.PrefetchCount` a tulajdonságok határozzák meg. Minden ügyfél, amely lehetővé teszi az előzetes betöltése fenntartja a saját gyorsítótár. A gyorsítótár nincs megosztva az ügyfelek között. Ha az ügyfél fogadási műveletet kezdeményez, és gyorsítótára üres, a szolgáltatás üzenetek kötegét továbbítja. A köteg mérete megegyezik a gyorsítótár vagy a 256 KB méretével, attól függően, hogy melyik a kisebb. Ha az ügyfél fogadási műveletet kezdeményez, és a gyorsítótár üzenetet tartalmaz, az üzenet a gyorsítótárból kerül ki.

Ha egy üzenet előzetes betöltése, a szolgáltatás zárolja az előre beolvasott üzenetet. A zárolással az előre bekért üzenetet nem fogadhatja másik fogadó. Ha a címzett nem tudja befejezni az üzenetet, mielőtt a zárolás lejár, az üzenet elérhetővé válik a többi fogadók számára. Az üzenet előre bekért példánya a gyorsítótárban marad. A lejárt gyorsítótárazott példányt használó fogadó kivételt kap, amikor megpróbálja befejezni az üzenetet. Alapértelmezés szerint az üzenetzárolás 60 másodperc után lejár. Ez az érték 5 percre meghosszabbítható. A lejárt üzenetek felhasználásának megakadályozása érdekében a gyorsítótár méretének mindig kisebbnek kell lennie, mint az ügyfél által a zárolási időközön belül felhasználható üzenetek száma.

Ha az alapértelmezett zárolási lejárat 60 `PrefetchCount` másodperc, egy jó érték 20-szor a maximális feldolgozási sebesség az összes vevő a gyárban. Például egy gyár három fogadót hoz létre, és minden fogadó másodpercenként legfeljebb 10 üzenetet képes feldolgozni. Az előzetes betöltések száma nem haladhatja meg a 20 X 3 X 10 = 600-at. Alapértelmezés szerint `PrefetchCount` 0-ra van állítva, ami azt jelenti, hogy a szolgáltatásból nem töltődnek be további üzenetek.

Az üzenetek előzetes betöltése növeli a várólista vagy előfizetés teljes átviteli átaputét, mivel csökkenti az üzenetműveletek vagy az oda-vissza utak teljes számát. Az első üzenet beolvasása azonban hosszabb időt vesz igénybe (az üzenet méretének növekedése miatt). Az előre beolvasott üzenetek fogadása gyorsabb lesz, mivel ezeket az üzeneteket az ügyfél már letöltötte.

Az üzenet idő-hátralévő (TTL) tulajdonságát a kiszolgáló akkor ellenőrzi, amikor a kiszolgáló elküldi az üzenetet az ügyfélnek. Az ügyfél nem ellenőrzi az üzenet TTL tulajdonságát, amikor az üzenet érkezik. Ehelyett az üzenet akkor is fogadható, ha az üzenet TTL-je átment, miközben az ügyfél gyorsítótárazta az üzenetet.

Az előzetes betöltés nincs hatással a számlázható üzenetküldési műveletek számára, és csak a Service Bus ügyfélprotokollszámára érhető el. A HTTP protokoll nem támogatja az előzetes betöltést. Az előzetes betöltés szinkron és aszinkron fogadási műveletekhez is elérhető.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

További információt az alábbi `PrefetchCount` tulajdonságokban talál:

* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.servicebus.subscriptionclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

További információt az alábbi `PrefetchCount` tulajdonságokban talál:

* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.queueclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

## <a name="prefetching-and-receivebatch"></a>Előzetes betöltés és ReceiveBatch

> [!NOTE]
> Ez a szakasz csak a WindowsAzure.ServiceBus SDK-ra vonatkozik, mivel a Microsoft.Azure.ServiceBus SDK nem teszi elérhetővé a kötegelt függvényeket.

Míg a fogalmak előhívása több üzenetet együtt hasonló szemantika`ReceiveBatch`az üzenetek feldolgozása egy kötegben ( ), van néhány kisebb különbségek, amelyeket szem előtt kell tartani, ha kihasználva ezeket együtt.

Az előzetes betöltés az ügyfélen`QueueClient` (és `SubscriptionClient`) `ReceiveBatch` egy konfiguráció (vagy mód), és egy művelet (amely kérés-válasz szemantikával rendelkezik).

Ezeket együttesen vegye figyelembe a következő

* Az előzetes betöltésnek nagyobbnak vagy egyenlőnek kell lennie `ReceiveBatch`a programtól várt üzenetek számával.
* Az előzetes betöltés a másodpercenként feldolgozott üzenetek számának legfeljebb n/3-szorosa lehet, ahol n az alapértelmezett zárolási időtartam.

Vannak bizonyos kihívások, amelyek a kapzsi megközelítés (azaz vezetése a prefetch száma nagyon magas), mert ez azt jelenti, hogy az üzenet le van zárva, hogy egy adott vevő. A javaslat az, hogy próbálja ki a fent említett küszöbértékek közötti előzetes betöltési értékeket, és empirikusan azonosítsa, hogy mi illik.

## <a name="multiple-queues"></a>Több várólista

Ha a várt terhelést egyetlen várólista vagy témakör nem tudja kezelni, több üzenetkezelő entitást kell használnia. Ha több entitást használ, hozzon létre egy dedikált ügyfelet minden entitáshoz, ahelyett, hogy ugyanazt az ügyfelet használná az összes entitáshoz.

## <a name="development-and-testing-features"></a>Fejlesztési és tesztelési funkciók

> [!NOTE]
> Ez a szakasz csak a WindowsAzure.ServiceBus SDK-ra vonatkozik, mivel a Microsoft.Azure.ServiceBus SDK nem teszi elérhetővé ezt a funkciót.

A Service Bus egy olyan funkcióval rendelkezik, amelyet kifejezetten [`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering]fejlesztésre használnak, és amelyet soha nem szabad éles **környezetben használni:**.

Amikor új szabályokat vagy szűrőket ad [`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering] hozzá a témakörhöz, ellenőrizheti, hogy az új szűrőkifejezés a várt módon működik-e.

## <a name="scenarios"></a>Forgatókönyvek

A következő szakaszok ismertetik a tipikus üzenetkezelési forgatókönyveket, és ismertetik az előnyben részesített Service Bus-beállításokat. Az átviteli sebesség kicsi (kevesebb, mint 1 üzenet/másodperc), mérsékelt (1 üzenet/másodperc vagy nagyobb, de kevesebb mint 100 üzenet/másodperc) és magas (100 üzenet/másodperc vagy nagyobb). Az ügyfelek száma kis (5 vagy kevesebb), mérsékelt (5-nél nagyobb, de legfeljebb 20- as) és nagy (20-nál nagyobb) kategóriába sorolható.

### <a name="high-throughput-queue"></a>Nagy átviteli sebességű várólista

Cél: Egyetlen várólista átviteli idejének maximalizálása. A feladók és a fogadók száma kicsi.

* A várólistába való teljes küldési arány növeléséhez használjon több üzenetgyárat a feladók létrehozásához. Minden feladóhoz használjon aszinkron műveleteket vagy több szálat.
* A várólista teljes fogadási arányának növeléséhez használjon több üzenetgyárat fogadók létrehozásához.
* Aszinkron műveletek használatával kihasználhatja az ügyféloldali kötegelés előnyeit.
* Állítsa a kötegelési időközt 50 ms-ra a Service Bus ügyfélprotokoll-átvitelek számának csökkentése érdekében. Ha több feladót használ, növelje a kötegelési időközt 100 ms-ra.
* Hagyja engedélyezve a kötegelt tároló-hozzáférést. Ez a hozzáférés növeli az üzenetek várólistába írási arányának általános sebességét.
* Állítsa be az előzetes betöltési számlálót a gyár összes vevőjének maximális feldolgozási arányának 20-szorosára. Ez a szám csökkenti a Service Bus ügyfélprotokoll-átvitelek számát.

### <a name="multiple-high-throughput-queues"></a>Több nagy átviteli sebességű várólista

Cél: Több várólista teljes átviteli idejének maximalizálása. Az egyes várólisták átviteli valószínűleg közepes vagy magas.

A maximális átviteli teljesítmény elérése több várólisták között, használja a leírt beállításokat, hogy maximalizálja az átviteli egy sor. Emellett használjon különböző gyárakat a különböző várólistákból küldő vagy fogadott ügyfelek létrehozásához.

### <a name="low-latency-queue"></a>Alacsony késleltetésű várólista

Cél: Egy várólista vagy témakör teljes késleltetésének minimalizálása. A feladók és a fogadók száma kicsi. A várólista átviteli igénye kicsi vagy közepes.

* Ügyféloldali kötegelés letiltása. Az ügyfél azonnal üzenetet küld.
* A kötegelt tárolóhozzáférés letiltása. A szolgáltatás azonnal beírja az üzenetet az üzletbe.
* Ha egyetlen ügyfelet használ, állítsa az előzetes betöltési számot a fogadó feldolgozási arányának 20-szorosára. Ha egyszerre több üzenet érkezik a várólistára, a Service Bus ügyfélprotokoll egyszerre továbbítja az összeset. Amikor az ügyfél megkapja a következő üzenetet, az üzenet már a helyi gyorsítótárban van. A gyorsítótárnak kicsinek kell lennie.
* Ha több ügyfelet használ, állítsa az előzetes betöltési számot 0-ra. A számláló beállításával a második ügyfél is fogadhatja a második üzenetet, miközben az első ügyfél még mindig az első üzenetet dolgozza fel.

### <a name="queue-with-a-large-number-of-senders"></a>Nagy számú feladóval rendelkező várólista

Cél: Egy várólista vagy témakör átviteli idejének maximalizálása nagy számú feladóval. Minden feladó mérsékelt sebességgel küld üzeneteket. A vevőkészülékek száma kicsi.

A Service Bus legfeljebb 1000 egyidejű kapcsolatot biztosít egy üzenetküldő entitással (vagy 5000-et az AMQP használatával). Ez a korlát a névtér szintjén van kényszerítve, és a várólisták/témakörök/előfizetések felső határa a névtérenkénti egyidejű kapcsolatok korlátozása. Várólisták esetén ez a szám meg van osztva a feladók és a fogadók között. Ha a feladók számára mind az 1000 kapcsolat szükséges, cserélje le a várólistát egy témakörre és egyetlen előfizetésre. A témakör legfeljebb 1000 egyidejű kapcsolatot fogad el a feladóktól, míg az előfizetés további 1000 egyidejű kapcsolatot fogad el a fogadóktól. Ha több mint 1000 egyidejű feladóra van szükség, a feladóknak HTTP-n keresztül kell üzeneteket küldeniük a Service Bus protokollnak.

Az átviteli teljesítmény maximalizálásához hajtsa végre a következő lépéseket:

* Ha minden feladó egy másik folyamatban található, folyamatonként csak egyetlen gyárat használjon.
* Aszinkron műveletek használatával kihasználhatja az ügyféloldali kötegelés előnyeit.
* A Service Bus ügyfélprotokoll-átvitelek számának csökkentéséhez használja a 20 ms-os alapértelmezett kötegelési időközt.
* Hagyja engedélyezve a kötegelt tároló-hozzáférést. Ez a hozzáférés növeli az üzenetek várólistába vagy témakörbe történő írásának általános sebességét.
* Állítsa be az előzetes betöltési számlálót a gyár összes vevőjének maximális feldolgozási arányának 20-szorosára. Ez a szám csökkenti a Service Bus ügyfélprotokoll-átvitelek számát.

### <a name="queue-with-a-large-number-of-receivers"></a>Nagy számú vevővel rendelkező várólista

Cél: Maximalizálja egy várólista vagy előfizetés fogadási sebességét nagy számú fogadóval. Minden fogadó mérsékelt sebességgel fogadja az üzeneteket. A feladók száma kicsi.

A Service Bus legfeljebb 1000 egyidejű kapcsolatot biztosít egy entitással. Ha egy várólistához 1000-nél több fogadóra van szükség, cserélje le a várólistát egy témakörre és több előfizetésre. Minden előfizetés legfeljebb 1000 egyidejű kapcsolatot támogathat. Másik lehetőségként a fogadók a HTTP protokollon keresztül is elérhetik a várólistát.

Az átviteli arány maximalizálásához tegye a következőket:

* Ha minden vevő egy másik folyamatban van, folyamatonként csak egy gyárat használjon.
* A fogadók szinkron vagy aszinkron műveleteket használhatnak. Tekintettel az egyes fogadók mérsékelt fogadási sebességére, a teljes kérelem ügyféloldali kötegelése nincs hatással a vevő átviteli sebességére.
* Hagyja engedélyezve a kötegelt tároló-hozzáférést. Ez a hozzáférés csökkenti az entitás teljes terhelését. Emellett csökkenti az üzenetek várólistába vagy témakörbe történő írásának általános sebességét is.
* Állítsa be az előzetes betöltési számlálót egy kis értékre (például PrefetchCount = 10). Ez a számláló megakadályozza, hogy a fogadók tétlenek legyenek, míg más fogadók nagy számú üzenetet tárolnak.

### <a name="topic-with-a-small-number-of-subscriptions"></a>Téma kis számú előfizetéssel

Cél: A témakör átviteli igényének maximalizálása kis számú előfizetéssel. Egy üzenetet számos előfizetés fogad, ami azt jelenti, hogy az összes előfizetés relevancia-díja nagyobb, mint a küldési díj. A feladók száma kicsi. A fogadók száma előfizetésenként kicsi.

Az átviteli arány maximalizálásához tegye a következőket:

* A témakör teljes küldési arányának növeléséhez használjon több üzenetgyárat a feladók létrehozásához. Minden feladóhoz használjon aszinkron műveleteket vagy több szálat.
* Az előfizetésteljes fogadási arányának növeléséhez használjon több üzenetgyárat fogadók létrehozásához. Minden fogadóhoz használjon aszinkron műveleteket vagy több szálat.
* Aszinkron műveletek használatával kihasználhatja az ügyféloldali kötegelés előnyeit.
* A Service Bus ügyfélprotokoll-átvitelek számának csökkentéséhez használja a 20 ms-os alapértelmezett kötegelési időközt.
* Hagyja engedélyezve a kötegelt tároló-hozzáférést. Ez a hozzáférés növeli az üzenetek témakörbe való írásának általános sebességét.
* Állítsa be az előzetes betöltési számlálót a gyár összes vevőjének maximális feldolgozási arányának 20-szorosára. Ez a szám csökkenti a Service Bus ügyfélprotokoll-átvitelek számát.

### <a name="topic-with-a-large-number-of-subscriptions"></a>Téma nagy számú előfizetéssel

Cél: A témakör nagy számú előfizetéssel való átviteli idejének maximalizálása. Egy üzenetet számos előfizetés fogad, ami azt jelenti, hogy az összes előfizetés összesített fogadási díja sokkal nagyobb, mint a küldési sebesség. A feladók száma kicsi. A fogadók száma előfizetésenként kicsi.

A nagy számú előfizetéssel rendelkező témakörök általában alacsony teljes átviteli feszültséget jelentenek, ha az összes üzenet az összes előfizetéshez van irányítva. Ezt az alacsony átviteli feszültséget az okozza, hogy minden üzenet többször érkezik, és a témakörben található összes üzenet és annak összes előfizetése ugyanabban a tárolóban van tárolva. Feltételezzük, hogy a feladók száma és a fogadók száma előfizetésenként kicsi. A Service Bus témakörenként legfeljebb 2000 előfizetést támogat.

Az átviteli arány maximalizálásához próbálkozzon az alábbi lépésekkel:

* Aszinkron műveletek használatával kihasználhatja az ügyféloldali kötegelés előnyeit.
* A Service Bus ügyfélprotokoll-átvitelek számának csökkentéséhez használja a 20 ms-os alapértelmezett kötegelési időközt.
* Hagyja engedélyezve a kötegelt tároló-hozzáférést. Ez a hozzáférés növeli az üzenetek témakörbe való írásának általános sebességét.
* Állítsa az előzetes betöltési számlálót a várt fogadási arány 20-szorosára másodpercben. Ez a szám csökkenti a Service Bus ügyfélprotokoll-átvitelek számát.

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