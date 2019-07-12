---
title: Az Azure Stream Analytics-feladatok szolgáltatáskiesések elkerülése
description: A cikk útmutatást a Stream Analytics-feladatok frissítése rugalmas gondoskodik.
services: stream-analytics
author: jseb225
ms.author: sidram
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: e38f8a923daa210d8aa5b56631e5f8157d4b3f70
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620882"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Garantáljuk a Stream Analytics-feladatok megbízhatósága szolgáltatás frissítései során

Folyamatban van egy teljes körűen felügyelt szolgáltatás részét képezi a funkció be a szolgáltatás új funkciók és fejlesztések gyors ütemben. Ennek eredményeképpen a Stream Analytics üzembe helyezése heti (vagy gyakoribb) szolgáltatás frissítése is rendelkezhet. Függetlenül attól, hogy mekkora tesztelés történik van még egy kockázata, hogy egy meglévő, futó feladat bevezetésével egy hiba miatt megszakadhat. Futtatásakor működés szempontjából kritikus feladatok, ezek a kockázatok el kell kerülni kell. Csökkentheti a kockázat, az alábbi Azure **[párosított régióra](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** modell. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Hajtsa végre az Azure párosított régiói hogyan képesek teljesíteni a veszélyt?

Stream Analytics biztosítja a feladatok a társított két régió bármelyikén külön tételekben frissülnek. Ennek eredményeképpen nincs elegendő idő kimaradást között kapcsolatos lehetséges problémák azonosítása és javítása érdekében ezeket a frissítéseket.

_Közép-India kivételével_ (amelynek párosított régióra, Dél-India, nem rendelkezik Stream Analytics jelenlét), a Stream Analytics egy frissítés telepítése a fel nem egy időben két régió bármelyikén készletét. Több régióban üzemelő **ugyanabba a csoportba** fordulhat elő, **egyszerre**.

A cikk a **[rendelkezésre állás és a társított két régió bármelyikén](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** rendelkezik a legtöbb naprakész információt, amelyre vannak párosított régiók.

Javasoljuk, hogy mindkét régió bármelyikén azonos feladatok üzembe helyezése. Meg kell majd [ezek a feladatok monitorozásához](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) , értesítést kaphat, amikor valami váratlan történik. Ha ezek egyikét feladatok építhet be egy [hibás állapotban](https://docs.microsoft.com/azure/stream-analytics/job-states) egy Stream Analytics szolgáltatás frissítése után lépjen kapcsolatba az ügyfélszolgálattal a legfelső szintű hiba okának meghatározásához. Meg is átadja a feladatokat a kifogástalan állapotú feladat kimeneti alsóbb rétegbeli fogyasztóval.

## <a name="next-steps"></a>További lépések

* [Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Stream Analytics használatának első lépései](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics-feladatok méretezése](stream-analytics-scale-jobs.md)
* [Stream Analytics lekérdezési nyelv leírása](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Stream Analytics felügyeleti REST API-referencia](https://msdn.microsoft.com/library/azure/dn835031.aspx)
