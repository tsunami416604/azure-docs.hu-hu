---
title: Azure Time Series Insights előzetes Adatlekérdezési szolgáltatás | Microsoft Docs
description: Azure Time Series Insights az előnézeti adatlekérdezést.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/07/2019
ms.custom: seodec18
ms.openlocfilehash: 7b7d041b678ccf2a476afc6d0744f6971349677e
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034881"
---
# <a name="data-querying"></a>Adatok lekérdezése

A Azure Time Series Insights Preview lehetővé teszi az adatlekérdezést a környezetben tárolt eseményeken és metaadatokon a nyilvános felületi API-kon keresztül. Ezeket az API-kat a [Time Series Insights Preview Explorerben](./time-series-insights-update-explorer.md)is használják.

A Time Series Insightsban három elsődleges API-kategória érhető el:

* **Környezeti API**-k: A Time Series Insights környezet lekérdezéseit is lehetővé teszi. Ilyenek például azok a környezetek listája, amelyekhez a hívó hozzáfér és környezeti metaadatokat tartalmaz.

* **Time Series Model – Query (TSM-Q) API-k**: Lehetővé teszi a létrehozási, olvasási, frissítési és törlési műveleteket az idősorozat-modell környezet részében tárolt metaadatokon. Ilyenek például a példányok, a típusok és a hierarchiák.

* **Time Series Query (TSQ) API-k**: Lehetővé teszi az események adatainak lekérését, ahogy az a forrás szolgáltatótól rögzítve van. Ezek az API-k műveleteket végezhetnek az idősorozat-adatokra vonatkozó számítások átalakításához, összevonásához és végrehajtásához.

A [Time Series kifejezés (TSX) nyelve](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx) egy hatékony negyedik kategória. Idősorozat-modelleket használ a speciális számítások összeállításának lehetővé tételéhez.

## <a name="azure-time-series-insights-preview-core-apis"></a>Azure Time Series Insights előzetes verziójú alapapi-k

A következő alapvető API-k támogatottak.

[@no__t – 1Time sorozat lekérdezésének áttekintése](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>Környezeti API-k

A következő környezeti API-k érhetők el:

* [Környezeti API beolvasása](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environments-api): Azon környezetek listáját adja vissza, amelyekhez a hívó jogosult az elérésére.
* [Környezet rendelkezésre állási API-k beolvasása](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environment-availability-api): Az események számának eloszlását adja vissza az esemény időbélyegzője @no__t – 0. Ez az API segít meghatározni, hogy vannak-e események az időbélyegen az események számának visszaadásával, ha vannak ilyenek.
* [Event Schema API beszerzése](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-event-schema-api): Egy adott keresési span esemény-sémájának metaadatait adja vissza. Ez az API segít beolvasni a sémában elérhető összes metaadatot és tulajdonságot a megadott keresési tartományhoz.

## <a name="time-series-model-query-tsm-q-apis"></a>Time Series Model – Query (TSM-Q) API-k

A következő idősorozat-modell-lekérdezési API-k érhetők el:

* [Modell beállításai API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api): Engedélyezi a beolvasást és a javítást az alapértelmezett típus és a környezet modellje nevében.
* [Típusok API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api): Lehetővé teszi a szifilisz idősorozat-típusok és a hozzájuk tartozó változók használatát.
* [Hierarchiák API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api): Lehetővé teszi a szifilisz használatát az idősorozat-hierarchiákban és a hozzájuk tartozó mezők elérési útjain.
* [Példányok API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api): Lehetővé teszi a szifilisz használatát a Time Series-példányokon és a hozzájuk társított példány mezőin.

## <a name="time-series-query-tsq-apis"></a>Idősorozat-lekérdezési (TSQ) API-k

A következő Time Series lekérdezési API-k érhetők el:

* [Események beolvasása API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-events-api): Lehetővé teszi Time Series Insights adatok lekérdezését és lekérését az eseményekről, amikor azokat Time Series Insights a forrás-szolgáltatótól rögzítik.

* [Series API beolvasása](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-series-api): Lehetővé teszi a rögzített események Time Series Insights adatainak lekérdezését és lekérését a huzalon rögzített adatok használatával. A visszaadott értékek a modellben definiált változókon alapulnak, vagy beágyazottként vannak megadva.

    >[!NOTE]
    > Az összesítési záradékot a rendszer figyelmen kívül hagyja, még akkor is, ha az egy modellben van megadva, vagy ha beágyazott.

  A Get Series API az egyes változók idősorozatának értékét adja vissza az egyes intervallumokhoz. Az idősorozat-érték olyan formátum, amelyet Time Series Insights a kimenet JSON-hoz a lekérdezésből. A visszaadott értékek az idősorozat-azonosító és a megadott változók halmazán alapulnak.

* [Összesítő sorozat API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#aggregate-series-api): Lehetővé teszi a rögzített események Time Series Insights adatainak lekérdezését és lekérését mintavételezéssel és a rögzített adatok összesítésével.

  Az összesítő sorozat API az egyes változók idősorozatának értékét adja vissza az egyes intervallumokhoz. Az értékek az idősorozat-azonosító és a megadott változók halmazán alapulnak. Az összesítő sorozat API az idősorozat-modellben tárolt változók használatával csökkenti az adatmennyiséget, vagy beágyazott vagy összegzett adatokat tartalmaz.

  Támogatott összesített típusok: `Min`, `Max`, `Sum`, `Count`, `Average`

## <a name="next-steps"></a>További lépések

- További információ a [tárolásról és a bejövő](./time-series-insights-update-storage-ingress.md) forgalomról a Azure Time Series Insights előzetes verziójában.

- Olvassa el a Time Series Insights előzetes verziójának [adatmodellezési](./time-series-insights-update-tsm.md) cikkét.

- [Az Idősorozat-azonosítók kiválasztásakor ajánlott eljárások](./time-series-insights-update-how-to-id.md)észlelése.
