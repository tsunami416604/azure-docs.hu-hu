---
title: Az előzetes verziós környezet megtervezése – Azure Time Series Insights | Microsoft dokumentumok
description: Az Azure Time Series Insights előzetes környezetének konfigurálásához, kezeléséhez, megtervezéséhez és üzembe helyezéséhez ajánlott eljárások.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/15/2020
ms.custom: seodec18
ms.openlocfilehash: b16d78c9670d05fcec8126c5544d1dd97f6a03bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045720"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Az Azure Time Series Insights előzetes verziójának megtervezése

Ez a cikk az Azure Time Series Insights előzetes verziójának használatával történő gyors tervezésés gyors kezdés ajánlott eljárásokat ismerteti.

> [!NOTE]
> Az általánosan rendelkezésre álló Time Series Insights-példány megtervezéséhez ajánlott gyakorlati tanácsokért olvassa el [az Azure Time Series Insights általános rendelkezésre állási környezetének megtervezése](time-series-insights-environment-planning.md)című gyakorlati tanácst.

## <a name="best-practices-for-planning-and-preparation"></a>Bevált gyakorlatok a tervezéshez és az előkészítéshez

A környezet tervezésével és előkészítésével kapcsolatos gyakorlati tanácsokat a következő cikkek ismertetik:

* Mit kap, ha [a Time Series Insights előzetes környezetben.](#the-preview-environment)
* Mik a [Time Series-azonosítók és az időbélyeg-tulajdonságok.](#configure-time-series-ids-and-timestamp-properties)
* Mi az új [Time Series modell](#understand-the-time-series-model), és hogyan kell építeni a saját.
* Hogyan [küldjünk eseményeket hatékonyan JSON](#shape-your-events).
* A Time Series Insights [üzleti vész-helyreállítási lehetőségei](#business-disaster-recovery).

Az Azure Time Series Insights alkalmazásként i-you-business modellt alkalmaz. A díjakról és a kapacitásról a [Time Series Insights díjszabása](https://azure.microsoft.com/pricing/details/time-series-insights/)című olvasnivalóban olvashat bővebben.

## <a name="the-preview-environment"></a>Az előnézeti környezet

A Time Series Insights előzetes verziójú környezet kiépítésekor két Azure-erőforrást hoz létre:

* Azure Time Series Insights előzetes verziójú környezet
* Általános célú V1-fiók az Azure Storage-ban

A kiépítési folyamat részeként adja meg, hogy szeretné-e engedélyezni a meleg bolt. A Warm store többszintű lekérdezési élményt nyújt. Ha engedélyezve van, meg kell adnia egy 7 és 30 nap közötti megőrzési időszakot. A meleg tároló megőrzési időszakán belül végrehajtott lekérdezések általában gyorsabb válaszidőt biztosítanak. Ha egy lekérdezés a meleg bolt megőrzési időszakát öleli fel, a hűtőházból szolgálják fel.

A meleg boltlekérdezései ingyenesek, míg a hűtőházra vonatkozó lekérdezések költségei merülnek fel. Fontos, hogy megértse a lekérdezési mintákat, és ennek megfelelően tervezze meg a melegbolt-konfigurációt. Azt javasoljuk, hogy a legújabb adatok interaktív elemzése a melegboltban és a mintaelemzésben, valamint a hosszú távú trendek hidegben található.

> [!NOTE]
> Ha többet szeretne megtudni a meleg adatok lekérdezéséről, olvassa el az [API-referencia című olvasni.](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters)

A kezdéshez három további elemre van szükség:

* [Idősorozat-modell](./time-series-insights-update-tsm.md)
* [A Time Series Insights-hoz kapcsolódó eseményforrás](./time-series-insights-how-to-add-an-event-source-iothub.md)
* [Az eseményforrásba áramló események,](./time-series-insights-send-events.md) amelyek a modellhez vannak rendelve, és érvényes JSON formátumúak

## <a name="review-preview-limits"></a>Előzetes verziós korlátok áttekintése

[!INCLUDE [Review Time Series Insights Preview limits](../../includes/time-series-insights-preview-limits.md)]

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Idősorozat-azonosítók és időbélyeg-tulajdonságok konfigurálása

Új Time Series Insights-környezet létrehozásához jelöljön ki egy Idősorozat-azonosítót. Ezzel logikai partícióként működik az adatok számára. Mint megjegyezte, győződjön meg arról, hogy a Time Series azonosítók kész.

> [!IMPORTANT]
> Az idősorozat-azonosítók *később nem módosíthatók.* Ellenőrizze mindegyiket a végső kiválasztás és az első használat előtt.

Legfeljebb három kulcsot választhat ki az erőforrások egyedi megkülönböztetéséhez. További információért olvassa el [az Idősorozat-azonosító kiválasztására vonatkozó gyakorlati tanácsokat,](./time-series-insights-update-how-to-id.md) [valamint a tárolást és a be- és be- és betárolást.](./time-series-insights-update-storage-ingress.md)

A **Timestamp** tulajdonság is fontos. Ezt a tulajdonságot eseményforrások hozzáadásakor jelölheti ki. Minden eseményforrás rendelkezik egy opcionális Időbélyeg tulajdonsággal, amely az eseményforrások időbeli nyomon követésére szolgál. Az időbélyeg-értékek a kis- és nagybetűket megkülönböztetők, és az egyes eseményforrások egyedi specifikációjának megfelelően kell formázni őket.

> [!TIP]
> Ellenőrizze az eseményforrások formázási és elemzési követelményeit.

Ha üresen hagyja, az eseményforrás eseményvárólistás ideje lesz az esemény időbélyege. Ha előzményadatokat vagy kötegelt eseményeket küld, a Timestamp tulajdonság testreszabása hasznosabb, mint az alapértelmezett eseményvárólistás idő. További információt az [Eseményforrások Azure IoT Hubban való hozzáadásáról](./time-series-insights-how-to-add-an-event-source-iothub.md)olvashat.

## <a name="understand-the-time-series-model"></a>A Time Series modell ismertetése

Most már konfigurálhatja a Time Series Insights környezet Time Series model. Az új modell megkönnyíti az IoT-adatok megkeresését és elemzését. Lehetővé teszi az idősorozat-adatok kiírását, karbantartását és bővítését, és segít a fogyasztóra kész adatkészletek előkészítésében. A modell idősorozat-azonosítókat használ, amelyek leképeznek egy olyan példányt, amely az egyedi erőforrást változókkal, más néven típusokkal és hierarchiákkal társítja. Olvassa el az új [Idősorozat-modellt.](./time-series-insights-update-tsm.md)

A modell dinamikus, így bármikor megépíthető. A gyors kezdéshez hozza létre és töltse fel azt, mielőtt adatokat szeretne bejuttatni a Time Series Insightsba. A modell létrehozásához olvassa el [A Time Series Model használata](./time-series-insights-update-how-to-tsm.md)című olvasni valót.

Sok ügyfél számára a Time Series Model leképezi egy már meglévő eszközmodellre vagy ERP-rendszerre. Ha nem rendelkezik egy meglévő modell, egy előre összeállított felhasználói élményt [biztosít,](https://github.com/Microsoft/tsiclient) hogy gyorsan elinduljon. Ha el szeretné képzelni, hogy egy modell hogyan segíthet, tekintse meg a [mintabemutató-környezetet.](https://insights.timeseries.azure.com/preview/demo)

## <a name="shape-your-events"></a>Az események alakítása

You can verify the way that you send events to Time Series Insights. Ideális esetben az események denormalizált jól és hatékonyan.

Egy jó ökölszabály:

* Metaadatok tárolása a Time Series modellben.
* Győződjön meg arról, hogy a Time Series Mode, a példánymezők és az események csak a szükséges információkat tartalmazzák, például egy Idősorozat-azonosítót vagy egy Időbélyeg tulajdonságot.

További információ: [Shape events](./time-series-insights-send-events.md#supported-json-shapes).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>További lépések

- Tekintse át az [Azure Advisort](../advisor/advisor-overview.md) az üzleti helyreállítási konfigurációs beállítások megtervezéséhez.
- További információ a tárolásról és a [be- és bevitelről](./time-series-insights-update-storage-ingress.md) a Time Series Insights előzetes verziójában.
- Ismerje meg az [adatmodellezést](./time-series-insights-update-tsm.md) a Time Series Insights előzetes verziójában.
