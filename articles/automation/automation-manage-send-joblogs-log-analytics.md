---
title: Azure Automation-feladat adatainak továbbítása a Log Analyticsbe
description: Ez a cikk bemutatja, hogyan küldhet feladat állapotát és a runbook feladatstreamek az Azure Log Analyticsben, hogy további betekintést és a felügyeleti.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 34a695daa077e882e911d3fb59f8a30e39c3a9d2
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55756631"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-log-analytics"></a>Feladat állapota és a feladatstreamek automatizálást továbbítása a Log Analyticsbe

Automation küldhet a runbook állapota és a feladat feladatstreamek a Log Analytics-munkaterületre. Ez a folyamat nem érintik a munkaterület összekapcsolása, és teljesen független. Feladatnaplók és a feladatstreamek láthatók az Azure Portalon, vagy a PowerShell-lel, az egyes feladatok, és ez lehetővé teszi egyszerű vizsgálatokhoz végrehajtásához. Most már a Log Analytics-szel a következőket teheti:

* Információk szerzése az Automation-feladatokról.
* A trigger egy e-mailben vagy a riasztások alapján a forgatókönyv-feladat állapota (például felfüggesztett vagy sikertelen).
* Speciális lekérdezések futtatása több feladatstreamre.
* Feladatok korrelációjának megállapítása több Automation-fiókra kiterjedően.
* Feladatelőzmények időbeli megjelenítése.

## <a name="prerequisites-and-deployment-considerations"></a>Előfeltételek és telepítésével kapcsolatos megfontolások

Az Automation-naplók küldése a Log Analyticshez való indításához lesz szüksége:

* A November 2016 vagy újabb kiadását [Azure PowerShell-lel](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) (v2.3.0).
* Egy Log Analytics-munkaterület. További információkért lásd: [Ismerkedés a Log Analytics](../log-analytics/log-analytics-get-started.md). 
* Az Azure Automation-fiókhoz tartozó erőforrás azonosítója.

Az Azure Automation-fiókban található az erőforrás-azonosító:

```powershell-interactive
# Find the ResourceId for the Automation Account
Get-AzureRmResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Az erőforrás-azonosító a Log Analytics-munkaterület, futtassa az alábbi PowerShell-lel:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Ha egynél több Automation-fiókok, vagy a munkaterületeket, a fenti parancsok kimenetének, keresse meg a *neve* kell konfigurálni, és másolja az értéket a *ResourceId*.

Ha meg kell keresnie a *neve* az Automation-fiók az Azure Portalon válassza ki az Automation-fiókját a **Automation-fiók** panelhez, és válassza **minden beállítás** . A **Minden beállítás** panel **Fiókbeállítások** részénél válassza a **Tulajdonságok** lehetőséget.  A **Tulajdonságok** panelen megtalálja a keresett értékeket.<br> ![Automation-fiók tulajdonságai](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="set-up-integration-with-log-analytics"></a>A Log Analytics-integráció beállítása

1. A számítógépen indítsa el a **Windows PowerShell** származó a **Start** képernyő.
2. Futtassa az alábbi PowerShell-lel, és az értéke Szerkesztés a `[your resource id]` és `[resource id of the log analytics workspace]` az előző lépésből származó értékekkel.

   ```powershell-interactive
   $workspaceId = "[resource id of the log analytics workspace]"
   $automationAccountId = "[resource id of your automation account]"

   Set-AzureRmDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled $true
   ```

Ez a szkript futtatása után is igénybe vehet egy órát, a Log Analytics új JobLogs vagy ír JobStreams rekordok megtekintése előtt.

A naplók megtekintéséhez futtassa a következő lekérdezést a Log Analytics naplóbeli keresés: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Konfiguráció ellenőrzése

Győződjön meg arról, hogy az Automation-fiók naplókat küld a Log Analytics-munkaterülethez, ellenőrizze, hogy diagnosztikai helyesen van konfigurálva az Automation-fiókot a következő PowerShell-lel:

```powershell-interactive
Get-AzureRmDiagnosticSetting -ResourceId $automationAccountId
```

A kimenetben ellenőrizze, hogy:

* A *naplók*, értéke *engedélyezve* van *igaz*.
* Az érték *munkaterület azonosítója* az erőforrás-azonosító, a Log Analytics-munkaterület értékre van állítva.

## <a name="log-analytics-records"></a>Log Analytics-rekordok

Diagnosztika az Azure Automation két rekordtípust hoz létre a Log Analytics és a címkével **AzureDiagnostics**. A következő lekérdezéseket a Log Analytics frissített lekérdezési nyelvet használja. Az örökölt lekérdezési nyelv és az Azure Log Analytics új lekérdezési nyelvre közötti gyakori lekérdezések információt [örökölt az új Azure Log Analytics lekérdezési nyelv – Adatlap](https://docs.loganalytics.io/docs/Learn/References/Legacy-to-new-to-Azure-Log-Analytics-Language)

### <a name="job-logs"></a>Feladatnaplók

| Tulajdonság | Leírás |
| --- | --- |
| TimeGenerated |A runbook-feladat végrehajtásának dátuma és időpontja. |
| RunbookName_s |A runbook neve. |
| Caller_s |A művelet kezdeményezője. Lehetséges értékek: egy e-mail-cím vagy egy ütemezett feladatokat tartalmazó rendszer. |
| Tenant_g | A hívónak a bérlői azonosító GUID. |
| JobId_g |GUID, a runbook-feladat azonosítója. |
| ResultType |A runbook-feladat állapota. Lehetséges értékek:<br>– Új<br>- Elindítva<br>- Leállítva<br>- Felfüggesztve<br>- Sikertelen<br>-Befejeződött |
| Kategória | Az adattípus besorolása. Az Automation esetében az érték JobLogs. |
| OperationName | Meghatározza az Azure-ban végrehajtott művelet típusát. Az Automation esetében az érték feladat. |
| Erőforrás | Az Automation-fiók neve |
| SourceSystem | A Log Analytics hogyan összegyűjti az adatokat. Mindig *Azure* Azure Diagnostics. |
| ResultDescription |Ismerteti a runbook-feladat eredményállapotát. Lehetséges értékek:<br>- A feladat elindult<br>- A feladat nem sikerült<br>- A feladat befejeződött |
| CorrelationId |GUID, a runbook-feladat korrelációs azonosítója. |
| ResourceId |Itt adhatja meg az Azure Automation fiók erőforrás-azonosítója a runbook. |
| SubscriptionId | Az Azure-előfizetés azonosítóját (GUID) az Automation-fiókhoz. |
| ResourceGroup | Az Automation-fiókot az erőforráscsoport neve. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |


### <a name="job-streams"></a>Feladatstreamek
| Tulajdonság | Leírás |
| --- | --- |
| TimeGenerated |A runbook-feladat végrehajtásának dátuma és időpontja. |
| RunbookName_s |A runbook neve. |
| Caller_s |A művelet kezdeményezője. Lehetséges értékek: egy e-mail-cím vagy egy ütemezett feladatokat tartalmazó rendszer. |
| StreamType_s |A feladatstream típusa. Lehetséges értékek:<br>- Folyamatban<br>- Kimenet<br>- Figyelmeztetés<br>- Hiba<br>- Hibakeresés<br>- Részletes |
| Tenant_g | A hívónak a bérlői azonosító GUID. |
| JobId_g |GUID, a runbook-feladat azonosítója. |
| ResultType |A runbook-feladat állapota. Lehetséges értékek:<br>– Folyamatban |
| Kategória | Az adattípus besorolása. Az Automation esetében az érték JobStreams. |
| OperationName | Meghatározza az Azure-ban végrehajtott művelet típusát. Az Automation esetében az érték feladat. |
| Erőforrás | Az Automation-fiók neve |
| SourceSystem | A Log Analytics hogyan összegyűjti az adatokat. Mindig *Azure* Azure Diagnostics. |
| ResultDescription |A runbook kimeneti streamjét tartalmazza. |
| CorrelationId |GUID, a runbook-feladat korrelációs azonosítója. |
| ResourceId |Itt adhatja meg az Azure Automation fiók erőforrás-azonosítója a runbook. |
| SubscriptionId | Az Azure-előfizetés azonosítóját (GUID) az Automation-fiókhoz. |
| ResourceGroup | Az Automation-fiókot az erőforráscsoport neve. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |

## <a name="viewing-automation-logs-in-log-analytics"></a>A Log Analytics-naplók Automation megtekintése

Most, hogy az Automation-feladat naplókat küld a Log Analytics kezdi, lássuk, mit tehet a Log Analytics belül ezeket a naplókat.

A naplók megtekintéséhez futtassa a következő lekérdezést: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>E-mail küldése, ha a runbook-feladat meghiúsul, vagy felfüggeszti a
A kiemelt ügyfeleknek küldtük egyik kéri arra, hogy küldjön egy e-mailt vagy egy szöveges, ha valami probléma merül fel a runbook-feladat van.   

Létrehoz egy riasztási szabályt, hogy először létrehozni egy naplókeresést kell meghívnia a riasztás runbook-feladat rekordok. Kattintson a **riasztás** gombot létrehozni és konfigurálni a riasztási szabályt.

1. A Log Analytics áttekintése oldalon kattintson **naplóbeli keresés**.
2. Hozzon létre egy naplóbeli keresési lekérdezés a riasztás a következő keresési beírni a szöveget a lekérdezési mezőtől: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`  Emellett szerint csoportosíthatók az RunbookName használatával: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Ha beállította naplók egynél több Automation-fiókot vagy előfizetést a munkaterületre, előfizetése és az Automation-fiók által a riasztásokat csoportosíthatók. Automation-fiók neve JobLogs keresése erőforrás mezőjében található.
3. Megnyitásához a **létrehozás szabály** kattintson **+ Új riasztási szabály** az oldal tetején. A beállítások konfigurálása a riasztás további információkért lásd: [Naplóriasztások az Azure-ban](../azure-monitor/platform/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Keresse meg az összes feladatot, amely hibákkal fejeződött be
Mellett hibák riasztást küld, amikor egy runbook-feladat nem megszakító hibát tartalmaz találja. Ezekben az esetekben a PowerShell-hibafolyam eredményez, de a megszakítást nem okozó hibákat nem okoznak a feladat felfüggesztése vagy sikertelen.    

1. A Log Analytics munkaterületén kattintson **naplóbeli keresés**.
2. A lekérdezés mezőbe írja be a `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g` és kattintson a **keresési** gombra.

### <a name="view-job-streams-for-a-job"></a>A feladat nézet feladatstreamek
Amikor egy feladat hibakeresése, is érdemes megvizsgáljuk a feladatstreamek. A következő lekérdezést egy egyetlen GUID 2ebd22ea-e05e-4eb9 – 9d 76-d73cbd4356e0 feladathoz tartozó összes adatfolyamot látható:   

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Korábbi feladat állapotának megtekintése
Végül, előfordulhat, hogy szeretné megjeleníteni a feladatelőzmények idővel. Ez a lekérdezés segítségével keresse meg a feladatok állapota idővel.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`  
<br> ![Log Analytics korábbi feladat állapot diagramja](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="remove-diagnostic-settings"></a>Diagnosztikai beállítások törlése

Az Automation-fiók diagnosztikai beállításának eltávolításához futtassa a következő parancsokat:

```powershell-interactive
$automationAccountId = "[resource id of your automation account]"

Remove-AzureRmDiagnosticSetting -ResourceId $automationAccountId
```

## <a name="summary"></a>Összegzés

Az Automation-feladat állapotának és az adatfolyam adatainak küld a Log Analytics, az állapot az Automation-feladatok által jobb betekintést kaphat:
+ Riasztások beállítása arra az esetre, ha probléma van.
+ Egyéni nézetek és a keresési lekérdezések segítségével a runbook eredményeinek képi megjelenítése, forgatókönyv-feladat állapota, és egyéb kapcsolódó legfontosabb mutatók vagy a metrikákat.  

Log Analytics az Automation-feladatokkal, nagyobb átláthatók biztosít, és segíthet a cím az incidensek gyorsabb.  

## <a name="next-steps"></a>További lépések
* Más keresési lekérdezéseket hozhat létre, és tekintse át az Automation feladatnaplóit Log Analytics szolgáltatással kapcsolatos további tudnivalókért lásd: [Log Analytics naplóbeli kereséseivel](../log-analytics/log-analytics-log-searches.md).
* Megtudhatja, hogyan hozhat létre és kimenetet és üzeneteket beolvasni a runbookok, lásd: [Runbook kimeneti és az üzenetek](automation-runbook-output-and-messages.md).
* A runbook végrehajtásával, a runbook-feladatok figyelésével, illetve az egyéb technikai részletekkel kapcsolatos további tudnivalókat a [Runbook-feladatok nyomon követése](automation-runbook-execution.md) című rész tartalmazza.
* A Log Analytics használatával és adatgyűjtési forrásokkal kapcsolatos további információkért lásd: [gyűjtése az Azure storage-adatok a Log Analytics – áttekintés](../azure-monitor/platform/collect-azure-metrics-logs.md).

