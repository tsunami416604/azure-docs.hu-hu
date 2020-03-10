---
title: .NET programozási útmutató – Azure Event Hubs (örökölt) | Microsoft Docs
description: Ez a cikk információt nyújt az írási kódot az Azure Event hubs az Azure .NET SDK használatával.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
ms.service: event-hubs
ms.custom: seodec18
ms.topic: article
ms.date: 01/15/2020
ms.author: shvija
ms.openlocfilehash: d958c2d32c16874676f46bb216067fe2d7bbe784
ms.sourcegitcommit: e6bce4b30486cb19a6b415e8b8442dd688ad4f92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945609"
---
# <a name="net-programming-guide-for-azure-event-hubs-legacy-microsoftazureeventhubs-package"></a>.NET programozási útmutató az Azure Event Hubshoz (örökölt Microsoft. Azure. EventHubs csomag)
Ez a cikk ismerteti az Azure Event Hubs használatával kód írása néhány gyakori forgatókönyvet. A témakör feltételezi az Event Hubs szolgáltatással kapcsolatos előzetes ismeretek meglétét. Az Event Hubs fogalmi áttekintése: [Event Hubs – áttekintés](event-hubs-what-is-event-hubs.md).

> [!WARNING]
> Ez az útmutató a régi **Microsoft. Azure. EventHubs** csomaghoz készült. Javasoljuk, hogy a legújabb [Azure. Messaging. EventHubs](get-started-dotnet-standard-send-v2.md) csomag használatára [telepítse át](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md) a kódot.  


## <a name="event-publishers"></a>Esemény-közzétevők

Eseményeket küldeni egy eseményközpontba, vagy a HTTP POST használatával vagy egy AMQP 1.0-kapcsolaton keresztül. Hogy melyiket kívánja használni a kiválasztott és mikor javítása folyamatban az adott forgatókönyvtől függ. Az AMQP 1.0-kapcsolatok mérése közvetített kapcsolatként történik a Service Bus szolgáltatásban, és az olyan forgatókönyvekben megfelelőbbek, ahol gyakoriak a nagyobb üzenetmennyiségek és alacsony késés szükséges, mivel ezek állandó üzenetkezelési csatornát biztosítanak.

A .NET által felügyelt API-k használatakor az adatoknak az Event Hubs számára történő közzétételére szolgáló elsődleges szerkezetek az [EventHubClient][] és az [EventData][] osztály. A [EventHubClient][] biztosítja azt a AMQP kommunikációs csatornát, amelyen keresztül az Event hub az eseményeket elküldi. A [EventData][] osztály egy eseményt jelöl, és az üzenetek az Event hub-ba való közzétételére szolgál. Ebbe az osztályba beletartozik a törzs, az egyes metaadatok (Tulajdonságok) és a fejléc információi (SystemProperties) az eseményről. A [EventData][] objektumhoz más tulajdonságok is bekerülnek, ahogy az egy Event hub-n halad át.

## <a name="get-started"></a>Bevezetés
A Event Hubst támogató .NET-osztályok a [Microsoft. Azure. EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) NuGet csomagban találhatók. A Visual Studio Solution Explorer vagy a [Package Manager konzol](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) használatával is telepíthető. Ehhez adja ki a következő parancsot a [Csomagkezelő konzol](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) ablakában:

```shell
Install-Package Microsoft.Azure.EventHubs
```

## <a name="create-an-event-hub"></a>Eseményközpont létrehozása

Az Azure Portalon, az Azure PowerShell vagy az Azure CLI használatával az Event Hubs létrehozása. Részletekért lásd: [Event Hubs névtér és az Event hub létrehozása a Azure Portal használatával](event-hubs-create.md).

## <a name="create-an-event-hubs-client"></a>Event Hubs-ügyfél létrehozása

A Event Hubshoz való interakció elsődleges osztálya a [Microsoft. Azure. EventHubs. EventHubClient][EventHubClient]. Ezt az osztályt a [CreateFromConnectionString](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createfromconnectionstring) metódussal hozhatja létre, ahogy az az alábbi példában is látható:

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

Eseményeket küld egy [EventHubClient][] -példány létrehozásával, és aszinkron módon küldi el a [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) metódussal. Ez a metódus egyetlen [EventData][] -példány paramétert vesz igénybe, és aszinkron módon küldi el azt egy Event hub-nak.

## <a name="event-serialization"></a>Eseményszerializáció

A [EventData][] osztálynak [két túlterhelt konstruktora](/dotnet/api/microsoft.azure.eventhubs.eventdata.-ctor) van, amelyek számos paramétert, bájtot vagy byte tömböt foglalnak magukban, amelyek az esemény adattartalmát jelölik. Ha JSON-t használ az [EventData][] osztállyal, az **Encoding.UTF8.GetBytes()** használatával kérheti le a bájttömböt a JSON-kódolású sztringhez. Például:

```csharp
for (var i = 0; i < numMessagesToSend; i++)
{
    var message = $"Message {i}";
    Console.WriteLine($"Sending message: {message}");
    await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
}
```

## <a name="partition-key"></a>Partíciókulcs

> [!NOTE]
> Ha nem ismeri a partíciókat, tekintse meg [ezt a cikket](event-hubs-features.md#partitions). 

Eseményadatokat küld, ha egy partíció-hozzárendelést kivonatolásával értéket is megadhat. A partíciót a [PartitionSender. PartitionID](/dotnet/api/microsoft.azure.eventhubs.partitionsender.partitionid) tulajdonsággal adhatja meg. Azonban a partíciók használata mellett is választhat rendelkezésre állás és konzisztencia. 

### <a name="availability-considerations"></a>Rendelkezésre állási szempontok

A partíciós kulcs használata nem kötelező, és gondolja át alaposan-e használni. Ha nem ad meg partíciókulcsot az események közzétételekor, a rendszer ciklikus időszeleteléses hozzárendelést használ. Sok esetben a partíciókulcsok használatával akkor hasznos, ha fontos események rendezése. Ha partíciókulcsot használ, ezeket a partíciókat megkövetelése a rendelkezésre állás egyetlen csomóponton, és idővel; leállások is például ha a számítási csomópontok újraindítás és a javítás. Mint ilyen Ha a Partícióazonosító és adott partíció valamilyen okból elérhetetlenné válik, az adott partíciók adatainak elérésére tett kísérlet sikertelen lesz. Ha magas rendelkezésre állás legfontosabb, nem ad meg partíciókulcsot; Ebben az esetben események küldése a partíciókat a korábban leírt Ciklikus időszeleteléses modell használatával. Ebben a forgatókönyvben egy explicit lehetőség rendelkezésre állás (nincs Partícióazonosító) és a konzisztencia (rögzítés a Partícióazonosító események) között végez.

Egy másik szempont események feldolgozása az késleltetések kezeli. Néhány esetben lehet jobb dobja el az adatokat, majd próbálkozzon újra, mint próbálja feldolgozási feladatokhoz ajánljuk, amelyek teljesítményproblémákat okozhat, további alárendelt feldolgozási késedelem tartani. Például a tőzsdei árfolyamjelző célszerűbb várakozási teljes naprakész adatokat, de az élő Csevegésben vagy VOIP-forgatókönyv, ahelyett hogy az adatok gyors, még akkor is, ha még nem fejeződött be.

Rendelkezésre állási ezek alapján ezekben a forgatókönyvekben, előfordulhat, hogy válasszon egyet a következő hiba kezelési stratégiák:

- Leállítás (stop mindaddig, amíg a dolgok rögzítettek az Event hubs-Eseményközpontok olvasása)
- Dobja el (üzenetek nem fontos, el kell dobni ezeket)
- Ismételje meg a (ismételje meg az üzenetek tetszés szerint)

A rendelkezésre állás és a konzisztencia közötti kompromisszumokkal kapcsolatos további információkért lásd: [rendelkezésre állás és konzisztencia a Event Hubsban](event-hubs-availability-and-consistency.md). 

## <a name="batch-event-send-operations"></a>Kötegelt eseményküldési műveletek

Események kötegekben való küldésével segíthet az átviteli sebesség növelése. A [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) API-val létrehozhat egy köteget, amely számára az adatobjektumok később hozzáadhatók egy [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) -híváshoz.

Egyetlen köteg nem lépheti túl az esemény 1 MB-os korlátját. Továbbá a kötegben lévő egyes üzenetek ugyanazzal a közzétevői identitással rendelkezik majd. A küldő felelőssége annak biztosítása, hogy a köteg mérete nem haladja meg az események maximális méretét. Ha mégis meghaladja, az ügyfél **Küldési** hibát jelez. A [EventHubClient. CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) segítő metódussal biztosíthatja, hogy a köteg ne haladja meg az 1 MB-ot. Üres [EventDataBatch](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch) kap a [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) API-ból, majd a [TryAdd](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch.tryadd) használatával hozzáadhat eseményeket a köteg létrehozásához. 

## <a name="send-asynchronously-and-send-at-scale"></a>Aszinkron küldés és nagy léptékű küldés

Eseményeket küldeni egy eseményközpontba aszinkron módon történik. Az aszinkron küldés növeli az, amikor egy ügyfél az üzenetküldési sebességét. A [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) egy [feladat](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx) objektumot ad vissza. Az ügyfél újrapróbálkozási beállításainak vezérléséhez használhatja a [RetryPolicy](/dotnet/api/microsoft.servicebus.retrypolicy) osztályt.

## <a name="event-consumers"></a>Eseményfelhasználók
Az [EventProcessorHost][] osztály az eseményközpontokból származó adatokat dolgozza fel. Akkor használja ezt a megvalósítást, ha a .NET platformon hoz létre eseményolvasókat. Az [EventProcessorHost][] egy szálbiztos, több folyamatot lehetővé tevő, biztonságos futtatókörnyezetet biztosít az eseményfeldolgozói megvalósításokhoz, ami lehetővé teszi az ellenőrzőpontok használatát és a partícióbérlés-kezelést is.

Az [EventProcessorHost][] osztály használatához megvalósítható az [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). Ez az interfész négy metódust tartalmaz:

* [OpenAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.openasync)
* [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.closeasync)
* [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)
* [ProcessErrorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processerrorasync)

Az események feldolgozásának megkezdéséhez hozza létre a [EventProcessorHost][], és adja meg az Event hub megfelelő paramétereit. Például:

> [!NOTE]
> A EventProcessorHost és a hozzá kapcsolódó osztályok a **Microsoft. Azure. EventHubs. Processor** csomagban találhatók. Adja hozzá a csomagot a Visual Studio-projekthez a [jelen cikk](event-hubs-dotnet-framework-getstarted-send.md#add-the-event-hubs-nuget-package) utasításait követve, vagy a következő parancs kiadásával a [Package Manager konzol](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) ablakban:`Install-Package Microsoft.Azure.EventHubs.Processor`.

```csharp
var eventProcessorHost = new EventProcessorHost(
        EventHubName,
        PartitionReceiver.DefaultConsumerGroupName,
        EventHubConnectionString,
        StorageConnectionString,
        StorageContainerName);
```

Ezután hívja meg a [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync) -t, hogy regisztrálja a [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) implementációját a futtatókörnyezettel:

```csharp
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();
```

Ezen a ponton az állomás megpróbál bérletet szerezni az event hubs a "mohó" algoritmus használatával minden partíció. Ezek a bérletek az elmúlt egy adott időtartamra szólnak, és ezután meg kell újítani. Ahogy újabb csomópontok, esetünkben feldolgozópéldányok válnak online állapotúvá, bérletfoglalásokat végeznek, és idővel a terhelés elmozdul a csomópontok között, ahogy egyre több bérletet próbálnak szerezni.

![Event Processor Host](./media/event-hubs-programming-guide/IC759863.png)

Idővel kialakul egy egyensúlyi állapot. Ennek a dinamikus képességnek a segítségével processzoralapú automatikus skálázás alkalmazható a felhasználókra felfelé és lefelé méretezéshez egyaránt. Az Event Hubs nincs közvetlen megoldással az üzenetek számlálásához, mert átlagos processzorhasználat gyakran a legjobb módszer vissza záró vagy felhasználói lépték megállapítására. Ha a közzétevők idővel több eseményt tesznek közzé, mint amennyit a felhasználók fel tudnak dolgozni, a felhasználókon a processzor növelésével indítható el a feldolgozópéldányok számának automatikus skálázása.

Az [EventProcessorHost][] osztály megvalósít továbbá egy Azure Storage-alapú ellenőrzőpont-kezelési mechanizmust is. A mechanizmus az eltolást partíciónkénti alapon tárolja, így mindegyik felhasználó megállapíthatja, hogy melyik volt az előző felhasználó utolsó ellenőrzőpontja. Ahogy a partíciók váltanak a csomópontok között a bérletek használatával, ez a szinkronizálási mechanizmus az, amely leginkább támogatja a terhelés áthelyezését.

## <a name="publisher-revocation"></a>Közzétevők visszavonása

Az Event Processor Host speciális futásidejű funkciói mellett a Event Hubs szolgáltatás lehetővé teszi a [közzétevők visszavonását](/rest/api/eventhub/revoke-publisher) annak érdekében, hogy letiltsa bizonyos közzétevők számára esemény küldését az Event hub-ba. Ezek a funkciók hasznosak, ha egy közzétevői token biztonsága sérült, vagy ha egy szoftverfrissítés okozza, azokat nem megfelelően viselkednek. Ilyen helyzetekben a közzétevő identitása (amely a SAS-token részét képezi) blokkolható az események közzétételének a megakadályozásához.

> [!NOTE]
> Jelenleg csak a REST API támogatja ezt a funkciót (a[közzétevő visszavonása](/rest/api/eventhub/revoke-publisher)).

A közzétevők visszavonásával és az eseményközpontokba közzétevőként való küldés módjával kapcsolatban a [Event Hubs Large Scale Secure Publishing](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab) (Event Hubs nagyléptékű biztonságos közzététele) mintában tekinthet meg további információt.

## <a name="next-steps"></a>További lépések

Az Event Hubs-forgatókönyvekkel kapcsolatos további információkért látogasson el a következő hivatkozásokra:

* [Event Hubs API overview](event-hubs-api-overview.md) (Event Hubs API – áttekintés)
* [Mi az Event Hubs?](event-hubs-what-is-event-hubs.md)
* [Rendelkezésre állás és konzisztencia az Event Hubsban](event-hubs-availability-and-consistency.md)
* [Event Processor Host – API-referencia](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)

[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[EventHubClient]: /dotnet/api/microsoft.azure.eventhubs.eventhubclient
[EventData]: /dotnet/api/microsoft.azure.eventhubs.eventdata
[CreateEventHubIfNotExists]: /dotnet/api/microsoft.servicebus.namespacemanager.createeventhubifnotexists
[PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.eventdata#Microsoft_ServiceBus_Messaging_EventData_PartitionKey
[EventProcessorHost]: /dotnet/api/microsoft.azure.eventhubs.processor
