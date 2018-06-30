---
title: Azure Event Hubs Event Processor Host és miért érdemes használni |} Microsoft Docs
description: Áttekintés és az Azure Event Hubs Event Processor Host bemutatása
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
ms.date: 06/26/2018
ms.author: shvija
ms.openlocfilehash: 4907004f4bb88cf0fe9fb799cab236ebf98bba7a
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37132924"
---
# <a name="azure-event-hubs-event-processor-host-overview"></a>Azure Event Hubs Event Processor Host – áttekintés

Azure Event Hubs egy hatékony telemetriai adatfeldolgozást szolgáltatás, amely segítségével több millió az események streamjét alacsony költséggel. Ez a cikk ismerteti, hogyan használja, a feldolgozott események felhasználásához a *Event Processor Host* (EPH); egy intelligens Felhasználóügynök, amely egyszerűbbé teszi a ellenőrzőpontokat, bérbeadás és párhuzamos eseményolvasókat.  

Az Event Hubs méretezési kulcsa a meghatározni, hogy a fogyasztók particionált. A kal ellentétben a [versengő fogyasztó számára](http://msdn.microsoft.com/en-us/library/dn568101.aspx) mintát, a particionált felhasználói mintát lehetővé teszi a nagy méretű versengés szűk eltávolításával, majd alakíthatja ki a teljes körű párhuzamosságát.

## <a name="home-security-scenario"></a>Otthoni biztonsági forgatókönyv

Vegye figyelembe, amely figyeli a 100 000 házak otthoni biztonsági vállalat egy példán keresztül. Percenként, az adatok lekérése különböző érzékelők például mozgásérzékelő, ajtó/ablak megnyitása érzékelő, vészhelyzeti break érzékelő, stb., minden egyes otthoni telepítve. A vállalati webhely megadása közel valós idejű otthon tevékenységének figyelését lakosai biztosít.

Minden érzékelő adatokat leküldi az eseményközpontba. Az event hubs 16 partíciókkal van konfigurálva. A fogyasztó végén kell egy olyan mechanizmus, amely ezeket az eseményeket olvasni, egyesítheti őket (szűrés, összesített, stb.) és a tárolási blob, amely majd van vetítve felhasználóbarát weblapon összesítés dump.

## <a name="write-the-consumer-application"></a>A felhasználói alkalmazás

A fogyasztó elosztott környezetekben tervezésekor a forgatókönyv kezelni kell a következő követelményeknek:

1. **Skála:** hozzon létre több felhasználóból, mindegyik felhasználó tulajdonjogát a néhány Event Hubs partíciók olvasásakor.
2. **A terhelést:** növelése, vagy csökkentse a a fogyasztók dinamikusan. Például ha egy új Érzékelőtípus (például egy szénmonoxid érzékelő) ad hozzá minden egyes home, események száma növekszik. Ebben az esetben a következő operátor (emberi) növeli a felhasználói példányok száma. Ezt követően a fogyasztók körét is egyensúlyba saját, partíciók száma a terhelés megosztása az újonnan hozzáadott felhasználók.
3. **Zökkenőmentes folytatása hibáiról:** , ha a fogyasztó (**A fogyasztói**) sikertelen (például a virtuális gép üzemeltet a fogyasztó hirtelen összeomlik), majd más fogyasztók tulajdonábanpartíciókátvételéhezképesnekkelllenniük**A fogyasztói** és a folytatáshoz. Emellett a folytatási pont, egy *ellenőrzőpont* vagy *eltolás*, kell lennie, ahol a pontos ponton **A fogyasztói** sikertelen, vagy azt megelőzően némileg.
4. **Események felhasználásához:** amíg az előző három pont a fogyasztó felügyeleti foglalkozik, csatlakoznia kell az események felhasználásához és csinálhat valami hasznos vele; például összesíteni az és töltse fel a blob-tároló kódot.

Helyett a saját megoldás létrehozása a, az Event Hubs – ezt a funkciót biztosít a [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) felület és a [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) osztály.

## <a name="ieventprocessor-interface"></a>IEventProcessor felület

Először fel az alkalmazások megvalósítása a [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) felület, amely négy metódusokkal rendelkezik: [OpenAsync, CloseAsync, ProcessErrorAsync és ProcessEventsAsnyc](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor?view=azure-dotnet#methods). Ez az interfész az Event Hubs küldő események felhasználásához tényleges kódját tartalmazza. A következő kód bemutatja egy egyszerű végrehajtását:

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

A következő példányosítható egy [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) példány. Attól függően, hogy a túlterhelési létrehozásakor a [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) példány a konstruktorban, az alábbi paraméterek szerepelnek:

- **Állomásnév:** minden felhasználói példány nevét. Minden példánya **EventProcessorHost** rendelkeznie kell egy egyedi értéket a változóhoz egy fogyasztói csoporton belül, a legjobb, ha nem ez az érték merevlemez kód.
- **eventHubPath:** az eseményközpont nevét.
- **consumerGroupName:** az Event Hubs használ **$Default** , az alapértelmezett felhasználói csoport, de neve nem célszerű a feldolgozás az adott aspektus felhasználói csoport létrehozása.
- **eventHubConnectionString:** az event hubs, amely az Azure portálról lehet beolvasni a kapcsolati karakterláncot. Ez a kapcsolati karakterlánc rendelkeznie kell **figyelésére** az event hubs engedélyeit.
- **storageConnectionString:** a belső erőforrás-kezelésre szolgáló tárfiókot.

Végezetül a fogyasztók regisztrálja a [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) példány az Event Hubs szolgáltatással. Az Event Hubs szolgáltatás várható, hogy a fogyasztó alkalmazás feldolgozó események bizonyos annak egyik partíciójához, és a meghívni kívánt regisztrálása arra utasítja a [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) megvalósítási kódot, amikor az események felhasználásához leküldéses értesítések.

### <a name="example"></a>Példa

Tegyük fel, képzelhető el, hogy vannak-e 5 virtuális gépek (VM) fel az eseményeket, és az egyes virtuális gépek egyszerű konzolalkalmazásként számára fenntartva, amely a fogyasztás működik. Minden egyes Konzolalkalmazás majd létrehoz egy [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) példányt, és regisztrálja azt az Event Hubs szolgáltatás.

Az ebben a példaforgatókönyvben tegyük fel, hogy az 5 16 partíciók kiosztott **EventProcessorHost** példányok. Néhány **EventProcessorHost** példányok előfordulhat, hogy saját néhány további partíciók, mint mások. Az egyes particionálása, amely egy **EventProcessorHost** példányát birtokolja, létrehoz egy példányát a `SimpleEventProcessor` osztály. Ezért 16 példánya van `SimpleEventProcessor` általános, egy minden partícióhoz rendelt.

Az alábbi lista tartalmazza az ebben a példában:

- 16 event Hubs-partíciókat.
- 5 virtuális gép, 1 fogyasztó alkalmazás (például Consumer.exe) minden egyes virtuális gépen.
- 5 EPH példányok regisztrált, az egyes virtuális gépek által Consumer.exe 1.
- 16 `SimpleEventProcessor` 5 EPH példányainak által létrehozott objektumok.
- 1 Consumer.exe alkalmazás tartalmazhat 4 `SimpleEventProcessor` objektumok, mivel a 1 EPH példány saját 4 partíciók.

## <a name="partition-ownership-tracking"></a>Partíció tulajdonjoga nyomon követése

Egy partíció EPH példány (vagy egy fogyasztó) tulajdonjogát nyomon követés megadott Azure Storage-fiók keresztül van. Jelenítheti meg a követési egyszerű táblázatként, az alábbiak szerint. A megadott tárfiók a BLOB megvizsgálásával látható tényleges végrehajtására:

| **Felhasználói csoport neve** | **Partícióazonosító** | **Állomásnév (tulajdonos)** | **Címbérlet tulajdonjoga szerzett alkalommal vagy** | **A partíció (ellenőrzőpont) eltolása** |
| --- | --- | --- | --- | --- |
| $Alapértelmezett | 0 | Fogyasztó\_VM3 | 2018-04-15T01:23:45 | 156 |
| $Alapértelmezett | 1 | Fogyasztó\_VM4 | 2018-04-15T01:22:13 | 734 |
| $Alapértelmezett | 2 | Fogyasztó\_VM0 | 2018-04-15T01:22:56 | 122 |
| : |   |   |   |   |
| : |   |   |   |   |
| $Alapértelmezett | 15 | Fogyasztó\_VM3 | 2018-04-15T01:22:56 | 976 |

Itt minden állomás egy bizonyos ideje (a címbérlet) szerez be a partíció tulajdonjogát. Ha egy állomás sikertelen (VM leállítja), majd a bérlete lejár. Más gazdagépek próbálja beolvasni a partíció tulajdonjogát, és a gazdagépek sikerrel. Ez a folyamat visszaállítja az új tulajdonos partíciót bérleti idejét. Ezzel a módszerrel egyszerre csak egyetlen olvasó olvashatók be egy fogyasztói csoporton belül bármely adott partíció.

## <a name="receive-messages"></a>Üzenetek fogadása

Minden egyes [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) események gyűjteményét biztosítja. A feladata ezen események kezeléséhez. Javasoljuk, hogy program feladatokra viszonylag gyors; Ez azt jelenti tegye a lehető csekély a feldolgozás. Ehelyett használja a fogyasztói csoportok. Ha szeretné írni a tárolási és néhány útválasztás van szüksége, célszerűbb általában két felhasználói csoportot használja, és két [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) által külön futtatja.

A feldolgozás során egy ponton érdemes nyomon követheti, mi elolvasta és befejeződött. Fontos, ha újra kell indítania az olvasási, így nem adnak vissza az adatfolyam kezdete nyomon követési lehetőség. [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) egyszerűbbé teszi a nyomon követési használatával *ellenőrzőpontokat*. Egy ellenőrzőpont egy helyet, vagy az eltolás egy adott partíció egy adott fogyasztói csoporton belül, a, melyik Ön pontot meggyőződik arról, hogy az üzenetek alkalmazáskérés feldolgozása. Az ellenőrzőpont jelölés **EventProcessorHost** hívása úgy érhető el a [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) metódust a [PartitionContext](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext) objektum. Ez a művelet általában belül történik a [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) módszerrel is elvégezhető, de [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync).

## <a name="checkpointing"></a>Ellenőrzőpontok használata

A [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) metódus két túlterheléssel rendelkezik: az első, paraméter nélküli, a legmagasabb esemény eltolását a gyűjteményben által visszaadott ellenőrzőpontok [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Az eltolás értéke a "magas" vízjelek; azt feltételezi, hogy minden megőrző legutóbbi események azt hívásakor alkalmazáskérés feldolgozása. Ha így ezt a módszert használja, vegye figyelembe, hogy meg kellene neki, miután a más esemény feldolgozása kódot adott vissza. A második túlterhelési lehetővé teszi, hogy adjon meg egy [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata) ellenőrzőpont-példányt. Ez a módszer lehetővé teszi, hogy más típusú vízjel ellenőrzőpont. A vízjel is alkalmazhat a "alacsony" vízjelek: biztos előkészített legalacsonyabb esemény feldolgozása megtörtént. Ez a túlterhelés eltolási felügyeleti rugalmasságot engedélyezése valósul meg.

Az ellenőrzőpont hajtja végre, ha a partíció-specifikus adatait (pontosabban az eltolás), egy JSON-fájl beíródik a konstruktort a megadott tárfiók [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Ez a fájl folyamatosan frissül. Nagyon fontos figyelembe venni az ellenőrzőpontok használata a környezetben – lenne, az ellenőrzőpont unwise minden üzenetet. Ellenőrzőpontok valószínűleg használt tárfiók ugyanúgy kezeli a terhelés, de ennél is fontosabb ellenőrzőpontok minden eseményhez, amelynek a Service Bus-üzenetsorba lehet, mint az eseményközpontok jobb megoldás egy aszinkron üzenettovábbítási mintának azonosítóját. Az Event Hubs mögött lényege, hogy a "legalább egyszeri" szállítási nagy léptékű beolvasása. Azáltal, hogy az alsóbb rétegbeli rendszerek idempotent, könnyen helyreállni hibák után vagy eredményező azonos események fogadása többször újraindul.

## <a name="thread-safety-and-processor-instances"></a>Biztonság és a processzor-példányok szál

Alapértelmezés szerint [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) szál biztonságban van, és a példányának tekintetében szinkron módon viselkedik [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). A partíció események érkezésekor [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) metódust a **IEventProcessor** példányt, amely partíció, és letiltja a további hívások **ProcessEventsAsync**a partíció. Egymást követő üzenetek és a **ProcessEventsAsync** sorba a háttérben, az üzenet szivattyú továbbra is fut a háttérben lévő más szálak. A szál biztonsági szálbiztos gyűjtemények nem kell, és jelentős mértékben növeli a teljesítményt.

## <a name="shut-down-gracefully"></a>Biztonságos leállítása

Végezetül [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) lehetővé teszi, hogy a tiszta Leállítás utáni összes partíció-olvasók, és mindig lehet meghívni, amikor egy példánya leállítása [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Elmulasztása késést okozhat, más példányát indításakor **EventProcessorHost** bérleti lejárati és Epoch ütközések miatt. Ezen részletesen is ismertetjük Epoch felügyeleti [blogbejegyzés](https://blogs.msdn.microsoft.com/gyan/2014/09/02/event-hubs-receiver-epoch/)

## <a name="lease-management"></a>A partícióbérlés-kezelést

Ahogy korábban, a nyomkövetési táblát jelentősen egyszerűbb automatikus méretezése jellege [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync). Az példányaként **EventProcessorHost** elindul, szerez be a lehető legtöbb címbérleteket lehető, és megkezdi az események olvasását. A bérletek lejáró, mint **EventProcessorHost** próbál megújítani őket úgy, hogy a foglalás. Ha a bérlet megújítási érhető el, a processzor továbbra is fennáll, olvasási, de nem ez történik, ha az Adatolvasó be van zárva, és [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync) nevezik. **CloseAsync** egy jó ideje, hogy a végső karbantartást végez az adott partícióhoz.

**EventProcessorHost** tartalmaz egy [PartitionManagerOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.partitionmanageroptions) tulajdonság. Ez a tulajdonság lehetővé teszi, hogy az ellenőrzése alatt tartja a partícióbérlés-kezelést. Állítsa be ezeket a beállításokat, mielőtt regisztrálja a [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) végrehajtására.

## <a name="control-event-processor-host-options"></a>Event Processor Host beállítások

Emellett több túlterhelésének [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) tart egy [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) objektum paraméterként. Ez a paraméter használatával felügyeli az [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) magát. [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions) négy tulajdonságok és egy esemény határozza meg:

- [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize): a gyűjtemény szeretne kapni a hívás a maximális méretét [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Ez a méret nincs minimális, csak a maximális méretet. Ha kevesebb üzenetek fogadását, **ProcessEventsAsync** annyit, álltak rendelkezésre a végrehajtja.
- [PrefetchCount](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount): az alapul szolgáló AMQP csatorna alapján határozzák meg a felső korlátja hány üzenetek az ügyfél értéket kell kapnia. Ennek az értéknek nagyobbnak vagy egyenlőnek kell lennie. [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize).
- [InvokeProcessorAfterReceiveTimeout](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.invokeprocessorafterreceivetimeout): Ha ez a paraméter **igaz**, [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) van meghívva, amikor a partíción lévő események fogadásához az alapul szolgáló hívás túllépi az időkorlátot. Ez a módszer akkor hasznos, az időalapú műveletek végrehajtása során a partícióra tétlen időszakokat.
- [InitialOffsetProvider](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider): lehetővé teszi, hogy egy függvény mutató vagy lambda kifejezést kell beállítani, arra, hogy a kezdeti, eltolás: egy partíció olvasása olvasó megkezdésekor nevezzük. Az eltolás megadása, nélkül az olvasó kezdődik, a legrégebbi esemény, kivéve, ha a megadott tárfiók már mentett JSON-fájl értékű eltolással a [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) konstruktor. Ez a módszer akkor hasznos, ha meg szeretné változtatni az olvasó indítási viselkedését. Ez a metódus meghívásakor a objektum paraméter tartalmazza a Partícióazonosító, amelynek az olvasó indítása folyamatban van.
- [ExceptionReceivedEventArgs](/dotnet/api/microsoft.azure.eventhubs.processor.exceptionreceivedeventargs): lehetővé teszi az előforduló alapul szolgáló kivételek értesítést szeretne kapni [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Dolgot nem várt módon dolgozik, ezt az eseményt akkor egy remek kezdőpont keresése.

## <a name="next-steps"></a>További lépések

Most, hogy ismeri a Event Processor Host, tekintse meg a következő cikkekben olvashat az Event Hubs:

* Bevezetés az [Event Hubs használatába oktatóanyag](event-hubs-dotnet-standard-getstarted-send.md)
* [Event Hubs programozási útmutató](event-hubs-programming-guide.md)
* [Rendelkezésre állás és konzisztencia az Event Hubsban](event-hubs-availability-and-consistency.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)
* [Event Hubs minták a Githubon](https://github.com/Azure/azure-event-hubs/tree/master/samples)