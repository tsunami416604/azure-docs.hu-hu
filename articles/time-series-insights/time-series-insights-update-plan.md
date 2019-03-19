---
title: Az Azure Time Series Insights – előzetes környezet megtervezése |} A Microsoft Docs
description: Az Azure Time Series Insights – előzetes környezet megtervezése.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 251e95744f57d9b5e42df9bdc3743f4880ff5381
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58076996"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Az Azure Time Series Insights – előzetes környezet megtervezése

Ez a cikk ismerteti az ajánlott eljárásokat és az Azure Time Series Insights előzetes verziója gyorsan használatának első lépései.

## <a name="best-practices-for-planning-and-preparation"></a>Ajánlott eljárások tervezése és előkészítése

Ismerkedés a Time Series Insights, az a legcélszerűbb megismerése:

* Hogy mit kap egy Time Series Insights előzetes környezet üzembe helyezésekor.
* Milyen a Time Series azonosítók és időbélyegzés tulajdonságok akkor vannak.
* Az új Idősorozat-modell mi, és hogyan hozhat létre saját.
* Hogyan küldhet eseményeket hatékonyan a JSON-ban. 
* Time Series Insights üzleti vész-helyreállítási lehetőségeket.

A Time Series Insights egy üzleti használatalapú modellt alkalmaz. További információ a költségek és a kapacitás: [Time Series Insights díjszabása](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-time-series-insights-preview-environment"></a>A Time Series Insights előzetes környezet

A Time Series Insights előzetes környezet üzembe helyezésekor, hozzon létre két Azure-erőforrások:

* Time Series Insights előzetes verziójú környezet
* Az Azure Storage általános célú V1 fiók

Indítsa el, három további elemek szükségesek:
 
- A [Time Series-modell](./time-series-insights-update-tsm.md) 
- Egy [eseményforrás csatlakozik a Time Series Insights](./time-series-insights-how-to-add-an-event-source-iothub.md) 
- [Az eseményforrás beérkeznek események](./time-series-insights-send-events.md) , amely mindkét képeződik a modellt, és érvényes JSON formátumban vannak 

## <a name="configure-your-time-series-ids-and-timestamp-properties"></a>A Time Series azonosítók és időbélyegzés tulajdonságainak konfigurálása

Hozzon létre egy új Time Series Insights-környezetet, válassza a Time Series azonosítóját. Ez tehát az adatok logikai partíció funkcionál. Feljegyzett, győződjön meg arról, hogy az a Time Series azonosítók készen áll.

> [!IMPORTANT]
> Time Series-azonosítók *nem módosítható* és *később már nem módosítható*. Minden egyes végső kiválasztása előtt ellenőrizze, és először használja.

Kiválaszthatja, hogy az erőforrások egyedi megkülönböztetéséhez a kulcsok legfeljebb három (3). További információkért olvassa el [ajánlott eljárások a Time Series ID kiválasztására vonatkozó](./time-series-insights-update-how-to-id.md) és [tárolási a bejövő és kimenő](./time-series-insights-update-storage-ingress.md).

Az időbélyeg-tulajdonság is fontos. Ez a tulajdonság is kijelölhet, eseményforrások hozzáadásakor. Minden egyes esemény forrása egy nem kötelező időbélyeg-tulajdonság, amely nyomon követése eseményforrások használta idővel rendelkezik. Időbélyegző értékeit a rendszer megkülönbözteti a kis-és nagybetűket, és formátumban kell lenniük az egyes eseményforrás egyes specifikációjának.

> [!TIP]
> Ellenőrizze a eseményforrások formázás és elemzés követelményei.

Ha üresen hagyja, az Eseménynapló Timestamp lesz eseményforrás eseményt sorba idején. Előzményadatok vagy kötegelt eseményeket küld, ha az időbélyeg-tulajdonság testreszabása hasznos több, mint az alapértelmezett esemény sorba időpontja. További információkért olvassa el [eseményforrások hozzáadása az IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md). 

## <a name="understand-the-time-series-model"></a>Megismerheti a Time Series modell

Most már beállíthatja a Time Series Insights-környezet Idősorozat-modellben. Az új modell egyszerűen keresése és elemzése IoT-adatot. Ez lehetővé teszi, hogy a válogatott, a karbantartással és a felderítési bővítést az idősorozat-adatok, és segít fogyasztói használatra kész adatkészleteket előkészítése. A modell **Time Series azonosítók**, amelyek leképezése egy példányt, amely összekapcsolja az egyedi erőforráscsoport változók, típusok és a hierarchiák néven. Olvassa el az új [Idősorozat-modell](./time-series-insights-update-tsm.md).

A modell le dinamikus, így bármikor építhetők fel. Gyorsan és egyszerűen hozhat létre, és töltse fel be a Time Series Insights adatok elküldése előtt. A modell létrehozásához, lásd: [használja az Idősorozat-modell](./time-series-insights-update-how-to-tsm.md).

Sok ügyfél az Idősorozat-modell képezi le egy meglévő eszköz modell vagy a rendszer ERP már megfelel. Ha nem rendelkezik egy meglévő modell, egy előre elkészített felhasználói élmény van [megadott](https://github.com/Microsoft/tsiclient) gyors üzembe helyezéséig. Elképzeli, hogyan modell segíthetnek, tekintse meg a [minta bemutató környezetben](https://insights.timeseries.azure.com/preview/demo). 

## <a name="shape-your-events"></a>Az események formázása

A Time Series Insights eseményeket küldeni módon ellenőrizheti. Ideális esetben az események vannak denormalizált hatékony és jól.

Jó tapasztalatok:

* Az Idősorozat-modellben levő Store metaadatok
* Time Series mód, a szolgáltatáspéldány-mezők és az események csak a szükséges információkat, mint például a következők:
  * Idősorozat azonosítója
  * Időbélyeg

További információkért lásd: [alakzat-események](./time-series-insights-send-events.md#json).

## <a name="business-disaster-recovery"></a>Üzleti katasztrófa utáni helyreállítás

A Time Series Insights egy magas rendelkezésre állású szolgáltatás, amely a redundanciát használja az Azure-régióban szinten. Konfigurációs ezek rejlő funkciók használatához nem szükséges. A Microsoft Azure platform olyan funkciókat, hozzon létre megoldásokat a vész-helyreállítási lehetőségei vagy régiók rendelkezésre állása érdekében is. Ahhoz, hogy globális, eszközöket vagy felhasználókat, a régiók közötti magas rendelkezésre állás előnyeit az alábbi Azure vész-helyreállítási funkciók. 

Az üzletmenet-folytonosság és vészhelyreállítás (BCDR) az Azure-ban a beépített funkciók további információkért lásd: [Azure üzleti folytonossági műszaki útmutatást](https://docs.microsoft.com/azure/resiliency/resiliency-technical-guidance). Architektúra-útmutató az Azure-alkalmazások elérjék a magas rendelkezésre állású és vész-helyreállítási stratégiát, tekintse meg a papír [vészhelyreállítás és magas rendelkezésre állás az Azure-alkalmazások](https://docs.microsoft.com/azure/architecture/resiliency/index).

> [!NOTE]
> 
>  A Time Series Insights nem rendelkezik beépített BCDR.
> Alapértelmezés szerint az Azure Storage, Azure IoT Hub és az Azure Event Hubs a beépített helyreállítási rendelkeznek.

További tudnivalókért olvassa el:

* [Az Azure Storage-redundancia](https://docs.microsoft.com/azure/storage/common/storage-redundancy)
* [Az IoT Hub magas rendelkezésre állású katasztrófa utáni helyreállítás](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr)
* [Eseményközpont-szabályzatok](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)

BCDR van szükség, ha továbbra is egy stratégiára is alkalmazható. Hozzon létre egy második Time Series Insights-környezet biztonsági másolat az Azure-régióban. Események küldése a másodlagos környezetbe az elsődleges eseményből. Használja a második dedikált fogyasztói csoportot és a eseményforrás BCDR irányelveket.

Kövesse az alábbi lépéseket hozhat létre és használhat egy másodlagos Time Series Insights-környezetet.

1. Hozzon létre egy környezetet egy második régióba. További információkért lásd: [Time Series Insights-környezetek](./time-series-insights-get-started.md).
1. Hozzon létre az eseményforrás második dedikált fogyasztói csoportot. Az esemény-adatforráshoz csatlakozhat az új környezet. Győződjön meg arról, hogy a második dedikált fogyasztói csoportot kijelölje. További tudnivalókért tekintse meg a [az IoT Hub dokumentációja](./time-series-insights-how-to-add-an-event-source-iothub.md) vagy a [Event Hub dokumentáció](./time-series-insights-data-access.md).
1. Ha az elsődleges régióban egy vészhelyreállítási incidens van hatással, irányítsa át a műveletek a biztonsági mentési Time Series Insights-környezetbe.

> [!IMPORTANT]
> * Vegye figyelembe, hogy a késés előfordulhat, hogy észlelt feladatátvétel esetén.
> * Feladatátvételi is okozhat pillanatnyi ugrásszerű üzenet feldolgozását műveletek vannak átirányítva.
> * További információkért lásd: [a Time Series Insightsban késés csökkentése](./time-series-insights-environment-mitigate-latency.md).

## <a name="next-steps"></a>További lépések

További tudnivalókért olvassa el:

- [Az Azure Time Series Insights előzetes verziója storage és a bejövő forgalom](./time-series-insights-update-storage-ingress.md)
- [Adatmodellezés](./time-series-insights-update-tsm.md)