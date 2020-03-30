---
title: .NET programozási útmutató – Azure Event Hubs (örökölt) | Microsoft dokumentumok
description: Ez a cikk az Azure Event Hubs-hoz az Azure .NET SDK használatával történő kódírásával kapcsolatos információkat tartalmaz.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
ms.service: event-hubs
ms.custom: seodec18
ms.topic: article
ms.date: 01/15/2020
ms.author: shvija
ms.openlocfilehash: d958c2d32c16874676f46bb216067fe2d7bbe784
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280975"
---
# <a name="net-programming-guide-for-azure-event-hubs-legacy-microsoftazureeventhubs-package"></a>.NET programozási útmutató az Azure Event Hubs szolgáltatáshoz (örökölt Microsoft.Azure.EventHubs csomag)
Ez a cikk ismerteti néhány gyakori forgatókönyvírási kód írása az Azure Event Hubs használatával. A témakör feltételezi az Event Hubs szolgáltatással kapcsolatos előzetes ismeretek meglétét. Az Event Hubs fogalmi áttekintése: [Event Hubs – áttekintés](event-hubs-what-is-event-hubs.md).

> [!WARNING]
> Ez az útmutató a régi **Microsoft.Azure.EventHubs** csomaghoz szól. Azt javasoljuk, hogy [migrálja a](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md) kódot a legújabb [Azure.Messaging.EventHubs](get-started-dotnet-standard-send-v2.md) csomag használatához.  


## <a name="event-publishers"></a>Esemény-közzétevők

Az eseményeket egy eseményközpontba küldi HTTP POST vagy AMQP 1.0-s kapcsolaton keresztül. A használathoz és mikor a ttól függ, hogy melyik forgatókönyvet kell használni. Az AMQP 1.0-kapcsolatok mérése közvetített kapcsolatként történik a Service Bus szolgáltatásban, és az olyan forgatókönyvekben megfelelőbbek, ahol gyakoriak a nagyobb üzenetmennyiségek és alacsony késés szükséges, mivel ezek állandó üzenetkezelési csatornát biztosítanak.

A .NET által felügyelt API-k használatakor az adatoknak az Event Hubs számára történő közzétételére szolgáló elsődleges szerkezetek az [EventHubClient][] és az [EventData][] osztály. [Az EventHubClient][] biztosítja azt az AMQP kommunikációs csatornát, amelyen keresztül a rendszer eseményeket küld az eseményközpontnak. Az [EventData][] osztály egy eseményt jelöl, és üzenetek közzétételére szolgál egy eseményközpontban. Ez az osztály tartalmazza a törzset, néhány metaadatot(Tulajdonságok) és fejlécinformációt (SystemProperties) az eseményről. Az [EventData][] objektumhoz más tulajdonságok is hozzáadódnak, amint azok egy eseményközponton haladnak át.

## <a name="get-started"></a>Bevezetés
Az Event Hubs-t támogató .NET osztályok a [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) NuGet csomagban találhatók. A telepítés a Visual Studio Solution Explorer vagy a [Visual Studio Csomagkezelő konzoljával](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) telepíthető. Ehhez adja ki a következő parancsot a [Csomagkezelő konzol](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) ablakában:

```shell
Install-Package Microsoft.Azure.EventHubs
```

## <a name="create-an-event-hub"></a>Eseményközpont létrehozása

Az Azure Portalon, az Azure PowerShellben vagy az Azure CLI-ben event hubokat hozhat létre. További információt az [Event Hubs-névtér létrehozása és egy eseményközpont az Azure Portalhasználatával című témakörben talál.](event-hubs-create.md)

## <a name="create-an-event-hubs-client"></a>Event Hubs-ügyfél létrehozása

Az Event Hubs szolgáltatással való interakció elsődleges osztálya a [Microsoft.Azure.EventHubs.EventHubClient][EventHubClient]. Ezt az osztályt a [CreateFromConnectionString](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createfromconnectionstring) metódussal hozhatja létre, ahogy az a következő példában látható:

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

Eseményeket küld egy eseményközponthoz egy [EventHubClient-példány][] létrehozásával és aszinkron módon a [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) metóduson keresztül. Ez a metódus egyetlen [EventData-példányparamétert][] vesz fel, és aszinkron módon elküldi azt egy eseményközpontnak.

## <a name="event-serialization"></a>Eseményszerializáció

Az [EventData][] osztály [két túlterhelt konstruktorral rendelkezik,](/dotnet/api/microsoft.azure.eventhubs.eventdata.-ctor) amelyek különböző paramétereket, bájtokat vagy bájttömböket vesznek fel, amelyek az eseményadatok hasznos adatát képviselik. Ha JSON-t használ az [EventData][] osztállyal, az **Encoding.UTF8.GetBytes()** használatával kérheti le a bájttömböt a JSON-kódolású sztringhez. Példa:

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
> Ha nem ismeri a partíciókat, olvassa el [ezt a cikket.](event-hubs-features.md#partitions) 

Eseményadatok küldésekor megadhatja a partíció-hozzárendelés létrehozásához kivonatolt értéket. A partíciót a [PartitionSender.PartitionID](/dotnet/api/microsoft.azure.eventhubs.partitionsender.partitionid) tulajdonsággal adhatja meg. A partíciók használatára vonatkozó döntés azonban a rendelkezésre állás és a konzisztencia közötti választást vonja maga után. 

### <a name="availability-considerations"></a>Rendelkezésre állási szempontok

A partíciókulcs használata nem kötelező, és alaposan meg kell fontolnia, hogy használ-e ilyet. Ha nem ad meg partíciókulcsot az események közzétételekor, a rendszer ciklikus időszeleteléses hozzárendelést használ. Sok esetben a partíciókulcs használata jó választás, ha fontos az eseményrendezés. Partíciókulcs használata esetén ezek a partíciók egyetlen csomóponton való rendelkezésre állást igényelnek, és idővel kimaradások fordulhatnak elő; például, ha a számítási csomópontok újraindulnak és javítanak. Ilyenként, ha partícióazonosítót állít be, és a partíció valamilyen okból elérhetetlenné válik, a partícióban lévő adatok elérésére tett kísérlet sikertelen lesz. Ha a magas rendelkezésre állás a legfontosabb, ne adjon meg partíciókulcsot; ebben az esetben az események et a korábban leírt ciklikus multiplexelési modell használatával küldi el a partíciók. Ebben a forgatókönyvben explicit választást hoz a rendelkezésre állás (nincs partícióazonosító) és a konzisztencia (az események egy partícióazonosítóra való rögzítése) között.

Egy másik szempont az események feldolgozásának késései kezelése. Bizonyos esetekben jobb lehet az adatok ejtése és az újrapróbálkozás, mint a feldolgozás, ami potenciálisan további későbbi feldolgozási késéseket okozhat. Például egy tőzsdei árfolyamjelző esetén jobb, ha megvárja a teljes naprakész adatokat, de egy élő csevegésben vagy VOIP-forgatókönyvben inkább gyorsan szeretné az adatokat, még akkor is, ha azok nem teljesek.

Az alábbi elérhetőségi szempontok at figyelembe véve az alábbi esetekben választhat az alábbi hibakezelési stratégiák közül:

- Leállítás (olvasás leállítása az Eseményközpontokból, amíg a dolgok helyre nem állnak)
- Eldobás (az üzenetek nem fontosak, dobja el őket)
- Próbálkozzon újra (próbálkozzon újra az üzenetekkel, ahogy jónak látja)

A rendelkezésre állás és a konzisztencia közötti kompromisszumokkal kapcsolatos további információkért és a [rendelkezésre állás és a konzisztencia az Eseményközpontokban](event-hubs-availability-and-consistency.md)című témakörben talál további információt. 

## <a name="batch-event-send-operations"></a>Kötegelt eseményküldési műveletek

Az események kötegekben történő küldése növelheti az átviteli bírom. A [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) API segítségével létrehozhat egy köteget, amelyhez később adatokobjektumok adhatók hozzá egy [SendAsync-híváshoz.](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync)

Egyetlen köteg nem haladhatja meg az esemény 1 MB-os korlátját. Továbbá a kötegben lévő egyes üzenetek ugyanazzal a közzétevői identitással rendelkezik majd. A küldő felelőssége annak biztosítása, hogy a köteg mérete nem haladja meg az események maximális méretét. Ha mégis meghaladja, az ügyfél **Küldési** hibát jelez. Használhatja a helper metódus [EventHubClient.CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) annak biztosítására, hogy a köteg nem haladja meg az 1 MB-ot. Egy üres [EventDataBatch-et](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch) kap a [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) API-ból, majd a [TryAdd](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch.tryadd) segítségével eseményeket ad hozzá a köteg létrehozásához. 

## <a name="send-asynchronously-and-send-at-scale"></a>Aszinkron küldés és nagy léptékű küldés

Az eseményeket aszinkron módon küldi el egy eseményközpontba. Az aszinkron küldés növeli az ügyfél által események küldésére képes sebességet. [A SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) [egy Feladatobjektumot](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx) ad vissza. Az ügyfél [újrapróbálkozási](/dotnet/api/microsoft.servicebus.retrypolicy) beállításainak vezérléséhez használhatja az ügyfél újrapróbálkozási beállításait.

## <a name="event-consumers"></a>Eseményfelhasználók
Az [EventProcessorHost][] osztály az eseményközpontokból származó adatokat dolgozza fel. Akkor használja ezt a megvalósítást, ha a .NET platformon hoz létre eseményolvasókat. Az [EventProcessorHost][] egy szálbiztos, több folyamatot lehetővé tevő, biztonságos futtatókörnyezetet biztosít az eseményfeldolgozói megvalósításokhoz, ami lehetővé teszi az ellenőrzőpontok használatát és a partícióbérlés-kezelést is.

Az [EventProcessorHost][] osztály használatához megvalósítható az [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). Ez az interfész négy módszert tartalmaz:

* [OpenAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.openasync)
* [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.closeasync)
* [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)
* [ProcessErrorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processerrorasync)

Az eseményfeldolgozás elindításához példányosítsa meg [az EventProcessorHost][]szolgáltatást, amely biztosítja az eseményközpont megfelelő paramétereit. Példa:

> [!NOTE]
> Az EventProcessorHost és a hozzá kapcsolódó osztályok a **Microsoft.Azure.EventHubs.Processor** csomagban találhatók. Adja hozzá a csomagot a Visual Studio-projekthez a [jelen cikkben](event-hubs-dotnet-framework-getstarted-send.md#add-the-event-hubs-nuget-package) található utasítások`Install-Package Microsoft.Azure.EventHubs.Processor`követésével, vagy adja ki a következő parancsot a [Csomagkezelő konzol](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) ablakában: .

```csharp
var eventProcessorHost = new EventProcessorHost(
        EventHubName,
        PartitionReceiver.DefaultConsumerGroupName,
        EventHubConnectionString,
        StorageConnectionString,
        StorageContainerName);
```

Ezután hívja meg [a RegisterEventProcessorAsync metódust](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync) az [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) implementációjának regisztrálásához a futásidővel:

```csharp
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();
```

Ezen a ponton a gazdagép megkísérli a címbérlet megszerzését az eseményközpont minden partícióján egy "kapzsi" algoritmus használatával. Ezek a bérletek egy adott időkereten belül tartanak, majd meg kell újítani őket. Ahogy újabb csomópontok, esetünkben feldolgozópéldányok válnak online állapotúvá, bérletfoglalásokat végeznek, és idővel a terhelés elmozdul a csomópontok között, ahogy egyre több bérletet próbálnak szerezni.

![Event Processor Host](./media/event-hubs-programming-guide/IC759863.png)

Idővel kialakul egy egyensúlyi állapot. Ennek a dinamikus képességnek a segítségével processzoralapú automatikus skálázás alkalmazható a felhasználókra felfelé és lefelé méretezéshez egyaránt. Mivel az Event Hubs nem rendelkezik az üzenetek számának közvetlen fogalmával, az átlagos CPU-kihasználtság gyakran a legjobb mechanizmus a háttérrendszer vagy a fogyasztói méretezés mérésére. Ha a közzétevők idővel több eseményt tesznek közzé, mint amennyit a felhasználók fel tudnak dolgozni, a felhasználókon a processzor növelésével indítható el a feldolgozópéldányok számának automatikus skálázása.

Az [EventProcessorHost][] osztály megvalósít továbbá egy Azure Storage-alapú ellenőrzőpont-kezelési mechanizmust is. A mechanizmus az eltolást partíciónkénti alapon tárolja, így mindegyik felhasználó megállapíthatja, hogy melyik volt az előző felhasználó utolsó ellenőrzőpontja. Ahogy a partíciók váltanak a csomópontok között a bérletek használatával, ez a szinkronizálási mechanizmus az, amely leginkább támogatja a terhelés áthelyezését.

## <a name="publisher-revocation"></a>Közzétevők visszavonása

Az Event Hubs szolgáltatás az Event Hubs szolgáltatás az Event Hubs szolgáltatás a processzorgazda speciális futásidejű szolgáltatásai mellett lehetővé teszi a [közzétevők visszavonását](/rest/api/eventhub/revoke-publisher) annak érdekében, hogy bizonyos közzétevők ne küldjenek eseményt egy eseményközpontba. Ezek a szolgáltatások akkor hasznosak, ha egy közzétevői jogkivonat sérült, vagy egy szoftverfrissítés okozza, hogy helytelenül viselkednek. Ilyen helyzetekben a közzétevő identitása (amely a SAS-token részét képezi) blokkolható az események közzétételének a megakadályozásához.

> [!NOTE]
> Jelenleg csak a REST API támogatja ezt a szolgáltatást ([közzétevő visszavonása](/rest/api/eventhub/revoke-publisher)).

A közzétevők visszavonásával és az eseményközpontokba közzétevőként való küldés módjával kapcsolatban a [Event Hubs Large Scale Secure Publishing](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab) (Event Hubs nagyléptékű biztonságos közzététele) mintában tekinthet meg további információt.

## <a name="next-steps"></a>További lépések

Az Event Hubs-forgatókönyvekkel kapcsolatos további információkért látogasson el a következő hivatkozásokra:

* [Event Hubs API overview (Event Hubs API – áttekintés)](event-hubs-api-overview.md)
* [Mi az eseményközpontok](event-hubs-what-is-event-hubs.md)
* [Rendelkezésre állás és konzisztencia az Event Hubsban](event-hubs-availability-and-consistency.md)
* [Eseményfeldolgozó gazdaGÉP API-jának hivatkozása](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)

[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[EventHubClient]: /dotnet/api/microsoft.azure.eventhubs.eventhubclient
[EventData (Eseményadatok)]: /dotnet/api/microsoft.azure.eventhubs.eventdata
[CreateEventHubIfNotExists]: /dotnet/api/microsoft.servicebus.namespacemanager.createeventhubifnotexists
[PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.eventdata#Microsoft_ServiceBus_Messaging_EventData_PartitionKey
[EventProcessorHost]: /dotnet/api/microsoft.azure.eventhubs.processor
