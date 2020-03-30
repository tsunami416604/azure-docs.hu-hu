---
title: A feladatfigyelés ismertetése az Azure Stream Analytics szolgáltatásban
description: Ez a cikk ismerteti, hogyan figyelheti az Azure Stream Analytics-feladatok at az Azure Portalon.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2018
ms.custom: seodec18
ms.openlocfilehash: 4e9f90035816269d2d41781be34d0d8080628b12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431649"
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>A Stream Analytics-feladatfigyelés és a lekérdezések figyelésének ismertetése

## <a name="introduction-the-monitor-page"></a>Bevezetés: A monitor oldala
Az Azure Portal felületek kulcsfontosságú teljesítmény metrikák, amelyek segítségével a lekérdezés és a feladat teljesítményének figyelésére és hibaelhárítására használható. Ezeknek a metrikáknak a megtekintéséhez keresse meg azt a Stream Analytics-feladatot, amely et érdekli, hogy tekintse meg a mérőszámokat, és tekintse meg a **Figyelés** szakaszt az Áttekintés oldalon.  

![A Stream Analytics feladatfigyelési hivatkozása](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

Az ablak a következőképpen jelenik meg:

![Stream Analytics-feladatfigyelési irányítópult](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>A Stream Analytics számára elérhető mérőszámok
| Metrika                 | Meghatározás                               |
| ---------------------- | ---------------------------------------- |
| Eldugott bemeneti események       | Az elmaradt bemeneti események száma. A metrika nem nulla értéke azt jelenti, hogy a feladat nem képes lépést tartani a bejövő események számával. Ha ez az érték lassan növekszik, vagy következetesen nem nulla, akkor horizontális felskálázása a munkát. Ha többet szeretne megtudni, látogasson [el megérteni és állítsa streaming egységek](stream-analytics-streaming-unit-consumption.md). |
| Adatkonvertálási hibák | Azon kimeneti események száma, amelyeket nem lehetett a várt kimeneti sémára konvertálni. A hibaházirend "Drop"-ra módosítható, hogy eldobja az ebben a forgatókönyvben előforduló eseményeket. |
| Korai bemeneti események       | Események, amelyek alkalmazás időbélyege több mint 5 perccel korábbi, mint az érkezési idő. |
| Sikertelen függvénykérelmek | Sikertelen Azure Machine Learning függvényhívások száma (ha van). |
| Függvényesemények        | Az Azure Machine Learning függvénybe küldött események száma (ha van). |
| Függvénykérések      | Az Azure Machine Learning függvény hívásainak száma (ha van). |
| Bemeneti deszerializálási hibák       | A nem deszerializálható bemeneti események száma.  |
| Bemeneti eseménybájtjai      | A Stream Analytics-feladat által fogadott adatok mennyisége bájtban. Ezzel ellenőrizheti, hogy az események a bemeneti forrásba kerülnek-e. |
| Bemeneti események           | A bemeneti eseményekből deszerializált rekordok száma. Ez a szám nem tartalmazza az olyan bejövő eseményeket, amelyek deszerializálási hibákat eredményeznek. |
| Fogadott bemeneti források       | A feladat által fogadott üzenetek száma. Az Event Hub esetében az üzenet egyetlen EventData. Blob esetén az üzenet egyetlen blob. Kérjük, vegye figyelembe, hogy a bemeneti források at a deszerializálás előtt számolja a számlálás. Deszerializálási hibák esetén a bemeneti források nagyobbak lehetnek, mint a bemeneti események. Ellenkező esetben lehet kisebb vagy egyenlő a bemeneti események, mivel minden üzenet tartalmazhat több eseményt. |
| Késői bemeneti események      | A beállított késői érkezési toleranciaablaknál később érkezett események. További információ az [Azure Stream Analytics eseményrendeléssel kapcsolatos szempontjairól.](stream-analytics-out-of-order-and-late-events.md) |
| Nem sorrendben lévő események    | Az eseményrendezési szabályzat alapján eldobott vagy módosított időbélyeggel módosított események sorrenden kívül fogadott események száma. Erre hatással lehet a Rendelésen kívüli tolerancia ablak konfigurációja. |
| Kimeneti események          | A Stream Analytics-feladat által a kimeneti célhoz küldött adatok mennyisége az események számában. |
| Futásidejű hibák         | A lekérdezésfeldolgozással kapcsolatos hibák teljes száma (kivéve az események betöltése vagy az eredmények kiírása során talált hibákat) |
| SU % kihasználtság       | A feladathoz rendelt streamelő egység(ek) kihasználtsága a feladat Méretezés lapjáról. Ha ez a mutató eléri a 80%-ot vagy annál magasabb, nagy a valószínűsége annak, hogy az esemény feldolgozása késleltethető vagy leállhat a folyamatban. |
| Vízjel késleltetése       | A maximális vízjel késleltetés a feladat összes kimenetének összes partícióján. |

Ezekkel a mérőszámokkal [figyelheti a Stream Analytics-feladat teljesítményét.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) 

## <a name="customizing-monitoring-in-the-azure-portal"></a>Figyelés testreszabása az Azure Portalon
A Diagram szerkesztése beállításokban módosíthatja a diagram típusát, a megjelenített mutatókat és az időtartományt. További információt a [Figyelés testreszabása](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)című témakörben talál.

  ![Stream Analytics-lekérdezésfigyelő időgrafikonja](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>Legutóbbi kimenet
Egy másik érdekes adatpont a feladat figyelésére az utolsó kimenet időpontja, amely az Áttekintés oldalon látható.
Ez az idő a feladat legújabb kimenetének alkalmazásideje (azaz az eseményadatok időbélyegének használatával történő használata).

## <a name="get-help"></a>Segítségkérés
További támogatásért keresse fel az [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>További lépések
* [Bevezetés az Azure Stream Analytics szolgáltatásba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs (Azure Stream Analytics-feladatok méretezése)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
