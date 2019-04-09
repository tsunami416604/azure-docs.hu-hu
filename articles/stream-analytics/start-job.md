---
title: Azure Stream Analytics-feladat indítása
description: Ez a cikk ismerteti a Stream Analytics-feladat elindítása.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: 9bc3e4132919e5fc5baadc78841e66efd3c34bcd
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "59005938"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Azure Stream Analytics-feladat indítása

Az Azure Stream Analytics-feladat az Azure Portalon, a Visual Studio és a PowerShell használatával elindíthatja. Amikor elindít egy feladatot, ki kell választania egy idő, amíg a feladat kimeneti létrehozásának megkezdéséhez. Az Azure portal, a Visual Studio és a PowerShell is állítja a kezdési időpont különböző módszereket. Azokat a módszereket az alábbiakban tekintheti át.

## <a name="start-options"></a>Indítsa el a beállítások
A három alábbi beállítások érhetők el a feladat elindításához. Vegye figyelembe, hogy a fentiekben említett mindig címében megadottaktól [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics). Ha a TIMESTAMP BY nincs megadva, érkezési ideje lesz használható.
* **Most már**: Lehetővé teszi a kiindulási pont, a kimeneti esemény streamelése ugyanaz, mint amikor a feladat elindult. A historikus operátor használata esetén (pl. időablak LAG vagy ILLESZTÉSI), az Azure Stream Analytics lesz automatikusan nézze meg az adatokat a bemeneti forrás. Például ha "Most" indít el egy feladatot, és ha a lekérdezés egy 5 percenként Átfedésmentes ablak használ, az Azure Stream Analytics törekszik 5 perccel ezelőtt, a bemeneti adatok.
Az első lehetséges kimeneti esemény kellene egyenlő vagy nagyobb, mint az aktuális időt egy időbélyeget, és az ASA garantálja, hogy az összes bemeneti események, amelyek logikailag járulhatnak hozzá a kimenetet a létezik-e. Ha például nem részleges ablakos összesítéseket jönnek létre. Mindig a teljes összesített értéket.

* **Egyéni**: Kiválaszthatja, hogy a kimenet a kiindulási pont. Hasonlóan a **most** beállítást, az Azure Stream Analytics automatikusan beolvassa az adatokat, ezen időpont előtti historikus operátor használata esetén 

* **Utolsó leállítás időpontjában**. Ez a beállítás érhető el a feladat korábban elindult, de volt leállításakor manuálisan vagy sikertelen volt. Ez a beállítás kiválasztásakor az Azure Stream Analytics használatával a legutóbbi kimeneti indítsa újra a feladatot, így adatvesztés. Ehhez hasonlóan az előző beállítások, az Azure Stream Analytics automatikusan az adatok beolvasása ezen időpont előtti historikus operátor használata esetén. Több bemeneti partíciók előfordulhat, hogy több különböző ideje lesz, mivel a legkorábbi leállási idő, az összes partícióra szolgál, emiatt néhány ismétlődések előfordulhat, hogy látható a kimeneti. További információ a pontosan-egyszeri feldolgozását érhetők el az oldalon [esemény kézbesítési garanciával könnyítik](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics).


## <a name="azure-portal"></a>Azure Portal

Keresse meg a feladatot az Azure Portalon, és válassza a **Start** az Áttekintés oldalon. Válassza ki a **feladatkimenet kezdési idő** majd **Start**.

Válassza a beállítások egyikét a **feladatkimenet kezdési idő**. A lehetőségek a következők *most*, *egyéni*, és ha korábban már futtatták a feladat, *utolsó leállítás időpontjában*. Az alábbi beállításokkal kapcsolatos további információkért lásd a fenti.

## <a name="visual-studio"></a>Visual Studio

A feladatok nézetben válassza ki a zöld nyílra a feladat indításához. Állítsa be a **feladat kimeneti mód indítása** válassza **Start**. A feladat állapota változik **futó**.

A három lehetőség áll rendelkezésre **feladat kimeneti mód indítása**: *JobStartTime*, *CustomTime*, és *LastOutputEventTime*. Ez a tulajdonság hiányzik, ha az alapértelmezett érték *JobStartTime*. Az alábbi beállításokkal kapcsolatos további információkért lásd a fenti.


## <a name="powershell"></a>PowerShell

A PowerShell-lel feladat indításához használja a következő parancsmagot:

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

A három lehetőség áll rendelkezésre **OutputStartMode**: *JobStartTime*, *CustomTime*, és *LastOutputEventTime*. Ez a tulajdonság hiányzik, ha az alapértelmezett érték *JobStartTime*. Az alábbi beállításokkal kapcsolatos további információkért lásd a fenti.

További információ a `Start-AzStreamAnalyitcsJob` parancsmagot, a nézet a [Start-AzStreamAnalyticsJob referencia](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob).

## <a name="next-steps"></a>További lépések

* [Gyors útmutató: Stream Analytics-feladat létrehozása az Azure portal használatával](stream-analytics-quick-create-portal.md)
* [Gyors útmutató: Azure PowerShell-lel a Stream Analytics-feladat létrehozása](stream-analytics-quick-create-powershell.md)
* [Gyors útmutató: Stream Analytics-feladat létrehozása az Azure Stream Analytics tools for Visual Studio használatával](stream-analytics-quick-create-vs.md)
