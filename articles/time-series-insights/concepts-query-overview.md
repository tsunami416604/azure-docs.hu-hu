---
title: Adatlekérdezés az előzetes verzióban – Azure Time Series Insights | Microsoft Docs
description: Az Adatlekérdezési fogalmak és a HTTP-REST API áttekintése Azure Time Series Insights előzetes verzióban.
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/25/2020
ms.custom: seodec18
ms.openlocfilehash: b6da701081f13055aee3dc40eb3712f2ce2aec60
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86049953"
---
# <a name="data-querying-in-azure-time-series-insights-preview"></a>Adatlekérdezés Azure Time Series Insights előzetes verzióban

Azure Time Series Insights lehetővé teszi az adatok lekérdezését a környezetben tárolt eseményeken és metaadatokon a nyilvános felületi API-kon keresztül. Ezeket az API-kat a [Time Series Insights Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer)is használja.

A Time Series Insightsban három elsődleges API-kategória érhető el:

* **Környezeti API**-k: ezek az API-k lehetővé teszik a Time Series Insights-környezet lekérdezéseit. Ezek összegyűjthetők azon környezetek listáját, amelyekhez a hívó hozzáfér és környezeti metaadatokat használ.
* **Idősorozat-modell – Query (TSM-Q) API-k**: lehetővé teszi a létrehozási, olvasási, frissítési és törlési (szifilisz) műveleteket a környezet idősorozat-modelljében tárolt metaadatokon. Ezek a példányok, típusok és hierarchiák eléréséhez és szerkesztéséhez használhatók.
* **Idősorozat-lekérdezés (TSQ) API-k**: lehetővé teszi a telemetria és az események adatainak lekérését a forrás szolgáltató által rögzített módon, és lehetővé teszi a teljesítménybeli számítások és összesítések használatát az adatokhoz a speciális skaláris és összesítő függvények használatával.

A Time Series Insights egy Rich string-alapú kifejezés nyelvét, az [Idősorozat kifejezését (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)használja a számítások kifejezésére.

## <a name="azure-time-series-insights-core-apis"></a>Azure Time Series Insights Core API-k

A következő alapvető API-k támogatottak.

[![Idősorozat-lekérdezés áttekintése](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>Környezeti API-k

* [Környezetek beolvasása API](https://docs.microsoft.com/rest/api/time-series-insights/management/environments/get): azon környezetek listáját adja vissza, amelyekhez a hívó jogosult az elérésére.
* [Környezetek rendelkezésre állási API-k beolvasása](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability): az események számának eloszlását adja vissza az esemény időbélyegén `$ts` . Ez az API segít meghatározni, hogy van-e olyan esemény a környezetben, amely az események számát időintervallumra bontva adja vissza, ha vannak ilyenek.
* [Event Schema API beolvasása](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema): egy adott keresési span esemény-séma metaadatainak beolvasása. Ez az API segít beolvasni a sémában elérhető összes metaadatot és tulajdonságot a megadott keresési tartományhoz.

## <a name="time-series-model-query-tsm-q-apis"></a>Time Series Model – Query (TSM-Q) API-k

Ezen API-k többsége támogatja a Batch-végrehajtási műveletet, hogy több idősorozatos modell entitáson engedélyezze a Batch-SZIFILISZi műveleteket:

* [Model Settings API](https://docs.microsoft.com/rest/api/time-series-insights/preview#model-settings-api): a *Get* és a *patch* engedélyezése az alapértelmezett típus és a környezet modellje számára.
* [Típusok API](https://docs.microsoft.com/rest/api/time-series-insights/preview#types-api): lehetővé teszi a szifilisz használatát az idősorozat-típusoknál és a hozzájuk társított változóknál.
* [Hierarchiák API](https://docs.microsoft.com/rest/api/time-series-insights/preview#hierarchies-api): lehetővé teszi a szifilisz használatát az idősorozat-hierarchiákban és a hozzájuk tartozó mezők elérési útjain.
* [Példányok API](https://docs.microsoft.com/rest/api/time-series-insights/preview#instances-api): lehetővé teszi a szifilisz használatát a Time Series-példányokon és a hozzájuk társított példány mezőin. A példányok API emellett a következő műveleteket is támogatja:
  * [Keresés](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/search): lekéri a találatok részleges listáját az idősorozat-példányok keresésekor a példány attribútumai alapján.
  * [Javaslat](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/suggest): megkeresi és javasolja a találatok részleges listáját az idősorozat-példányok a példány attribútumai alapján történő keresésekor.

## <a name="time-series-query-tsq-apis"></a>Idősorozat-lekérdezési (TSQ) API-k

Ezek az API-k az Time Series Insights többrétegű tárolási megoldásának mindkét áruházában elérhetők. A lekérdezés URL-paramétereinek használatával megadható, hogy a lekérdezés milyen [típusú tárolási típust](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters) hajtson végre:

* [Események beolvasása API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents): lehetővé teszi a nyers események lekérdezését és beolvasását, valamint a hozzájuk tartozó esemény-időbélyegeket, ahogy azokat a forrás szolgáltató Time Series Insights rögzíti. Ez az API lehetővé teszi a nyers események lekérését egy adott idősorozat-azonosító és keresési tartomány számára. Ez az API támogatja a tördelést a kiválasztott bemenet teljes válasz adatkészletének beolvasásához. 

* [Series API beolvasása](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries): lehetővé teszi a kiszámított értékek lekérdezését és lekérését, valamint a kapcsolódó esemény időbélyegét a változók által a nyers eseményeken definiált számítások alkalmazásával. Ezek a változók az idősorozat-modellben definiálhatók, vagy a lekérdezésben beágyazottan is elérhetők. Ez az API támogatja a tördelést a kiválasztott bemenet teljes válasz adatkészletének beolvasásához. 

* [Összesítő sorozat API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries): lehetővé teszi az összesített értékek lekérdezését és lekérését, valamint a hozzájuk tartozó intervallum-időbélyegeket a változók által a nyers eseményeken definiált számítások alkalmazásával. Ezek a változók az idősorozat-modellben definiálhatók, vagy a lekérdezésben beágyazottan is elérhetők. Ez az API támogatja a tördelést a kiválasztott bemenet teljes válasz adatkészletének beolvasásához. 
  
  A megadott keresési időszakra és intervallumra vonatkozóan az API változó időszakonként összesített választ ad vissza egy idősorozat-AZONOSÍTÓhoz. A válasz adatkészletben lévő intervallumok számának kiszámítása a következő időpontok számlálásával történik (a UNIX EPOCH óta eltelt ezredmásodpercek száma – január 1., 1970), és az osztásjelek osztása a lekérdezésben megadott intervallum-tartomány méretével.

  A válaszban visszaadott időbélyegek a bal oldali intervallumok, nem pedig az intervallumban szereplő események. 

## <a name="next-steps"></a>További lépések

- További információ az [idősorozat-modellben](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-tsm)definiálható különböző változókról.

- További információ az adatok lekérdezéséről a [Time Series Insights Explorerben](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer).
