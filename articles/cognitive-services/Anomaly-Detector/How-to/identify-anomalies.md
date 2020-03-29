---
title: Az Anomáliadetektor API használata az idősorozat-adatokon
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan észlelheti az adatokak anomáliákat kötegként vagy streamelési adatokon.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: aahi
ms.openlocfilehash: ca93de71f64efaf21c78b37b9c9aee193d13b28d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "71840222"
---
# <a name="how-to-use-the-anomaly-detector-api-on-your-time-series-data"></a>Útmutató: Az anomáliadetektor API használata az idősorozat-adatokon  

Az [anomáliadetektor API](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) két anomáliadetektálási módszert biztosít. Észlelheti az anomáliákat kötegként az idősorozatban, vagy az adatok a legújabb adatpont anomáliaállapotának észlelésével. Az észlelési modell anomáliaeredményeket ad vissza az egyes adatpont várható értékével együtt, valamint a felső és alsó anomáliadetektálási határokat. Ezeket az értékeket használhatja a normál értékek tartományának és az adatok anomáliáinak megjelenítéséhez.

## <a name="anomaly-detection-modes"></a>Anomáliadetektálási módok 

Az Anomáliadetektor API észlelési módokat biztosít: kötegelt és streamelési.

> [!NOTE]
> A következő kérelem URL-címeket kell kombinálni a megfelelő végpontot az előfizetéshez. Például:`https://<your-custom-subdomain>.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/entire/detect`


### <a name="batch-detection"></a>Kötegfelismerés

Ha egy adott időtartományon keresztül szeretné észlelni az adatpontok kötegében lévő rendellenességeket, használja a következő kérelem URI-t az idősorozat-adatokkal: 

`/timeseries/entire/detect`. 

Az idősorozat-adatok egyidejű elküldésével az API létrehoz egy modellt a teljes sorozat használatával, és elemzi az egyes adatsorokat.  

### <a name="streaming-detection"></a>Folyamatos átvitel észlelése

A streamelési adatok anomáliáinak folyamatos észleléséhez használja a következő kérelem URI-t a legújabb adatponttal: 

`/timeseries/last/detect'`. 

Ha új adatpontokat küld létrehozásukkor, valós időben figyelheti az adatokat. A rendszer egy modellt hoz létre az elküldött adatpontokkal, és az API meghatározza, hogy az idősorozat legújabb pontja anomália-e.

## <a name="adjusting-lower-and-upper-anomaly-detection-boundaries"></a>Az alsó és felső anomáliadetektálási határok beállítása

Alapértelmezés szerint az anomáliadetektálás felső `expectedValue`és `upperMargin`alsó `lowerMargin`határait a program a , a és a használatával számítja ki. Ha különböző határokat szeretne, javasoljuk, `marginScale` `upperMargin` hogy `lowerMargin`a vagyoni kívánta. A határokat a következőképpen kell kiszámítani:

|Határ  |Számítás  |
|---------|---------|
|`upperBoundary` | `expectedValue + (100 - marginScale) * upperMargin`        |
|`lowerBoundary` | `expectedValue - (100 - marginScale) * lowerMargin`        |

Az alábbi példák egy anomáliadetektor API-eredményt mutatnak be különböző érzékenységek esetén.

### <a name="example-with-sensitivity-at-99"></a>Példa érzékenységgel 99-nél

![Alapértelmezett érzékenység](../media/sensitivity_99.png)

### <a name="example-with-sensitivity-at-95"></a>Példa érzékenységgel 95

![99 Érzékenység](../media/sensitivity_95.png)

### <a name="example-with-sensitivity-at-85"></a>Példa 85-ös érzékenységgel

![85 Érzékenység](../media/sensitivity_85.png)

## <a name="next-steps"></a>Következő lépések

* [Mi az az Anomaly Detector API?](../overview.md)
* [Rövid útmutató: Az anomáliadetektor REST API-jával észleli az idősorozat-adatok anomáliáinak anomáliáit](../quickstarts/detect-data-anomalies-csharp.md)
