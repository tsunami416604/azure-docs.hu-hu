---
title: A Azure Stream Analytics a feladatok figyelésének megismerése
description: Ez a cikk azt ismerteti, hogyan lehet figyelni Azure Stream Analytics feladatokat a Azure Portalban.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2018
ms.custom: seodec18
ms.openlocfilehash: 4e9f90035816269d2d41781be34d0d8080628b12
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75431649"
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>A Stream Analytics feladatok figyelésének és a lekérdezések figyelésének ismertetése

## <a name="introduction-the-monitor-page"></a>Bevezetés: a figyelő oldal
A Azure Portal felületek fő teljesítménymutatói, amelyek a lekérdezés és a feladatok teljesítményének figyelésére és hibaelhárítására használhatók. A metrikák megtekintéséhez keresse meg azt a Stream Analytics feladatot, amelyre kíváncsi, és tekintse meg a **figyelés** szakaszt az Áttekintés oldalon.  

![Stream Analytics feladatok figyelésére szolgáló hivatkozás](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

Ekkor megjelenik az ablak:

![Stream Analytics feladatok figyelésének irányítópultja](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Stream Analytics elérhető metrikák
| Metrika                 | Meghatározás                               |
| ---------------------- | ---------------------------------------- |
| Várakozó bemeneti események       | A várakozó bemeneti eseményeinek száma. A metrika nem nulla értéke azt jelenti, hogy a feladatnak nem sikerült megtartania a bejövő események számát. Ha ez az érték lassan növekszik vagy konzisztensen nem nulla, érdemes felmérni a feladatot. További információért tekintse meg a [folyamatos átviteli egységek megismerése és módosítása](stream-analytics-streaming-unit-consumption.md)című témakört. |
| Adatátalakítási hibák | Azon kimeneti események száma, amelyek nem alakíthatók át a várt kimeneti sémába. A hiba házirendje a "drop" értékre módosítható, hogy eldobják az ebben a forgatókönyvben észlelt eseményeket. |
| Korai bemeneti események       | Azok az események, amelyek esetében az alkalmazás időbélyeg-értéke korábbi, mint 5 perc. |
| Sikertelen függvénykérések | A sikertelen Azure Machine Learning függvények hívásának száma (ha van). |
| Függvényesemények        | Az Azure Machine Learning függvénynek eljuttatott események száma (ha van). |
| Függvénykérések      | A Azure Machine Learning függvényhez tartozó hívások száma (ha van). |
| Bemenet-deszerializálási hibák       | A nem deszerializálható bemeneti események száma.  |
| Bemeneti eseménybájtok      | A Stream Analyticsi feladattól fogadott adatok mennyisége bájtban kifejezve. Ezzel ellenőrizheti, hogy a rendszer elküldje-e az eseményeket a bemeneti forrásnak. |
| Bemeneti események           | A bemeneti eseményekből deszerializált rekordok száma. Ez a szám nem tartalmazza a deszerializálási hibákat eredményező bejövő eseményeket. |
| Fogadott bemeneti források       | A feladatokban fogadott üzenetek száma. Az Event hub esetében az üzenet egyetlen EventData. A blob esetében az üzenet egyetlen blob. Vegye figyelembe, hogy a bemeneti források a deszerializálás előtt számítanak. Deszerializálási hibák esetén a bemeneti források nagyobbak lehetnek a bemeneti eseményeknél. Ellenkező esetben előfordulhat, hogy a bemeneti események kisebbek vagy egyenlőek, mivel az egyes üzenetek több eseményt is tartalmazhatnak. |
| Késői bemeneti események      | A beállított késői beérkezési tolerancia időszaka után megjelenő események. További információ az [Azure stream Analytics az események rendezésével kapcsolatos megfontolásokról](stream-analytics-out-of-order-and-late-events.md) . |
| Megrendelésen kívüli események    | Azon események száma, amelyek eldobása vagy kiigazított időbélyege miatt a rendszer eldobott egy megadott időbélyegzőt, és az esemény rendezési házirendje alapján történt. Ezt befolyásolhatja a nem sorrendben beállított tűréshatárok ablakának konfigurációja. |
| Kimeneti események          | Az Stream Analyticsi feladatoknak a kimeneti célra, az események száma alapján továbbított mennyisége. |
| Futásidejű hibák         | A lekérdezések feldolgozásával kapcsolatos hibák teljes száma (kivéve az események betöltése vagy az eredmények kihelyezése során talált hibákat) |
| SU% kihasználtsága       | A feladathoz rendelt folyamatos átviteli egység (ek) kihasználtsága a feladathoz tartozó skála lapról. Ha ez a kijelző eléri a 80%-ot vagy újabbat, nagy valószínűséggel előfordulhat, hogy az események feldolgozása késleltethető vagy leáll. |
| Vízjel késleltetése       | A feladatokban lévő összes kimenet összes partícióján a maximális vízjel-késleltetés. |

Ezek a metrikák a [stream Analytics feladatok teljesítményének figyelésére](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor)használhatók. 

## <a name="customizing-monitoring-in-the-azure-portal"></a>A Azure Portal figyelésének testreszabása
A diagram beállításainak szerkesztése lehetőséggel módosíthatja a diagram, a mérőszámok és az időtartomány típusát. Részletekért lásd: [a figyelés testreszabása](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

  ![Stream Analytics lekérdezési idő gráfja](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>Legújabb kimenet
A feladatok figyelésének egy másik érdekes adatpontja az utolsó kimenet időpontja, amely az Áttekintés oldalon látható.
Ez az idő az alkalmazás legutóbbi kimenetének ideje (azaz az esemény adataiból származó időbélyegzőt használva).

## <a name="get-help"></a>Segítség
További támogatásért keresse fel az [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Következő lépések
* [Bevezetés a Azure Stream Analyticsba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
