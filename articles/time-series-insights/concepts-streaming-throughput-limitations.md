---
title: Adatfolyam-betöltési átviteli sebesség korlátozásai – Azure Time Series Insights | Microsoft Docs
description: További információ a bejövő átviteli sebesség korlátairól Azure Time Series Insights.
author: lyrana
ms.author: lyhughes
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: baa132589f5242ab480c9caa55ca793e0652ad6e
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135012"
---
# <a name="streaming-ingestion-throughput-limits"></a>Adatfolyam-betöltési átviteli sebesség korlátai

A Azure Time Series Insights adatfolyam-továbbítási korlátozások alább olvashatók.

> [!TIP]
> Tekintse meg az előzetes verzióra vonatkozó összes korlát átfogó listáját az [előnézeti környezet megtervezése](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits) című cikkből.

## <a name="per-environment-limitations"></a>/Környezet korlátozásai

Általánosságban elmondható, hogy a bejövő forgalom díjszabása a szervezet eszközeinek száma, az esemény-emisszió gyakorisága, valamint az egyes események mérete:

*  **Az eszközök száma** × **esemény kibocsátásának gyakorisága** × **az egyes események mérete**.

Alapértelmezés szerint a Time Series Insights **Time Series Insights-környezetben legfeljebb 1 megabájt/másodperc (Mbps)** sebességgel képes befogadni a bejövő adatmennyiséget. A [hub-partíciók esetében](concepts-streaming-throughput-limitations.md#hub-partitions-and-per-partition-limits)további korlátozások vannak érvényben.

> [!TIP]
>
> * A sávszélesség-támogatás a 16 MBps-ig terjedő sebességek igény szerinti megadásával adható meg.
> * Vegye fel velünk a kapcsolatot, ha a támogatási jegyet a Azure Portalon keresztül küldi el, ha nagyobb átviteli sebességre van szüksége.
 
* **1. példa:**

    A contoso szállítása 100 000 olyan eszközzel rendelkezik, amely percenként három alkalommal bocsát ki eseményt. Az események mérete 200 bájt. A IoT Hub négy partíciót használnak a Time Series Insights-esemény forrásaként.

    * A Time Series Insights-környezet betöltési sebessége a következő: **100 000 eszköz * 200 bájt/esemény * (3/60 esemény/másodperc) = 1 MB/s**.
    * A másodpercenkénti betöltési arány 0,25 MBps.
    * A contoso szállításának betöltési aránya a méretezési korlátozáson belül lenne.

* **2. példa:**

    A contoso Fleet Analytics 60 000 olyan eszközt tartalmaz, amely másodpercenként egy eseményt bocsát ki. Egy Event hub-t használnak, amelynek a partícióinak száma 4, Time Series Insights eseményforrás. Az események mérete 200 bájt.

    * A környezet betöltési sebessége a következő: **60 000 eszköz * 200 bájt/esemény * 1 esemény/mp = 12 Mbps**.
    * A/partíciók aránya 3 MBps.
    * A contoso Fleet Analytics betöltési aránya a környezet és a partíciók korlátain alapul. Kérést küldhetnek Time Series Insightson keresztül Azure Portal a környezet betöltési arányának növeléséhez, és létrehozhat egy olyan Event hub-t, amely több partícióval rendelkezik az előzetes verzió korlátain belül.

## <a name="hub-partitions-and-per-partition-limits"></a>Hub-partíciók és partíciós korlátok

Time Series Insights környezet megtervezésekor fontos figyelembe venni azon eseményforrás (ok) konfigurációját, amelyekhez csatlakozni fog Time Series Insightshoz. Mind az Azure-IoT Hub, mind a Event Hubs partíciókat használ az események feldolgozásához szükséges horizontális méretezés lehetővé tételéhez. 

A *partíciók* a központban tárolt események rendezett sorrendje. A partíciók száma a központ létrehozási fázisában van beállítva, és nem módosítható.

Event Hubs particionálással kapcsolatos ajánlott eljárások esetében ellenőrizze, [hogy hány partícióra van szükségem?](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need)

> [!NOTE]
> A Azure Time Series Insights leggyakrabban használt IoT huboknak négy partícióra van szükségük.

Akár új központot hoz létre a Time Series Insights-környezethez, akár egy meglévőt használ, ki kell számítania a partíciók betöltési arányát annak megállapításához, hogy az a határértékeken belül van-e. 

A Azure Time Series Insights előzetes **verziójának jelenleg a 0,5 Mbps-ra vonatkozó általános korlátja**van.

### <a name="iot-hub-specific-considerations"></a>IoT Hub-specifikus megfontolások

Ha egy eszköz a IoT Hubban jön létre, akkor véglegesen hozzá van rendelve egy partícióhoz. Ennek során a IoT Hub képes biztosítani az események rendezését (mivel a hozzárendelés soha nem változik).

A rögzített partíciós hozzárendelések olyan Time Series Insights példányokat is érintenek, amelyek IoT Hub alsóbb rétegből érkező adatok betöltését hajtják végre. Ha több eszközről származó üzeneteket továbbítanak a központba ugyanazzal az átjáró-eszköz azonosítójával, akkor előfordulhat, hogy ugyanazon a partíción érkeznek, amely a partíciós méretezési korlátokat is meghaladja.

**Hatás**:

* Ha egy partíció az előzetes verzióra vonatkozó korláton belül tartósan teljesíti a betöltési sebességet, lehetséges, hogy a Time Series Insights nem fogja szinkronizálni az összes eszközt a IoT Hub adatmegőrzési időszak túllépése előtt telemetria. Ennek eredményeképpen a továbbított adatmennyiségek elvesznek, ha a betöltési korlátokat folyamatosan túllépik.

Ennek a körülménynek a mérséklése érdekében a következő ajánlott eljárásokat javasoljuk:

* A megoldás üzembe helyezése előtt számítsa ki a környezet és a partíciók betöltési díjait.
* Győződjön meg arról, hogy a IoT Hub-eszközök terheléselosztása a lehető legtávolabbi mértékben történik.

> [!IMPORTANT]
> Az IoT Hub esemény forrásaként használó környezetek esetében a használatban lévő hub-eszközök számával Számítsuk ki a betöltési arányt, hogy a sebesség a partícióra vonatkozó korlátozás szerint a 0,5 MBps-ra essen.
>
> * Még ha több esemény is érkezik egyszerre, az előzetes verzióra vonatkozó korlátot a rendszer nem fogja meghaladni.

  ![IoT Hub partíciós diagram](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

Az alábbi forrásokból tájékozódhat a hub átviteli sebességének és partícióinak optimalizálásáról:

* [IoT Hub skála](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Event hub-skála](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Event hub-partíciók](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

## <a name="next-steps"></a>Következő lépések

* További információ az [adattárolásról](concepts-storage.md)