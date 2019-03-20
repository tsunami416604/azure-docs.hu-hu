---
title: Programozási útmutató – Azure Event Hubs |} A Microsoft Docs
description: Ez a cikk információt nyújt az írási kódot az Azure Event hubs az Azure .NET SDK használatával.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
ms.service: event-hubs
ms.custom: seodec18
ms.topic: article
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: e96abfbdbd9394d27fbffbcb64aa9cbfabbbcb69
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57835435"
---
# <a name="programming-guide-for-azure-event-hubs"></a>Az Azure Event Hubs programozási útmutatója
Ez a cikk ismerteti az Azure Event Hubs használatával kód írása néhány gyakori forgatókönyvet. A témakör feltételezi az Event Hubs szolgáltatással kapcsolatos előzetes ismeretek meglétét. Az Event Hubs fogalmi áttekintése: [Event Hubs – áttekintés](event-hubs-what-is-event-hubs.md).

## <a name="event-publishers"></a>Esemény-közzétevők

Eseményeket küldeni egy eseményközpontba, vagy a HTTP POST használatával vagy egy AMQP 1.0-kapcsolaton keresztül. Hogy melyiket kívánja használni a kiválasztott és mikor javítása folyamatban az adott forgatókönyvtől függ. Az AMQP 1.0-kapcsolatok mérése közvetített kapcsolatként történik a Service Bus szolgáltatásban, és az olyan forgatókönyvekben megfelelőbbek, ahol gyakoriak a nagyobb üzenetmennyiségek és alacsony késés szükséges, mivel ezek állandó üzenetkezelési csatornát biztosítanak.

A .NET által felügyelt API-k használatakor az adatoknak az Event Hubs számára történő közzétételére szolgáló elsődleges szerkezetek az [EventHubClient][] és az [EventData][] osztály. [EventHubClient][] biztosít, amelyben az események küldhetők az event hubs AMQP kommunikációs csatornát. A [EventData][] osztály egy eseményt képvisel, és az üzeneteknek az eseményközpontba való közzétételéhez használható. Ez az osztály az esemény törzsét, bizonyos metaadatait és fejléc-információit tartalmazza. Egyéb tulajdonságokkal is bővül, a [EventData][] objektumot, ahogy keresztülhalad az eseményközpontba.

## <a name="get-started"></a>Bevezetés
A .NET-osztályok támogató az Event Hubs szerepelnek a [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) NuGet-csomagot. Telepítheti a Visual Studio Solution explorer használatával vagy a [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) a Visual Studióban. Ehhez adja ki a következő parancsot a [Csomagkezelő konzol](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) ablakában:

```shell
Install-Package Microsoft.Azure.EventHubs
```

## <a name="create-an-event-hub"></a>Eseményközpont létrehozása

Az Azure Portalon, az Azure PowerShell vagy az Azure CLI használatával az Event Hubs létrehozása. További információkért lásd: [hozzon létre egy Event Hubs-névtér és a egy eseményközpontot, az Azure portal használatával](event-hubs-create.md).

## <a name="create-an-event-hubs-client"></a>Event Hubs-ügyfél létrehozása

Az elsődleges osztály az Event hubs szolgáltatással való interakcióhoz [Microsoft.Azure.EventHubs.EventHubClient][EventHubClient]. Ez az osztály használatával példányosítható a [CreateFromConnectionString](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createfromconnectionstring) módszerhez, ahogy az alábbi példában látható:

```csharp
private const string EventHubConnectionString = "Event Hubs namespace connection string";
private const string EventHubName = "event hub name";

var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
{
    EntityPath = EventHubName

};
eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

## <a name="send-events-to-an-event-hub"></a>Események küldése eseményközpontba

Eseményeket egy eseményközpontba létrehozásával küldeni egy [EventHubClient][] példány, és küldje el azt aszinkron módon történik a [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) metódus. Ez a metódus szükséges egyetlen [EventData][] példányparamétert, és aszinkron módon küld egy eseményközpontnak.

## <a name="event-serialization"></a>Eseményszerializáció

A [EventData][] osztály rendelkezik [két túlterhelt konstruktorral](/dotnet/api/microsoft.azure.eventhubs.eventdata.-ctor) , amelyek különböző paramétereket, bájt vagy Bájttömbbel, az esemény hasznos adatforgalmat jelölő igénybe. Ha JSON-t használ az [EventData][] osztállyal, az **Encoding.UTF8.GetBytes()** használatával kérheti le a bájttömböt a JSON-kódolású sztringhez. Példa:

```csharp
for (var i = 0; i < numMessagesToSend; i++)
{
    var message = $"Message {i}";
    Console.WriteLine($"Sending message: {message}");
    await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
}
```

## <a name="partition-key"></a>Partíciókulcs

Eseményadatokat küld, ha egy partíció-hozzárendelést kivonatolásával értéket is megadhat. Megadhatja, hogy a partíció használata a [PartitionSender.PartitionID](/dotnet/api/microsoft.azure.eventhubs.partitionsender.partitionid) tulajdonság. Azonban a partíciók használata mellett is választhat rendelkezésre állás és konzisztencia. 

### <a name="availability-considerations"></a>Rendelkezésre állási szempontok

A partíciós kulcs használata nem kötelező, és gondolja át alaposan-e használni. Ha nem ad meg partíciókulcsot az események közzétételekor, a rendszer ciklikus időszeleteléses hozzárendelést használ. Sok esetben a partíciókulcsok használatával akkor hasznos, ha fontos események rendezése. Ha partíciókulcsot használ, ezeket a partíciókat megkövetelése a rendelkezésre állás egyetlen csomóponton, és idővel; leállások is például ha a számítási csomópontok újraindítás és a javítás. Mint ilyen Ha a Partícióazonosító és adott partíció valamilyen okból elérhetetlenné válik, az adott partíciók adatainak elérésére tett kísérlet sikertelen lesz. Ha magas rendelkezésre állás legfontosabb, nem ad meg partíciókulcsot; Ebben az esetben események küldése a partíciókat a korábban leírt Ciklikus időszeleteléses modell használatával. Ebben a forgatókönyvben egy explicit lehetőség rendelkezésre állás (nincs Partícióazonosító) és a konzisztencia (rögzítés a Partícióazonosító események) között végez.

Egy másik szempont események feldolgozása az késleltetések kezeli. Néhány esetben lehet jobb dobja el az adatokat, majd próbálkozzon újra, mint próbálja feldolgozási feladatokhoz ajánljuk, amelyek teljesítményproblémákat okozhat, további alárendelt feldolgozási késedelem tartani. Például a tőzsdei árfolyamjelző célszerűbb várakozási teljes naprakész adatokat, de az élő Csevegésben vagy VOIP-forgatókönyv, ahelyett hogy az adatok gyors, még akkor is, ha még nem fejeződött be.

Rendelkezésre állási ezek alapján ezekben a forgatókönyvekben, előfordulhat, hogy válasszon egyet a következő hiba kezelési stratégiák:

- Leállítás (stop mindaddig, amíg a dolgok rögzítettek az Event hubs-Eseményközpontok olvasása)
- Dobja el (üzenetek nem fontos, el kell dobni ezeket)
- Ismételje meg a (ismételje meg az üzenetek tetszés szerint)

További információ és rendelkezésre állás és konzisztencia közötti skálán szóló vita: [rendelkezésre állás és konzisztencia az Event Hubs](event-hubs-availability-and-consistency.md). 

## <a name="batch-event-send-operations"></a>Kötegelt eseményküldési műveletek

Események kötegekben való küldésével segíthet az átviteli sebesség növelése. Használhatja a [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) hozzon létre egy, amelyhez objektumok később hozzáadhatók a batch API-t egy [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) hívja.

Az egy kötegben nem haladhatja meg a 1 Megabájtos korlátot, ha az eseményeket. Továbbá a kötegben lévő egyes üzenetek ugyanazzal a közzétevői identitással rendelkezik majd. A küldő felelőssége annak biztosítása, hogy a köteg mérete nem haladja meg az események maximális méretét. Ha mégis meghaladja, az ügyfél **Küldési** hibát jelez. Használhatja a segédmetódus [EventHubClient.CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) , győződjön meg arról, hogy a batch nem haladja meg az 1 MB. Megjelenik egy üres [EventDataBatch](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch) származó a [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) API-t, majd [TryAdd](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch.tryadd) eseményeket létrehozásához a batch hozzáadásához. 

## <a name="send-asynchronously-and-send-at-scale"></a>Aszinkron küldés és nagy léptékű küldés

Eseményeket küldeni egy eseményközpontba aszinkron módon történik. Az aszinkron küldés növeli az, amikor egy ügyfél az üzenetküldési sebességét. [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) adja vissza egy [feladat](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx) objektum. Használhatja a [RetryPolicy](/dotnet/api/microsoft.servicebus.retrypolicy) osztály vezérlő ügyfélnek az ügyfél újrapróbálkozási beállítások megadása.

## <a name="event-consumers"></a>Eseményfelhasználók
Az [EventProcessorHost][] osztály az eseményközpontokból származó adatokat dolgozza fel. Akkor használja ezt a megvalósítást, ha a .NET platformon hoz létre eseményolvasókat. Az [EventProcessorHost][] egy szálbiztos, több folyamatot lehetővé tevő, biztonságos futtatókörnyezetet biztosít az eseményfeldolgozói megvalósításokhoz, ami lehetővé teszi az ellenőrzőpontok használatát és a partícióbérlés-kezelést is.

Az [EventProcessorHost][] osztály használatához megvalósítható az [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). Ez az interfész négy metódust tartalmaz:

* [OpenAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.openasync)
* [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.closeasync)
* [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)
* [ProcessErrorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processerrorasync)

Az események feldolgozásának indításához példányosítható [EventProcessorHost][], így a megfelelő paramétereket az eseményközpont. Példa:

> [!NOTE]
> EventProcessorHost és a kapcsolódó osztályok szerepelnek a **Microsoft.Azure.EventHubs.Processor** csomagot. Adja hozzá a csomagot a Visual Studio-projektben található utasításokat követve [Ez a cikk](event-hubs-dotnet-framework-getstarted-receive-eph.md#add-the-event-hubs-nuget-package) vagy a következő parancsot a a [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) ablak:`Install-Package Microsoft.Azure.EventHubs.Processor`.

```csharp
var eventProcessorHost = new EventProcessorHost(
        EventHubName,
        PartitionReceiver.DefaultConsumerGroupName,
        EventHubConnectionString,
        StorageConnectionString,
        StorageContainerName);
```

Ezután [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync) regisztrálni a [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) megvalósítást a futtatókörnyezetben:

```csharp
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();
```

Ezen a ponton az állomás megpróbál bérletet szerezni az event hubs a "mohó" algoritmus használatával minden partíció. Ezek a bérletek az elmúlt egy adott időtartamra szólnak, és ezután meg kell újítani. Ahogy újabb csomópontok, esetünkben feldolgozópéldányok válnak online állapotúvá, bérletfoglalásokat végeznek, és idővel a terhelés elmozdul a csomópontok között, ahogy egyre több bérletet próbálnak szerezni.

![Event Processor Host](./media/event-hubs-programming-guide/IC759863.png)

Idővel kialakul egy egyensúlyi állapot. Ennek a dinamikus képességnek a segítségével processzoralapú automatikus skálázás alkalmazható a felhasználókra felfelé és lefelé méretezéshez egyaránt. Az Event Hubs nincs közvetlen megoldással az üzenetek számlálásához, mert átlagos processzorhasználat gyakran a legjobb módszer vissza záró vagy felhasználói lépték megállapítására. Ha a közzétevők idővel több eseményt tesznek közzé, mint amennyit a felhasználók fel tudnak dolgozni, a felhasználókon a processzor növelésével indítható el a feldolgozópéldányok számának automatikus skálázása.

Az [EventProcessorHost][] osztály megvalósít továbbá egy Azure Storage-alapú ellenőrzőpont-kezelési mechanizmust is. A mechanizmus az eltolást partíciónkénti alapon tárolja, így mindegyik felhasználó megállapíthatja, hogy melyik volt az előző felhasználó utolsó ellenőrzőpontja. Ahogy a partíciók váltanak a csomópontok között a bérletek használatával, ez a szinkronizálási mechanizmus az, amely leginkább támogatja a terhelés áthelyezését.

## <a name="publisher-revocation"></a>Közzétevők visszavonása

Fejlett futtatókörnyezeti szolgáltatásai mellett [EventProcessorHost][], az Event Hubs lehetővé teszi, hogy a közzétevők visszavonását annak érdekében, hogy adott közzétevők események eseményközpontba letiltása. Ezek a funkciók hasznosak, ha egy közzétevői token biztonsága sérült, vagy ha egy szoftverfrissítés okozza, azokat nem megfelelően viselkednek. Ilyen helyzetekben a közzétevő identitása (amely a SAS-token részét képezi) blokkolható az események közzétételének a megakadályozásához.

A közzétevők visszavonásával és az eseményközpontokba közzétevőként való küldés módjával kapcsolatban a [Event Hubs Large Scale Secure Publishing](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab) (Event Hubs nagyléptékű biztonságos közzététele) mintában tekinthet meg további információt.

## <a name="next-steps"></a>További lépések

Az Event Hubs-forgatókönyvekkel kapcsolatos további információkért látogasson el a következő hivatkozásokra:

* [Event Hubs API overview](event-hubs-api-overview.md) (Event Hubs API – áttekintés)
* [Mi az Event Hubs](event-hubs-what-is-event-hubs.md)
* [Rendelkezésre állás és konzisztencia az Event Hubsban](event-hubs-availability-and-consistency.md)
* [Event Processor Host – API-referencia](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)

[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[EventHubClient]: /dotnet/api/microsoft.azure.eventhubs.eventhubclient
[EventData]: /dotnet/api/microsoft.azure.eventhubs.eventdata
[CreateEventHubIfNotExists]: /dotnet/api/microsoft.servicebus.namespacemanager.createeventhubifnotexists
[PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.eventdata#Microsoft_ServiceBus_Messaging_EventData_PartitionKey
[EventProcessorHost]: /dotnet/api/microsoft.azure.eventhubs.processor
