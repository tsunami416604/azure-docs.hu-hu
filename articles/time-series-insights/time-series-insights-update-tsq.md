---
title: Adatok lekérdezése előzetes verzióban – Azure Time Series Insights | Microsoft dokumentumok
description: Adatlekérdezési fogalmak és a HTTP REST API áttekintése az Azure Time Series Insights előzetes verziójában.
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/25/2020
ms.custom: seodec18
ms.openlocfilehash: 23094ec71dac5780def10e16b90de0b818ef3c68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284891"
---
# <a name="data-querying-in-azure-time-series-insights-preview"></a>Adatlekérdezés az Azure Time Series Insights előzetes verziójában

Az Azure Time Series Insights lehetővé teszi a környezetben tárolt események és metaadatok lekérdezését a nyilvános felületi API-kon keresztül. Ezeket az API-kat a [Time Series Insights Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer)is használja.

A Time Series Insights ban három elsődleges API-kategória érhető el:

* **Környezeti API-k:** Ezek az API-k lehetővé teszik a time series insights-környezet lekérdezéseit. Ezek segítségével összegyűjtheti azon környezetek listáját, amelyekhez a hívó hozzáfér, valamint a környezeti metaadatokat.
* **Time Series Model-Query (TSM-Q) API-k**: Engedélyezi a létrehozása, olvasása, frissítése és törlése (CRUD) műveletek metaadatok at a time series modell a környezetben. Ezek a példányok, típusok és hierarchiák elérésére és szerkesztésére használhatók.
* **Time Series Query (TSQ) API-k**: Lehetővé teszi a telemetriai adatok lekérését a forrásszolgáltatótól rögzített adatokalapján, és lehetővé teszi az adatok teljesítményszámításokat és összesítéseket speciális skaláris és összesítő függvények használatával.

A Time Series Insights gazdag karakterlánc-alapú kifejezésnyelvet, [a Time Series Expression (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)kifejezést használja a számítások kifejezéséhez.

## <a name="azure-time-series-insights-core-apis"></a>Az Azure Time Series Insights alapvető API-k

A következő alapvető API-k támogatottak.

[![Idősorozat-lekérdezés – áttekintés](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>Környezeti API-k

* [Környezetek beszereznie API:](https://docs.microsoft.com/rest/api/time-series-insights/management/environments/get)Azon környezetek listáját adja vissza, amelyekhez a hívó jogosult.
* [Környezetek rendelkezésre állása API betöltése:](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability)Az `$ts`eseményszám eloszlását adja eredményül az esemény időbélyegén keresztül. Ez az API segít meghatározni, hogy vannak-e események a környezetben azáltal, hogy visszaadja az események számát időintervallumok, ha vannak ilyenek.
* [Eseményséma API bekésése:](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema)Egy adott keresési tartomány eseményséma metaadatait adja eredményül vissza. Ez az API segít beolvasni az összes metaadatot és tulajdonságot, amely elérhető a sémában az adott keresési tartományban.

## <a name="time-series-model-query-tsm-q-apis"></a>Idősorozat-modell-lekérdezés (TSM-Q) API-k

A legtöbb ilyen API támogatja a kötegelt végrehajtási műveletet, hogy több Time Series Model entitáson engedélyezze a kötegelt CRUD-műveleteket:

* [Modellbeállítások API:](https://docs.microsoft.com/rest/api/time-series-insights/preview#model-settings-api)Engedélyezi a *GET* és a *PATCH* értéket az alapértelmezett típuson és a környezet modellnevén.
* [TÍPUSOK API:](https://docs.microsoft.com/rest/api/time-series-insights/preview#types-api)Engedélyezi a CRUD-t az idősorozat-típusokon és a hozzájuk tartozó változókon.
* [Hierarchiák API:](https://docs.microsoft.com/rest/api/time-series-insights/preview#hierarchies-api)Engedélyezi a CRUD-t az idősorozat-hierarchiákon és a hozzájuk tartozó mezőútvonalakon.
* [Példányok API:](https://docs.microsoft.com/rest/api/time-series-insights/preview#instances-api)Engedélyezi a CRUD-t az idősorozat-példányokon és a hozzájuk tartozó példánymezőkön. Emellett a példányok API a következő műveleteket támogatja:
  * [Keresés:](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/search)A példányattribútumok alapján lekéri az idősorozat-példányok keresésére vonatkozó találatok részleges listáját.
  * [Javaslat](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/suggest): A példányattribútumok alapján megkeresi és javasolja az idősorozat-példányok keresésére vonatkozó találatok részleges listáját.

## <a name="time-series-query-tsq-apis"></a>Idősorozat-lekérdezési (TSQ) API-k

Ezek az API-k a Time Series Insights többrétegű tárolási megoldásának mindkét tárolóján elérhetők. A lekérdezés URL-paraméterei annak a [tárolótípusnak](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters) a megadására szolgálnak, amelyen a lekérdezést végre kell hajtani:

* [Események API beolvasása:](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents)Lehetővé teszi a nyers események és a kapcsolódó esemény időbélyegek lekérdezését és lekérését, ahogy azok a forrásszolgáltatótól származó Time Series Insights-ban kerülnek rögzítésre. Ez az API lehetővé teszi a nyers események lekérését egy adott idősorozat-azonosítóhoz és keresési időtartamhoz. Ez az API támogatja a tördelést a kijelölt bemenet teljes válaszadatkészletének lekéréséhez. 

* [Sorozat API beolvasása:](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries)Lehetővé teszi a számítási értékek és a kapcsolódó eseményidőbélyegek lekérdezését és lekérését a változók által meghatározott számítások alkalmazásával a nyers eseményeken. Ezek a változók definiálhatók a Time Series modellben, vagy a lekérdezésben megadott szövegközi. Ez az API támogatja a tördelést a kijelölt bemenet teljes válaszadatkészletének lekéréséhez. 

* [Összesített sorozat API:](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)Lehetővé teszi az összesített értékek és a kapcsolódó időintervallum-időbélyegek lekérdezését és lekérését a változók által meghatározott számítások alkalmazásával nyers eseményeken. Ezek a változók definiálhatók a Time Series modellben, vagy a lekérdezésben megadott szövegközi. Ez az API támogatja a tördelést a kijelölt bemenet teljes válaszadatkészletének lekéréséhez. 
  
  Egy megadott keresési tartomány és időköz esetén ez az API egy idősorozat-azonosító változónkénti összesített válaszát adja vissza. A válaszadatkészletintervallumok számát a program a korszaktick-ek (a Unix-korszak óta eltelt ezredmásodpercek száma) és a kullancsok és a lekérdezésben megadott intervallum-tartományméret alapján történő felosztásával számítja ki.

  A válaszkészletben visszaadott időbélyegek a bal oldali intervallum határokat, nem a mintavételezett események az intervallumból. 

## <a name="next-steps"></a>További lépések

- További információ a [Time Series modellben](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-tsm)definiálható különböző változókról.
- További információ [aRról,](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer)hogyan kérdezheti le az adatokat a Time Series Insights Explorer ből.
