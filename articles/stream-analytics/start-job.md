---
title: Azure Stream Analytics-feladatok indítása
description: Ez a cikk azt ismerteti, hogyan indíthat el egy Stream Analytics feladatot a Azure Portal, a PowerShell és a Visual Studio használatával.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: c393eb782c2ff16eb5b3e5967b39938dfe2f1534
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75426468"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Azure Stream Analytics-feladatok indítása

A Azure Stream Analytics feladatot a Azure Portal, a Visual Studio és a PowerShell használatával indíthatja el. Amikor elindít egy feladatot, ki kell választania egy időpontot a kimenet létrehozásának megkezdéséhez. A Azure Portal, a Visual Studio és a PowerShell mindegyike különböző módszerekkel rendelkezik a kezdési idő beállításához. Ezek a módszerek alább olvashatók.

## <a name="start-options"></a>Indítási beállítások
A feladatok elindításához a következő három lehetőség áll rendelkezésre: Vegye figyelembe, hogy az alább említett időpontok a következő [időbélyegzővel](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)vannak megadva:. Ha a TIMESTAMP BY paraméter nincs megadva, a rendszer az érkezési időt fogja használni.
* **Most**: a kimeneti esemény kiindulási pontja ugyanaz, mint a feladatok indításakor. Ha egy időbeli operátort (például időablakot, KÉSÉSt vagy CSATLAKOZÁSt) használ, Azure Stream Analytics automatikusan visszakeresi a bemeneti forrásban lévő adatokat. Ha például elindít egy "Now" feladatot, és ha a lekérdezés 5 perces késleltetési időszakot használ, Azure Stream Analytics 5 perccel ezelőtt keres adatokat a bemenetben.
Az első lehetséges kimeneti esemény időbélyege az aktuális időpontnál nagyobb vagy annál későbbi, a ASA pedig garantálja, hogy az összes olyan bemeneti esemény, amely logikailag hozzájárulhat a kimenethez, a rendszer elvégezte a számítást. A rendszer például nem hoz létre részleges ablakos összesítéseket. Mindig a teljes aggregált érték.

* **Egyéni**: kiválaszthatja a kimenet kezdőpontját. A **most** lehetőséghez hasonlóan a Azure stream Analytics automatikusan beolvassa az adott időpontot, ha egy ideiglenes operátor van használatban 

* **Utolsó leállítva**. Ez a beállítás akkor érhető el, ha a feladatot korábban elindították, de manuálisan vagy sikertelenül leállították. Ha ezt a beállítást választja, Azure Stream Analytics fogja használni az utolsó kimeneti időt a feladatok újraindításához, így az adatok nem vesznek el. Az előző beállításokhoz hasonlóan Azure Stream Analytics automatikusan beolvassa az adott időpontot, ha egy időbeli operátor van használatban. Mivel több bemeneti partíció is rendelkezhet különböző időponttal, a rendszer az összes partíció legkorábbi leállítási idejét használja. Ennek eredményeképpen előfordulhat, hogy néhány másodpéldány látható a kimenetben. A pontosan egyszeri feldolgozással kapcsolatos további információk az [esemény-kézbesítési garanciák](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)lapon érhetők el.


## <a name="azure-portal"></a>Azure Portal

A Azure Portalban navigáljon a feladatokhoz, és válassza a **Start** lehetőséget az Áttekintés oldalon. Válassza ki a **feladatok kimenetének kezdési idejét** , majd kattintson a **Start**gombra.

Válassza ki az egyik lehetőséget a **feladatok kimenetének kezdési idejére**. A beállítások *mostantól*, az *Egyéni*és a, ha a feladatot korábban futtatták, a *legutóbbi leállítás*után. A fenti beállításokkal kapcsolatos további információkért lásd a fenti lépéseket.

## <a name="visual-studio"></a>Visual Studio

A feladatok nézetben kattintson a zöld nyíl gombra a feladatok elindításához. Állítsa be a **feladatok kimenetének indítási módját** , és válassza az **Indítás**lehetőséget. A feladatok állapota **fut**értékre változik.

A **feladatok kimenetének indítási módja**három lehetőség közül választhat: *JobStartTime*, *CustomTime*és *LastOutputEventTime*. Ha ez a tulajdonság hiányzik, az alapértelmezett érték a *JobStartTime*. A fenti beállításokkal kapcsolatos további információkért lásd a fenti lépéseket.


## <a name="powershell"></a>PowerShell

A következő parancsmag használatával indíthatja el a feladatot a PowerShell használatával:

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

Három lehetőség áll rendelkezésre a **OutputStartMode**: *JobStartTime*, *CustomTime*és *LastOutputEventTime*. Ha ez a tulajdonság hiányzik, az alapértelmezett érték a *JobStartTime*. A fenti beállításokkal kapcsolatos további információkért lásd a fenti lépéseket.

A `Start-AzStreamAnalyitcsJob` parancsmaggal kapcsolatos további információkért tekintse meg a [Start-AzStreamAnalyticsJob referenciát](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob).

## <a name="next-steps"></a>További lépések

* [Útmutató: Stream Analytics-feladat létrehozása az Azure Portal használatával](stream-analytics-quick-create-portal.md)
* [Gyors útmutató: Stream Analytics-feladatok létrehozása Azure PowerShell használatával](stream-analytics-quick-create-powershell.md)
* [Gyors útmutató: Stream Analytics-feladat létrehozása a Visual Studio Azure Stream Analytics eszközeinek használatával](stream-analytics-quick-create-vs.md)
