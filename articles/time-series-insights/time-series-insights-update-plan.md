---
title: A Azure Time Series Insights előzetes verzió környezetének megtervezése | Microsoft Docs
description: Tervezze meg Azure Time Series Insights előnézeti környezetét.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/24/2019
ms.custom: seodec18
ms.openlocfilehash: b97db5fcebeea67cc593a4d2c1fd677a55ad8559
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72550178"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>A Azure Time Series Insights előzetes verzió környezetének megtervezése

Ez a cikk azt ismerteti, hogyan lehet gyorsan megtervezni és megkezdeni az első lépéseket a Azure Time Series Insights előzetes verziójának használatával.

> [!NOTE]
> Az általánosan elérhető Time Series Insights-példány megtervezéséhez ajánlott eljárásokat a [Azure Time Series Insights általános elérhetőségi környezet megtervezése](time-series-insights-environment-planning.md)című témakörben talál.

## <a name="best-practices-for-planning-and-preparation"></a>Ajánlott eljárások a tervezéshez és előkészítéshez

A Time Series Insights első lépéseihez érdemes megismernie a következőket:

* Mit kap, ha [kiépít egy Time Series Insights előnézeti környezetet](#the-preview-environment).
* Az [idősorozat-azonosítók és az időbélyeg tulajdonságai](#configure-time-series-ids-and-timestamp-properties).
* Az új [Idősorozat-modell](#understand-the-time-series-model), valamint a saját létrehozása.
* [Események hatékony küldése a JSON-ban](#shape-your-events).
* Time Series Insights [üzleti katasztrófák helyreállítási lehetőségei](#business-disaster-recovery).

A Azure Time Series Insights az üzleti modellt is alkalmazza. További információ a díjakról és a kapacitásról: [Time Series Insights díjszabása](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-preview-environment"></a>Az előzetes verziójú környezet

Time Series Insights előnézeti környezet kiépítésekor két Azure-erőforrást hoz létre:

* Azure Time Series Insights előnézeti környezet
* Azure Storage általános célú v1-fiók

A kezdéshez három további elemre van szükség:

* Egy [Idősorozat-modell](./time-series-insights-update-tsm.md)
* [Time Series Insightshoz csatlakoztatott eseményforrás](./time-series-insights-how-to-add-an-event-source-iothub.md)
* [Az eseményforrás](./time-series-insights-send-events.md) , amely a modellhez van rendelve, és érvényes JSON formátumú.

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Idősorozat-azonosítók és időbélyeg-tulajdonságok konfigurálása

Új Time Series Insights-környezet létrehozásához válasszon egy idősorozat-azonosítót. Ez logikai partícióként működik az adatai számára. Ahogy azt említettük, győződjön meg arról, hogy az idősorozat-azonosítók készen állnak.

> [!IMPORTANT]
> Az idősorozat-azonosítókat *később nem lehet módosítani*. Győződjön meg róla, hogy a végső kiválasztás és az első használat előtt ellenőrizzen egyet.

Az erőforrások egyedi megkülönböztetéséhez legfeljebb három kulcsot választhat ki. További információ: [ajánlott eljárások az idősorozat-azonosító](./time-series-insights-update-how-to-id.md) és a [tárolás és a bejövő](./time-series-insights-update-storage-ingress.md)adatok kiválasztásához.

Az időbélyeg tulajdonság szintén fontos. Ezt a tulajdonságot az eseményforrás hozzáadásakor lehet kijelölni. Minden eseményforrás egy nem kötelező időbélyeg-tulajdonsággal rendelkezik, amely az események időbeli alakulásának nyomon követésére szolgál. Az időbélyegző értékei megkülönböztetik a kis-és nagybetűket, és az egyes eseményforrás egyedi leírását kell formázni.

> [!TIP]
> Ellenőrizze az eseményforrás formázásának és elemzésének követelményeit.

Ha üresen hagyja, az esemény időbélyegzője az sorba helyezni időpontját használja. Ha a múltbeli vagy kötegelt eseményeket küldi el, az időbélyegző tulajdonság testreszabása az alapértelmezett Event sorba helyezni-időpontnál hasznosabb lehet. További információkért olvassa el a következő témakört [: Event sources hozzáadása az Azure IoT Hubban](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="understand-the-time-series-model"></a>Az idősorozat-modell ismertetése

Mostantól konfigurálhatja Time Series Insights környezete idősorozat-modelljét. Az új modell segítségével könnyedén megtalálhatja és elemezheti a IoT-adatbázisokat. Lehetővé teszi a Time Series-adatgyűjtési és-bővítési feladatok elvégzését, és segít a felhasználó által készített adatkészletek előkészítésében. A modell idősorozat-azonosítókat használ, amelyek olyan példányhoz képezhetők le, amely társítja az egyedi erőforrást változókkal, más néven típusokkal és hierarchiákkal. További információ az új [Idősorozat-modellről](./time-series-insights-update-tsm.md).

A modell dinamikus, így bármikor létrehozható. A gyors kezdéshez hozza létre és töltse fel az adatoknak a Time Series Insightsba való kitolása előtt. A modell létrehozásához tekintse meg [az idősorozat-modell használatát](./time-series-insights-update-how-to-tsm.md)ismertető témakört.

Sok ügyfél esetében az idősorozat-modell egy már meglévő eszköz-modellel vagy ERP-rendszerrel van leképezve. Ha nem rendelkezik meglévő modellel, a rendszer előre elkészített felhasználói élményt [biztosít](https://github.com/Microsoft/tsiclient) a gyors üzembe helyezéshez. Ha szeretné elképzelni, hogyan segíthet a modell, tekintse meg a [minta bemutató környezetét](https://insights.timeseries.azure.com/preview/demo).

## <a name="shape-your-events"></a>Az események formázása

Ellenőrizheti, hogy az események hogyan küldhetők Time Series Insightsba. Ideális esetben a rendszer jól és hatékonyan denormalizálja az eseményeket.

Egy jó ökölszabály:

* Az idősorozat-modellben tárolja a metaadatokat.
* Az idősorozat mód, a példány mezői és az események csak a szükséges információkat tartalmazzák, például egy idősorozat-azonosítót vagy egy időbélyeg-t.

További információ: [Shape Events](./time-series-insights-send-events.md#supported-json-shapes).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Következő lépések

- Tekintse át [Azure Advisor](../advisor/advisor-overview.md) az üzleti helyreállítás konfigurációs beállításainak megtervezéséhez.

- További információ a [tárolásról és a bejövő](./time-series-insights-update-storage-ingress.md) forgalomról a Time Series Insights előzetes verziójában.

- Tudnivalók az [adatmodellezésről](./time-series-insights-update-tsm.md) a Time Series Insights előzetes verziójában.