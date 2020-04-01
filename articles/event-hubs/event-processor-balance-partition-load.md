---
title: A partícióterhelés elosztása több példányközött – Azure Event Hubs | Microsoft dokumentumok
description: Bemutatja, hogyan lehet kiegyensúlyozni a partícióterhelést az alkalmazás több példányát egy eseményfeldolgozó és az Azure Event Hubs SDK használatával.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/16/2020
ms.author: shvija
ms.openlocfilehash: bf90120157bf64bd62a3b5ec9d8a6b2c6260e024
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398301"
---
# <a name="balance-partition-load-across-multiple-instances-of-your-application"></a>A partícióterhelés elosztása az alkalmazás több példányában
Az eseményfeldolgozó alkalmazás méretezéséhez futtathatja az alkalmazás több példányát, és kiegyensúlyozhatja a terhelést egymás között. A régebbi verziókban az [EventProcessorHost](event-hubs-event-processor-host.md) lehetővé tette a program több példánya és az ellenőrzőpont-események közötti terhelés egyensúlyát a fogadáskor. Az újabb verziókban (5.0-tól kezdve) az **EventProcessorClient** (.NET és Java) vagy az **EventHubConsumerClient** (Python és JavaScript) lehetővé teszi, hogy ugyanezt tegye. A fejlesztési modell egyszerűbbé vált az események használatával. Előfizet az eseményekre, amelyek érdeklik egy eseménykezelő regisztrálásával.

Ez a cikk egy mintaforgatókönyv segítségével több példányt egy eseményközpontból származó események olvasására, majd adja meg az eseményfeldolgozó-ügyfél szolgáltatásainak részleteit, amely lehetővé teszi, hogy egyszerre több partícióról érkező események fogadása és terheléselosztás más, ugyanazt az eseményközpontot és fogyasztói csoportot használó ügyfelekkel.

> [!NOTE]
> Az Event Hubs skálázásának kulcsa a particionált fogyasztók ötlete. A versengő [fogyasztói](https://msdn.microsoft.com/library/dn568101.aspx) mintával ellentétben a particionált fogyasztói minta lehetővé teszi a nagy léptékű azáltal, hogy megszünteti a versengés szűk keresztmetszetét, és megkönnyíti a végpontok közötti párhuzamosság.

## <a name="example-scenario"></a>Példaforgatókönyv

Példaként, fontolja meg egy otthoni biztonsági cég, amely figyeli 100.000 otthonok. Percenként különböző érzékelőktől kap adatokat, például mozgásérzékelőből, ajtó/ablak nyitott érzékelőből, üvegtörés-érzékelőből és így tovább, minden otthonban telepítve. A cég egy weboldalt biztosít a lakosok számára, hogy közel valós időben figyelemmel kísérjék otthonuk tevékenységét.

Minden érzékelő adatokat ad egy eseményközpontba. Az eseményközpont 16 partícióval van konfigurálva. A fogyasztó végén szüksége van egy olyan mechanizmusra, amely képes olvasni ezeket az eseményeket, konszolidálni őket (szűrés, összesítés, és így tovább), és az összesítést egy tárolóblobba, amelyet ezután egy felhasználóbarát weblapra vetít.

## <a name="write-the-consumer-application"></a>A fogyasztói alkalmazás írása

A fogyasztó elosztott környezetben történő tervezésekor a forgatókönyvnek a következő követelményeket kell kezelnie:

1. **Skála:** Hozzon létre több fogyasztót, és minden egyes fogyasztó saját tulajdonba veszi az olvasásnéhány Event Hubs partíciók.
2. **Terhelési mérleg:** Növelje vagy csökkentse a fogyasztókdinamikusan. Ha például minden otthonhoz új érzékelőtípust (például szén-monoxid-érzékelőt) adnak hozzá, az események száma nő. Ebben az esetben az üzemeltető (egy ember) növeli a fogyasztói példányok számát. Ezután a felhasználók készlete újraegyensúlyozhatja a saját partíciók számát, hogy megossza a terhelést az újonnan hozzáadott fogyasztókkal.
3. **Zökkenőmentes folytatás a hibákról:** Ha egy fogyasztó **("A" fogyasztó)** meghibásodik (például a fogyasztót üzemeltető virtuális gép hirtelen összeomlik), akkor más fogyasztók is felvehetik az **"A" fogyasztó** tulajdonában lévő partíciókat, és folytathatják. Emellett a folytatási pontnak, az úgynevezett *ellenőrzőpontnak* vagy *eltolásnak*pontosan azon a ponton kell lennie, ahol az **A fogyasztó** meghibásodott, vagy valamivel előtte.
4. **Események felhasználása:** Míg az előző három pont foglalkozik a menedzsment a fogyasztó, ott kell lennie kódot fogyasztani az eseményeket, és nem valami hasznosvele. Például összesítse, és töltse fel a blob storage-ba.

## <a name="event-processor-or-consumer-client"></a>Eseményfeldolgozó vagy -ügyfél

Nem kell saját megoldást készítenie ahhoz, hogy megfeleljen ezeknek a követelményeknek. Az Azure Event Hubs SDK-k biztosítják ezt a funkciót. A .NET vagy a Java SDK-kban egy eseményfeldolgozó ügyfelet (EventProcessorClient), a Python és a Java Script SDK-kban pedig az EventHubConsumerClient-t használja. Az SDK régi verziójában az eseményprocesszor-állomás (EventProcessorHost) támogatta ezeket a szolgáltatásokat.

Az éles környezetek többségéhez azt javasoljuk, hogy az eseményfeldolgozó ügyfelet használja az események olvasására és feldolgozására. A processzorügyfél célja, hogy egy eseményközpont összes partícióján egy teljesítmény- és hibatűrő módon hatékony élményt nyújtson az események feldolgozásához, miközben biztosítja a folyamat ellenőrzőpontjának ellenőrzőpontját. Az eseményfeldolgozó ügyfelek egy adott eseményközpont fogyasztói csoportjának környezetében is képesek együttműködni. Az ügyfelek automatikusan kezelik a szolgáltatások elosztását és kiegyensúlyozását, amint a példányok elérhetővé válnak vagy nem érhetők el a csoport számára.

## <a name="partition-ownership-tracking"></a>Partíció tulajdonjogának nyomon követése

Az eseményfeldolgozó példány általában egy vagy több partíció eseményeit birtokolja és dolgozza fel. A partíciók tulajdonjoga egyenletesen oszlik el az eseményközponthoz és a fogyasztói csoport kombinációhoz társított összes aktív eseményprocesszor-példány között. 

Minden eseményfeldolgozó egyedi azonosítót kap, és egy ellenőrzőpont-tárolóban egy bejegyzés hozzáadásával vagy frissítésével jogcímeket állít a partíciók tulajdonjogára. Az összes eseményfeldolgozó példány rendszeresen kommunikál ezzel a tárral, hogy frissítse saját feldolgozási állapotát, valamint hogy megismerjék az egyéb aktív példányokat. Ezeket az adatokat ezután az aktív processzorok közötti terhelés kiegyenlítésére használják. Új példányok csatlakozhatnak a feldolgozási készlet hez a felskálázáshoz. Ha a példányok hibák miatt leállnak, vagy a méretezés idotartama miatt, a partíció tulajdonjoga kecsesen átkerül más aktív processzorokra.

Az ellenőrzőpont-tárolóban lévő partíciótulajdonosi rekordok nyomon követik az Event Hubs névterét, az eseményközpont nevét, a fogyasztói csoportot, az eseményfeldolgozó-azonosítót (más néven tulajdonost), a partícióazonosítót és az utolsó módosítás időpontját.



| Event Hubs-névtér               | Eseményközpont neve | **Fogyasztói csoport** | Tulajdonos                                | Partícióazonosító | Utolsó módosítás időpontja  |
| ---------------------------------- | -------------- | :----------------- | :----------------------------------- | :----------- | :------------------ |
| mynamespace.servicebus.windows.net | myeventhub     | sajátfogyasztói csoport    | 3be3f9d3-9d9e-4c50-9491-85ece8334ff6 | 0            | 2020-01-15T01:22:15 |
| mynamespace.servicebus.windows.net | myeventhub     | sajátfogyasztói csoport    | f5cc5176-ce96-4bb4-bbaa-a0e3a9054ecf | 1            | 2020-01-15T01:22:17 |
| mynamespace.servicebus.windows.net | myeventhub     | sajátfogyasztói csoport    | 72b980e9-2efc-4ca7-ab1b-ffd7bece8472 | 2            | 2020-01-15T01:22:10 |
|                                    |                | :                  |                                      |              |                     |
|                                    |                | :                  |                                      |              |                     |
| mynamespace.servicebus.windows.net | myeventhub     | sajátfogyasztói csoport    | 844bd8fb-1f3a-4580-984d-6324f9e208af | 15           | 2020-01-15T01:22:00 |

Minden eseményprocesszor-példány megszerzi egy partíció tulajdonjogát, és megkezdi a partíció feldolgozását az utolsó ismert [ellenőrzőpontról.](# Checkpointing) Ha egy processzor meghibásodik (a virtuális gép leáll), akkor más példányok észlelik ezt az utolsó módosítás időpontjának megnézésével. Más példányok az inaktív példány korábban tulajdonában lévő partíciók tulajdonjogát próbálják meg beszerezni, és az ellenőrzőpont-tároló garantálja, hogy csak az egyik példány nak sikerül megszereznie egy partíció tulajdonjogát. Tehát egy adott időpontban legbőleg egy processzor fogadja az eseményeket egy partícióról.

## <a name="receive-messages"></a>Üzenetek fogadása

Eseményfeldolgozó létrehozásakor megadhatja az eseményeket és hibákat feldolgozó függvényeket. Az eseményeket feldolgozó függvény minden egyes hívása egyetlen eseményt biztosít egy adott partícióról. A te felelősséged, hogy kezeld ezt az eseményt. Ha azt szeretné, hogy a fogyasztó legalább egyszer minden üzenetet feldolgoz, meg kell írnia a saját kódját újrapróbálkozási logikával. De légy óvatos a mérgezett üzenetekkel kapcsolatban.

Javasoljuk, hogy viszonylag gyorsan végezze el a dolgokat. Ez azt, hogy nem a lehető legkisebb feldolgozás. Ha írnia kell a tárolóba, és némi útválasztást kell végeznie, akkor jobb, ha két fogyasztói csoportot használ, és két eseményfeldolgozóval rendelkezik.

## <a name="checkpointing"></a>Ellenőrzőpontok használata

*Az ellenőrzőpontok* olyan folyamat, amelynek során egy eseményfeldolgozó megjelöli vagy véglegesíti az utoljára sikeresen feldolgozott esemény pozícióját egy partíción belül. Az ellenőrzőpontok megjelölése általában az eseményeket feldolgozza, és egy fogyasztói csoporton belül partíciónként történik. 

Ha egy eseményfeldolgozó bontja a kapcsolatot egy partícióval, egy másik példány folytathatja a partíció feldolgozását azon az ellenőrzőponton, amelyet az adott partíció utolsó processzora véglegesített az adott fogyasztói csoportban. Amikor a processzor csatlakozik, átmegy az eltolásaz eseményközpontba, hogy megadja azt a helyet, ahol el szeretné kezdeni az olvasást. Ily módon az ellenőrzőpontok használatával mindkét eseményt "teljes" ként jelölheti meg az alsóbb rétegbeli alkalmazások által, és rugalmasságot biztosíthat, ha egy eseményfeldolgozó leáll. Lehetséges visszatérni a régebbi adatokhoz egy alacsonyabb értékű eltolás megadásával az ellenőrzőpontok használata során. 

Amikor az ellenőrzőpontot egy esemény feldolgozottként való megjelölésére hajtja végre, az ellenőrzőpont-tárolóban egy bejegyzés kerül hozzáadásra vagy frissítésre az esemény eltolásával és sorszámával. A felhasználóknak kell eldönteniük az ellenőrzőpont frissítésének gyakoriságát. Minden sikeresen feldolgozott esemény után frissítés teljesítmény- és költséghatással járhat, mivel írási műveletet indít el az alapul szolgáló ellenőrzőpont-tárolóba. Továbbá ellenőrzőpontok minden egyes esemény jelzi a várólistára helyezett üzenetkezelési minta, amely a Service Bus-várólista lehet jobb választás, mint egy eseményközpont. Az Event Hubs ötlete az, hogy "legalább egyszer" nagy méretű kézbesítést kap. Azáltal, hogy a későbbi rendszerek idempotent, könnyen helyreállítható a hibák vagy újraindítások, amelyek eredményeként ugyanazokat az eseményeket többször kapták.

> [!NOTE]
> Ha az Azure Blob Storage-t használja ellenőrzőpont-tárolóként egy olyan környezetben, amely a Storage Blob SDK egy másik verzióját támogatja, mint az Azure-ban általában elérhető, kódot kell használnia a Storage service API-verziójának az adott környezet által támogatott verzióra való módosításához. Ha például az [Event Hubs szolgáltatást egy 2002-es Azure Stack Hub-verzión futtatja,](https://docs.microsoft.com/azure-stack/user/event-hubs-overview)a Storage szolgáltatás legmagasabb elérhető verziója a 2017-11-09-es verzió. Ebben az esetben a Storage service API-verziójának 2017-11-09-re való célzásához kódot kell használnia. Egy adott Storage API-verzió célzásával kapcsolatos példát tekintse meg ezeket a mintákat a GitHubon: 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs). 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithOlderStorageVersion.java)
> - [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsWithDownleveledStorage.js) vagy [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsWithDownleveledStorage.ts)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/event_processor_blob_storage_example_with_storage_api_version.py)

## <a name="thread-safety-and-processor-instances"></a>Menetbiztonsági és processzorpéldányok

Alapértelmezés szerint az eseményfeldolgozó vagy -fogyasztó szálbiztos, és szinkron módon viselkedik. Amikor események érkeznek egy partícióra, az eseményeket feldolgozó függvény neve meg lesz hívva. A függvény további üzenetei és hívásai a színfalak mögött, ahogy az üzenetszivattyú továbbra is fut a háttérben más szálakon. Ez a menetbiztonság szükségtelent jelent a menetbiztos gyűjtemények számára, és jelentősen növeli a teljesítményt.

## <a name="next-steps"></a>További lépések
Tekintse meg a következő rövid útmutatókat:

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](get-started-python-send-v2.md)
- [JavaScript](get-started-node-send-v2.md)
