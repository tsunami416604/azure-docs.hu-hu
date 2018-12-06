---
title: Feladat figyelése az Azure Stream Analytics ismertetése
description: Ez a cikk ismerteti az Azure Stream Analytics-feladatok figyelése
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: 81c5eabcfcca6818293ff9dbbfb1515d5729dd79
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52963284"
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Megismerheti a Stream Analytics-feladat figyelése és lekérdezések figyelése

## <a name="introduction-the-monitor-page"></a>Bemutatása: A figyelő lap
Az Azure portál mindkét surface fő teljesítménymutatói, figyelése és hibaelhárítása a lekérdezés és a feladat teljesítményét is lehet. Ezek a metrikák megtekintéséhez tallózással keresse meg a Stream Analytics-feladat, a metrikák megjelenítésének iránt, és megtekintheti a **figyelés** szakasz az Áttekintés oldalon.  

![Figyelési hivatkozás](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

Az ablakban látható módon jelenik meg:

![Figyelési feladat irányítópult](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Stream Analytics-metrikáit
| Metrika                 | Meghatározás                               |
| ---------------------- | ---------------------------------------- |
| Várakozó bemeneti események       | Bemeneti vannak várakozó események száma. |
| Adatkonverziós hibák | Nem konvertálható a várt kimeneti sémájának kimeneti események számát. |
| Korai bemeneti események       | Fogadott korai események száma. |
| Sikertelen függvénykérések | Nem sikerült az Azure Machine Learning függvényhívások (ha van ilyen) száma. |
| Függvényesemények        | Az Azure Machine Learning-függvény (ha van ilyen) küldött események száma. |
| Függvénykérések      | Az Azure Machine Learning-függvény (ha van ilyen)-hívások száma. |
| A deszerializálás bemeneti hibái       | Nem sikerült deszerializálni események számát.  |
| Bemeneti esemény bájtokban      | A Stream Analytics-feladatot (bájt) által fogadott adatok mennyisége. Ez használható ellenőrzése, hogy a bemeneti forrás eseményt kap. |
| Bemeneti események           | A Stream Analytics-feladat, az események száma által fogadott adatok mennyisége. Ez használható ellenőrzése, hogy a bemeneti forrás eseményt kap. |
| Fogadott bemeneti források       | A bemeneti forrásból érkező események száma. |
| Késedelmes bemeneti események      | A forrás, amely vagy el lett dobva vagy időbélyegzőik későn érkező események száma be van állítva, késői érkezési tolerancia ablak beállítás esemény rendezése házirend konfigurációja alapján. |
| Kimenő soron kívüli események    | Fogadott lettek eldobva, vagy egy módosított időbélyeget, az esemény rendezése házirend alapján adott üzemen kívüli események száma. Ez lehet negatív hatással lehet a konfigurációs beállítás Out of rendelés tolerancia ablakban. |
| Kimeneti események          | A kimeneti tárolóhoz az események száma a Stream Analytics-feladat által küldött adatok mennyisége. |
| Futásidejű hibák         | Lekérdezés-feldolgozás (kivéve a fürtjét az események vagy outputing eredmények során talált hibákat) kapcsolatos hibák teljes száma |
| SU százalékos kihasználtsága       | A folyamatos átviteli egység felhasználása a méretezés lapon, a feladat hozzárendelt feladathoz. Ez a kijelző elérhető 80 %-os, vagy a fenti nincs nagy valószínűséggel, hogy az események feldolgozása késhet, vagy megáll. |
| Vízjel-késleltetés       | A vízjel maximális késleltetés, a feladat összes kimeneti összes partíciójára. |

## <a name="customizing-monitoring-in-the-azure-portal"></a>Figyelés testreszabása az Azure Portalon
Módosíthatja a diagram, metrika is látható, típusát és időtartomány a diagram szerkesztése beállításaiban. További információkért lásd: [testreszabása figyelése annak](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

  ![Lekérdezési idő figyelése graph](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>Legújabb kimenet
A feladat figyeléséhez más érdekes adatpont az az idő az utolsó kimeneti az Áttekintés lapon látható.
Az idő az az alkalmazás idő (azaz az idő az eseményadatokat időbélyege használatával), a feladat legfrissebb kimenetét.

## <a name="get-help"></a>Segítségkérés
További támogatásért keresse fel az [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

