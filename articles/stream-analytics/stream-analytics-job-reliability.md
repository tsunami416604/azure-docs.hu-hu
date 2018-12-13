---
title: Az Azure Stream Analytics-feladatok szolgáltatáskiesések elkerülése
description: A cikk útmutatást a Stream Analytics-feladatok frissítése rugalmas gondoskodik.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 7375fb2763ad83e049b1ef30a623f164e059a792
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090805"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Garantáljuk a Stream Analytics-feladatok megbízhatósága szolgáltatás frissítései során

Folyamatban van egy teljes körűen felügyelt szolgáltatás részét képezi a funkció be a szolgáltatás új funkciók és fejlesztések gyors ütemben. Ennek eredményeképpen a Stream Analytics üzembe helyezése heti (vagy gyakoribb) szolgáltatás frissítése is rendelkezhet. Függetlenül attól, hogy mekkora tesztelés történik van még egy kockázata, hogy egy meglévő, futó feladat bevezetésével egy hiba miatt megszakadhat. Ügyfelek, akik kritikus streamelési feldolgozási feladatok futtatása az említett kockázatok kell el kell kerülni. A felhasználók használhatják a kockázat csökkentéséhez mechanizmusra az Azure **[párosított régióra](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** modell. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Hajtsa végre az Azure párosított régiói hogyan képesek teljesíteni a veszélyt?

Stream Analytics biztosítja a feladatok a társított két régió bármelyikén külön tételekben frissülnek. Ennek eredményeképpen nincs elegendő idő kimaradást között használhatatlanná tévő lehetséges hibák azonosítása és javítása érdekében ezeket a frissítéseket.

_Közép-India kivételével_ (amelynek párosított régióra, Dél-India, nem rendelkezik Stream Analytics jelenlét), a Stream Analytics egy frissítés telepítése a fel nem egy időben két régió bármelyikén készletét. Több régióban üzemelő **ugyanabba a csoportba** fordulhat elő, **egyszerre**.

A cikk a **[rendelkezésre állás és a társított két régió bármelyikén](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** rendelkezik a legtöbb naprakész információt, amelyre vannak párosított régiók.

Ügyfelek javasolja, hogy mindkét régió bármelyikén azonos feladatok üzembe helyezése. Stream Analytics belső figyelési képességek mellett ügyfelek is javasolja, hogy a feladatok monitorozásához, mintha **mindkét** éles feladatok. Ha egy szünet a Stream Analytics szolgáltatás frissítése okozhatják, eszkalálása megfelelően, és átadja a feladatokat a kifogástalan állapotú feladat kimeneti alsóbb rétegbeli fogyasztóval. Eszkalációs támogatásához megakadályozza, hogy a párosított régióban az új központi telepítési vonatkozzon, és a párosított feladatok biztonságának fenntartása.

## <a name="next-steps"></a>További lépések

* [Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Stream Analytics használatának első lépései](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics-feladatok méretezése](stream-analytics-scale-jobs.md)
* [Stream Analytics lekérdezési nyelv leírása](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Stream Analytics felügyeleti REST API-referencia](https://msdn.microsoft.com/library/azure/dn835031.aspx)
