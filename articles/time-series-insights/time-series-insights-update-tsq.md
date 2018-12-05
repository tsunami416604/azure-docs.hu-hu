---
title: Az Azure Time Series Insights (előzetes verzió) adatok lekérdezése |} A Microsoft Docs
description: Az Azure Time Series Insights (előzetes verzió) adatok lekérdezése
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/04/2018
ms.openlocfilehash: 289d047a68fabb73737c3528e1322a1d08a38741
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52888154"
---
# <a name="data-querying"></a>Adatok lekérdezése

Az Azure Time Series Insights (TSI) lehetővé teszi, hogy az adatok lekérdezéséhez az események, és felület nyilvános API-kon keresztül a környezetben tárolt metaadatok. Ezen API-k ugyanazok, mint a [TSI explorer](./time-series-insights-update-explorer.md).

Az Azure TSI által biztosított három elsődleges API kategóriába sorolhatók:

* Környezet API-k engedélyezése a TSI-környezet maga lekérdezése, például környezetek listája hívó férhet hozzá, környezet metaadatokat, és így tovább.

* Time series modell-lekérdezés (TSM-Q) API-k engedélyezése létrehozása, olvasása, frissítése és törlés műveletekhez az idősorozat-modell környezet részében tárolt metaadatok. Például a példányok típusok, hierarchiák, stb.

* Time series lekérdezés (TSQ) API-k engedélyezése az események adatok lekérése adatforrás-szolgáltatója rögzíti, vagy átalakítása, Összevonás és idősorozat-adatok számításokat hajthat végre műveleteket hajthat végre.

A [Time Series kifejezésnyelveket](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx) (TSX) egy hatékony, a negyedik, kategória. Time Series modellek (TSM) használ a speciális számítási összeállítás engedélyezése.

## <a name="azure-time-series-insights-core-apis"></a>Az Azure Time Series Insights core API-k

Az alábbiakban alapvető fontosságú API-k is nyújtunk támogatást.

![tsq][1]

### <a name="the-environment-apis"></a>A környezet API-k

Környezet API-kat a következők:

* [KÖRNYEZET API első](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environments-api): a környezetek listáját adja vissza, hogy a hívó jogosult-e el.
* [KÖRNYEZET rendelkezésre ÁLLÁSI API első](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environment-availability-api): az esemény időbélyegzője keresztül eloszlás események száma `$ts`. Ez az API segítségével határozza meg, ha más eseményeket az időbélyeg visszaadó események száma, ha az létezik.
* [ELSŐ esemény SÉMA API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-event-schema-api): az esemény séma-metaadatok meg egy adott keresés adja vissza. Ez az API segít az összes metaadat/tulajdonság meg a megadott keresés a sémában érhető el.

### <a name="time-series-model-query-tsm-q-apis"></a>Time Series modell-lekérdezés (TSM-Q) API-k

Time Series modell-Query API-kat a következők:

* [MODELL beállítások API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api): lehetővé teszi a első és az alapértelmezett típusától és a modell neve, a környezet javítására.
* [TÍPUSOK API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api): a Time Series típusok és azok kapcsolódó változók lehetővé teszi a CRUD.
* [HIERARCHIÁK API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api): CRUD lehetővé teszi, hogy a Time Series hierarchiák és azok kapcsolódó mező elérési utak.
* [PÉLDÁNYOK API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api): CRUD lehetővé teszi, hogy az idő sorozat példányai és a kapcsolódó szolgáltatáspéldány-mezők.

### <a name="the-time-series-query-tsq-apis"></a>A Time Series lekérdezés (TSQ) API-k

Time Series lekérdezési API-kat a következők:

* [GET-események API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-events-api): A getEvents API lehetővé teszi a lekérdezés és a TSI-események adatainak lekéréséhez az Azure TSI az adatforrás-szolgáltatója a rögzített.

* [ADATSOROZAT API első](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-series-api): a modell vagy megadott soron belüli definiálása lehetővé teszi, hogy lekérdezés és a változók használata a vezetéken rögzített adatok nyújtotta előnyöket kihasználva rögzített események Azure TSI-adatainak beolvasása.

    >[!NOTE]
    > Az összesítési záradék a getSeries, figyelmen kívül hagyja, akkor is, ha a modellben megadott változók része vagy beágyazott megadva.

  A sorozat első API adja vissza egy TSV (sorozat időérték TSI használja a lekérdezés eredményének JSON formátumban) minden egyes változójánál minden egyes időtartam alatt, a megadott alapján **Time Series azonosító** , valamint a megadott változókat.

* [ÖSSZESÍTETT sorozat API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#aggregate-series-api): lehetővé teszi, hogy a lekérdezés és a TSI-mintavétel és összesítés által rögzített események adatainak lekéréséhez rögzített adatokat.

  Az összesített sorozat API minden egyes időtartam alatt, a megadott alapján adja vissza egy TSV minden egyes változójánál **Time Series azonosító** , valamint a megadott változókat. Az API aggregateSeries csökkentése TSM tárolt vagy beágyazott összesítés vagy mintát adatokhoz megadott változók használatával éri el.

  Támogatott összesített típusok: `Min`, `Max`, `Sum`, `Count`, `Average`

## <a name="next-steps"></a>További lépések

Olvassa el a [Storage (előzetes verzió) az Azure TSI a bejövő és kimenő](./time-series-insights-update-storage-ingress.md).

További információ [adatmodellezés](./time-series-insights-update-tsm.md).

További információ [ajánlott eljárásokat, amikor kiválasztja a Time Series ID](./time-series-insights-update-how-to-id.md).

<!-- Images -->
[1]: media/v2-update-tsq/tsq.png
