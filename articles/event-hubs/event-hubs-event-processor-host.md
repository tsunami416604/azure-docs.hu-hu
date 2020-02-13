---
title: Események fogadása Event Processor Host – Azure Event Hubs használatával |} A Microsoft Docs
description: Ez a cikk bemutatja az Event Processor Host, az Azure Event Hubs, amely leegyszerűsíti a felügyeleti az ellenőrzőpontok használata, bérlés, valamint az események adatmegőrzési párhuzamos olvasását.
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
ms.openlocfilehash: 414179d62970315a7575be0411bf1cb152349fdc
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162293"
---
# <a name="event-processor-host"></a>Event Processor Host
> [!NOTE]
> Ez a cikk az Azure Event Hubs SDK régi verziójára vonatkozik. Az alábbi áttelepítési útmutatókból megtudhatja, hogyan telepítheti át a programkódot az SDK újabb verziójára. 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MIGRATIONGUIDE.md)
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/migration-guide.md)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)
> - [Java-parancsfájl](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md)
>
> Lásd még: [a partíciók terhelésének elosztása az alkalmazás több példánya között](event-processor-balance-partition-load.md).

Az Azure Event Hubs szolgáltatás a egy hatékony telemetriai adatokat betöltő szolgáltatás, amely több millió esemény áramoltatása biztosítva alacsony költségek mellett használhatók. Ez a cikk azt ismerteti, hogyan lehet a betöltött eseményeket az *Event Processor Host* (EF) használatával használni. intelligens fogyasztói ügynök, amely leegyszerűsíti az ellenőrzőpontok, a lízingek és a párhuzamos események kezelését.  

Az Event Hubs méretezhető a lényeg az elgondolásra épül, particionált fogyasztókat. A [versengő fogyasztók](https://msdn.microsoft.com/library/dn568101.aspx) mintával ellentétben a particionált fogyasztói minta nagy méretekben teszi lehetővé a szűk keresztmetszetet, és megkönnyíti a végpontok közötti párhuzamosságot.

## <a name="home-security-scenario"></a>Otthoni biztonsági forgatókönyv

Fontolja meg egy otthoni biztonsági vállalat, amely figyeli a 100 000 házak egy példán keresztül. Percenként, például a motion detector használatával, ajtó vagy-ablakban megnyitott érzékelő, vészhelyzeti break detector használatával, stb., mindegyik a saját telepített különböző érzékelőadatok nyúlna adatok. A vállalat lakosoknak tevékenységének figyelését, közel valós időben otthon webhelyet biztosít.

Minden érzékelő adatokat leküldi az eseményközpontba. Az event hubs 16 partícióval rendelkező van konfigurálva. A fogyasztó végén kell egy mechanizmust, olvassa el ezeket az eseményeket, egyesítheti őket (szűrés, összesítése, stb.) és a storage-blobba, amely egy felhasználóbarát weblapra majd előre összesített dump.

## <a name="write-the-consumer-application"></a>A fogyasztó alkalmazás írása

A fogyasztó elosztott környezetben tervezésekor a forgatókönyv az alábbi követelményeknek kell kezelnie:

1. **Skála:** Több fogyasztót is létrehozhat, és minden fogyasztónak több Event Hubs partícióból kell elolvasnia a tulajdonjogát.
2. **Terheléselosztás:** Növelje vagy csökkentse dinamikusan a fogyasztókat. Például új érzékelő típus (például egy szénmonoxid detector használatával) minden egyes kezdőlapjára kerül, ha az események száma növekszik. Ebben az esetben az operátor (emberi) növeli a fogyasztói példányok száma. Ezt követően a fogyasztókészletek is újraegyensúlyozására saját, partíciók száma a terhelés megosztása a újonnan felvett felhasználók.
3. **Zökkenőmentes folytatás a hibáknál:** Ha a fogyasztó (**a fogyasztó**) nem sikerül (például a fogyasztót futtató virtuális gép hirtelen összeomlik), akkor a többi fogyasztónak képesnek kell lennie az **a fogyasztó** által birtokolt és folytatott partíciók felvételére. Emellett a folytatási pontnak, amely *ellenőrzőpontnak* vagy *eltolásnak*nevezhető, pontosan olyan ponton kell lennie, ahol a **fogyasztó** sikertelen volt, vagy valamivel azelőtt.
4. **Események felhasználása:** Míg az előző három pont a fogyasztó felügyeletével foglalkozik, az események felhasználásához programkódot kell használni, és ehhez hasznosnak kell lennie. például összesítheti és feltöltheti a blob Storage-ba.

Ahelyett, hogy kiépíti a saját megoldását, Event Hubs biztosítja ezt a funkciót a [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) felületen és a [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) osztályban.

## <a name="ieventprocessor-interface"></a>Ievent Processor felület implementálása

Első lépésként az alkalmazások az [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) felületet implementálják, amely négy módszerrel rendelkezik: [OpenAsync, CloseAsync, ProcessErrorAsync és ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor?view=azure-dotnet#methods). Ez az interfész felhasználni az eseményeket küld az Event Hubs, kódot tartalmaz. A következő kód bemutatja egy egyszerű végrehajtását:

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

Ezután hozza létre az [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) -példányt. A [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) példánynak a konstruktorban történő létrehozásakor a túlterhelés függvényében a következő paramétereket kell használni:

- **állomásnév:** az egyes fogyasztói példányok neve. A **EventProcessorHost** minden példányának egyedi értékkel kell rendelkeznie ehhez a változóhoz egy fogyasztói csoporton belül, ezért ne jegyezze fel ezt az értéket.
- **eventHubPath:** Az Event hub neve.
- **consumerGroupName:** A Event Hubs a **$default** használja az alapértelmezett fogyasztói csoport neveként, de célszerű létrehozni egy fogyasztói csoportot a feldolgozás konkrét aspektusához.
- **eventHubConnectionString:** Az Event hub kapcsolati karakterlánca, amely a Azure Portalból kérhető le. A kapcsolati sztringnek **figyelnie** kell az Event hub figyelő engedélyeit.
- **storageConnectionString:** A belső erőforrás-kezeléshez használt Storage-fiók.

Végezetül a felhasználók regisztrálják a [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) -példányt a Event Hubs szolgáltatással. Események feldolgozása processzor eseményosztály Regisztrálás az EventProcessorHost példányát elindul. A regisztrálás arra utasítja a Event Hubs szolgáltatást, hogy a fogyasztói alkalmazás egy adott partícióból származó eseményeket fogyasszon el, és meghívja a [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) implementációs kódot, amikor az eseményeket leküldi a felhasználásra. 


### <a name="example"></a>Példa

Például tegyük fel, hogy nincsenek-e 5 virtuális gépeken (VM) események és a egy egyszerű konzolalkalmazást az egyes virtuális gépek számára kijelölt, amely a tényleges használat működik. Ezután mindegyik konzolszoftver létrehoz egy [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) -példányt, és regisztrálja azt a Event Hubs szolgáltatásban.

Ebben a példában tegyük fel, hogy 16 partíció van lefoglalva az 5 **EventProcessorHost** -példányhoz. Egyes **EventProcessorHost** -példányok a többinél több partíciót is tartalmazhatnak. Minden olyan partícióhoz, amely egy **EventProcessorHost** -példány tulajdonosa, létrehozza a `SimpleEventProcessor` osztály egy példányát. Ezért a `SimpleEventProcessor` 16 példánya van, és az egyes partíciók közül eggyel van hozzárendelve.

A következő lista foglalja össze az ebben a példában:

- Az event Hubs-partíciók 16.
- 5 virtuális gép, 1 fogyasztói alkalmazások (például Consumer.exe) minden virtuális gépre.
- 5 EPH-példánnyal regisztrált, az egyes virtuális gépek által Consumer.exe 1.
- 16 `SimpleEventProcessor` az 5 EF-példány által létrehozott objektumokat.
- 1 a Consumer. exe alkalmazás 4 `SimpleEventProcessor` objektumot tartalmazhat, mivel az 1 EF-példány 4 partícióval rendelkezhet.

## <a name="partition-ownership-tracking"></a>Partíció tulajdonjog nyomon követése

Egy partíció EPH példányát (vagy egy fogyasztó) tulajdonjogát nyomon követés megadott Azure Storage-fiók használatával van. A követési egyszerű táblázatként módon jelenítheti meg. A blobok mellett a megadott tárfiók megvizsgálásával tekintheti meg a tényleges implementációt:

| **Fogyasztói csoport neve** | **Partíció azonosítója** | **Állomásnév (tulajdonos)** | **Bérlet (vagy tulajdonos) beszerzett ideje** | **Eltolás a partícióban (ellenőrzőpont)** |
| --- | --- | --- | --- | --- |
| $Alapértelmezett | 0 | Fogyasztói\_VM3 | 2018-04-15T01:23:45 | 156 |
| $Alapértelmezett | 1 | Fogyasztói\_VM4 | 2018-04-15T01:22:13 | 734 |
| $Alapértelmezett | 2 | Fogyasztói\_VM0 | 2018-04-15T01:22:56 | 122 |
| : |   |   |   |   |
| : |   |   |   |   |
| $Alapértelmezett | 15 | Fogyasztói\_VM3 | 2018-04-15T01:22:56 | 976 |

Itt minden állomás egy bizonyos időtartamon (a címbérlet élettartama) szerez be partíció tulajdonjogát. Ha egy gazdagép meghibásodik (virtuális Gépet leállítja), majd a bérleti jog vége. Más gazdagépek próbálják meg beolvasni a partíció tulajdonjogát, és a gazdagépet sikeres lesz. Ez a folyamat visszaállítja a bérlet az új tulajdonost a partíción. Ezzel a módszerrel egyszerre csak egyetlen olvasó egy fogyasztói csoporton belül bármely adott partícióról olvashatja.

## <a name="receive-messages"></a>Üzenetek fogadása

A [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) minden egyes hívása események gyűjteményét biztosítja. A feladata ezeket az eseményeket kezelésére. Ha azt szeretné, hogy a feldolgozó gazdagépe legalább egyszer dolgozza fel az összes üzenetet, meg kell írnia a saját Keep rewrite kódját. Legyen óvatos a mérgezett üzenetekkel kapcsolatban.

Javasoljuk, hogy végrehajtja-e a dolgok viszonylag gyorsan; azt jelenti tegye a csekély a feldolgozás, amennyire csak lehetséges. Ehelyett használja a fogyasztói csoportok. Ha a tárhelyre kell írnia, és el kell végeznie néhány útválasztást, érdemes két fogyasztói csoportot használnia, és két [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) -implementációja van, amelyek külön futnak.

A feldolgozás során egy ponton érdemes nyomon követheti, mi elolvasta és befejeződött. Így nyomon követheti, kritikus fontosságú, ha újra kell indítania az olvasó, így nem tér a stream elején. A [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) *ellenőrzőpontok*használatával egyszerűsíti ezt a nyomkövetést. Egy ellenőrzőpont egy helyet, vagy az eltolás egy adott partíció egy adott felhasználói csoportban, mely pont, a rendszer meggyőződik arról, hogy az üzenetek feldolgozta. Az ellenőrzőpontok **EventProcessorHost** való megjelölése a [PartitionContext](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext) objektum [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) metódusának meghívásával valósítható meg. Ez a művelet a [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) metóduson belül történik, de a [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync)is végezhető el.

## <a name="checkpointing"></a>Ellenőrzőpontok használata

A [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) metódus két túlterheléssel rendelkezik: az első, paraméterek nélkül, a [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)által visszaadott gyűjtemény legmagasabb eseményének eltolására vonatkozó ellenőrzőpontok. Az eltolás egy "jó" vízjel; azt feltételezi, feldolgozta az összes legutóbbi esemény fogja meghívni. Ha ezzel a módszerrel ezt a módszert használja, vegye figyelembe, hogy várhatóan meghívására, miután a eseményfeldolgozás kódot adott vissza. A második túlterhelés lehetővé teszi egy [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata) -példány megadását az ellenőrzőponthoz. Ez a módszer lehetővé teszi, hogy más típusú vízjel ellenőrzőpont. A vízjel is alkalmazhat egy "alacsony" vízjel: inkonzisztensek előkészített legalacsonyabb esemény feldolgozása megtörtént. Ez a túlterhelés megadott eltolás kezelése a rugalmasság.

Az ellenőrzőpont elvégzése után a rendszer a partícióra vonatkozó adatokat tartalmazó JSON-fájlt (pontosabban az eltolást) a konstruktorban a [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)megadott Storage-fiókba írja. Ez a fájl folyamatosan frissül. Rendkívül fontos figyelembe venni az ellenőrzőpontok használata a környezetben – unwise ellenőrzőpontra lenne az összes üzenetet. Az ellenőrzőpontok használata valószínűleg használt tárfiók ugyanúgy kezeli a terhelés, de még fontosabb ellenőrzőpontok használata minden eseményhez, amelynek egy Service Bus-üzenetsorba, mint egy eseményközpontba jobb megoldás lehet egy aszinkron üzenetkezelési minta jelezhetnek. Az Event Hubs működési, hogy az "legalább egyszeri" szállítási nagy méretekben. Azáltal, hogy az alsóbb rétegbeli rendszerek idempotens, azt könnyen helyreállni hibák után vagy újraindítja, amelyek ugyanazokat az eseményeket több alkalommal fogadja.

## <a name="thread-safety-and-processor-instances"></a>Biztonság és a processzor-példányok hozzászóláslánc

Alapértelmezés szerint a [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) a szál biztonságos, és szinkron módon viselkedik a [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor)-példányra vonatkozóan. Amikor az események megérkeznek egy partícióra, a rendszer a [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) hívja meg a partíció **IEventProcessor** -példányát, és letiltja a **ProcessEventsAsync** további hívásait a partícióhoz. A következő üzenetek és hívások a **ProcessEventsAsync** várólistára a háttérben, mivel az üzenet-szivattyú továbbra is fut a háttérben a többi szálon. A hozzászóláslánc biztonsági kiküszöböli az szálbiztos gyűjtemények, és jelentősen növeli a teljesítményt.

## <a name="shut-down-gracefully"></a>Biztonságos leállítása

Végezetül, a [EventProcessorHost. UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) lehetővé teszi az összes partíciós olvasó tiszta leállítását, és a [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)egy példányának leállításakor mindig meg kell hívni. Ha ezt elmulasztja, akkor késések jelentkezhetnek a **EventProcessorHost** más példányainak a bérlet lejárati ideje és az EPOCH-ütközések miatt. Az időszakos felügyeletet részletesen ismertetjük a cikk [EPOCH (alapkorszak](#epoch) ) szakaszában. 

## <a name="lease-management"></a>Partícióbérlés-kezeléssel
Események feldolgozása processzor eseményosztály Regisztrálás az EventProcessorHost példányát elindul. A gazdagép-példány szerzi be az egyes partíciók az Event Hubs-bérletek valószínűleg lekérhetem néhány más gazdagép-példányokról, úgy, hogy a partíciók az egyenletes eloszlás szerveződik át az összes gazdagép-példányok között. Minden bérelt partíció esetében a gazdagép-példány hoz létre a megadott event processor osztály egy példányát, majd fogadja az eseményeket az adott partíció és azokat az esemény processzor-példányba továbbítja. További példányok hozzáadja, és további bérleteket is olvassa be a program, az EventProcessorHost végül elosztja a terhelést minden felhasználó között.

Ahogy azt korábban említettük, a követési táblázat nagy mértékben leegyszerűsíti az [EventProcessorHost. UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync)autoskálázási jellegét. A **EventProcessorHost** egy példánya elindítja a lehető legtöbb bérletet, és megkezdi az események olvasását. A bérletek lejárata közelében a **EventProcessorHost** a foglalások elhelyezésével megkísérli megújítani őket. Ha a bérlet megújítható, a processzor folytatja az olvasást, de ha nem, akkor az olvasó bezárult, és a [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync) hívja meg. A **CloseAsync** jó alkalom arra, hogy elvégezze az adott partíció végleges törlését.

A **EventProcessorHost** tartalmaz egy [PartitionManagerOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.partitionmanageroptions) tulajdonságot. Ez a tulajdonság lehetővé teszi, hogy a partícióbérlés-kezeléssel felett. Ezeket a beállításokat a [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) -megvalósítás regisztrálása előtt állíthatja be.

## <a name="control-event-processor-host-options"></a>Event Processor Host beállítások

Emellett a [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) egyik túlterhelése egy [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) objektumot használ paraméterként. Ezzel a paraméterrel szabályozhatja a [EventProcessorHost. UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) viselkedését. A [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions) négy tulajdonságot és egy eseményt határoz meg:

- [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize): a [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)hívásakor fogadni kívánt gyűjtemény maximális mérete. Ez a méret nem áll a minimális, maximális mérete. Ha kevesebb üzenet érkezik, a **ProcessEventsAsync** a lehető legtöbb módon hajtja végre.
- [PrefetchCount](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount): a mögöttes AMQP-csatorna által használt érték, amely meghatározza, hogy az ügyfél hány üzenetet kapjon. Ennek az értéknek nagyobbnak vagy egyenlőnek kell lennie a [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize).
- [InvokeProcessorAfterReceiveTimeout](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.invokeprocessorafterreceivetimeout): Ha ez a paraméter **igaz**, akkor a rendszer akkor hívja meg a [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) , ha az alapul szolgáló hívás egy partíción időtúllépést kap. Ez a módszer hasznos lehet a partíción belüli inaktivitási időszakok idejének időalapú műveleteinek megkezdéséhez.
- [InitialOffsetProvider](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider): lehetővé teszi egy függvény mutatójának vagy lambda kifejezésének beállítását, amelyet a rendszer meghív a kezdeti eltolás megadására, amikor egy olvasó elkezd egy partíciót olvasni. Az eltolás megadása nélkül az olvasó a legrégebbi eseményen indul el, kivéve, ha egy eltolású JSON-fájl már el lett mentve a [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) konstruktorhoz megadott Storage-fiókban. Ez a módszer akkor hasznos, ha meg szeretné változtatni az olvasó indítási viselkedését. Ez a metódus meghívásakor az objektumot a paraméter tartalmazza a Partícióazonosító, amelynek az olvasó indítása folyamatban van.
- [ExceptionReceivedEventArgs](/dotnet/api/microsoft.azure.eventhubs.processor.exceptionreceivedeventargs): lehetővé teszi, hogy értesítést kapjon a [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)-ben előforduló mögöttes kivételekről. Dolgok nem várt módon működik, ha ez az esemény remek hogy elkezdhessük megvizsgálni.

## <a name="epoch"></a>Alapidőszak

A fogadási korszak működése:

### <a name="with-epoch"></a>EPOCH
A EPOCH a szolgáltatás által használt egyedi azonosító (EPOCH érték) a partíció/bérlet tulajdonjogának érvényesítéséhez. Létre kell hoznia egy EPOCH-alapú fogadót a [CreateEpochReceiver](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createepochreceiver?view=azure-dotnet) metódus használatával. Ez a metódus egy EPOCH-alapú fogadót hoz létre. A fogadó egy adott Event hub-partícióhoz jön létre a megadott fogyasztói csoportból.

A EPOCH szolgáltatás lehetővé teszi a felhasználók számára, hogy egy adott időpontban csak egy fogadót biztosítson a fogyasztói csoport számára a következő szabályokkal:

- Ha egy fogyasztói csoport nem rendelkezik meglévő fogadóval, a felhasználó bármilyen EPOCH értékű fogadót hozhat létre.
- Ha van egy, az E1 értékkel rendelkező fogadó, és az új fogadó egy olyan EPOCH értékkel jön létre, amelyben az E1 < = E2, a fogadó az E1-vel automatikusan le lesz választva, és az E2-vel rendelkező fogadó sikeresen létrejön.
- Ha van egy olyan fogadó, amely az E1 értékkel rendelkezik, és az új fogadó egy olyan EPOCH-értékkel jön létre, ahol az E1 > E2, majd az E2 létrehozása a következő hibával meghiúsult: egy olyan fogadó, amelynél a EPOCH E1 már létezik.

### <a name="no-epoch"></a>Nincs EPOCH
Nem EPOCH-alapú fogadót hoz létre a [CreateReceiver](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createreceiver?view=azure-dotnet) metódus használatával. 

Az adatfolyam-feldolgozás bizonyos forgatókönyveket mutat be, ahol a felhasználók több fogadót szeretnének létrehozni egyetlen felhasználói csoporton. Az ilyen forgatókönyvek támogatása érdekében lehetőség van arra, hogy EPOCH nélkül hozzon létre egy fogadót, ebben az esetben pedig legfeljebb 5 egyidejű fogadót engedélyezünk a fogyasztói csoportban.

### <a name="mixed-mode"></a>Kevert mód
Nem javasoljuk, hogy az alkalmazások használatakor hozzon létre egy olyan fogadót, amelynél a EPOCH, majd a nem-EPOCH vagy fordítva értékre váltson ugyanarra a fogyasztói csoportra. Ha azonban ez a viselkedés tapasztalható, a szolgáltatás a következő szabályok alapján kezeli azt:

- Ha van egy olyan fogadó, amely a EPOCH E1-mel lett létrehozva, és aktívan fogad eseményeket, és az új fogadót alapkorszak nélkül hozták létre, az új fogadó létrehozása sikertelen lesz. Az EPOCH-vevőkészülékek mindig elsőbbséget élveznek a rendszeren.
- Ha már létrejött egy, a (z) és a (z), a (z) és a (z) rendszerű, és egy új MessagingFactory, az új fogadó létrehozása sikeres lesz. Itt van egy figyelmeztetés arról, hogy a rendszer a "fogadó leválasztását" körülbelül 10 perc múlva fogja felderíteni.
- Ha van egy vagy több olyan fogadó, amely alapértékkel lett létrehozva, és egy új fogadó jön létre a EPOCH E1-vel, az összes régi fogadó le lesz választva.


> [!NOTE]
> Azt javasoljuk, hogy különböző fogyasztói csoportokat alkalmazzon olyan alkalmazásokhoz, amelyek alapértékeket használnak, és amelyek nem használják a korokat a hibák elkerülésére. 


## <a name="next-steps"></a>Következő lépések

Most, hogy már ismeri az Event Processor Host, talál további információt az Event Hubs az alábbi cikkeket:

- Bevezetés az Event Hubs használatába
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
* [Event Hubs programozási útmutató](event-hubs-programming-guide.md)
* [Rendelkezésre állás és konzisztencia az Event Hubsban](event-hubs-availability-and-consistency.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)
* [Event Hubs minták a GitHubon](https://github.com/Azure/azure-event-hubs/tree/master/samples)
