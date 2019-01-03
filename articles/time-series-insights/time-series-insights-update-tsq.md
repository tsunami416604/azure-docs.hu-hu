---
title: Az Azure Time Series Insights előzetes verziója adatok lekérdezése |} A Microsoft Docs
description: Az Azure Time Series Insights előzetes verziója az adatok lekérdezéséhez.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 7885473d029556e10663675a9886c7ea3b9c709c
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53555421"
---
# <a name="data-querying"></a>Adatok lekérdezése

Az Azure Time Series Insights előzetes verziója lehetővé teszi, hogy az adatok lekérdezéséhez az események, és felület nyilvános API-kon keresztül a környezetben tárolt metaadatok. Ezen API-k is használatban vannak a [Time Series Insights előzetes verziója explorer](./time-series-insights-update-explorer.md).

A Time Series Insights három elsődleges API-kategóriák érhetők el:

* **Környezet API-k**: A Time Series Insights-környezet maga lekérdezéseket tesz lehetővé. Lekérdezések példák hozzáfér a hívó környezetek és a környezet metaadatok listája.

* **Time Series modell-lekérdezés (TSM-Q) API-k**: Lehetővé teszi, hogy létrehozása, olvasása, frissítése és törlés műveletekhez az idősorozat-modell környezet részében tárolt metaadatok. Példák példányok, típusok és hierarchiák.

* **Time Series (TSQ) API-k lekérdezése**: Az adatforrás-szolgáltatója a bejegyzett lehetővé teszi, hogy az események adatok lekéréséhez. Ezen API-k átalakítása, Összevonás és idősorozat-adatok számításokat hajthat végre műveleteket hajthat végre.

A [Time Series kifejezés (TSX) nyelvi](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx) egy hatékony negyedik kategória. Time Series modelleket használ a speciális számítási összeállítás engedélyezése.

## <a name="azure-time-series-insights-preview-core-apis"></a>Az Azure Time Series Insights előzetes verziója core API-k

A következő fő API-k használata támogatott.

![tsq][1]

### <a name="environment-apis"></a>Környezet API-k

A következő környezet API-k érhetők el:

* [API-környezet](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environments-api): A környezetek listáját adja vissza, hogy a hívó jogosult-e el.
* [Környezet rendelkezésre állás API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environment-availability-api): Az esemény időbélyegzője keresztül eloszlás események száma `$ts`. Ez az API segít meghatározni, hogy van-e más eseményeket történő küldés időbélyegzője legyen a visszatérő száma, események, ha vannak ilyenek.
* [Eseményséma API első](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-event-schema-api): Az esemény séma-metaadatok meg egy adott keresés adja vissza. Ez az API segít a metaadatok és a tulajdonságok a séma meg a megadott keresés érhetők el.

### <a name="time-series-model-query-tsm-q-apis"></a>Time Series modell-lekérdezés (TSM-Q) API-k

A következő alkalommal sorozat modell-lekérdezés API-k érhetők el:

* [Beállítások API minta](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api): Lehetővé teszi, hogy kaphat, és az alapértelmezett típusától és a modell neve, a környezet javítására.
* [API-típusok](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api): A Time Series-típusok és azok kapcsolódó változók lehetővé teszi a CRUD-MŰVELETEKKEL.
* [Hierarchiák API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api): A Time Series-hierarchiát és azok kapcsolódó mező elérési utak lehetővé teszi a CRUD-MŰVELETEKKEL.
* [API-példányok](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api): A Time Series-példányok és a társított példány mezők lehetővé teszi a CRUD-MŰVELETEKKEL.

### <a name="time-series-query-tsq-apis"></a>Time Series lekérdezés (TSQ) API-k

A következő alkalommal sorozat lekérdezési API-k érhetők el:

* [Események API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-events-api): Lehetővé teszi a lekérdezés és a Time Series Insights-események adatainak lekéréséhez, már rögzített a Time Series Insightsban az az adatforrás-szolgáltatója.

* [Adatsorozat API első](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-series-api): Lehetővé teszi, hogy lekérdezést, illetve a Time Series Insights adatait a rögzített események adatok használatával rögzíti a keresztülhaladnak a hálózaton. A visszaadott értékek a változókat, amelyek a modellben meghatározott vagy a beágyazott megadott alapulnak.

    >[!NOTE]
    > A rendszer figyelmen kívül hagyja az összesítési záradék, még akkor is, ha a modellben megadott vagy beágyazott megadott.

  A sorozat első API minden egyes változójánál Time Series értéket ad vissza minden egyes időtartam alatt. Egy időértéket sorozat formátuma a Time Series Insights használ a kimeneti JSON-lekérdezésből. A visszaadott értékek a Time Series-azonosító és a egy megadott változók alapulnak.

* [Adatsorozat API összesített](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#aggregate-series-api): Lehetővé teszi, hogy lekérdezést, illetve a mintavételi és összesítés által rögzített események a Time Series Insights adatait rögzíti az adatokat.

  A sorozat összesített API minden egyes változójánál Time Series értéket ad vissza minden egyes időtartam alatt. Az értékeket a Time Series-azonosító és a egy megadott változók alapulnak. A sorozat összesített API csökkentése az Idősorozat-modellben tárolva, vagy a beágyazott összesítés vagy mintát adatokhoz megadott változók használatával éri el.

  Támogatott összesített típusok: `Min`, `Max`, `Sum`, `Count`, `Average`

## <a name="next-steps"></a>További lépések

További információ:

- [Az Azure Time Series Insights előzetes verziója storage és a bejövő forgalom](./time-series-insights-update-storage-ingress.md)
- [Adatmodellezés](./time-series-insights-update-tsm.md)
- [Ajánlott eljárások a Time Series ID kiválasztásakor](./time-series-insights-update-how-to-id.md)

<!-- Images -->
[1]: media/v2-update-tsq/tsq.png
