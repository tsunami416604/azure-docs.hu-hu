---
title: Események fogadása az Eseményfeldolgozó-állomás használatával – Azure Event Hubs | Microsoft dokumentumok
description: Ez a cikk ismerteti az Eseményfeldolgozó gazdagép az Azure Event Hubs, amely leegyszerűsíti az ellenőrzőpontok, lízing, és az olvasási események párhuzamos kezelése.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 01/10/2020
ms.author: shvija
ms.openlocfilehash: 485f51e45e342ca28d54d609fd975bef5b204f7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372229"
---
# <a name="event-processor-host"></a>Event Processor Host
> [!NOTE]
> Ez a cikk az Azure Event Hubs SDK régi verziójára vonatkozik. Ha meg szeretné tudni, hogyan telepítheti át a kódot az SDK újabb verziójába, tekintse meg ezeket az áttelepítési útmutatókat. 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/migration-guide.md)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)
> - [Java-parancsfájl](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md)
>
> Lásd még: [A partícióterhelés elosztása az alkalmazás több példányában.](event-processor-balance-partition-load.md)

Az Azure Event Hubs egy hatékony telemetriai betöltési szolgáltatás, amely több millió esemény alacsony költségű streamelésére használható. Ez a cikk azt ismerteti, hogyan kell felhasználni a bevitt eseményeket az *Event Processor Host* (EPH) használatával; intelligens fogyasztói ügynök, amely leegyszerűsíti az ellenőrzőpontok, lízingek és párhuzamos eseményolvasók kezelését.  

Az Event Hubs skálázásának kulcsa a particionált fogyasztók ötlete. A versengő [fogyasztói](https://msdn.microsoft.com/library/dn568101.aspx) mintával ellentétben a particionált fogyasztói minta lehetővé teszi a nagy léptékű azáltal, hogy megszünteti a versengés szűk keresztmetszetét, és megkönnyíti a végpontok közötti párhuzamosság.

## <a name="home-security-scenario"></a>Otthoni biztonsági forgatókönyv

Példaként, fontolja meg egy otthoni biztonsági cég, amely figyeli 100.000 otthonok. Percenként különböző érzékelőktől kap adatokat, például mozgásérzékelőből, ajtó/ablak nyitott érzékelőből, üvegtörés-érzékelőből stb., amelyet minden otthonban telepítenek. A cég egy weboldalt biztosít a lakosok számára, hogy közel valós időben figyelemmel kísérjék otthonuk tevékenységét.

Minden érzékelő adatokat ad egy eseményközpontba. Az eseményközpont 16 partícióval van konfigurálva. A fogyasztó végén szüksége van egy olyan mechanizmusra, amely képes olvasni ezeket az eseményeket, konszolidálni őket (szűrő, összesítés stb.), és az összesítést egy tárolóblobba, amelyet ezután egy felhasználóbarát weblapra vetít.

## <a name="write-the-consumer-application"></a>A fogyasztói alkalmazás írása

A fogyasztó elosztott környezetben történő tervezésekor a forgatókönyvnek a következő követelményeket kell kezelnie:

1. **Skála:** Hozzon létre több fogyasztót, és minden egyes fogyasztó saját tulajdonba veszi az olvasásnéhány Event Hubs partíciók.
2. **Terhelési mérleg:** Növelje vagy csökkentse a fogyasztókdinamikusan. Ha például minden otthonhoz új érzékelőtípust (például szén-monoxid-érzékelőt) adnak hozzá, az események száma nő. Ebben az esetben az üzemeltető (egy ember) növeli a fogyasztói példányok számát. Ezután a felhasználók készlete újraegyensúlyozhatja a saját partíciók számát, hogy megossza a terhelést az újonnan hozzáadott fogyasztókkal.
3. **Zökkenőmentes folytatás a hibákról:** Ha egy fogyasztó **("A" fogyasztó)** meghibásodik (például a fogyasztót üzemeltető virtuális gép hirtelen összeomlik), akkor más fogyasztóknak képesnek kell lenniük az **"A" fogyasztó** tulajdonában lévő partíciók felvételére és folytatására. Emellett a folytatási pontnak, az úgynevezett *ellenőrzőpontnak* vagy *eltolásnak*pontosan azon a ponton kell lennie, ahol az **A fogyasztó** meghibásodott, vagy valamivel előtte.
4. **Események felhasználása:** Míg az előző három pont foglalkozik a fogyasztó kezelése, ott kell lennie kódot fogyasztani az eseményeket, és nem valami hasznosvele; például összesítheti, és töltse fel a blob storage-ba.

Ahelyett, hogy saját megoldást hozna erre, az Event Hubs ezt a funkciót az [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) felületen és az [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) osztályon keresztül biztosítja.

## <a name="ieventprocessor-interface"></a>IEventProcessor felület

Először is, a fogyasztó alkalmazások megvalósítják az [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) felületet, amely négy módszert tartalmaz: [OpenAsync, CloseAsync, ProcessErrorAsync és ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor?view=azure-dotnet#methods). Ez az összeköttetés tartalmazza az Eseményközpontok által küldött események felhasználásához a tényleges kódot. A következő kód egy egyszerű implementációt mutat be:

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    public Task CloseAsync(PartitionContext context, CloseReason reason)
    {
       Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
       return Task.CompletedTask;
    }

    public Task OpenAsync(PartitionContext context)
    {
       Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
       return Task.CompletedTask;
     }

    public Task ProcessErrorAsync(PartitionContext context, Exception error)
    {
       Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
       return Task.CompletedTask;
    }

    public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
       foreach (var eventData in messages)
       {
          var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
             Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
       }
       return context.CheckpointAsync();
    }
}
```

Ezután példányosítson egy [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) példányt. A túlterheléstől függően az [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) példány konstruktorban történő létrehozásakor a következő paramétereket használja a rendszer:

- **hostName:** az egyes fogyasztói példányok neve. **Az EventProcessorHost** minden példányának egyedi értékkel kell rendelkeznie ehhez a változóhoz egy fogyasztói csoporton belül, ezért ne kódolja ezt az értéket.
- **eventHubPath:** Az eseményközpont neve.
- **consumerGroupName:** Az Event Hubs **$Default** használja az alapértelmezett fogyasztói csoport neveként, de célszerű létrehozni egy fogyasztói csoportot a feldolgozás adott aspektusához.
- **eventHubConnectionString:** A kapcsolati karakterlánc az eseményközponthoz, amely lehívható az Azure Portalon. Ennek a kapcsolati karakterláncnak **figyelő** engedéllyel kell rendelkeznie az eseményközpontban.
- **storageConnectionString:** A belső erőforrás-kezeléshez használt tárfiók.

Végül a fogyasztók regisztrálják az [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) példányt az Event Hubs szolgáltatással. Egy eseményfeldolgozó osztály regisztrálása az EventProcessorHost egy példányával elindítja az eseményfeldolgozást. A regisztráció arra utasítja az Event Hubs szolgáltatást, hogy a fogyasztói alkalmazás bizonyos partícióiból származó eseményeket számítson fel, és hívja meg az [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) implementációs kódját, amikor leküldéses eseményeket használ fel. 


### <a name="example"></a>Példa

Például azt képzeljük el, hogy vannak 5 virtuális gépek (VM-ek) az események felhasználásával, és egy egyszerű konzolalkalmazás minden virtuális gép, amely a tényleges fogyasztási munkát. Minden konzolalkalmazás létrehoz egy [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) példányt, és regisztrálja azt az Event Hubs szolgáltatással.

Ebben a példában tegyük fel, hogy 16 partíció van lefoglalva az 5 **EventProcessorHost** példányok. Egyes **EventProcessorHost-példányok** néhány partícióval rendelkezhetnek, mint mások. Minden olyan partícióhoz, amely egy **EventProcessorHost** példány `SimpleEventProcessor` tulajdonosa, létrehoz egy példányt az osztályból. Ezért `SimpleEventProcessor` összesen 16 példány van, és minden partícióhoz egy van rendelve.

Az alábbi lista ezt a példát foglalja össze:

- 16 Event Hubs partíciók.
- 5 virtuális gép, 1 fogyasztói alkalmazás (például Consumer.exe) minden virtuális gépben.
- 5 EPH-példány regisztrált, 1 minden virtuális gépa Consumer.exe.
- 16 `SimpleEventProcessor` objektum, amelyet az 5 EPH példány hozott létre.
- 1 A Consumer.exe `SimpleEventProcessor` alkalmazás 4 objektumot tartalmazhat, mivel az 1 EPH példány 4 partícióval is birtokolhat.

## <a name="partition-ownership-tracking"></a>Partíció tulajdonjogának nyomon követése

Egy ePH-példány (vagy egy fogyasztó) partíciójának tulajdonjoga nyomon követhető az Azure Storage-fiók, amely a nyomon követéshez biztosított. A követést az alábbiak szerint egyszerű táblázatként jelenítheti meg. A tényleges megvalósítás megtekintéséhez vizsgálja meg a blobok a Storage-fiók biztosított:

| **Fogyasztói csoport neve** | **Partícióazonosító** | **Állomásnév (tulajdonos)** | **Lízing (vagy tulajdonjog) megszerzett idő** | **Eltolás a partíción (ellenőrzőpont)** |
| --- | --- | --- | --- | --- |
| $Default | 0 | Fogyasztói\_VM3 | 2018-04-15T01:23:45 | 156 |
| $Default | 1 | Fogyasztói\_VM4 | 2018-04-15T01:22:13 | 734 |
| $Default | 2 | Fogyasztói\_VM0 | 2018-04-15T01:22:56 | 122 |
| : |   |   |   |   |
| : |   |   |   |   |
| $Default | 15 | Fogyasztói\_VM3 | 2018-04-15T01:22:56 | 976 |

Itt minden állomás egy partíció tulajdonjogát szerzi meg egy bizonyos időtartamra (a bérlet időtartamára). Ha egy állomás meghibásodik (a virtuális gép leáll), akkor a bérlet lejár. Más állomások megpróbál-hoz kap tulajdonjogát a partíció, és az egyik a gazdagépek sikerül. Ez a folyamat új tulajdonossal állítja vissza a partíció bérletét. Ily módon egyszerre csak egy olvasó olvashat egy adott partícióról egy fogyasztói csoporton belül.

## <a name="receive-messages"></a>Üzenetek fogadása

A [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) minden egyes hívása események gyűjteményét biztosítja. Az Ön felelőssége, hogy kezelje ezeket az eseményeket. Ha meg szeretne győződni arról, hogy a processzorgazda minden üzenetet legalább egyszer feldolgoz, meg kell írnia a saját újrapróbálkozási kódját. De légy óvatos a mérgezett üzenetekkel kapcsolatban.

Javasoljuk, hogy a dolgokat viszonylag gyorsan; azaz a lehető legkevesebb feldolgozást végezze el. Ehelyett használjon fogyasztói csoportokat. Ha írnia kell a tárolóba, és némi útválasztást kell végeznie, akkor jobb, ha két fogyasztói csoportot használ, és két Külön futó [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) implementációval rendelkezik.

Egy bizonyos ponton a feldolgozás során, érdemes nyomon követni, hogy mit olvasott és befejezett. A nyomon követése kritikus fontosságú, ha újra kell indítania az olvasást, így nem tér vissza az adatfolyam elejére. [Az EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) ellenőrzőpontok használatával leegyszerűsíti ezt a nyomon *követést.* Az ellenőrzőpont egy adott partíció egy adott partícióhoz tartozó hely vagy eltolás egy adott fogyasztói csoporton belül, ahol meggyőződött arról, hogy feldolgozta az üzeneteket. Az **EventProcessorHost** ellenőrzőpont jának megjelölése úgy történik, hogy meghívja a [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) metódust a [PartitionContext](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext) objektumon. Ez a művelet a [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) metóduson belül történik, de a [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync)metódusban is elvégezhető.

## <a name="checkpointing"></a>Ellenőrzőpontok használata

A [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) metódus két túlterheléssel rendelkezik: az első, paraméterek nélkül, ellenőrzőpontok a [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)által visszaadott gyűjtemény legmagasabb eseményeltolásához. Ez az eltolás egy "magas víz" jel; azt feltételezi, hogy feldolgozta az összes közelmúltbeli eseményt, amikor hívja. Ha ezt a módszert használja ily módon, vegye figyelembe, hogy a másik eseményfeldolgozó kód visszaadása után meg kell adnia. A második túlterhelés lehetővé [teszi,](/dotnet/api/microsoft.azure.eventhubs.eventdata) hogy adjon meg egy EventData-példányt az ellenőrzőponthoz. Ez a módszer lehetővé teszi, hogy egy másik típusú vízjel ellenőrzőpont. Ezzel a vízjellel egy "alacsony vízszint" jelet valósíthat meg: a legalacsonyabb szekvenált esemény, amibiztos, hogy fel lett dolgozva. Ez a túlterhelés biztosítja a rugalmasságot az ofszetkezelésben.

Az ellenőrzőpont végrehajtásakor egy JSON-fájl, amely partícióspecifikus információkat tartalmaz (különösen az eltolást), az [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)konstruktorában megadott tárfiókba kerül. Ez a fájl folyamatosan frissül. Rendkívül fontos, hogy fontolja meg ellenőrzőpontok összefüggésben - nem lenne bölcs dolog ellenőrzőpont minden üzenetet. Az ellenőrzőpontok hoz használt tárfiók valószínűleg nem kezeli ezt a terhelést, de ami még fontosabb, ellenőrzőpontok minden egyes esemény jelzi a várólistára helyezett üzenetkezelési minta, amely a Service Bus-várólista lehet jobb megoldás, mint egy eseményközpont. Az Event Hubs ötlete az, hogy "legalább egyszer" nagy méretű kézbesítést kap. Azáltal, hogy a későbbi rendszerek idempotent, könnyen helyreállítható a hibák vagy újraindítások, amelyek eredményeként ugyanazokat az eseményeket többször kapták.

## <a name="thread-safety-and-processor-instances"></a>Menetbiztonsági és processzorpéldányok

Alapértelmezés szerint [az EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) szálbiztos, és szinkron módon viselkedik az [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor)példányához képest. Amikor események érkeznek egy partícióra, a [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) az adott partíció **IEventProcessor** példányára van meghívva, és blokkolja a további hívásokat a **ProcessEventsAsync-hez** a partícióhoz. Ezt követő üzenetek és hívások **ProcessEventsAsync** várólista fel a színfalak mögött, mint az üzenet szivattyú továbbra is fut a háttérben más szálak. Ez a menetbiztonság szükségtelent jelent a menetbiztos gyűjtemények számára, és jelentősen növeli a teljesítményt.

## <a name="shut-down-gracefully"></a>Állítsa le kecsesen

Végül az [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) lehetővé teszi az összes partícióolvasó tiszta leállítását, és mindig meg kell hívni, amikor leállítja az [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)egy példányát. Ennek elmulasztása késedelmet okozhat az **EventProcessorHost** más példányainak indításakor a bérlet lejárata és az Epoch ütközések miatt. Az Epoch menedzsmentet részletesen bemutatja a cikk [Epoch](#epoch) szakasza. 

## <a name="lease-management"></a>Lízingkezelés
Egy eseményfeldolgozó osztály regisztrálása az EventProcessorHost egy példányával elindítja az eseményfeldolgozást. A gazdapéldány az Event Hub egyes partícióin szerez le címbérleteket, esetleg más gazdagéppéldányokból is leküzdhet néhányat oly módon, hogy a partíciók egyenletes elosztása az összes gazdagéppéldány között konvergenciát eredményez. Minden egyes bérelt partícióhoz az állomáspéldány létrehoz egy példányt a megadott eseményfeldolgozó-osztályból, majd eseményeket fogad az adott partícióról, és továbbítja azokat az eseményfeldolgozó-példánynak. Ahogy egyre több példány tőbb, és több bérletet ragadnak meg, az EventProcessorHost végül kiegyenlíti a terhelést az összes fogyasztó között.

Amint azt korábban kifejtettük, a követési tábla nagymértékben leegyszerűsíti az [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync)automatikus skálázási jellegét. Ahogy egy példánya **EventProcessorHost** elindul, hogy beszerzi a lehető legtöbb bérletek, és megkezdi az események olvasását. Mivel a bérleti közel lejárati, **EventProcessorHost** megpróbálja megújítani őket azáltal, hogy a foglalás. Ha a címbérlet megújítható, a processzor folytatja az olvasást, de ha nem, az olvasó le van zárva, és a [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync) neve meg van hívva. **CloseAsync** egy jó ideje, hogy végre minden végső karbantartása, hogy a partíció.

**Az EventProcessorHost** [partitionmanageroptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.partitionmanageroptions) tulajdonságot tartalmaz. Ez a tulajdonság lehetővé teszi a bérletkezelés vezérlését. Az [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) implementáció regisztrálása előtt adja meg ezeket a beállításokat.

## <a name="control-event-processor-host-options"></a>Eseményprocesszor-állomás beállításainak vezérlése

Ezenkívül a [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) egy túlterhelése egy [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) objektumot vesz fel paraméterként. Ezzel a paraméterrel szabályozhatja az [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) viselkedését. [Az EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions) négy tulajdonságot és egy eseményt határoz meg:

- [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize): A [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)meghívása kor megkapni kívánt gyűjtemény maximális mérete. Ez a méret nem a minimális, csak a maximális méret. Ha kevesebb üzenet érkezik, a **ProcessEventsAsync** a rendelkezésre álló amtalánakkal hajt végre.
- [PrefetchCount](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount): Az alapul szolgáló AMQP-csatorna által használt érték az ügyfél által fogadott üzenetek felső határának meghatározásához. Ennek az értéknek nagyobbnak vagy egyenlőnek kell lennie a [MaxBatchSize értékkel.](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize)
- [InvokeProcessorAfterReceiveTimeout](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.invokeprocessorafterreceivetimeout): Ha ez a paraméter **igaz**, [processEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) hívása akkor jön meg, amikor az alapul szolgáló hívás egy partíción események fogadására idővel kimarad. Ez a módszer akkor hasznos, ha időalapú műveleteket a partícióin inaktív időszakokban.
- [InitialOffsetProvider](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider): Lehetővé teszi egy függvénymutató vagy lambda kifejezés beállítását, amely a kezdeti eltolás megadására szolgál, amikor az olvasó elkezdi olvasni a partíciót. Az eltolás megadása nélkül az olvasó a legrégebbi eseménynél indul, kivéve, ha az [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) konstruktornak megadott tárfiókba már mentett egy eltolású JSON-fájlt. Ez a módszer akkor hasznos, ha módosítani szeretné az olvasó indítási viselkedését. A metódus meghívásakor az objektumparaméter azt a partícióazonosítót tartalmazza, amelynek az olvasóindítása folyamatban van.
- [ExceptionReceivedEventArgs](/dotnet/api/microsoft.azure.eventhubs.processor.exceptionreceivedeventargs): Lehetővé teszi, hogy értesítést kapjon az [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)rendszerben előforduló kivételekről. Ha a dolgok nem úgy működnek, ahogy elvárható, ez az esemény egy jó hely a keresést.

## <a name="epoch"></a>Epoch

Itt van, hogyan működik a fogadó korszak:

### <a name="with-epoch"></a>Az Epoch
Az Epoch egy egyedi azonosító (korszakérték), amelyet a szolgáltatás a partíció/címbérlet tulajdonjogának kényszerítésére használ. Hozzon létre egy Epoch-alapú vevő a [CreateEpochReceiver](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createepochreceiver?view=azure-dotnet) metódus használatával. Ez a módszer egy epoch-alapú vevőt hoz létre. A fogadó jön létre egy adott eseményközpont-partícióa megadott fogyasztói csoportból.

A korszak funkció lehetővé teszi a felhasználók számára, hogy a fogyasztói csoportban bármikor csak egy vevő egység legyen, a következő szabályokkal:

- Ha nincs meglévő vevőegy fogyasztói csoport, a felhasználó hozhat létre egy fogadó bármilyen korszakértékkel.
- Ha van egy e1-es korszakértékű vevőegység, és egy új vevő jön létre az e2 korszakértékkel, ahol az e1 <= e2, az e1-es vevő automatikusan lelesz választva, az e2-vel rendelkező vevő sikeresen létrejön.
- Ha van egy e1 e1-es számú korszakértékű fogadó, és egy új fogadó jön létre egy e2-es e2-es értékkel, ahol az e1 > e2, akkor az e2 létrehozása sikertelen a hibával: Az e1 korszakot alkalmazó fogadó már létezik.

### <a name="no-epoch"></a>Nincs korszak
Nem Epoch-alapú fogadót hoz létre a [CreateReceiver](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createreceiver?view=azure-dotnet) metódus használatával. 

Vannak olyan forgatókönyvek adatfolyam-feldolgozás, ahol a felhasználók szeretnének létrehozni több fogadók egy fogyasztói csoportban. Az ilyen forgatókönyvek támogatása érdekében képesek vagyunk létrehozni egy vevőt korszak nélkül, és ebben az esetben akár 5 egyidejű vevőt engedélyezünk a fogyasztói csoportban.

### <a name="mixed-mode"></a>Vegyes mód
Nem javasoljuk az alkalmazás használatát, ahol hozzon létre egy fogadó korszak, majd váltson a no-epoch vagy fordítva ugyanazon a fogyasztói csoporton. Ha azonban ez a jelenség bekövetkezik, a szolgáltatás a következő szabályok szerint kezeli a kezelőit:

- Ha van egy fogadó már létrehozott e1-es kor, és aktívan fogadja az eseményeket, és egy új fogadó jön létre nem korszak, az új fogadó nem fog sikerülni. Az epoch-fogadók mindig elsőbbséget élveznek a rendszerben.
- Ha volt egy vevő már létrehozott korszak e1 és van kapcsolva, és egy új vevő jön létre nem korszak egy új MessagingFactory, az új vevő sikeres lesz. Van itt egy kikötés, hogy a rendszerünk ~ 10 perc után észleli a "vevő lekapcsolását".
- Ha egy vagy több fogadó kondió nélkül jön létre, és az e1 korszakmal új fogadó jön létre, az összes régi fogadó kapcsolat megszakad.


> [!NOTE]
> Azt javasoljuk, hogy különböző fogyasztói csoportokat használjon a korszakokat használó alkalmazásokhoz, és azokszámára, amelyek nem használnak korszakokat a hibák elkerülése érdekében. 


## <a name="next-steps"></a>További lépések

Most, hogy már ismeri az Eseményfeldolgozó-állomást, az alábbi cikkekben többet megtudhat az Eseményközpontokról:

- Bevezetés az Event Hubs használatába
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [Javascript](get-started-java-send-v2.md)
* [Event Hubs programozási útmutató](event-hubs-programming-guide.md)
* [Rendelkezésre állás és konzisztencia az Event Hubsban](event-hubs-availability-and-consistency.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)
* [Az Event Hubs-minták a GitHubon](https://github.com/Azure/azure-event-hubs/tree/master/samples)
