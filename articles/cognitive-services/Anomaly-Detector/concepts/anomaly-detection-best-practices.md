---
title: Ajánlott eljárások az Anomáliadetektálási detector használatával API használatához
description: Bevált gyakorlatok megismeréséhez, amikor az érzékelő Anomáliadetektálás API-val rendellenességek észlelése.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 467ac4e475a1c23e25b62c76cfbc959e7ed49465
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484034"
---
# <a name="best-practices-for-using-the-anomaly-detector-api"></a>Az Anomáliadetektálási detector használatával API használatának ajánlott eljárásai

Az Anomáliadetektálási detector használatával API egy olyan állapot nélküli anomáliadetektálási szolgáltatás. A pontosság és az eredményeket teljesítményét is negatív hatással lehet:

* Hogyan készül, az idősorozat-adatok.
* Az Anomáliadetektálási detector használatával API paraméterek használt.
* Az API-kérelem szereplő adatpontok száma. 

Ez a cikk segítségével az API-t a legjobb eredmények elérése érdekében az adatok beolvasása a bevált gyakorlatok megismeréséhez. 

## <a name="data-preparation"></a>Adatok előkészítése

Az Anomáliadetektálási detector használatával API elfogadja a time series adatok JSON-kérelem objektum formázva. Időbeli adatsorok lehet egymást követő sorrendben idővel bármely numerikus adatokkal. Elküldheti az idősoros adatokat, a windows az Anomáliadetektálási detector használatával API-végpont az API-k teljesítményének javítása érdekében. Elküldheti az adatpontok minimális száma 12, a maximális pedig 8640 pontokat. 

Az Anomáliadetektálási detector használatával API-nak elküldött adatpontok rendelkeznie kell az egyezményes világidő (UTC) érvényes időbélyeget, és numerikus érték. 

```json
{
    "granularity": "daily",
    "series": [
      {
        "timestamp": "2018-03-01T00:00:00Z",
        "value": 32858923
      },
      {
        "timestamp": "2018-03-02T00:00:00Z",
        "value": 29615278
      },
    ]
}
```

### <a name="missing-data-points"></a>Hiányzó adatpontokat

Hiányzó adatpontokat gyakoriak a egyenlően elosztott idő sorozat adatkészletek, különösen kiépítettektől egy vékony granularitással (egy kis mintavételi időköz. For example, az adatok mintavételezése néhány percenként). Hiányzik a kevesebb mint 10 %-a várt számú pontot az adatok nem is van negatív hatással lehet a észlelésének eredménye. Vegye figyelembe, hogy az adatok, például egy korábbi időszakban, lineáris interpolációs vagy mozgóátlaga adatpontok helyettesítse a jellemzők alapján hiányosságok beküldésekor.

### <a name="aggregate-distributed-data"></a>Összesített elosztott adatok

Az Anomáliadetektálási detector használatával API optimális egy egyenletesen idősorozat. Véletlenszerűen oszlanak meg az adatokat, ha meg kell összesíteni idő, például a percalapú, óránkénti vagy napi egység által például.

## <a name="anomaly-detection-on-data-with-seasonal-patterns"></a>Rendellenességek észlelése az adatok szezonális minták

Ha tudja, hogy rendelkezik-e az idősoros adatokat szezonális mintát (egyet, amely rendszeres időközönként történik), a pontosság és API-válaszidő is javítható. 

Adjon meg egy `period` amikor hozhat létre a JSON-kérelmi akár 50 %-kal csökkentheti rendellenességek észlelése késés. A `period` az egész szám, amely meghatározza, hogy hány nagyjából adatpontok a time series ismételje meg a mintát vesz igénybe. Például egy idősorozat-egy adatpont naponta lenne egy `period` , `7`, és a egy ponttal (ugyanilyen mintájú heti) óránként egy idősorozat lenne egy `period` , `7*24`. Ha biztos benne, hogy az adatok, minták, nem kell megadni ezt a paramétert.

A legjobb eredmények érdekében adja meg a 4 `period`a adatpont, valamint egy naplóban. Például óránkénti adatok heti mintával a fent leírt módon kell biztosítania a kérelem törzsében szereplő 673 adatpontok (`7 * 24 * 4 + 1`).

### <a name="sampling-data-for-real-time-monitoring"></a>Az adatok mintavételekor valós idejű figyelés

Ha rövid időközönként (például másodperceken vagy perceken) feladata a streamelési adatok küldése a ajánlott száma az adatpontok haladhatja meg az Anomáliadetektálási detector használatával API engedélyezett (8640 adatpont). Ha az adatok egy stabil szezonális minta azt mutatja, fontolja meg a mintát az idősoros adatokat küldő időközönként nagyobb, mint óra. Ezzel a módszerrel az adatok mintavételezésének észrevehetően is javítja az API válaszideje. 

## <a name="next-steps"></a>További lépések

* [Mi az az Anomáliadetektálási detector használatával API-t?](../overview.md)
* [Rövid útmutató: Rendellenességek észlelése az idősoros adatokat Anomáliadetektálási detector használatával REST API használatával](../quickstarts/detect-data-anomalies-csharp.md)
