---
title: Az Azure Time Series Insights – előzetes környezet megtervezése |} A Microsoft Docs
description: Az Azure Time Series Insights – előzetes környezet megtervezése.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: 435e5f2163270672ac5f1f5695ca2fe9be22ee6b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66388728"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Az Azure Time Series Insights – előzetes környezet megtervezése

Ez a cikk ismerteti az ajánlott eljárásokat és az Azure Time Series Insights előzetes verziója használatával gyorsan használatba.

> [!NOTE]
> Ajánlott eljárásokat, és egy általánosan elérhető a Time Series Insights-példány megtervezése, lásd: [az Azure Time Series Insights általánosan elérhető környezet megtervezése](time-series-insights-environment-planning.md).

## <a name="best-practices-for-planning-and-preparation"></a>Ajánlott eljárások tervezése és előkészítése

Ismerkedés a Time Series Insights, az a legcélszerűbb megismerése:

* Hogy mit kap mikor, [a Time Series Insights előzetes környezet kiépítése](#the-preview-environment).
* Mi a [Time Series azonosítók és a Timestamp tulajdonság](#configure-time-series-ids-and-timestamp-properties).
* Milyen új [Idősorozat-modell van](#understand-the-time-series-model), és hogyan hozhat létre saját.
* Hogyan [események küldése hatékonyan JSON](#shape-your-events).
* Time Series Insights [üzleti vész-helyreállítási lehetőségeket](#business-disaster-recovery).

Az Azure Time Series Insights egy üzleti használatalapú modellt alkalmaz. További információ a költségek és a kapacitás: [Time Series Insights díjszabása](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-preview-environment"></a>Az előzetes verziójú környezet

A Time Series Insights előzetes környezet üzembe helyezésekor, hozzon létre két Azure-erőforrások:

* Az Azure Time Series Insights – előzetes környezet
* Azure Storage-általános célú V1 fiók

Indítsa el, három további elemek szükségesek:

* A [Time Series-modell](./time-series-insights-update-tsm.md)
* Egy [eseményforrás csatlakozik a Time Series Insights](./time-series-insights-how-to-add-an-event-source-iothub.md)
* [Az eseményforrás beérkeznek események](./time-series-insights-send-events.md) , amely mindkét képeződik a modellt, és érvényes JSON formátumban vannak

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Time Series azonosítók és időbélyegzés tulajdonságainak konfigurálása

Hozzon létre egy új Time Series Insights-környezetet, válassza a Time Series azonosítóját. Ez tehát az adatok logikai partíció funkcionál. Feljegyzett, győződjön meg arról, hogy az a Time Series azonosítók készen áll.

> [!IMPORTANT]
> Time Series-azonosítók *nem módosítható* és *később már nem módosítható*. Minden egyes végső kiválasztása előtt ellenőrizze, és először használja.

Kiválaszthatja, hogy legfeljebb három kulcsok egyedileg megkülönböztetni azokat az erőforrásokat. További információkért olvassa el [ajánlott eljárások a Time Series ID kiválasztására vonatkozó](./time-series-insights-update-how-to-id.md) és [tárolási a bejövő és kimenő](./time-series-insights-update-storage-ingress.md).

Az időbélyeg-tulajdonság is fontos. Ez a tulajdonság is kijelölhet, eseményforrások hozzáadásakor. Minden egyes esemény forrása egy nem kötelező időbélyeg-tulajdonság, amely nyomon követése eseményforrások használta idővel rendelkezik. Időbélyegző értékeit a rendszer megkülönbözteti a kis-és nagybetűket, és formátumban kell lenniük az egyes eseményforrás egyes specifikációjának.

> [!TIP]
> Ellenőrizze a eseményforrások formázás és elemzés követelményei.

Ha üresen hagyja, az Eseménynapló Timestamp lesz eseményforrás eseményt sorba idején. Előzményadatok vagy kötegelt eseményeket küld, ha az időbélyeg-tulajdonság testreszabása hasznos több, mint az alapértelmezett esemény sorba időpontja. További információkért olvassa el, hogyan [eseményforrások hozzáadása az Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="understand-the-time-series-model"></a>Megismerheti a Time Series modell

Most már beállíthatja a Time Series Insights-környezet Idősorozat-modellben. Az új modell egyszerűen keresése és elemzése IoT-adatot. Ez lehetővé teszi, hogy a válogatott, a karbantartással és a felderítési bővítést az idősorozat-adatok, és segít fogyasztói használatra kész adatkészleteket előkészítése. A modell egy példányt, amely összekapcsolja az egyedi erőforráscsoport változók, típusok és a hierarchiák néven ismert leképezése idő sorozat azonosítók használ. Olvassa el az új [Idősorozat-modell](./time-series-insights-update-tsm.md).

A modell le dinamikus, így bármikor építhetők fel. Gyorsan és egyszerűen hozhat létre, és töltse fel be a Time Series Insights adatok elküldése előtt. A modell létrehozásához, lásd: [használja az Idősorozat-modell](./time-series-insights-update-how-to-tsm.md).

Sok ügyfél az Idősorozat-modell képezi le egy meglévő eszköz modell vagy a rendszer ERP már megfelel. Ha nem rendelkezik egy meglévő modell, egy előre elkészített felhasználói élmény van [megadott](https://github.com/Microsoft/tsiclient) gyors üzembe helyezéséig. Elképzeli, hogyan modell segíthetnek, tekintse meg a [minta bemutató környezetben](https://insights.timeseries.azure.com/preview/demo).

## <a name="shape-your-events"></a>Az események formázása

A Time Series Insights eseményeket küldeni módon ellenőrizheti. Ideális esetben az események vannak denormalizált hatékony és jól.

Jó tapasztalatok:

* Az Idősorozat-modellben levő Store a metaadatokat.
* Time Series mód, a szolgáltatáspéldány-mezők és az események tartalmazzák az csak a szükséges információkat, például egy Time Series-azonosító és az időbélyegző.

További információkért lásd: [alakzat-események](./time-series-insights-send-events.md#json).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [tárolási a bejövő és kimenő](./time-series-insights-update-storage-ingress.md) a Time Series Insights előzetes verziója.
- Ismerje meg [adatmodellezés](./time-series-insights-update-tsm.md) a Time Series Insights előzetes verziója.