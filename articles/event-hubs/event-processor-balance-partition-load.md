---
title: Partíciók terhelésének elosztása több példány között – Azure Event Hubs | Microsoft Docs
description: Ismerteti, hogyan lehet terheléselosztást végezni az alkalmazás több példánya között egy eseményvezérelt processzor és az Azure Event Hubs SDK használatával.
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
ms.openlocfilehash: 1244fe64d0c23782fdae7a0f92415bada4bef55a
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76907655"
---
# <a name="balance-partition-load-across-multiple-instances-of-your-application"></a>A partíciók terhelésének elosztása az alkalmazás több példánya között
Az Event Processing-alkalmazás méretezéséhez az alkalmazás több példánya is futtatható, és saját maguk is elérhetik a terhelést. A régebbi verziókban a [EventProcessorHost](event-hubs-event-processor-host.md) lehetővé tette a program több példánya közötti terhelés elosztását és az ellenőrzőpont-események fogadását. Az újabb verziókban (5,0-ig) a **EventProcessorClient** (.net és Java) vagy a **EventHubConsumerClient** (Python és JavaScript) is lehetővé teszi ugyanezt. A fejlesztési modellt az események használatával egyszerűbbé teszik. Előfizet az Önt érdeklő eseményekre egy eseménykezelő regisztrálásával.

Ez a cikk azt szemlélteti, hogyan lehet több példányt használni az Event hub eseményeinek beolvasásához, majd az Event Processor-ügyfél funkcióinak részletes ismertetését, amely lehetővé teszi, hogy egyszerre több partícióról kapjon eseményeket, és a terheléselosztást más azok a felhasználók, akik ugyanazt az Event hub-t és fogyasztói csoportot használják.

> [!NOTE]
> A Event Hubs méretezésének kulcsa a particionált fogyasztók ötlete. A [versengő fogyasztók](https://msdn.microsoft.com/library/dn568101.aspx) mintával ellentétben a particionált fogyasztói minta nagy méretekben teszi lehetővé a szűk keresztmetszetet, és megkönnyíti a végpontok közötti párhuzamosságot.

## <a name="example-scenario"></a>Példaforgatókönyv

Példaként vegyünk egy olyan otthoni biztonsági vállalatot, amely az 100 000-es otthonok figyelését végzi. Minden percben különböző érzékelőkből, például egy mozgásérzékelőből, egy ajtó/ablak nyitott érzékelőből, az üvegből történő kiderítés és így tovább. A vállalat olyan webhelyet biztosít a lakosok számára, amely közel valós időben figyeli a saját otthonuk tevékenységeit.

Minden érzékelő egy Event hub-ba küldi az adatküldést. Az Event hub 16 partícióval van konfigurálva. A felhasználás végén olyan mechanizmusra van szüksége, amely képes beolvasni ezeket az eseményeket, összevonja őket (szűrés, összesítés stb.), és kiírja az összesítést egy Storage-blobba, amelyet aztán egy felhasználóbarát weboldalra terveztek.

## <a name="write-the-consumer-application"></a>A fogyasztói alkalmazás írása

A fogyasztó elosztott környezetben történő tervezésekor a forgatókönyvnek a következő követelményeket kell kezelnie:

1. **Skála:** Több fogyasztót is létrehozhat, és minden fogyasztónak több Event Hubs partícióból kell elolvasnia a tulajdonjogát.
2. **Terheléselosztás:** Növelje vagy csökkentse dinamikusan a fogyasztókat. Ha például egy új érzékelő típusa (például egy szén-monoxid-detektor) hozzá van adva az egyes kezdőlapokhoz, az események száma nő. Ebben az esetben az operátor (emberi) növeli a fogyasztói példányok számát. Ezután a felhasználók készlete megoszthatja a saját maga által birtokolt partíciók számát, hogy megosszák a terhelést az újonnan hozzáadott fogyasztókkal.
3. **Zökkenőmentes folytatás a hibáknál:** Ha a fogyasztó (**a fogyasztó**) nem sikerül (például a fogyasztót futtató virtuális gép hirtelen összeomlik), akkor a többi fogyasztó az **a felhasználó** által birtokolt és folytatott partíciókat is felveheti. Emellett a folytatási pontnak, amely *ellenőrzőpontnak* vagy *eltolásnak*nevezhető, pontosan olyan ponton kell lennie, ahol a **fogyasztó** sikertelen volt, vagy valamivel azelőtt.
4. **Események felhasználása:** Míg az előző három pont a fogyasztó felügyeletével foglalkozik, az események felhasználásához programkódot kell használni, és ehhez hasznos lehet. Például összesítheti és feltöltheti a blob Storage-ba.

## <a name="event-processor-or-consumer-client"></a>Event processzor vagy fogyasztói ügyfél

A követelmények teljesítéséhez nem kell saját megoldást létrehoznia. Az Azure Event Hubs SDK-k biztosítják ezt a funkciót. .NET-vagy Java SDK-k esetén az EventHubConsumerClient-t használja a EventProcessorClient, valamint a Python-és Java-parancsfájlok SDK-ban. Az SDK régi verziójában ez volt az a funkciókat támogató EventProcessorHost-gazda.

Az éles környezetek többsége számára azt javasoljuk, hogy az Event Processor-ügyfelet az események olvasására és feldolgozására használja. A processzor-ügyfél hatékony élményt biztosít az Event hub összes partícióján lévő események teljesítménybeli és hibatűrő módon történő feldolgozásához, miközben a folyamat előrehaladását is lehetővé teszi. Az Event Processor-ügyfelek az adott Event hub felhasználói csoportjainak kontextusában is képesek együttműködni. Az ügyfelek automatikusan kezelik a munka eloszlását és kiegyensúlyozását, mivel a példányok elérhetővé válnak, vagy nem lesznek elérhetők a csoport számára.

## <a name="partition-ownership-tracking"></a>Partíció tulajdonjogának nyomon követése

Egy esemény-feldolgozó példány általában egy vagy több partícióról származó eseményeket birtokol és dolgoz fel. A partíciók tulajdonjoga egyenletesen oszlik el az Event hub és a fogyasztói csoport kombinációhoz társított aktív Event Processor-példányok között. 

Minden eseményvezérelt processzor egyedi azonosítót kap, és a partíciók tulajdonjogát egy ellenőrzőpont-tárolóban lévő bejegyzés hozzáadásával vagy frissítésével állítja be. Az összes Event Processor-példány rendszeresen kommunikál a tárolóval, hogy frissítse a saját feldolgozási állapotát, valamint az egyéb aktív példányok megismerését. Ezt követően a rendszer az aktív processzorok közötti terhelés elosztására használja fel ezeket az adatkészleteket. Az új példányok a felskálázáshoz csatlakozhatnak a feldolgozó készlethez. Ha a példányok leállnak, a meghibásodások vagy a vertikális leskálázás miatt, a partíciók tulajdonjoga szabályosan átkerül más aktív processzorokra.

Az ellenőrzőpont-tárolóban található partíciós tulajdonosi rekordok nyomon követik Event Hubs névteret, az Event hub nevét, a fogyasztói csoportot, az eseményvezérelt processzor azonosítóját (más néven tulajdonost), a partíció azonosítóját és az utolsó módosítás időpontját.



| Event Hubs-névtér               | Eseményközpont neve | **Fogyasztói csoport** | Tulajdonos                                | Partíció azonosítója | Utolsó módosítás időpontja  |
| ---------------------------------- | -------------- | :----------------- | :----------------------------------- | :----------- | :------------------ |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 3be3f9d3-9d9e-4c50-9491-85ece8334ff6 | 0            | 2020 – 01-15T01:22:15 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | f5cc5176-ce96-4bb4-bbaa-a0e3a9054ecf | 1            | 2020 – 01-15T01:22:17 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 72b980e9-2efc-4ca7-ab1b-ffd7bece8472 | 2            | 2020 – 01-15T01:22:10 |
|                                    |                | :                  |                                      |              |                     |
|                                    |                | :                  |                                      |              |                     |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 844bd8fb-1f3a-4580-984d-6324f9e208af | 15           | 2020 – 01-15T01:22:00 |

Minden Event Processor-példány egy partíció tulajdonjogát szerzi be, és elindítja az utolsó ismert [ellenőrzőpontról](# Checkpointing)a partíció feldolgozását. Ha a processzor meghibásodik (a virtuális gép leáll), akkor a többi példány ezt az utolsó módosítás időpontja alapján észlelte. Más példányok megpróbálják megszerezni az inaktív példány által korábban birtokolt partíciók tulajdonjogát, és az ellenőrzőpont-tároló garantálja, hogy csak az egyik példány sikerül a partíció tulajdonjogának igénylésében. Tehát egy adott időpontban egy processzor legfeljebb egy partíción fogad eseményeket.

## <a name="receive-messages"></a>Üzenetek fogadása

Eseményvezérelt processzor létrehozásakor meg kell adnia azokat a függvényeket, amelyek feldolgozzák az eseményeket és a hibákat. Az eseményeket feldolgozó függvények minden hívása egyetlen eseményt biztosít egy adott partícióról. Ezt az eseményt az Ön felelőssége kezelni. Ha azt szeretné, hogy a fogyasztó minden üzenetet legalább egyszer feldolgozzon, újra kell írnia a saját kódját az újrapróbálkozási logikával. Legyen óvatos a mérgezett üzenetekkel kapcsolatban.

Javasoljuk, hogy a dolgokat viszonylag gyorsan végezze. Ez a lehető legkevesebb feldolgozás. Ha a tárhelyre kell írnia, és el kell végeznie az útválasztást, érdemes két fogyasztói csoportot használnia, és két eseménykezelővel kell rendelkeznie.

## <a name="checkpointing"></a>Ellenőrzőpontok használata

Az *ellenőrzőpontok* olyan folyamat, amellyel egy adott esemény processzora megjelöli vagy véglegesíti az utolsó sikeresen feldolgozott esemény pozícióját egy partíción belül. Az ellenőrzőpontok megjelölése általában a függvényen belül történik, amely feldolgozza az eseményeket, és a felhasználói csoporton belül partíciós alapon történik. 

Ha egy esemény-feldolgozó egy partícióról bontja a kapcsolatot, egy másik példány folytathatja a partíció feldolgozását az ellenőrzőponton, amelyet korábban az adott felhasználói csoportban lévő partíció utolsó processzora véglegesített. Amikor a processzor csatlakozik, átadja az eltolást az Event hub-nak, hogy megadja azt a helyet, ahol az olvasást el szeretné indítani. Ily módon az ellenőrzőpontok segítségével megadhatja az eseményeket "befejezettként" az alárendelt alkalmazások számára, és biztosíthatja a rugalmasságot, ha az esemény processzora leáll. Lehetséges visszatérni a régebbi adatokhoz egy alacsonyabb értékű eltolás megadásával az ellenőrzőpontok használata során. 

Ha az ellenőrzőpontot egy esemény feldolgozottként való megjelölésére hajtja végre, az ellenőrzőpont-tárolóban egy bejegyzés kerül hozzáadásra vagy frissítésre az esemény eltolásával és sorszámával. A felhasználóknak el kell dönteniük az ellenőrzőpont frissítésének gyakoriságát. Az egyes sikeres feldolgozott események utáni frissítés teljesítményre és költséghatékonyságra is hatással lehet, mivel írási műveletet indít el az alapul szolgáló ellenőrzőpont-tárolóba. Emellett az ellenőrzőpontok minden egyes eseménynél egy várólistán lévő üzenetküldési mintát jelezhetnek, amelyhez egy Service Bus üzenetsor jobb megoldás lehet, mint az Event hub. A Event Hubs mögötti ötlet az, hogy "legalább egyszer" kézbesítést kap nagy léptékben. Az alsóbb rétegbeli rendszerek idempotens egyszerűen helyreállítható a hibák vagy újraindítások, amelyek ugyanazt az eseményt többször kapják meg.

## <a name="thread-safety-and-processor-instances"></a>A szál biztonsági és processzor-példányai

Alapértelmezés szerint az eseményvezérelt processzor vagy a fogyasztó a szál biztonságos, és szinkron módon viselkedik. Amikor az események megérkeznek egy partícióra, a rendszer az eseményeket feldolgozó függvényt hívja meg. A függvény további üzenetei és hívásai a jelenetek mögött, az üzenet-szivattyú pedig továbbra is a háttérben futnak a többi szálon. Ez a szál biztonsága megszünteti a szál-biztonságos gyűjtemények szükségességét, és jelentősen növeli a teljesítményt.

## <a name="next-steps"></a>Következő lépések
Tekintse meg az alábbi rövid útmutatókat:

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](get-started-python-send-v2.md)
- [JavaScript](get-started-node-send-v2.md)
