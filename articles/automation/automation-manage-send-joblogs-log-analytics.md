---
title: "Azure Automation-feladat adatok továbbítására OMS szolgáltatáshoz |} Microsoft Docs"
description: "Ez a cikk bemutatja, hogyan küldhet feladat állapotát és a runbook feladat adatfolyamokat a Microsoft Operations Management Suite Log Analytics képes biztosítani a további betekintést és kezelése."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: tysonn
ms.assetid: c12724c6-01a9-4b55-80ae-d8b7b99bd436
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/31/2017
ms.author: magoedte
ms.openlocfilehash: 0319a7b9248dec9d7cdabba9c18a25463d94284b
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="forward-job-status-and-job-streams-from-automation-to-log-analytics-oms"></a>Továbbítsa feladat állapotát és a feladat adatfolyamok Automation való Naplóelemzés (OMS)
Automatizálási küldhet runbook feladat állapotát és a feladat adatfolyamokat a Microsoft Operations Management Suite (OMS) Naplóelemzési munkaterületet. Feladat naplózza, és a feladat adatfolyamok láthatók az Azure portálon, vagy a PowerShell használatával, az egyes feladatokat, és ez lehetővé teszi egyszerű vizsgálatok végrehajtását. Most Log Analytics segítségével:

* Az automatizálási feladatok insight szerezni.
* A runbook-feladat állapota (például felfüggesztett vagy sikertelen) alapuló riasztás vagy e-mail eseményindító.
* Speciális lekérdezéseket írhat a feladat adatfolyamok között.
* Feladatok összefüggéseket Automation-fiókok között.
* A feladatelőzmények megjelenítheti az adott idő alatt.

## <a name="prerequisites-and-deployment-considerations"></a>Előfeltételek és központi telepítésével kapcsolatos megfontolások
Az automatizálási naplók értesítésküldés Naplóelemzési, az alábbiak szükségesek:

* A November 2016 vagy újabb kiadása [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) (v2.3.0).
* A Naplóelemzési munkaterület. További információkért lásd: [Ismerkedés a Naplóelemzési](../log-analytics/log-analytics-get-started.md). 
* Az erőforrás-azonosítója az Azure Automation-fiókhoz.


Az erőforrás-azonosítója az Azure Automation-fiókban található:

```powershell-interactive
# Find the ResourceId for the Automation Account
Find-AzureRmResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Az erőforrás-azonosítója a Naplóelemzési munkaterület megkereséséhez futtassa a következő PowerShell:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Ha egynél több Automation-fiók, vagy a munkaterületek között, az előző parancs kimenetében található a *neve* meg kell konfigurálnia, és másolja a következő *ResourceId*.

Ha meg szeretné tudni a *neve* az Automation-fiók, az Azure portálon válassza ki az Automation-fiók a a **Automation-fiók** panelhez, és válassza **összes beállítás**. A **Minden beállítás** panel **Fiókbeállítások** részénél válassza a **Tulajdonságok** lehetőséget.  A **Tulajdonságok** panelen megtalálja a keresett értékeket.<br> ![Automation-fiók tulajdonságai](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="set-up-integration-with-log-analytics"></a>Log Analytics-integráció beállítása

1. A számítógépen indítása **Windows PowerShell** a a **Start** képernyő.
2. Futtassa a következő PowerShell-, és a megadott értéket a `[your resource id]` és `[resource id of the log analytics workspace]` az előző lépésben értékekkel.

   ```powershell-interactive
   $workspaceId = "[resource id of the log analytics workspace]"
   $automationAccountId = "[resource id of your automation account]"

   Set-AzureRmDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled $true
   ```

A parancsfájl futtatása után új JobLogs vagy írása JobStreams 10 percen belül megjelennek a Log Analytics-rekordok.

A naplók megtekintéséhez futtassa a következő lekérdezés Naplóelemzési napló keresése:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION""`

### <a name="verify-configuration"></a>Konfiguráció ellenőrzése
Annak ellenőrzéséhez, hogy az Automation-fiók naplókat küld a Naplóelemzési munkaterületet, ellenőrizze, hogy diagnosztika helyesen van konfigurálva az Automation-fiók a következő PowerShell használatával:

```powershell-interactive
Get-AzureRmDiagnosticSetting -ResourceId $automationAccountId
```

A kimenet győződjön meg arról, hogy:
+ A *naplók*, értéke *engedélyezve* van *igaz*.
+ Értékének *WorkspaceId* az erőforrás-azonosítója a Naplóelemzési munkaterület értékre van állítva.

## <a name="log-analytics-records"></a>Log Analytics-rekordok

Azure Automation diagnosztika kétféle típusú rekordok Naplóelemzési hoz létre, és címkével rendelkeznek, **AzureDiagnostics**. Az alábbi lekérdezéseket a frissített lekérdezési nyelv szolgáltatáshoz használja. A hagyományos lekérdezési nyelv és az új Azure Log Analytics lekérdezési nyelv között közös lekérdezésekkel kapcsolatos információt [örökölt az új Azure napló Analytics Query Language-Adatlap](https://docs.loganalytics.io/docs/Learn/References/Legacy-to-new-to-Azure-Log-Analytics-Language)

### <a name="job-logs"></a>Feladatnaplóit
| Tulajdonság | Leírás |
| --- | --- |
| TimeGenerated |A runbook-feladat végrehajtásának dátuma és időpontja. |
| RunbookName_s |A runbook neve. |
| Caller_s |A művelet kezdeményezője. Lehetséges értékek: egy e-mail-cím vagy egy ütemezett feladatokat tartalmazó rendszer. |
| Tenant_g | A hívónak a bérlői azonosító GUID. |
| JobId_g |GUID, a runbook-feladat azonosítója. |
| ResultType |A runbook-feladat állapota. Lehetséges értékek:<br>– Új<br>- Elindítva<br>- Leállítva<br>- Felfüggesztve<br>- Sikertelen<br>-Befejeződött |
| Kategória | Az adattípus besorolása. Az Automation esetében az érték JobLogs. |
| OperationName | Meghatározza az Azure-ban végrehajtott művelet típusát. Az automatizáláshoz értéke feladat. |
| Erőforrás | Az Automation-fiók neve |
| SourceSystem | Hogyan Naplóelemzési gyűjti az adatokat. Mindig *Azure* az Azure diagnostics. |
| ResultDescription |Ismerteti a runbook-feladat eredményállapotát. Lehetséges értékek:<br>- A feladat elindult<br>- A feladat nem sikerült<br>- A feladat befejeződött |
| CorrelationId |GUID, a runbook-feladat korrelációs azonosítója. |
| ResourceId |A runbook Azure Automation szolgáltatásbeli fiók erőforrás azonosítóját adja meg. |
| SubscriptionId | Az Azure-előfizetés azonosítója (GUID) az Automation-fiókhoz. |
| ResourceGroup | Az erőforráscsoport neve az Automation-fiók. |
| ResourceProvider | MICROSOFT. AUTOMATIZÁLÁS |
| ResourceType | AUTOMATIONACCOUNTS |


### <a name="job-streams"></a>Feladat adatfolyamok
| Tulajdonság | Leírás |
| --- | --- |
| TimeGenerated |A runbook-feladat végrehajtásának dátuma és időpontja. |
| RunbookName_s |A runbook neve. |
| Caller_s |A művelet kezdeményezője. Lehetséges értékek: egy e-mail-cím vagy egy ütemezett feladatokat tartalmazó rendszer. |
| StreamType_s |A feladatstream típusa. Lehetséges értékek:<br>- Folyamatban<br>- Kimenet<br>- Figyelmeztetés<br>- Hiba<br>- Hibakeresés<br>- Részletes |
| Tenant_g | A hívónak a bérlői azonosító GUID. |
| JobId_g |GUID, a runbook-feladat azonosítója. |
| ResultType |A runbook-feladat állapota. Lehetséges értékek:<br>-Folyamatban |
| Kategória | Az adattípus besorolása. Az Automation esetében az érték JobStreams. |
| OperationName | Meghatározza az Azure-ban végrehajtott művelet típusát. Az automatizáláshoz értéke feladat. |
| Erőforrás | Az Automation-fiók neve |
| SourceSystem | Hogyan Naplóelemzési gyűjti az adatokat. Mindig *Azure* az Azure diagnostics. |
| ResultDescription |A runbook kimeneti streamjét tartalmazza. |
| CorrelationId |GUID, a runbook-feladat korrelációs azonosítója. |
| ResourceId |A runbook Azure Automation szolgáltatásbeli fiók erőforrás azonosítóját adja meg. |
| SubscriptionId | Az Azure-előfizetés azonosítója (GUID) az Automation-fiókhoz. |
| ResourceGroup | Az erőforráscsoport neve az Automation-fiók. |
| ResourceProvider | MICROSOFT. AUTOMATIZÁLÁS |
| ResourceType | AUTOMATIONACCOUNTS |

## <a name="viewing-automation-logs-in-log-analytics"></a>A Naplóelemzési bejelentkezik Automation megtekintése
Most, hogy az Automation-feladat naplók küldése Naplóelemzési használatához nézzük meg, mi mindent, ezek a naplók Naplóelemzési belül.

A naplók megtekintéséhez futtassa a következő lekérdezést:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>E-mailt küld, ha egy runbook-feladat sikertelen lesz, vagy felfüggesztése
Az első ügyfél egyik kéri van arra, hogy a szöveg vagy egy e-mailt küldeni, ha probléma merül fel a runbook-feladatok.   

A riasztási szabályt létrehozni, akkor először hozzon létre egy napló keressen rá a runbook feladat azt jelzi, hogy a riasztás kell meghívnia. Kattintson a **riasztás** gombra kattintva hozza létre és konfigurálja a riasztási szabályt.

1. A napló elemzés áttekintése lapon kattintson **naplófájl-keresési**.
2. A riasztás napló keresési lekérdezés létrehozásához írja be a következő keresést a lekérdezés mezőbe: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")` szerint is csoportosíthatók a RunbookName által használatával:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Ha korábban beállított naplók egynél több Automation-fiók vagy előfizetés a munkaterületet, csoportosíthatók a a riasztások előfizetés és az Automation-fiók. Automation-fiók neve JobLogs keresése mezőjében erőforrás található.
1. Lehetőségre a **riasztási szabály hozzáadása** kattintson **riasztási** az oldal tetején. A riasztás konfigurálása lehetőségekről további információkért lásd: [Naplóelemzési riasztások](../log-analytics/log-analytics-alerts.md#alert-rules).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Található összes feladatot, amely hibákkal fejeződött be
Mellett hibák riasztást küld, ha a runbook-feladatok nem okozó hibát tartalmaz található. Ezekben az esetekben PowerShell egy hibafolyam eredményez, de az nem okozó hibák nem okoznak a feladat felfüggesztése, vagy sikertelen.    

1. A Naplóelemzési munkaterületet, kattintson **naplófájl-keresési**.
2. A lekérdezés mezőbe írja be a `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g` , majd a **keresési** gombra.

### <a name="view-job-streams-for-a-job"></a>Nézet feladat adatfolyamok feladat
Ha egy feladat hibakeresése, is érdemes lehet megismerhetők a feladat adatfolyamokat. A következő lekérdezés a GUID 2ebd22ea-e05e-4eb9 – 9d 76-d73cbd4356e0 egyetlen feladat az adatfolyamokat jeleníti meg:   

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Korábbi feladat állapotának megtekintése
Végül érdemes lehet a feladatelőzményekben megjelenítheti az adott idő alatt. Ez a lekérdezés segítségével keresse meg a feladatok állapotának adott idő alatt.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`  
<br> ![OMS korábbi feladat állapota diagram](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="summary"></a>Összefoglalás
Az Automation feladat állapotát és az adatfolyam adatokat küld a Naplóelemzési, által az automatizálási feladatok állapotának jobb betekintést kaphat:
+ Riasztások beállítása értesítést küldenek, ha probléma van.
+ Egyéni nézetei és a keresési lekérdezések segítségével a runbook eredményeinek képi megjelenítése, runbook-feladat állapotát, és egyéb kapcsolódó fő mutatók vagy metrikákat.  

A Naplóelemzési az Automation-feladat működési áttekinthetősége biztosít, és segít a cím incidensek gyorsabb.  

## <a name="next-steps"></a>Következő lépések
* Különböző keresési lekérdezések összeállításához, és tekintse át az Automation-feladat naplók és a Naplóelemzési kapcsolatos további tudnivalókért lásd: [Log Analytics-e jelentkezni a keresések](../log-analytics/log-analytics-log-searches.md).
* Szeretné megtudni, hogyan hozhat létre és runbook-kimenet és a hiba üzeneteket beolvasni, lásd: [Runbook kimenet és üzenetek](automation-runbook-output-and-messages.md).
* A runbook végrehajtásával, a runbook-feladatok figyelésével, illetve az egyéb technikai részletekkel kapcsolatos további tudnivalókat a [Runbook-feladatok nyomon követése](automation-runbook-execution.md) című rész tartalmazza.
* Az OMS szolgáltatáshoz és a gyűjtemény adatforrások kapcsolatos további információkért lásd: [gyűjtése Azure storage adatok a Naplóelemzési – áttekintés](../log-analytics/log-analytics-azure-storage.md).
