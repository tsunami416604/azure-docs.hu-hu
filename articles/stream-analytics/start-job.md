---
title: Azure Stream Analytics-feladat indítása
description: Ez a cikk bemutatja, hogyan indíthat el egy Stream Analytics-feladatot az Azure Portalról, a PowerShellből és a Visual Studióból.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: c393eb782c2ff16eb5b3e5967b39938dfe2f1534
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426468"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Azure Stream Analytics-feladat indítása

Az Azure Stream Analytics-feladatot az Azure Portal, a Visual Studio és a PowerShell használatával indíthatja el. Amikor elindít egy feladatot, kiválasztja a feladat létrehozásának idejét. Az Azure Portal, a Visual Studio és a PowerShell mindegyike különböző módszerekkel rendelkezik a kezdési időpont beállításához. Ezeket a módszereket az alábbiakban ismertetjük.

## <a name="start-options"></a>Indítási beállítások
A következő három lehetőség áll rendelkezésre a feladat elindításához. Ne feledje, hogy az alább említett időpontok mindegyike a [TIMESTAMP BY-](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)ben meghatározott a . Ha nincs megadva időbélyegaláns, a lesz használva az érkezési idő.
* **Most:** A kimeneti eseményadatfolyam kezdőpontját ugyanúgy teszi, mint amikor a feladat elindul. Ha időbeli operátort használ (pl. időablak, HELYI AKCIÓS-csoport vagy JOIN), az Azure Stream Analytics automatikusan visszatekint a bemeneti forrásban lévő adatokra. Például ha elindítja a feladatot "Most", és ha a lekérdezés egy 5 perces tumbling ablak, az Azure Stream Analytics adatokat keres 5 perccel ezelőtt a bemeneti.
Az első lehetséges kimeneti esemény időbélyege megegyezik vagy nagyobb, mint az aktuális idő, és az ASA garantálja, hogy minden olyan bemeneti esemény, amely logikailag hozzájárulhat a kimenethez, el lett számolva. Például nem jönnek létre részleges ablakos összesítések. Mindig a teljes összesített érték.

* **Egyéni**: Kiválaszthatja a kimenet kezdőpontját. A **Most** beállításhoz hasonlóan az Azure Stream Analytics automatikusan beolvassa az adatokat, ha időbeli operátort használ. 

* **Amikor utoljára megállt**. Ez a beállítás akkor érhető el, ha a feladat korábban elindult, de manuálisan leállították vagy nem sikerült. Ha ezt a lehetőséget választja, az Azure Stream Analytics az utolsó kimeneti időt használja a feladat újraindításához, így nem vesznek el adatok. A korábbi beállításokhoz hasonlóan az Azure Stream Analytics automatikusan beolvassa az adatokat, mielőtt ez alkalommal egy időbeli operátort használ. Mivel több bemeneti partíciónak eltérő ideje lehet, az összes partíció legkorábbi leállítási ideje használatos, ennek következtében a kimenetben bizonyos ismétlődések is megjelenhetnek. A pontosan egyszeri feldolgozásról további információ az [Eseménykézbesítési garanciák](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)oldalon található.


## <a name="azure-portal"></a>Azure portál

Keresse meg a feladatát az Azure Portalon, és válassza a **Start** lehetőséget az áttekintő lapon. Válassza ki **a feladat kimeneti kezdési idejét,** majd válassza **a Start**gombot.

Válassza ki a **Feladat kimeneti kezdési időpontjának**egyik ét. A beállítások *a*most , *egyéni*, és ha a feladat korábban futott, *Mikor utoljára leállt*. Ezekről a lehetőségekről lásd fent a fenti információkat.

## <a name="visual-studio"></a>Visual Studio

A feladat nézetben a zöld nyíl gombbal indítsa el a feladatot. Állítsa be a **Feladatkimenet indítási módját,** és válassza a **Start**gombot. A feladat állapota **Futás**állapotra változik.

A **Feladatkimenet indítási módjának**három lehetősége van: *JobStartTime*, *CustomTime*és *LastOutputEventTime*. Ha ez a tulajdonság hiányzik, az alapértelmezett *jobstarttime*. Ezekről a lehetőségekről lásd fent a fenti információkat.


## <a name="powershell"></a>PowerShell

A következő parancsmag segítségével indítsa el a feladatot a PowerShell használatával:

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

**Az OutputStartMode**három lehetőség közül választhat: *JobStartTime*, *CustomTime*és *LastOutputEventTime*. Ha ez a tulajdonság hiányzik, az alapértelmezett *jobstarttime*. Ezekről a lehetőségekről lásd fent a fenti információkat.

A parancsmagról `Start-AzStreamAnalyitcsJob` további információt a [Start-AzStreamAnalyticsJob hivatkozásban](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob)talál.

## <a name="next-steps"></a>További lépések

* [Útmutató: Stream Analytics-feladat létrehozása az Azure Portal használatával](stream-analytics-quick-create-portal.md)
* [Rövid útmutató: Stream Analytics-feladat létrehozása az Azure PowerShell használatával](stream-analytics-quick-create-powershell.md)
* [Gyors útmutató: Stream Analytics-feladat létrehozása a Visual Studio Azure Stream Analytics eszközeinek használatával](stream-analytics-quick-create-vs.md)
