---
title: "Az Azure Event Hubs programozási útmutatója | Microsoft Docs"
description: "Az Azure Event Hubs az Azure .NET SDK használatával írhat kódot."
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 64cbfd3d-4a0e-4455-a90a-7f3d4f080323
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/17/2017
ms.author: sethm
ms.openlocfilehash: 405ec2b27b488b570c4a5c86e4950ff98233360e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="event-hubs-programming-guide"></a>Event Hubs programozási útmutató

A cikk ismerteti az Azure Event Hubs és az Azure .NET SDK használatával programozás olyan gyakori forgatókönyveket tartalmaz. A témakör feltételezi az Event Hubs szolgáltatással kapcsolatos előzetes ismeretek meglétét. Az Event Hubs fogalmi áttekintése: [Event Hubs – áttekintés](event-hubs-what-is-event-hubs.md).

## <a name="event-publishers"></a>Esemény-közzétevők

Az eseményközpontok felé, vagy a HTTP POST használatával vagy egy AMQP 1.0-kapcsolaton keresztül küldött események. A választott használandó és mikor függ az adott forgatókönyv határozza meg. Az AMQP 1.0-kapcsolatok mérése közvetített kapcsolatként történik a Service Bus szolgáltatásban, és az olyan forgatókönyvekben megfelelőbbek, ahol gyakoriak a nagyobb üzenetmennyiségek és alacsony késés szükséges, mivel ezek állandó üzenetkezelési csatornát biztosítanak.

Az Event Hubs a [NamespaceManager][] osztály használatával hozható létre és felügyelhető. A .NET által felügyelt API-k használatakor az adatoknak az Event Hubs számára történő közzétételére szolgáló elsődleges szerkezetek az [EventHubClient](/dotnet/api/microsoft.servicebus.messaging.eventhubclient) és az [EventData][] osztály. [EventHubClient][] biztosít, amelyben az események küldhetők az event hubs AMQP kommunikációs csatornát. A [EventData][] osztály egy eseményt képvisel, és üzeneteket az eseményközpontba közzétételére szolgál. Ez az osztály az esemény törzsét, bizonyos metaadatait és fejléc-információit tartalmazza. Más tulajdonságokkal is bővül a [EventData][] objektumot, ahogy keresztülhalad az eseményközpontban.

## <a name="get-started"></a>Bevezetés

Az Event Hubs szolgáltatást támogató .NET-osztályok a Microsoft.ServiceBus.dll szerelvényben találhatók. A Service Bus API-ra való hivatkozásnak és az alkalmazás az összes Service Bus-függőséggel való konfigurálásának a legegyszerűbb módja, ha letölti a [Service Bus NuGet-csomagot](https://www.nuget.org/packages/WindowsAzure.ServiceBus). Használhatja a Visual Studio [Csomagkezelő konzolját](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) is. Ehhez adja ki a következő parancsot a [Csomagkezelő konzol](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) ablakában:

```
Install-Package WindowsAzure.ServiceBus
```

## <a name="create-an-event-hub"></a>Eseményközpont létrehozása
A [NamespaceManager][] osztályt használhatja az eseményközpontok létrehozásához. Példa:

```csharp
var manager = new Microsoft.ServiceBus.NamespaceManager("mynamespace.servicebus.windows.net");
var description = manager.CreateEventHub("MyEventHub");
```

A legtöbb esetben ajánlott a [CreateEventHubIfNotExists][] metódusok használata a kivételek elkerülése érdekében, ha a szolgáltatás újraindul. Példa:

```csharp
var description = manager.CreateEventHubIfNotExists("MyEventHub");
```

Minden Event Hubs létrehozási művelet, beleértve a [CreateEventHubIfNotExists][] műveletet is, **Kezelés** jogosultságokat igényel az adott névtérben. Amennyiben a közzétevő vagy felhasználó alkalmazások jogosultságait korlátozni kívánja, elkerülheti ezeket a létrehozásiművelet-hívásokat az éles kódban, ha korlátozott jogosultságokkal rendelkező hitelesítési adatokat használ.

A [EventHubDescription](/dotnet/api/microsoft.servicebus.messaging.eventhubdescription) osztály tartalmazza az eseményközpontban, beleértve az engedélyezési szabályokat, az üzenetek megőrzési idejét, partíciók azonosítóit, állapota és az elérési utat. Ez az osztály használatával frissítse a metaadatokat az eseményközpontban.

## <a name="create-an-event-hubs-client"></a>Event Hubs-ügyfél létrehozása
Az elsődleges osztály az Event Hubs kommunikáció [Microsoft.ServiceBus.Messaging.EventHubClient][EventHubClient]. Ez az osztály küldői és fogadói képességeket is biztosít. Az osztály a [Create](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.create) metódus használatával példányosítható, ahogy az alábbi példában látható.

```csharp
var client = EventHubClient.Create(description.Path);
```

Ez a metódus a Service Bus szolgáltatásnak az App.config fájl `appSettings` szakaszában található kapcsolati információit használja. A Service Bus kapcsolati információit tároló `appSettings` XML egy példájáért tekintse meg a [Microsoft.ServiceBus.Messaging.EventHubClient.Create(System.String)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Create_System_String_) metódus dokumentációját.

Egy másik lehetőség az ügyfél létrehozása egy kapcsolati karakterláncból. Ez a lehetőség akkor működik jól, ha Azure feldolgozói szerepköröket használ, mivel a karakterláncot tárolhatja a feldolgozó konfigurációs tulajdonságaiban. Példa:

```csharp
EventHubClient.CreateFromConnectionString("your_connection_string");
```

A kapcsolati karakterlánc formátumba megegyezik a korábbi metódusok App.config fájljában használt formátummal:

```
Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[key]
```

Végül, [EventHubClient][] objektum létrehozható [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) példányból is, amint az alábbi példában látható.

```csharp
var factory = MessagingFactory.CreateFromConnectionString("your_connection_string");
var client = factory.CreateEventHubClient("MyEventHub");
```

Fontos megjegyezni, hogy az üzenetkezelési gyár példányából létrehozott további [EventHubClient][] objektumok ugyanazt az alapul szolgáló TCP-kapcsolatot használják majd. Ezért ezek az objektumok ügyféloldali korláttal rendelkeznek majd az átvitelre vonatkozóan. A [Create](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Create_System_String_) metódus újból felhasználja az üzenetkezelési gyárat. Amennyiben nagyon nagy átviteli kapacitás szükséges egyetlen küldőtől, létrehozhat több üzenetkezelési gyárat, illetve egy [EventHubClient][] objektumot mindegyik üzenetkezelési gyárból.

## <a name="send-events-to-an-event-hub"></a>Események küldése az event hubs
Események küldött hozzon létre egy eseményközpontba egy [EventData][] példányt, és küldje el azt a [küldése](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Send_Microsoft_ServiceBus_Messaging_EventData_) metódust. Ez a metódus egyetlen veszi [EventData][] példányparamétert és szinkron módon elküldi azt az eseményközpontba.

## <a name="event-serialization"></a>Eseményszerializáció
Az [EventData][] osztály [négy túlterhelt konstruktorral](/dotnet/api/microsoft.servicebus.messaging.eventdata#constructors_) rendelkezik, amelyek különféle paramétereket vesznek fel, például egy objektumot és egy szerializálót, egy bájttömböt vagy egy streamet. Az [EventData][] osztály példányosítható is, és törzsstream beállítható ezt követően. Ha JSON-t használ az [EventData][] osztállyal, az **Encoding.UTF8.GetBytes()** használatával kérheti le a bájttömböt a JSON-kódolású karakterlánchoz.

## <a name="partition-key"></a>Partíciókulcs
Az [EventData][] osztály a [PartitionKey][] tulajdonsággal rendelkezik, amelynek segítségével a küldő meghatározhat egy értéket, amelynek kivonatolásával létrehozható egy partíció-hozzárendelés. A partíciókulcsok használatával ugyanazzal a kulccsal rendelkező események küldött egyazon partícióra kerüljenek a központ biztosítja. Az általános partíciókulcsok többek közt a felhasználói munkamenetek azonosítói és az egyedi küldőazonosítók. A [PartitionKey][] tulajdonság nem kötelező, és a [Microsoft.ServiceBus.Messaging.EventHubClient.Send(Microsoft.ServiceBus.Messaging.EventData)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Send_Microsoft_ServiceBus_Messaging_EventData_) vagy a [Microsoft.ServiceBus.Messaging.EventHubClient.SendAsync(Microsoft.ServiceBus.Messaging.EventData)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_SendAsync_Microsoft_ServiceBus_Messaging_EventData_) metódus használatakor adható meg. Ha nem ad meg értéket a [PartitionKey][] számára, a küldött események elosztása a partíciókra ciklikus időszeleteléses modell használatával fog történni.

### <a name="availability-considerations"></a>Rendelkezésre állási szempontok

A partíciós kulcs használata nem kötelező, és meg kell alaposan fontolja meg, hogy használhatja-e. Sok esetben a partíciókulcsok használatával akkor hasznos, ha fontos esemény rendezés. A partíciós kulcs használata esetén ezek a partíciók egycsomópontos rendelkezésre szükség, és kiesések is időbeli; például, ha a számítási csomópontok újraindítás és a javítás. Ilyen el a Partícióazonosító és adott partíció valamilyen okból kifolyólag nem érhető el, ha az adott partíció adatainak elérésére tett kísérlet sikertelen lesz. Ha magas rendelkezésre állású legfontosabb, nem ad meg partíciókulcsot; Ebben az esetben a fentiekben említett Ciklikus időszeleteléses modell használatával partíciók események kapnak. Ebben a forgatókönyvben teszi rendelkezésre állási (nincs Partícióazonosító) és (a Partícióazonosító események rögzítése) konzisztencia között kifejezett választást.

Egy másik szempont kezeli a késlelteti az események feldolgozásával. Bizonyos esetekben előfordulhat, hogy célszerű dobja el az adatokat, és próbálkozzon újra, mint próbálja és feldolgozási tartani, amely potenciálisan okozhat további alárendelt feldolgozási késedelmeket. Például a tőzsdei árfolyamjelző célszerűbb várja meg a teljes legfrissebb adatokat, de egy élő vagy gyors, az adatok ahelyett, hogy kellene VOIP a forgatókönyv, még akkor is, ha nem, akkor a teljes.

A rendelkezésre állási lehetőségekért megadott forgatókönyvekben választása a következő hiba egyik kezelési stratégiák:

- Leállítás (olvasásakor az Event Hubs dolgot megoldásáig leállítás)
- Közvetlen (üzenetek nem fontos, dobja el őket)
- Ismételje meg a (az üzenetek ismertető elférjen újrapróbálkozási)
- [Kézbesítetlen levelek](../service-bus-messaging/service-bus-dead-letter-queues.md) (várólista használja, vagy egy másik eseményközpontnak azon halott levél csak azokat az üzeneteket nem tudta feldolgozni)

További információt és rendelkezésre állás és a konzisztencia közötti kompromisszumot kapcsolatos döntéseken: [rendelkezésre állását és az Event Hubs következetes](event-hubs-availability-and-consistency.md). 

## <a name="batch-event-send-operations"></a>Kötegelt eseményküldési műveletek
Az események kötegekben való küldésével jelentősen növelhető az átvitel. A [SendBatch](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_SendBatch_System_Collections_Generic_IEnumerable_Microsoft_ServiceBus_Messaging_EventData__) metódus egy **IEnumerable** típusú paramétert [EventData][] és az egész köteget elemi műveletként az event hubs küld.

```csharp
public void SendBatch(IEnumerable<EventData> eventDataList);
```

Vegye figyelembe, hogy a kötegek mérete egyenként nem haladhatja meg az események 256 KB-os korlátját. Továbbá a kötegben lévő egyes üzenetek ugyanazzal a közzétevői identitással rendelkezik majd. A küldő felelőssége annak biztosítása, hogy a köteg mérete nem haladja meg az események maximális méretét. Ha mégis meghaladja, az ügyfél **Küldési** hibát jelez.

## <a name="send-asynchronously-and-send-at-scale"></a>Aszinkron küldés és nagy léptékű küldés
Is küldhet események eseményközpontokba való aszinkron módon történik. Az aszinkron küldés növelheti az ügyfelek üzenetküldési sebességét. A [Send](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Send_Microsoft_ServiceBus_Messaging_EventData_) és a [SendBatch](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_SendBatch_System_Collections_Generic_IEnumerable_Microsoft_ServiceBus_Messaging_EventData__) metódus egyaránt elérhetők aszinkron verzióban, amely egy [Task](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx) objektumot ad vissza. Jóllehet ezzel a technikával növelhető az átvitel, ugyanakkor azt is eredményezheti, hogy az ügyfél továbbra is küldi az eseményeket, miután az Event Hubs szolgáltatás leszabályozta, és így az ügyfél hibába ütközhet, vagy üzenetek veszhetnek el, ha a megvalósítás helytelenül ment végbe. Emellett használhatja a [RetryPolicy](/dotnet/api/microsoft.servicebus.messaging.cliententity#Microsoft_ServiceBus_Messaging_ClientEntity_RetryPolicy) tulajdonságot az ügyfélen az ügyfél újrapróbálkozási beállításainak vezérlésére.

## <a name="create-a-partition-sender"></a>Partícióküldő létrehozása
Bár a leggyakrabban használt üzenetküldési nem tartozik partíciós kulcs az eseményközpontok felé, bizonyos esetekben előfordulhat, hogy kívánt események küldése közvetlenül egy adott partícióra. Példa:

```csharp
var partitionedSender = client.CreatePartitionedSender(description.PartitionIds[0]);
```

[CreatePartitionedSender](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_CreatePartitionedSender_System_String_) adja vissza egy [EventHubSender](/dotnet/api/microsoft.servicebus.messaging.eventhubsender) használható események közzétételére egy adott esemény hub partíció objektum.

## <a name="event-consumers"></a>Eseményfelhasználók
Az Event Hubs két elsődleges modellel rendelkezik az események felhasználásához: közvetlen fogadók és a magasabb szintű absztrakciók, például az [EventProcessorHost][]. A közvetlen fogadók felelőssége koordinálni a saját hozzáférésüket az egyes partíciókhoz egy adott felhasználói csoportban.

### <a name="direct-consumer"></a>Közvetlen felhasználó
Az adott felhasználói csoportban lévő egyes partíciók olvasásának legközvetlenebb módja az [EventHubReceiver](/dotnet/apie/microsoft.servicebus.messaging.eventhubreceiver) osztály használata. Az osztály egy példányának létrehozásához az [EventHubConsumerGroup](/dotnet/api/microsoft.servicebus.messaging.eventhubconsumergroup) osztály egy példányát kell használni. A következő példában a felhasználói csoport fogadójának létrehozásakor a partícióazonosítót kell megadni.

```csharp
EventHubConsumerGroup group = client.GetDefaultConsumerGroup();
var receiver = group.CreateReceiver(client.GetRuntimeInformation().PartitionIds[0]);
```

A [CreateReceiver](/dotnet/api/microsoft.servicebus.messaging.eventhubconsumergroup#methods_summary) metódus több túlterheléssel rendelkezik, amelyek segítik a létrehozni kívánt olvasó vezérlését. Ezek a metódusok lehetnek egy eltolás megadása karakterlánc vagy időbélyeg használatával, valamint annak megadása, hogy a megadott eltolást tartalmazza-e a visszaadott stream, vagy azután kezdődjön. Miután létrehozta a fogadót, máris fogadhatja az eseményeket a visszaadott objektumon. A [Receive](/dotnet/api/microsoft.servicebus.messaging.eventhubreceiver#methods_summary) metódus négy túlterheléssel rendelkezik, amelyek a fogadási művelet paramétereit szabályozzák, például a kötegméretet és a várakozási időt. A metódusok aszinkron verzióinak használatával itt is növelhető a felhasználók átvitele. Példa:

```csharp
bool receive = true;
string myOffset;
while(receive)
{
    var message = receiver.Receive();
    myOffset = message.Offset;
    string body = Encoding.UTF8.GetString(message.GetBytes());
    Console.WriteLine(String.Format("Received message offset: {0} \nbody: {1}", myOffset, body));
}
```

Adott partíciók vonatkozásában az üzenetek fogadása ugyanabban a sorrendben, amelyben lettek küldve az eseményközpontba. Az eltolás egy karakterlánc-token, amely egy adott üzenetet azonosít a partíción.

Vegye figyelembe, hogy egy felhasználói csoportban egy adott partíció egy adott pillanatban nem rendelkezhet 5-nél több egyidejű olvasóval. Ahogy az olvasók csatlakoznak vagy lecsatlakoznak, a munkameneteik néhány percig még aktívak maradhatnak, mielőtt a szolgáltatás észleli, hogy lecsatlakoztak. Ezen időtartam során a partícióra való újracsatlakozás meghiúsulhat. Átfogó példát a közvetlen fogadóknak az Event Hubs írása, tekintse meg a [Event Hubs közvetlen fogadók](https://code.msdn.microsoft.com/Event-Hub-Direct-Receivers-13fa95c6) minta.

### <a name="event-processor-host"></a>Event Processor Host
Az [EventProcessorHost][] osztály az eseményközpontokból származó adatokat dolgozza fel. Akkor használja ezt a megvalósítást, ha a .NET platformon hoz létre eseményolvasókat. Az [EventProcessorHost][] egy szálbiztos, több folyamatot lehetővé tevő, biztonságos futtatókörnyezetet biztosít az eseményfeldolgozói megvalósításokhoz, ami lehetővé teszi az ellenőrzőpontok használatát és a partícióbérlés-kezelést is.

Az [EventProcessorHost][] osztály használatához megvalósítható az [IEventProcessor](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor). Ez a felület három metódust tartalmaz:

* [OpenAsync](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor#Microsoft_ServiceBus_Messaging_IEventProcessor_OpenAsync_Microsoft_ServiceBus_Messaging_PartitionContext_)
* [CloseAsync](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor#Microsoft_ServiceBus_Messaging_IEventProcessor_CloseAsync_Microsoft_ServiceBus_Messaging_PartitionContext_Microsoft_ServiceBus_Messaging_CloseReason_)
* [ProcessEventsAsync](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor#Microsoft_ServiceBus_Messaging_IEventProcessor_ProcessEventsAsync_Microsoft_ServiceBus_Messaging_PartitionContext_System_Collections_Generic_IEnumerable_Microsoft_ServiceBus_Messaging_EventData__)

Az események feldolgozásának indításához példányosítható [EventProcessorHost][], így a megfelelő paramétereket az eseményközpont. Ezután a [RegisterEventProcessorAsync](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost#Microsoft_ServiceBus_Messaging_EventProcessorHost_RegisterEventProcessorAsync__1) meghívásával regisztrálja az [IEventProcessor](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor) megvalósítást a futtatókörnyezetben. Ezen a ponton az állomás megpróbál bérletet a "mohó" algoritmus segítségével eseményközpont minden partícióján szerezni. Ezek a bérletek egy adott időtartamra szólnak, és azt követően meg kell újítani őket. Ahogy újabb csomópontok, esetünkben feldolgozópéldányok válnak online állapotúvá, bérletfoglalásokat végeznek, és idővel a terhelés elmozdul a csomópontok között, ahogy egyre több bérletet próbálnak szerezni.

![Event Processor Host](./media/event-hubs-programming-guide/IC759863.png)

Idővel kialakul egy egyensúlyi állapot. Ennek a dinamikus képességnek a segítségével processzoralapú automatikus skálázás alkalmazható a felhasználókra felfelé és lefelé méretezéshez egyaránt. Mivel az eseményközpontok nem rendelkeznek közvetlen megoldással az üzenetek számlálásához, az átlagos processzorhasználat gyakran a legjobb módszer a háttérrendszeri vagy felhasználói lépték megállapítására. Ha a közzétevők idővel több eseményt tesznek közzé, mint amennyit a felhasználók fel tudnak dolgozni, a felhasználókon a processzor növelésével indítható el a feldolgozópéldányok számának automatikus skálázása.

Az [EventProcessorHost][] osztály megvalósít továbbá egy Azure Storage-alapú ellenőrzőpont-kezelési mechanizmust is. A mechanizmus az eltolást partíciónkénti alapon tárolja, így mindegyik felhasználó megállapíthatja, hogy melyik volt az előző felhasználó utolsó ellenőrzőpontja. Ahogy a partíciók váltanak a csomópontok között a bérletek használatával, ez a szinkronizálási mechanizmus az, amely leginkább támogatja a terhelés áthelyezését.

## <a name="publisher-revocation"></a>Közzétevők visszavonása
A speciális futásidejű szolgáltatásai mellett [EventProcessorHost][], az Event Hubs lehetővé teszi, hogy a közzétevők visszavonásával ahhoz, hogy adott közzétevők közzétételét egy eseményközpontba blokkolása. Ezek a szolgáltatások akkor igazán hasznosak, ha egy közzétevői token biztonsága sérült, vagy egy szoftverfrissítés eredményeként nem megfelelően működik. Ilyen helyzetekben a közzétevő identitása (amely a SAS-token részét képezi) blokkolható az események közzétételének a megakadályozásához.

A közzétevők visszavonásával és az eseményközpontokba közzétevőként való küldés módjával kapcsolatban a [Event Hubs Large Scale Secure Publishing](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab) (Event Hubs nagyléptékű biztonságos közzététele) mintában tekinthet meg további információt.

## <a name="next-steps"></a>Következő lépések
Az Event Hubs-forgatókönyvekkel kapcsolatos további információkért látogasson el a következő hivatkozásokra:

* [Event Hubs API overview](event-hubs-api-overview.md) (Event Hubs API – áttekintés)
* [Mi az az Event Hubs](event-hubs-what-is-event-hubs.md)
* [Rendelkezésre állás és konzisztencia az Event Hubsban](event-hubs-availability-and-consistency.md)
* [Event Processor Host – API-referencia](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)

[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[EventHubClient]: /dotnet/api/microsoft.servicebus.messaging.eventhubclient
[EventData]: /dotnet/api/microsoft.servicebus.messaging.eventdata
[CreateEventHubIfNotExists]: /dotnet/api/microsoft.servicebus.namespacemanager.createeventhubifnotexists
[PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.eventdata#Microsoft_ServiceBus_Messaging_EventData_PartitionKey
[EventProcessorHost]: /dotnet/api/microsoft.servicebus.messaging.eventprocessorhost
