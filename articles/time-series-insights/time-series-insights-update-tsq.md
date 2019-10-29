---
title: Azure Time Series Insights előzetes Adatlekérdezési szolgáltatás | Microsoft Docs
description: Azure Time Series Insights az előnézeti adatlekérdezést.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/21/2019
ms.custom: seodec18
ms.openlocfilehash: 97265a83a73d45f45a4bd1183df61521f4ca29bf
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989689"
---
# <a name="data-querying"></a>Adatok lekérdezése

A Azure Time Series Insights Preview lehetővé teszi az adatlekérdezést a környezetben tárolt eseményeken és metaadatokon a nyilvános felületi API-kon keresztül. Ezeket az API-kat a [Time Series Insights Preview Explorerben](./time-series-insights-update-explorer.md)is használják.

A Time Series Insightsban három elsődleges API-kategória érhető el:

* **Környezeti API**-k: ezek az API-k lehetővé teszik a Time Series Insights-környezet lekérdezéseit. Ilyenek például azok a környezetek listája, amelyekhez a hívó hozzáfér és környezeti metaadatokat tartalmaz.
* **Idősorozat-modell – Query (TSM-Q) API-k**: lehetővé teszi a létrehozási, olvasási, frissítési és törlési (szifilisz) műveleteket az idősorozat-modell környezet részében tárolt metaadatokon. Ilyenek például a példányok, a típusok és a hierarchiák.
* **Idősorozat-lekérdezési (TSQ-) API-k**: lehetővé teszi a telemetria vagy az események adatainak lekérését a forrás szolgáltatótól való rögzítéshez, illetve az adatoknak a változók részét képező skaláris és összesítő függvények használatával történő lekérését. Ezek az API-k műveleteket végezhetnek az idősorozat-adatokra vonatkozó számítások átalakításához, összevonásához és alkalmazásához.

A Time Series Insights egy Rich string-alapú kifejezés nyelvét, az [Idősorozat kifejezését (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx)használja a számítások kifejezésére.

## <a name="azure-time-series-insights-preview-core-apis"></a>Azure Time Series Insights előzetes verziójú alapapi-k

A következő alapvető API-k támogatottak.

[![Time Series-lekérdezés áttekintése](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>Környezeti API-k

A következő környezeti API-k érhetők el:

* A [környezeti API beolvasása](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environments-api): azon környezetek listáját adja vissza, amelyekhez a hívó jogosult az elérésére.
* [Környezet rendelkezésre állási API-k beolvasása](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environment-availability-api): az esemény időbélyegének `$ts` az események számának eloszlását adja vissza. Ez az API segít meghatározni, hogy vannak-e események az időbélyegen az események számának visszaadásával, ha vannak ilyenek.
* [Event Schema API beolvasása](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-event-schema-api): egy adott keresési span esemény-séma metaadatainak beolvasása. Ez az API segít beolvasni a sémában elérhető összes metaadatot és tulajdonságot a megadott keresési tartományhoz.

## <a name="time-series-model-query-tsm-q-apis"></a>Time Series Model – Query (TSM-Q) API-k

A következő idősorozat-modell-lekérdezési API-k érhetők el. Ezen API-k többsége támogatja a Batch-végrehajtási műveletet, hogy több idősorozatos modell entitáson engedélyezze a Batch-SZIFILISZi műveleteket:

* [Model Settings API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api): a *Get* és a *patch* engedélyezése az alapértelmezett típus és a környezet modellje számára.
* [Típusok API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api): lehetővé teszi a szifilisz használatát az idősorozat-típusoknál és a hozzájuk társított változóknál.
* [Hierarchiák API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api): lehetővé teszi a szifilisz használatát az idősorozat-hierarchiákban és a hozzájuk tartozó mezők elérési útjain.
* [Példányok API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api): lehetővé teszi a szifilisz használatát a Time Series-példányokon és a hozzájuk társított példány mezőin. A példányok API emellett a következő műveleteket is támogatja:
  * [Keresés](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/search): lekéri a találatok részleges listáját az idősorozat-példányok keresésekor a példány attribútumai alapján.
  * [Javaslat](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/suggest): megkeresi és javasolja a találatok részleges listáját az idősorozat-példányok a példány attribútumai alapján történő keresésekor.

## <a name="time-series-query-tsq-apis"></a>Idősorozat-lekérdezési (TSQ) API-k

A következő Time Series lekérdezési API-k érhetők el. Ezek az API-k a Time Series Insights összes támogatott többrétegű tárolóján elérhetők. A lekérdezés URL-paramétereinek használatával megadható, hogy a lekérdezés milyen [típusú tárolási típust](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters) hajtson végre:

* [Események beolvasása API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-events-api): lehetővé teszi Time Series Insights adatok lekérdezését és lekérését az eseményekről, amikor azokat Time Series Insights a forrás-szolgáltatótól rögzítik. Ez az API lehetővé teszi a nyers események lekérését egy adott idősorozat-azonosító és keresési tartomány számára. Ez az API támogatja a tördelést a kiválasztott bemenet teljes adatkészletének lekéréséhez. 

* [Series API beolvasása](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-series-api): lehetővé teszi Time Series Insights adatok lekérdezését és lekérését a rögzített eseményekről a huzalon rögzített adatok használatával. A visszaadott értékek a modellben definiált változókon alapulnak, vagy beágyazottként vannak megadva. Ez az API támogatja a tördelést a kiválasztott bemenet teljes adatkészletének lekéréséhez. Ez az API segít a számított tulajdonságok vagy oszlopok definiálásában.

    >[!NOTE]
    > Az összesítési záradékot a rendszer figyelmen kívül hagyja, még akkor is, ha az egy modellben van megadva, vagy ha beágyazott.

  A Get Series API az egyes változók idősorozatának értékét adja vissza az egyes intervallumokhoz. Az idősorozat-érték olyan formátum, amelyet Time Series Insights a kimenet JSON-hoz a lekérdezésből. A visszaadott értékek az idősorozat-azonosító és a megadott változók halmazán alapulnak.

* [Összesítő sorozat API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#aggregate-series-api): lehetővé teszi Time Series Insights adatok lekérdezését és lekérését a rögzített eseményekről mintavételezéssel és a rögzített adatok összesítésével. Ez az API a [folytatási jogkivonatok](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#queryresultpage)használatával támogatja a folyamatos végrehajtást.

  Az összesítő sorozat API az egyes változók idősorozatának értékét adja vissza az egyes intervallumokhoz. Az értékek az idősorozat-azonosító és a megadott változók halmazán alapulnak. Az összesítő sorozat API az idősorozat-modellben tárolt változók használatával csökkenti az adatmennyiséget, vagy beágyazott vagy összegzett adatokat tartalmaz.

## <a name="next-steps"></a>Következő lépések

- További információ a [tárolásról és a bejövő](./time-series-insights-update-storage-ingress.md) forgalomról Azure Time Series Insights előzetes verzióban.
- Olvassa el a Time Series Insights előzetes verziójának [adatmodellezési](./time-series-insights-update-tsm.md) cikkét.
- [Az Idősorozat-azonosító kiválasztására vonatkozó ajánlott eljárások](./time-series-insights-update-how-to-id.md).
