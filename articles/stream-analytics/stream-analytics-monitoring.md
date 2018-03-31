---
title: Understanding Stream Analytics-feladat figyelése |} Microsoft Docs
description: Figyelés a Stream Analytics-feladat ismertetése
keywords: lekérdezés-figyelő
services: stream-analytics
documentationcenter: ''
author: jseb225
manager: ryanw
ms.assetid: 5f5cc00f-4a7b-491e-89e1-dbafea46d399
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeanb
ms.openlocfilehash: cbfbc653deccbd215a404479100ee5cae860cb3f
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>A Stream Analytics-feladat megfigyelés és a lekérdezések figyelése

## <a name="introduction-the-monitor-page"></a>Bemutató: A figyelő lapja
Az Azure portál mindkét surface fontos teljesítménymutatókat, és a lekérdezés és a feladat teljesítmény hibaelhárítása használható. A metrikák megtekintéséhez keresse meg a Stream Analytics-feladat metrikáját szükség, és megtekintheti a **figyelés** szakaszban Áttekintés lap.  

![Figyelési hivatkozás](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

Az ablak módon jelenik meg:

![Figyelési feladat irányítópult](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>A Stream Analytics elérhető metrikák
| Metrika                 | Meghatározás                               |
| ---------------------- | ---------------------------------------- |
| SU százalékos kihasználtsága       | A folyamatos átviteli egység (ek) használata az egy feladathoz hozzárendelt a skála lapot a feladat. Érje ezen mutató 80 %-át, vagy a fenti nincs nagy valószínűséggel, hogy az esemény feldolgozása később, vagy leállt, így a folyamat. |
| Bemeneti események           | A Stream Analytics-feladat, az események által fogadott adatok mennyisége. Ennek segítségével ellenőrizze, hogy a bemeneti forrás küldött események. |
| Kimeneti események          | A kimeneti cél események száma a Stream Analytics-feladat által küldött adatok mennyisége. |
| Soron események    | Lettek dobva, vagy egy módosított Timestamp értéket, az esemény rendelési házirend alapján megadott sorrendje nem fogadott események száma. Ez is negatív hatással lehet a soron of tűrési beállítás konfigurációját. |
| Adatkonverziós hibák | A Stream Analytics-feladat felmerült adatok átalakítás hibák száma. |
| Futásidejű hibák         | A lekérdezés feldolgozása (kivéve a választásával dolgozhat fel, az események vagy outputing eredmények során talált hibákat) kapcsolatos hibák száma összesen |
| Késedelmes bemeneti események      | A forrás, amely vagy el lett dobva vagy időbélyegzőik későn érkező események száma be van állítva, az esemény rendelési házirend konfiguráció késő érkezés tűrési beállítás alapján. |
| Függvénykérések      | Az Azure Machine Learning-függvény (ha van ilyen) hívások száma. |
| Sikertelen függvénykérések | Sikertelen Azure Machine Learning függvényhívások (ha van ilyen) száma. |
| Függvényesemények        | (Ha van ilyen) az Azure Machine Learning-függvény küldött események száma. |
| Bemeneti esemény bájtokban      | A Stream Analytics-feladat, bájtban által fogadott adatok mennyisége. Ennek segítségével ellenőrizze, hogy a bemeneti forrás küldött események. |


## <a name="customizing-monitoring-in-the-azure-portal"></a>Figyelés testreszabása az Azure-portálon
Módosíthatja a típusú diagramra, a feltüntetett, metrikákat és időtartománynak a diagram szerkesztése lehetőséget beállításaiban. További információkért lásd: [hogyan testreszabása figyelési](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

  ![Lekérdezési idő figyelése diagramhoz](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>Legújabb kimeneti
Figyelheti a feladat egy másik érdekes adatpont az az idő az utolsó kimenet, a – Áttekintés lapon látható.
Ezúttal az alkalmazás idő (azaz az időt az esemény adatokból időbélyeg) a feladat a legújabb kimenet.

## <a name="get-help"></a>Segítség kérése
További támogatásért keresse fel az [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

