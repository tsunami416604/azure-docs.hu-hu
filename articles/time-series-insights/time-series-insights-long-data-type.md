---
title: Támogatás hozzáadása hosszú adattípushoz | Microsoft Docs
description: Hosszú adattípus támogatása
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/16/2020
ms.custom: dpalled
ms.openlocfilehash: b4ddff197ff41ee9ddee129fae4699e18af8d0d0
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84898030"
---
# <a name="adding-support-for-long-data-type"></a>Hosszú adattípus támogatásának hozzáadása

Ezek a módosítások csak az előnézet (TB) környezetekre lesznek alkalmazva. Ha a standard (S) SKU ÁME-környezettel rendelkezik, figyelmen kívül hagyhatja ezeket a módosításokat.

Módosítjuk, hogyan tároljuk és indexeljük a numerikus adattípusokat Azure Time Series Insights előzetes verzióban, ami hatással lehet az Ön számára. Ha az alábbi esetek bármelyikét érinti, végezze el a szükséges módosításokat a lehető leghamarabb. Az adatai a régiótól függően a hosszú és a kétszeres, valamint a 2020. június 29. és 30. között eltelt időre lesznek indexelve. Ha bármilyen kérdése vagy problémája van a változással kapcsolatban, küldjön egy támogatási jegyet a Azure Portal és megemlítjük ezt a kommunikációt.

Ez a változás a következő esetekben van hatással:

1. Ha jelenleg idősorozat-modell változókat használ, és csak a telemetria-adataiban található szerves adattípusokat küldi el.
1. Ha jelenleg használja az idősorozat-modell változóit, és a telemetria-adataiban is elküldheti az integrált és a nem integrált adattípusokat.
1. Ha kategorikus változókat használ az egész értékek kategóriákra való leképezéséhez.
1. Ha a JavaScript SDK-t használja egyéni előtér-alkalmazás létrehozásához.
1. Ha a (z) a 1 000-tulajdonságnév korlátját a meleg tárolóban (WS) kívánja megkeresni, és az integrált és a nem integrált adatokat is el szeretné küldeni, a tulajdonságok száma a [Azure Portal](https://portal.azure.com/)metrikaként tekinthető meg.

Ha a fenti esetek bármelyike Önre vonatkozik, módosítania kell a modellt, hogy megfeleljen a változásnak. Frissítse az idősorozat kifejezést a változó definíciójában az ÁME Explorerben és bármely egyéni ügyfélen az API-k használatával az ajánlott változtatásokkal. Részletekért lásd alább.

A IoT-megoldástól és a megkötéstől függően előfordulhat, hogy nem jelenik meg az ÁME-TB-környezetbe küldendő információ. Ha nem biztos abban, hogy az adatai csak a belső és a nem integrált módon vannak integrálva, akkor néhány lehetőség közül választhat. Megvárhatja, hogy a szolgáltatás fel legyen szabadítva, majd vizsgálja meg a nyers eseményeket az Explorer felhasználói felületén, hogy megtudja, mely tulajdonságok lettek mentve két különálló oszlopban. Az alábbi módosításokat megelőző jelleggel teheti az összes numerikus címkével, vagy az események egy részhalmazát átmenetileg átirányíthatja a tárolóba a séma jobb megismerése és megismerése érdekében. Az események tárolásához kapcsolja be a Event Hubs az [esemény-rögzítést](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview) , vagy az IoT hub az Azure Blob Storage-be [irányítsa](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c#azure-storage) . Az adatkezelés az [Event hub Explorerben](https://marketplace.visualstudio.com/items?itemName=Summer.azure-event-hub-explorer)vagy az [Event Processor Host](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send#receive-events)használatával is megfigyelhető. Ha IoT Hub használ, tekintse meg a beépített végpont elérését [ismertető dokumentációt](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin) .

Vegye figyelembe, hogy ha ezeket a módosításokat a fenti dátumok alapján nem tudja elvégezni, akkor előfordulhat, hogy a lekérdezési API-kon keresztül elért érintett idősorozat-változók vagy a Time Series Insights Explorer *Null értéket* ad vissza (azaz nem jeleníti meg az adatkezelőt).

## <a name="recommended-changes"></a>Ajánlott módosítások

1. & 2. eset: **az Idősorozat-modell változóinak használata, és csak az integrált adattípusok küldése, illetve az telemetria-adatokban az integrált és a nem integrált típusok küldése.**

Ha jelenleg egész telemetria adatokat küld, az adatok két oszlopba lesznek osztva: "propertyValue_double" és "propertyValue_long".

Az egész számú adatot a rendszer a "propertyValue_long" értékre írja, amikor a változások életbe lépnek, és a korábban betöltött (és a jövőbeli betöltött) numerikus adatpropertyValue_double mennyiséget nem másolja át a rendszer.

Ha a "Tulajdonságérték" tulajdonsághoz tartozó két oszlop adatait szeretné lekérdezni, akkor az *Egyesítés ()* skaláris függvényt kell használnia a TSX. A függvény elfogadja ugyanazt az adattípust, és az első nem null értéket adja vissza az argumentumok listájában (További információ [a használatról](https://docs.microsoft.com/rest/api/time-series-insights/preview#other-functions)).

### <a name="variable-definition-in-time-series-explorer---numeric"></a>Változó definíciója a Time Series Explorerben – numerikus

*Előző változó definíciója:*

[![Előző változó definíciója](media/time-series-insights-long-data-type/var-def-previous.png)](media/time-series-insights-long-data-type/var-def-previous.png#lightbox)

*Új változó definíciója:*

[![Új változó definíciója](media/time-series-insights-long-data-type/var-def.png)](media/time-series-insights-long-data-type/var-def.png#lightbox)

Az *"egyesítés ($Event. tulajdonságérték. Double, toDouble ($Event. tulajdonságérték. Long)")* az egyéni [idősorozat kifejezésként](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) is használható.

### <a name="inline-variable-definition-using-time-series-query-apis---numeric"></a>Beágyazott változó definíciója a Time Series lekérdezési API-k használatával – numerikus

*Előző változó definíciója:*

    "PropertyValueVariable": {

        "kind": "numeric",

        "value": {

            "tsx": "$event.propertyValue.Double"

        },

        "filter": null,

        "aggregation": {

            "tsx": "avg($value)"

*Új változó definíciója:*

    "PropertyValueVariable ": {

        "kind": "numeric",

        "value": {

            "tsx": "coalesce($event.propertyValue.Long, toLong($event.propertyValue.Double))"

        },

        "filter": null,

        "aggregation": {

            "tsx": "avg($value)"

Az *"egyesítés ($Event. tulajdonságérték. Double, toDouble ($Event. tulajdonságérték. Long)")* az egyéni [idősorozat kifejezésként](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) is használható.

> [!NOTE]
> Azt javasoljuk, hogy minden olyan helyen frissítse ezeket a változókat, amelyek használhatók (idősorozat-modell, mentett lekérdezések Power BI összekötő-lekérdezések).

3. eset: **kategorikus változók használata egész értékek kategóriákra való leképezéséhez**

Ha jelenleg olyan kategorikus változókat használ, amelyek egész értékeket rendelnek a kategóriákhoz, valószínűleg a toLong függvényt használja az adatok dupla típusról hosszú típusra való átalakításához. A fenti esetekben hasonlóan meg kell egyesíteni a dupla és a hosszú adattípusú oszlopokat.

### <a name="variable-definition-in-time-series-explorer---categorical"></a>Változó definíciója a Time Series Explorerben – kategorikus

*Előző változó definíciója:*

[![Előző változó definíciója](media/time-series-insights-long-data-type/var-def-cat-previous.png)](media/time-series-insights-long-data-type/var-def-cat-previous.png#lightbox)

*Új változó definíciója:*

[![Új változó definíciója](media/time-series-insights-long-data-type/var-def-cat.png)](media/time-series-insights-long-data-type/var-def-cat.png#lightbox)

Az *"egyesítés ($Event. tulajdonságérték. Double, toDouble ($Event. tulajdonságérték. Long)")* az egyéni [idősorozat kifejezésként](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) is használható.

A kategorikus változók esetében az értéknek egész típusúnak kell lennie. Az egyesítés () összes argumentumának adattípusa csak hosszú lehet az egyéni [Idősorozat kifejezésében.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

### <a name="inline-variable-definition-using-time-series-query-apis---categorical"></a>Beágyazott változó definíciója a Time Series lekérdezési API-k használatával – kategorikus

*Előző változó definíciója:*

    "PropertyValueVariable_Long": {

        "kind": "categorical",

        "value": {

            "tsx": "tolong($event.propertyValue.Double)"

        },

        "categories": [

        {
            "label": "Good",

            "values": [0, 1, 2 ]

        },

        {

            "label": "Bad",

            "values": [ 3, 4 ]

        } ],

        "defaultCategory": {

            "label": "Unknown"

        }

    }

*Új változó definíciója:*

    "PropertyValueVariable_Long": {

        "kind": "categorical",

        "value": {

            "tsx": "coalesce($event.propertyValue.Long, tolong($event.propertyValue.Double))"

        },

        "categories": [

        {
            "label": "Good",

            "values": [0, 1, 2 ]

        },

        {

            "label": "Bad",

            "values": [ 3, 4 ]

        } ],

        "defaultCategory": {

            "label": "Unknown"

        }

    }

A kategorikus változók esetében az értéknek egész típusúnak kell lennie. Az egyesítés () összes argumentumának adattípusa csak hosszú lehet az egyéni [Idősorozat kifejezésében.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

> [!NOTE]
> Azt javasoljuk, hogy minden olyan helyen frissítse ezeket a változókat, amelyek használhatók (idősorozat-modell, mentett lekérdezések Power BI összekötő-lekérdezések).

4. eset: **a JavaScript SDK használatával hozzon létre egy egyéni előtér-alkalmazást**

Ha a fenti 1-3-es és egyéni alkalmazásokat felépítő esetek érintettek, frissítenie kell a lekérdezéseket az *Egyesítés ()* függvény használatára, ahogyan az a fenti példákban is látható.

5. eset: a **meleg tároló 1 000-es tulajdonságának korlátozása**

Ha nagy mennyiségű tulajdonsággal rendelkező, melegen tárolt felhasználó, és úgy véli, hogy ez a változás a 1 000 WS tulajdonság-név korláton keresztül küldi el a környezetet, küldjön támogatási jegyet a Azure Portal és megemlítjük ezt a kommunikációt.

## <a name="next-steps"></a>További lépések

* Tekintse át a [bejövő és a Storage](time-series-insights-update-storage-ingress.md) szolgáltatásról szóló cikket a támogatott adattípusok megismeréséhez.
