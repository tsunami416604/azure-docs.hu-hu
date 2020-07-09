---
title: Az előzetes verziójú környezet megtervezése – Azure Time Series Insights | Microsoft Docs
description: Ajánlott eljárások a Azure Time Series Insights-előnézeti környezet konfigurálásához, kezeléséhez, megtervezéséhez és üzembe helyezéséhez.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/27/2020
ms.custom: seodec18
ms.openlocfilehash: b57a7c04db0e601b90bc19059df70e63795784bf
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86036950"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>A Azure Time Series Insights előzetes verzió környezetének megtervezése

Ez a cikk azt ismerteti, hogyan lehet gyorsan megtervezni és megkezdeni az első lépéseket Azure Time Series Insights előzetes verzió használatával.

> [!NOTE]
> Az általánosan elérhető Time Series Insights-példány megtervezéséhez ajánlott eljárásokért olvassa el [a Azure Time Series Insights általános elérhetőségi környezet megtervezése](time-series-insights-environment-planning.md)című cikkből.

## <a name="best-practices-for-planning-and-preparation"></a>Ajánlott eljárások a tervezéshez és előkészítéshez

A környezet tervezésével és előkészítésével kapcsolatos ajánlott eljárásokat a következő cikkekben találja:

* Mit kap, ha [kiépít egy Time Series Insights előnézeti környezetet](#the-preview-environment).
* Az [idősorozat-azonosítók és az időbélyeg tulajdonságai](#configure-time-series-ids-and-timestamp-properties).
* Az új [Idősorozat-modell](#understand-the-time-series-model), valamint a saját létrehozása.
* [Események hatékony küldése a JSON-ban](#shape-your-events).
* Time Series Insights [üzleti katasztrófák helyreállítási lehetőségei](#business-disaster-recovery).

A Azure Time Series Insights az üzleti modellt is alkalmazza. További információ a díjakról és a kapacitásról: [Time Series Insights díjszabása](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-preview-environment"></a>Az előzetes verziójú környezet

Time Series Insights előnézeti környezet kiépítésekor két Azure-erőforrást hoz létre:

* Azure Time Series Insights előnézeti környezet
* Azure Storage-fiók

A kiépítési folyamat részeként meg kell adnia, hogy szeretné-e engedélyezni a meleg tárolót. A meleg tárolás többplatformos lekérdezési élményt biztosít. Ha engedélyezve van, meg kell adnia egy 7 és 30 nap közötti megőrzési időtartamot. A meleg tárolási megőrzési időszakon belül végrehajtott lekérdezések általában gyorsabb válaszidőt biztosítanak. Ha egy lekérdezés a meleg tároló megőrzési időtartamára terjed ki, a rendszer a hűtőházi tárolóból kézbesíti.

A melegen tárolt lekérdezések ingyenesek, míg a hűtőházi tárolással kapcsolatos lekérdezések költségekkel járnak. Fontos megérteni a lekérdezési mintákat, és ennek megfelelően tervezze meg a meleg tároló konfigurációját. Azt javasoljuk, hogy az interaktív elemzések a legújabb, a meleg áruházban található és a minták elemzése, valamint a hosszú távú trendek esetében is naprakészek legyenek.

> [!NOTE]
> Ha többet szeretne megtudni a meleg adatainak lekérdezéséről, olvassa el az [API-referenciát](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters).

A kezdéshez három további elemre van szükség:

* Egy [Idősorozat-modell](./concepts-model-overview.md)
* [Time Series Insightshoz csatlakoztatott eseményforrás](./time-series-insights-how-to-add-an-event-source-iothub.md)
* [Az eseményforrás](./time-series-insights-send-events.md) , amely a modellhez van rendelve, és érvényes JSON formátumú.

## <a name="review-preview-limits"></a>Előzetes verzió korlátainak áttekintése

[!INCLUDE [Review Time Series Insights Preview limits](../../includes/time-series-insights-preview-limits.md)]

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Idősorozat-azonosítók és időbélyeg-tulajdonságok konfigurálása

Új Time Series Insights-környezet létrehozásához válasszon egy idősorozat-azonosítót. Ez logikai partícióként működik az adatai számára. Ahogy azt említettük, győződjön meg arról, hogy az idősorozat-azonosítók készen állnak.

> [!IMPORTANT]
> Az idősorozat-azonosítókat *később nem lehet módosítani*. Győződjön meg róla, hogy a végső kiválasztás és az első használat előtt ellenőrizzen egyet.

Az erőforrások egyedi megkülönböztetéséhez legfeljebb három kulcsot választhat ki. További információ: [ajánlott eljárások az idősorozat-azonosító és a betöltési szabályok kiválasztásához](./time-series-insights-update-how-to-id.md) . [Ingestion rules](concepts-json-flattening-escaping-rules.md)

Az **időbélyeg** tulajdonság szintén fontos. Ezt a tulajdonságot az eseményforrás hozzáadásakor lehet kijelölni. Minden eseményforrás egy nem kötelező időbélyeg-tulajdonsággal rendelkezik, amely az események időbeli alakulásának nyomon követésére szolgál. Az időbélyegző értékei megkülönböztetik a kis-és nagybetűket, és az egyes eseményforrás egyedi leírását kell formázni.

> [!TIP]
> Ellenőrizze az eseményforrás formázásának és elemzésének követelményeit.

Ha üresen hagyja, az esemény időbélyegzője az sorba helyezni időpontját használja. Ha a múltbeli vagy kötegelt eseményeket küldi el, az időbélyegző tulajdonság testreszabása az alapértelmezett Event sorba helyezni-időpontnál hasznosabb lehet. További információkért olvassa el a következő témakört [: Event sources hozzáadása az Azure IoT Hubban](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="understand-the-time-series-model"></a>Az idősorozat-modell ismertetése

Mostantól konfigurálhatja Time Series Insights környezete idősorozat-modelljét. Az új modell segítségével könnyedén megtalálhatja és elemezheti a IoT-adatbázisokat. Lehetővé teszi a Time Series-adatgyűjtési és-bővítési feladatok elvégzését, és segít a felhasználó által készített adatkészletek előkészítésében. A modell idősorozat-azonosítókat használ, amelyek olyan példányhoz képezhetők le, amely társítja az egyedi erőforrást változókkal, más néven típusokkal és hierarchiákkal. További információ az új [Idősorozat-modellről](./concepts-model-overview.md).

A modell dinamikus, így bármikor létrehozható. A gyors kezdéshez hozza létre és töltse fel az adatoknak a Time Series Insightsba való kitolása előtt. A modell létrehozásához olvassa el [az idősorozat-modell használatát](./time-series-insights-update-how-to-tsm.md)ismertetőt.

Sok ügyfél esetében az idősorozat-modell egy már meglévő eszköz-modellel vagy ERP-rendszerrel van leképezve. Ha nem rendelkezik meglévő modellel, a rendszer előre elkészített felhasználói élményt [biztosít](https://github.com/Microsoft/tsiclient) a gyors üzembe helyezéshez. Ha szeretné elképzelni, hogyan segíthet a modell, tekintse meg a [minta bemutató környezetét](https://insights.timeseries.azure.com/preview/demo).

## <a name="shape-your-events"></a>Az események formázása

Ellenőrizheti, hogy az események hogyan küldhetők Time Series Insightsba. Ideális esetben a rendszer jól és hatékonyan denormalizálja az eseményeket.

Egy jó ökölszabály:

* Az idősorozat-modellben tárolja a metaadatokat.
* Győződjön meg arról, hogy az idősoros mód, a példány mezői és az események csak a szükséges információkat tartalmazzák, például egy idősorozat-azonosítót vagy egy időbélyeg-tulajdonságot.

További információ: [Shape Events](./time-series-insights-send-events.md#supported-json-shapes).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>További lépések

- Tekintse át [Azure Advisor](../advisor/advisor-overview.md) az üzleti helyreállítás konfigurációs beállításainak megtervezéséhez.
- További információ az [adatfeldolgozásról](./concepts-ingestion-overview.md).
- Tekintse át az [adattárolásról](./concepts-storage.md) szóló cikket
- Tudnivalók az [adatmodellezésről](./concepts-model-overview.md) a Time Series Insights előzetes verziójában.
