<properties 
    pageTitle="Az Azure Event Hubs programozási útmutatója | Microsoft Azure"
    description="Ez a témakör az Azure Event Hubs segítségével történő programozást ismerteti az Azure .NET SDK használatával."
    services="event-hubs"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="event-hubs"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="tbd"
    ms.date="08/16/2016"
    ms.author="sethm" />

# Event Hubs programozási útmutató

Ez a témakör az Azure Event Hubs segítségével történő programozást ismerteti az Azure .NET SDK használatával. A témakör feltételezi az Event Hubs szolgáltatással kapcsolatos előzetes ismeretek meglétét. Az Event Hubs fogalmi áttekintése: [Event Hubs – áttekintés](event-hubs-overview.md).

## Esemény-közzétevők

Az események küldése az egyes eseményközpontokba a HTTP POST használatával vagy egy AMQP 1.0-kapcsolaton keresztül történik. A használandó módszert az adott forgatókönyv határozza meg. Az AMQP 1.0-kapcsolatok mérése közvetített kapcsolatként történik a Service Bus szolgáltatásban, és az olyan forgatókönyvekben megfelelőbbek, ahol gyakoriak a nagyobb üzenetmennyiségek és alacsony késés szükséges, mivel ezek állandó üzenetkezelési csatornát biztosítanak.

Az Event Hubs a [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) osztály használatával hozható létre és felügyelhető. A .NET által felügyelt API-k használatakor az adatoknak az Event Hubs számára történő közzétételére szolgáló elsődleges szerkezetek az [EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx) és az [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) osztály. Az [EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx) egy AMQP kommunikációs csatornát biztosít, amelyen keresztül az események küldhetők az eseményközpontba. Az [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) osztály egy eseményt képvisel, és az üzeneteknek az eseményközpontban való közzétételéhez használható. Ez az osztály az esemény törzsét, bizonyos metaadatait és fejléc-információit tartalmazza. Az [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) objektum egyéb tulajdonságokkal is bővül, ahogy keresztülhalad az eseményközponton.

## Bevezetés

Az Event Hubs szolgáltatást támogató .NET-osztályok a Microsoft.ServiceBus.dll szerelvényben találhatók. A Service Bus API-ra való hivatkozásnak és az alkalmazás az összes Service Bus-függőséggel való konfigurálásának a legegyszerűbb módja, ha letölti a [Service Bus NuGet-csomagot](https://www.nuget.org/packages/WindowsAzure.ServiceBus). Használhatja a Visual Studio [Csomagkezelő konzolját](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) is. Ehhez adja ki a következő parancsot a [Csomagkezelő konzol](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) ablakában:

```
Install-Package WindowsAzure.ServiceBus
```

## Eseményközpont létrehozása

A [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) osztályt használhatja az eseményközpontok létrehozásához. Példa:

```
var manager = new Microsoft.ServiceBus.NamespaceManager("mynamespace.servicebus.windows.net");
var description = manager.CreateEventHub("MyEventHub");
```

A legtöbb esetben ajánlott a [CreateEventHubIfNotExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createeventhubifnotexists.aspx) metódusok használata a kivételek elkerülése érdekében, ha a szolgáltatás újraindul. Példa:

```
var description = manager.CreateEventHubIfNotExists("MyEventHub");
```

Minden Event Hubs létrehozási művelet, beleértve a [CreateEventHubIfNotExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createeventhubifnotexists.aspx) műveletet is, **Kezelés** jogosultságokat igényel az adott névtérben. Amennyiben a közzétevő vagy felhasználó alkalmazások jogosultságait korlátozni kívánja, elkerülheti ezeket a létrehozásiművelet-hívásokat az éles kódban, ha korlátozott jogosultságokkal rendelkező hitelesítési adatokat használ.

Az [EventHubDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubdescription.aspx) osztály az eseményközponttal kapcsolatos részleteket tartalmaz, beleértve az engedélyezési szabályokat, az üzenetek megőrzési idejét, a partíciók azonosítóit, az állapotot és az útvonalat. Ennek az osztálynak a használatával frissítheti a metaadatokat az eseményközpontban.

## Event Hubs-ügyfél létrehozása

Az elsődleges osztály az Event Hubs szolgáltatással folytatott interakcióhoz a [Microsoft.ServiceBus.Messaging.EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx). Ez az osztály küldői és fogadói képességeket is biztosít. Az osztály a [Create](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.create.aspx) metódus használatával példányosítható, ahogy az alábbi példában látható.

```
var client = EventHubClient.Create(description.Path);
```

Ez a metódus a Service Bus szolgáltatásnak az App.config fájl `appSettings` szakaszában található kapcsolati információit használja. A Service Bus kapcsolati információit tároló `appSettings` XML egy példájáért tekintse meg a [Microsoft.ServiceBus.Messaging.EventHubClient.Create(System.String)](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.create.aspx) metódus dokumentációját.

Egy másik lehetőség az ügyfél létrehozása egy kapcsolati karakterláncból. Ez a lehetőség akkor működik jól, ha Azure feldolgozói szerepköröket használ, mivel a karakterláncot tárolhatja a feldolgozó konfigurációs tulajdonságaiban. Példa:

```
EventHubClient.CreateFromConnectionString("your_connection_string");
```

A kapcsolati karakterlánc formátumba megegyezik a korábbi metódusok App.config fájljában használt formátummal:

```
Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=Manage;SharedAccessKey=[key]
```

Végül, [EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx) objektum létrehozható [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) példányból is, amint az alábbi példában látható.

```
var factory = MessagingFactory.CreateFromConnectionString("your_connection_string");
var client = factory.CreateEventHubClient("MyEventHub");
```

Fontos megjegyezni, hogy az üzenetkezelési gyár példányából létrehozott további [EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx) objektumok ugyanazt az alapul szolgáló TCP-kapcsolatot használják majd. Ezért ezek az objektumok ügyféloldali korláttal rendelkeznek majd az átvitelre vonatkozóan. A [Create](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.create.aspx) metódus újból felhasználja az üzenetkezelési gyárat. Amennyiben nagyon nagy átviteli kapacitás szükséges egyetlen küldőtől, létrehozhat több üzenetkezelési gyárat, illetve egy [EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx) objektumot mindegyik üzenetkezelési gyárból.

## Események küldése eseményközpontba

Eseményeknek egy eseményközpontba való elküldéséhez hozzon létre egy [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) példányt, és küldje el azt a [Send](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.send.aspx) metódussal. Ez a metódus egyetlen [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) példányparamétert vesz fel, és szinkron módon elküldi azt az eseményközpontba.

## Eseményszerializáció

Az [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) osztály [négy túlterhelt konstruktorral](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.eventdata.aspx) rendelkezik, amelyek különféle paramétereket vesznek fel, például egy objektumot és egy szerializálót, egy bájttömböt vagy egy streamet. Az [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) osztály példányosítható is, és törzsstream beállítható ezt követően. Ha JSON-t használ az [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) osztállyal, az **Encoding.UTF8.GetBytes()** használatával kérheti le a bájttömböt a JSON-kódolású karakterlánchoz.

## Partíciókulcs

Az [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) osztály a [PartitionKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.partitionkey.aspx) tulajdonsággal rendelkezik, amelynek segítségével a küldő meghatározhat egy értéket, amelynek kivonatolásával létrehozható egy partíció-hozzárendelés. A partíciókulcsok használatával biztosítható, hogy az azonos kulccsal rendelkező események küldése az eseményközpont ugyanazon partíciójára történjen. Az általános partíciókulcsok többek közt a felhasználói munkamenetek azonosítói és az egyedi küldőazonosítók. A [PartitionKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.partitionkey.aspx) tulajdonság nem kötelező, és a [Microsoft.ServiceBus.Messaging.EventHubClient.Send(Microsoft.ServiceBus.Messaging.EventData)](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) vagy a [Microsoft.ServiceBus.Messaging.EventHubClient.SendAsync(Microsoft.ServiceBus.Messaging.EventData)](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) metódus használatakor adható meg. Ha nem ad meg értéket a [PartitionKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.partitionkey.aspx) számára, a küldött események elosztása a partíciókra ciklikus időszeleteléses modell használatával fog történni.

## Kötegelt eseményküldési műveletek

Az események kötegekben való küldésével jelentősen növelhető az átvitel. A [SendBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.sendbatch.aspx) metódus az [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) típusú **IEnumerable** paramétert veszi fel, és az egész köteget elemi műveletként küldi el az eseményközpontba.

```
public void SendBatch(IEnumerable<EventData> eventDataList);
```

Vegye figyelembe, hogy a kötegek mérete egyenként nem haladhatja meg az események 256 KB-os korlátját. Továbbá a kötegben lévő egyes üzenetek ugyanazzal a közzétevői identitással rendelkezik majd. A küldő felelőssége annak biztosítása, hogy a köteg mérete nem haladja meg az események maximális méretét. Ha mégis meghaladja, az ügyfél **Küldési** hibát jelez.

## Aszinkron küldés és nagy léptékű küldés

Az események aszinkron módon is elküldhetők az eseményközpontokba. Az aszinkron küldés növelheti az ügyfelek üzenetküldési sebességét. A [Send](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.send.aspx) és a [SendBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.sendbatch.aspx) metódus egyaránt elérhetők aszinkron verzióban, amely egy [Task](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx) objektumot ad vissza. Jóllehet ezzel a technikával növelhető az átvitel, ugyanakkor azt is eredményezheti, hogy az ügyfél továbbra is küldi az eseményeket, miután az Event Hubs szolgáltatás leszabályozta, és így az ügyfél hibába ütközhet, vagy üzenetek veszhetnek el, ha a megvalósítás helytelenül ment végbe. Emellett használhatja a [RetryPolicy](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.cliententity.retrypolicy.aspx) tulajdonságot az ügyfélen az ügyfél újrapróbálkozási beállításainak vezérlésére.

## Partícióküldő létrehozása

Jóllehet az eseményeket általában partíciókulccsal szokás az eseményközpontokba küldeni, bizonyos esetekben előfordulhat, hogy közvetlenül egy adott partícióra szeretne elküldeni egy eseményt. Példa:

```
var partitionedSender = client.CreatePartitionedSender(description.PartitionIds[0]);
```

A [CreatePartitionedSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.createpartitionedsender.aspx) egy [EventHubSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubsender.aspx) objektumot ad vissza, amelynek használatával egy adott eseményközpont-partíción tehet közzé eseményeket.

## Eseményfelhasználók

Az Event Hubs két elsődleges modellel rendelkezik az események felhasználásához: közvetlen fogadók és a magasabb szintű absztrakciók, például az [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx). A közvetlen fogadók felelőssége koordinálni a saját hozzáférésüket az egyes partíciókhoz egy adott felhasználói csoportban.

### Közvetlen felhasználó

Az adott felhasználói csoportban lévő egyes partíciók olvasásának legközvetlenebb módja az [EventHubReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubreceiver.aspx) osztály használata. Az osztály egy példányának létrehozásához az [EventHubConsumerGroup](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubconsumergroup.aspx) osztály egy példányát kell használni. A következő példában a felhasználói csoport fogadójának létrehozásakor a partícióazonosítót kell megadni.

```
EventHubConsumerGroup group = client.GetDefaultConsumerGroup();
var receiver = group.CreateReceiver(client.GetRuntimeInformation().PartitionIds[0]);
```

A [CreateReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubconsumergroup.createreceiver.aspx) metódus több túlterheléssel rendelkezik, amelyek segítik a létrehozni kívánt olvasó vezérlését. Ezek a metódusok lehetnek egy eltolás megadása karakterlánc vagy időbélyeg használatával, valamint annak megadása, hogy a megadott eltolást tartalmazza-e a visszaadott stream, vagy azután kezdődjön. Miután létrehozta a fogadót, máris fogadhatja az eseményeket a visszaadott objektumon. A [Receive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubreceiver.receive.aspx) metódus négy túlterheléssel rendelkezik, amelyek a fogadási művelet paramétereit szabályozzák, például a kötegméretet és a várakozási időt. A metódusok aszinkron verzióinak használatával itt is növelhető a felhasználók átvitele. Példa:

```
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

Adott partíciók vonatkozásában az üzenetek fogadása ugyanabban a sorrendben történik, ahogy az eseményközpontba lettek küldve. Az eltolás egy karakterlánc-token, amely egy adott üzenetet azonosít a partíción.

Vegye figyelembe, hogy egy felhasználói csoportban egy adott partíció egy adott pillanatban nem rendelkezhet 5-nél több egyidejű olvasóval. Ahogy az olvasók csatlakoznak vagy lecsatlakoznak, a munkameneteik néhány percig még aktívak maradhatnak, mielőtt a szolgáltatás észleli, hogy lecsatlakoztak. Ezen időtartam során a partícióra való újracsatlakozás meghiúsulhat. A közvetlen fogadóknak az Event Hubs számára való írásával kapcsolatos átfogó példát a [Service Bus Event Hubs közvetlen fogadók](https://code.msdn.microsoft.com/Event-Hub-Direct-Receivers-13fa95c6) mintában tekinthet meg.

### Event Processor Host

Az [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx) osztály az eseményközpontokból származó adatokat dolgozza fel. Akkor használja ezt a megvalósítást, ha a .NET platformon hoz létre eseményolvasókat. Az [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx) egy szálbiztos, több folyamatot lehetővé tevő, biztonságos futtatókörnyezetet biztosít az eseményfeldolgozói megvalósításokhoz, ami lehetővé teszi az ellenőrzőpontok használatát és a partícióbérlés-kezelést is.

Az [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx) osztály használatához megvalósítható az [IEventProcessor](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.aspx). Ez a felület három metódust tartalmaz:

- [OpenAsync](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.openasync.aspx)

- [CloseAsync](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.closeasync.aspx)

- [ProcessEventsAsync](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.processeventsasync.aspx)

Az események feldolgozásának indításához példányosítsa az [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx) osztályt, megadva az eseményközpont megfelelő paramétereit. Ezután a [RegisterEventProcessorAsync](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.registereventprocessorasync.aspx) meghívásával regisztrálja az [IEventProcessor](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.aspx) megvalósítást a futtatókörnyezetben. Ezen a ponton az állomás megpróbál bérletet szerezni az eseményközpont minden partícióján a „mohó” algoritmus használatával. Ezek a bérletek egy adott időtartamra szólnak, és azt követően meg kell újítani őket. Ahogy újabb csomópontok, esetünkben feldolgozópéldányok válnak online állapotúvá, bérletfoglalásokat végeznek, és idővel a terhelés elmozdul a csomópontok között, ahogy egyre több bérletet próbálnak szerezni.

![Event Processor Host](./media/event-hubs-programming-guide/IC759863.png)

Idővel kialakul egy egyensúlyi állapot. Ennek a dinamikus képességnek a segítségével processzoralapú automatikus skálázás alkalmazható a felhasználókra felfelé és lefelé méretezéshez egyaránt. Mivel az eseményközpontok nem rendelkeznek közvetlen megoldással az üzenetek számlálásához, az átlagos processzorhasználat gyakran a legjobb módszer a háttérrendszeri vagy felhasználói lépték megállapítására. Ha a közzétevők idővel több eseményt tesznek közzé, mint amennyit a felhasználók fel tudnak dolgozni, a felhasználókon a processzor növelésével indítható el a feldolgozópéldányok számának automatikus skálázása.

Az [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx) osztály megvalósít továbbá egy Azure Storage-alapú ellenőrzőpont-kezelési mechanizmust is. A mechanizmus az eltolást partíciónkénti alapon tárolja, így mindegyik felhasználó megállapíthatja, hogy melyik volt az előző felhasználó utolsó ellenőrzőpontja. Ahogy a partíciók váltanak a csomópontok között a bérletek használatával, ez a szinkronizálási mechanizmus az, amely leginkább támogatja a terhelés áthelyezését.

## Közzétevők visszavonása

Az [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx) fejlett futtatókörnyezeti szolgáltatásai mellett az Event Hubs lehetővé teszi a közzétevők visszavonását is annak érdekében, hogy blokkolni lehessen adott közzétevők számára események közzétételét egy eseményközpontban. Ezek a szolgáltatások akkor igazán hasznosak, ha egy közzétevői token biztonsága sérült, vagy egy szoftverfrissítés eredményeként nem megfelelően működik. Ilyen helyzetekben a közzétevő identitása (amely a SAS-token részét képezi) blokkolható az események közzétételének a megakadályozásához.

A közzétevők visszavonásával és az eseményközpontokba közzétevőként való küldés módjával kapcsolatban a [Service Bus Event Hubs Large Scale Secure Publishing](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab) (Service Bus Event Hubs nagyléptékű biztonságos közzététel) mintában tekinthet meg további információt.

## Következő lépések

Az Event Hubs-forgatókönyvekkel kapcsolatos további információkért látogasson el a következő hivatkozásokra:

- [Event Hubs API overview (Event Hubs API – áttekintés)](event-hubs-api-overview.md)
- [Event Hubs – áttekintés](event-hubs-overview.md)
- [Event Hubs-kódminták](http://code.msdn.microsoft.com/site/search?query=event hub&f[0].Value=event hub&f[0].Type=SearchText&ac=5)
- [Event Processor Host – API-referencia](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx)



<!--HONumber=sep16_HO1-->


