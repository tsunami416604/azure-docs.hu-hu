---
title: Feladat figyelése az Azure Stream Analytics ismertetése
description: Ez a cikk ismerteti az Azure Portalon az Azure Stream Analytics-feladatok figyelése.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2018
ms.custom: seodec18
ms.openlocfilehash: 9432b43498cd48352427bf1ec2709a2d0f13a797
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172758"
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Megismerheti a Stream Analytics-feladat figyelése és lekérdezések figyelése

## <a name="introduction-the-monitor-page"></a>Bemutatása A figyelő oldal
A Azure Portal felületek fő teljesítménymutatói, amelyek a lekérdezés és a feladatok teljesítményének figyelésére és hibaelhárítására használhatók. Ezek a metrikák megtekintéséhez tallózással keresse meg a Stream Analytics-feladat, a metrikák megjelenítésének iránt, és megtekintheti a **figyelés** szakasz az Áttekintés oldalon.  

![Stream Analytics-feladat figyelése hivatkozás](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

Az ablakban látható módon jelenik meg:

![Stream Analytics-feladat figyelési irányítópult](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Stream Analytics-metrikáit
| Metrika                 | Meghatározás                               |
| ---------------------- | ---------------------------------------- |
| Várakozó bemeneti események       | Bemeneti vannak várakozó események száma. A metrika nem nulla értéke azt jelenti, hogy a feladatnak nem sikerült megtartania a bejövő események számát. Ha ez az érték lassan növekszik vagy konzisztensen nem nulla, érdemes felmérni a feladatot. További információért tekintse meg a [folyamatos átviteli egységek megismerése és módosítása](stream-analytics-streaming-unit-consumption.md)című témakört. |
| Adatkonverziós hibák | Nem konvertálható a várt kimeneti sémájának kimeneti események számát. A hiba házirendje a "drop" értékre módosítható, hogy eldobják az ebben a forgatókönyvben észlelt eseményeket. |
| Korai bemeneti események       | Azok az események, amelyek esetében az alkalmazás időbélyeg-értéke korábbi, mint 5 perc. |
| Sikertelen függvénykérések | Nem sikerült az Azure Machine Learning függvényhívások (ha van ilyen) száma. |
| Függvényesemények        | Az Azure Machine Learning-függvény (ha van ilyen) küldött események száma. |
| Függvénykérések      | Az Azure Machine Learning-függvény (ha van ilyen)-hívások száma. |
| A deszerializálás bemeneti hibái       | A nem deszerializálható bemeneti események száma.  |
| Bemeneti esemény bájtokban      | A Stream Analytics-feladatot (bájt) által fogadott adatok mennyisége. Ez használható ellenőrzése, hogy a bemeneti forrás eseményt kap. |
| Bemeneti események           | A bemeneti eseményekből deszerializált rekordok száma. Ez a szám nem tartalmazza a deszerializálási hibákat eredményező bejövő eseményeket. |
| Fogadott bemeneti források       | A feladatokban fogadott üzenetek száma. Az Event hub esetében az üzenet egyetlen EventData. A blob esetében az üzenet egyetlen blob. Vegye figyelembe, hogy a bemeneti források a deszerializálás előtt számítanak. Deszerializálási hibák esetén a bemeneti források nagyobbak lehetnek a bemeneti eseményeknél. Ellenkező esetben előfordulhat, hogy a bemeneti események kisebbek vagy egyenlőek, mivel az egyes üzenetek több eseményt is tartalmazhatnak. |
| Késedelmes bemeneti események      | A beállított késői beérkezési tolerancia időszaka után megjelenő események. További információ az [Azure stream Analytics az események rendezésével](stream-analytics-out-of-order-and-late-events.md) kapcsolatos megfontolásokról. |
| Kimenő soron kívüli események    | Fogadott lettek eldobva, vagy egy módosított időbélyeget, az esemény rendezése házirend alapján adott üzemen kívüli események száma. Ez lehet negatív hatással lehet a konfigurációs beállítás Out of rendelés tolerancia ablakban. |
| Kimeneti események          | A kimeneti tárolóhoz az események száma a Stream Analytics-feladat által küldött adatok mennyisége. |
| Futásidejű hibák         | A lekérdezések feldolgozásával kapcsolatos hibák teljes száma (kivéve az események betöltése vagy az eredmények kihelyezése során talált hibákat) |
| SU százalékos kihasználtsága       | A folyamatos átviteli egység felhasználása a méretezés lapon, a feladat hozzárendelt feladathoz. Ez a kijelző elérhető 80 %-os, vagy a fenti nincs nagy valószínűséggel, hogy az események feldolgozása késhet, vagy megáll. |
| Vízjel-késleltetés       | A vízjel maximális késleltetés, a feladat összes kimeneti összes partíciójára. |

Ezek a metrikák a [stream Analytics feladatok teljesítményének figyelésére](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor)használhatók. 

## <a name="customizing-monitoring-in-the-azure-portal"></a>Figyelés testreszabása az Azure Portalon
Módosíthatja a diagram, metrika is látható, típusát és időtartomány a diagram szerkesztése beállításaiban. További információkért lásd: [testreszabása figyelése annak](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

  ![Stream Analytics lekérdezési idő grafikon](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>Legújabb kimenet
A feladat figyeléséhez más érdekes adatpont az az idő az utolsó kimeneti az Áttekintés lapon látható.
Az idő az az alkalmazás idő (azaz az idő az eseményadatokat időbélyege használatával), a feladat legfrissebb kimenetét.

## <a name="get-help"></a>Segítségkérés
További támogatásért keresse fel az [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
