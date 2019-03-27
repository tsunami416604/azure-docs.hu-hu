---
title: Az Anomáliadetektálási detector használatával API használata az idősoros adatokat az
description: Útmutató a rendellenességek észlelése az adatok kötegelt, vagy a streamelt adatokon.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 63ede8fe90d5c19c2473ffb315bf6096599ffb9c
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58473232"
---
# <a name="how-to-use-the-anomaly-detector-api-on-your-time-series-data"></a>Útmutató: Az Anomáliadetektálási detector használatával API használata az idősorozat-adatok  

A [Anomáliadetektálási detector használatával API](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) két módszerrel rendellenesség-észlelést biztosít. Vagy észlelheti a rendellenességeket, a batch az idők során sorozat, vagy az adatok generálja a legújabb adatpont anomáliadetektálási állapotának észlelése. A modell észlelés határai együtt az egyes adatpontok várt értéket, és a felső és alsó anomáliadetektálási anomáliadetektálási eredményeket adja vissza. Ezeket az értékeket segítségével megjelenítheti a normál értékeket, és a rendellenességeket az adatok körét.

## <a name="anomaly-detection-modes"></a>Rendellenességek észlelése módok 

Az Anomáliadetektálási detector használatával API biztosítja az észlelési mód: batch- és streamelés céljából.

> [!NOTE]
> A következő kérés URL-címeket kell értékkel egyesítve jön létre a megfelelő végpontra az előfizetéshez. Például:`https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/entire/detect`


### <a name="batch-detection"></a>Batch-észlelés

Adatpontok kötegelt egész rendellenességek észlelése egy adott időtartományt keresztül, használja a következő kérés URI-t az idősoros adatokat: 

`/timeseries/entire/detect`. 

Küld egyszerre az idősoros adatokat, az API-t fogja létrehozni a teljes sorozatot használó modell, és elemezheti az egyes adatpontok vele.  

### <a name="streaming-detection"></a>Streamelési észlelése

Folyamatosan észlelje a rendellenességeket a streamelt adatokat, használja a következő kérés URI azonosítója a legújabb adatok ponttal: 

`/timeseries/last/detect'`. 

Küldésével új adatpontok szerint hoz létre, az adatok valós időben figyelheti. Az adatpontokhoz küld egy modellt hoz létre, és az API határozza meg, ha az idősor a legutóbbi időpontra-e egy rendellenességet.

## <a name="adjusting-lower-and-upper-anomaly-detection-boundaries"></a>Alsó és felső anomáliadetektálási észlelés határai beállítása

Alapértelmezés szerint az alsó és felső határai rendellenesség-észlelést kiszámítása használatával `expectedValue`, `upperMargin`, és `lowerMargin`. Ha másik határok van szüksége, javasoljuk, hogy alkalmazása egy `marginScale` való `upperMargin` vagy `lowerMargin`. A határok módon számítjuk ki:

|Határcsoport  |Számítás  |
|---------|---------|
|`upperBoundary` | `expectedValue + (100 - marginScale) * upperMargin`        |
|`lowerBoundary` | `expectedValue - (100 - marginScale) * lowerMargin`        |

Az alábbi példák az Anomáliadetektálási detector használatával API eredmény megjelenítése a különböző övvisszahúzónál.

### <a name="example-with-sensitivity-at-99"></a>Példa: 99 érzékenységgel

![Alapértelmezett érzékenység](../media/sensitivity_99.png)

### <a name="example-with-sensitivity-at-95"></a>Példa: 95 érzékenységgel

![99 érzékenység](../media/sensitivity_95.png)

### <a name="example-with-sensitivity-at-85"></a>Példa: 85 érzékenységgel

![85 érzékenység](../media/sensitivity_85.png)

## <a name="next-steps"></a>További lépések

* [Mi az az Anomáliadetektálási detector használatával API-t?](../overview.md)
* [Rövid útmutató: Rendellenességek észlelése az idősoros adatokat Anomáliadetektálási detector használatával REST API használatával](../quickstarts/detect-data-anomalies-csharp.md)