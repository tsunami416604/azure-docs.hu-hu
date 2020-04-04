---
title: Azure Automation-feladat adatainak továbbítása az Azure Monitor-naplói felé
description: Ez a cikk bemutatja, hogyan küldhet feladat állapot- és runbook-feladatstreameket az Azure Monitor-naplókba további betekintést és felügyeletet biztosít.
services: automation
ms.subservice: process-automation
ms.date: 02/05/2019
ms.topic: conceptual
ms.openlocfilehash: 54f77f55a127cd712d43419eb6a85fd5d93a478c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652176"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>Feladatállapotának és feladatadatfolyamának továbbítása az Automationből az Azure Monitor naplóiba

Az automatizálás runbook-feladatállapotot és feladatstreameket küldhet a Log Analytics-munkaterületre. Ez a folyamat nem jár munkaterület-csatolással, és teljesen független. A feladatnaplók és a feladatstreamek az Azure Portalon vagy a PowerShell ben láthatók az egyes feladatokhoz, és ez lehetővé teszi egyszerű vizsgálatok elvégzését. Most antól az Azure Monitor naplóia:

* Betekintést nyerhet az Automatizálási feladatokba.
* E-mailt vagy riasztást aktivál a runbook-feladat állapota alapján (például sikertelen vagy felfüggesztett).
* Speciális lekérdezéseket írhat a feladatstreamekközött.
* Feladatok korrelálása az Automation-fiókok között.
* A feladatelőzmények megjelenítése az idő múlásával.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-deployment-considerations"></a>Előfeltételek és üzembe helyezési szempontok

Az Automation-naplók Azure Monitor-naplókba küldésének megkezdéséhez a következőkre van szüksége:

* Az Azure [PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)legújabb kiadása.
* Egy Log Analytics-munkaterület. További információ: [Az Azure Monitor naplóinak első lépései című témakörben talál.](../log-analytics/log-analytics-get-started.md)
* Az Azure Automation-fiók erőforrás-azonosítója.

Az alábbi paranccsal megkeresheti az Azure Automation-fiók erőforrás-azonosítóját:

```powershell-interactive
# Find the ResourceId for the Automation Account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

A Log Analytics-munkaterület erőforrás-azonosítójának megkereséséhez futtassa a következő PowerShell-parancsot:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Ha az előző parancsok kimenetében egynél több Automation-fiókkal vagy munkaterülettel rendelkezik, keresse meg a konfigurálandó nevet, és másolja az erőforrás-azonosító értékét.

1. Az Azure Portalon válassza ki az Automation-fiókot az **Automation-fiók** panelen, és válassza a **Minden beállítás**lehetőséget. 
2. A **Minden beállítás** panel **Fiókbeállítások**területén válassza a **Tulajdonságok lehetőséget.**  
3. A **Tulajdonságok** panelen jegyezze fel ezeket az értékeket.<br> ![Az automatizálási fiók tulajdonságai](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).


## <a name="azure-monitor-log-records"></a>Az Azure Monitor naplórekordjai

Az Azure Automation diagnosztikai diagnosztikai adatok kétféle rekordot `AzureDiagnostics`hoznak létre az Azure Monitor naplóiban, amelyek a néven vannak címkézve. A következő szakaszokban található táblák példák az Azure Automation által létrehozott rekordokra és a naplókeresési eredményekben megjelenő adattípusokra.

### <a name="job-logs"></a>Feladatnaplók

| Tulajdonság | Leírás |
| --- | --- |
| TimeGenerated |A runbook-feladat végrehajtásának dátuma és időpontja. |
| RunbookName_s |A runbook neve. |
| Caller_s |A műveletet kezdeményező hívó. Lehetséges értékek: egy e-mail-cím vagy egy ütemezett feladatokat tartalmazó rendszer. |
| Tenant_g | GUID, amely azonosítja a bérlő a hívó. |
| JobId_g |GUID, amely azonosítja a runbook-feladat. |
| ResultType (Eredménytípusa) |A runbook-feladat állapota. Lehetséges értékek:<br>- Új<br>- Létrehozva<br>- Elindítva<br>- Leállítva<br>- Felfüggesztve<br>- Sikertelen<br>- Befejezve |
| Kategória | Az adattípus besorolása. Az Automation esetében az érték JobLogs. |
| OperationName | Az Azure-ban végrehajtott művelet típusa. Az automatizálás esetében az érték A feladat. |
| Erőforrás | Az Automation-fiók neve |
| SourceSystem | Az adatok gyűjtésére használt rendszer, amelyet az Azure Monitor naplói használnak. Az érték mindig az Azure-diagnosztika Azure-diagnosztika. |
| Eredményleírása |A runbook-feladat eredményállapota. Lehetséges értékek:<br>- A feladat elindult<br>- A feladat nem sikerült<br>- A feladat befejeződött |
| CorrelationId |A runbook-feladat korrelációs GUID azonosítója. |
| ResourceId |A runbook Azure Automation-fiók erőforrásazonosítója. |
| SubscriptionId | Az Automation-fiók Azure-előfizetés GUID azonosítója. |
| ResourceGroup | Az Automation-fiók erőforráscsoportjának neve. |
| ResourceProvider | Az erőforrás-szolgáltató. Az érték microsoft. Automatizálás. |
| ResourceType | Az erőforrás típusa. Az érték AUTOMATIONACCOUNTS. |

### <a name="job-streams"></a>Feladatstreamek
| Tulajdonság | Leírás |
| --- | --- |
| TimeGenerated |A runbook-feladat végrehajtásának dátuma és időpontja. |
| RunbookName_s |A runbook neve. |
| Caller_s |A műveletet kezdeményező hívó. Lehetséges értékek: egy e-mail-cím vagy egy ütemezett feladatokat tartalmazó rendszer. |
| StreamType_s |A feladatstream típusa. Lehetséges értékek:<br>- Folyamatban<br>- Kimenet<br>- Figyelmeztetés<br>- Hiba<br>- Hibakeresés<br>- Részletes |
| Tenant_g | GUID, amely azonosítja a bérlő a hívó. |
| JobId_g |GUID, amely azonosítja a runbook-feladat. |
| ResultType (Eredménytípusa) |A runbook-feladat állapota. Lehetséges értékek:<br>- Folyamatban |
| Kategória | Az adattípus besorolása. Az Automation esetében az érték JobStreams. |
| OperationName | Az Azure-ban végrehajtott művelet típusa. Az automatizálás esetében az érték A feladat. |
| Erőforrás | Az Automation-fiók neve. |
| SourceSystem | Az adatok gyűjtésére használt rendszer, amelyet az Azure Monitor naplói használnak. Az érték mindig az Azure-diagnosztika Azure-diagnosztika. |
| Eredményleírása |Leírás, amely tartalmazza a kimeneti adatfolyamot a runbook. |
| CorrelationId |A runbook-feladat korrelációs GUID azonosítója. |
| ResourceId |A runbook Azure Automation-fiók erőforrásazonosítója. |
| SubscriptionId | Az Automation-fiók Azure-előfizetés GUID azonosítója. |
| ResourceGroup | Az Automation-fiók erőforráscsoportjának neve. |
| ResourceProvider | Az erőforrás-szolgáltató. Az érték microsoft. Automatizálás. |
| ResourceType | Az erőforrás típusa. Az érték AUTOMATIONACCOUNTS. |

## <a name="setting-up-integration-with-azure-monitor-logs"></a>Integráció beállítása az Azure Monitor-naplókkal

1. A számítógépen indítsa el a Windows PowerShellt a **kezdőképernyőről.**
2. Futtassa a következő PowerShell-parancsokat, `[your resource ID]` `[resource ID of the log analytics workspace]` és szerkesztheti az előző szakasz és az értékek értékét.

   ```powershell-interactive
   $workspaceId = "[resource ID of the log analytics workspace]"
   $automationAccountId = "[resource ID of your Automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

A parancsfájl futtatása után egy órát is igénybe vehet, mielőtt megkezdené az új vagy `JobLogs` `JobStreams` írás alatt álló rekordok megtekintését az Azure Monitor naplóiban.

A naplók megtekintéséhez futtassa a következő lekérdezést a naplóelemzési naplókeresésben:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Konfiguráció ellenőrzése

Annak ellenőrzéséhez, hogy az Automation-fiók naplókat küld-e a Log Analytics-munkaterületre, ellenőrizze, hogy a diagnosztika megfelelően van-e konfigurálva az Automation-fiókban a következő PowerShell-parancs használatával.

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

A kimenetben győződjön meg arról, hogy:

* A `Logs`területen az `Enabled` érték igaz.
* `WorkspaceId`a Log `ResourceId` Analytics-munkaterület értékére van beállítva.

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>Automatizálási naplók megtekintése az Azure Monitor naplóiban

Most, hogy elkezdte elküldeni az Automation-feladat naplók at Azure Monitor naplók, nézzük meg, mit tehet ezekkel a naplókat az Azure Monitor naplókban.

A naplók megtekintéséhez futtassa a következő lekérdezést:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>E-mail küldése, ha egy runbook-feladat meghibásodik vagy felfüggesztésre kerül

Az egyik legnépszerűbb ügyfél kéri, hogy képes-e e-mailt vagy szöveget küldeni, ha valami elromlik egy runbook feladat.

Riasztási szabály létrehozásához először hozzon létre egy naplókeresést a runbook-feladat rekordok, amelyek meg kell hívnia a riasztást. A **riasztási** szabály létrehozásához és konfigurálásához kattintson a Riasztás gombra.

1. A Log Analytics munkaterület – áttekintés lapon kattintson a **Naplók megtekintése gombra.**
2. Naplókeresési lekérdezés létrehozása a riasztáshoz a következő keresés beírásával a lekérdezésmezőbe:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>A runbook neve szerint is csoportosíthat a következők használatával:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Ha több Automation-fiókból vagy előfizetésből állít be naplókat a munkaterületre, az értesítéseket előfizetés és Automation-fiók szerint csoportosíthatja. Az automatizálási fiók neve `Resource` a mezőben `JobLogs`található a keresésben.
3. A **Szabály létrehozása** képernyő megnyitásához kattintson a lap tetején a + **Új riasztási szabály** elemre. A riasztás konfigurálásának lehetőségeiről a Riasztások naplózása az [Azure-ban](../azure-monitor/platform/alerts-unified-log.md)című témakörben talál további információt.

### <a name="find-all-jobs-that-have-completed-with-errors"></a>A hibákkal befejezett összes feladat megkeresése

A hibákriasztás mellett azt is megkeresheti, ha egy runbook-feladat nem végződési hibát észlel. Ezekben az esetekben a PowerShell létrehoz egy hibastreamet, de a nem végződési hibák nem eredményezik a feladat felfüggesztését vagy sikertelenvé.

1. A Log Analytics-munkaterületen kattintson a **Naplók gombra.**
2. A lekérdezés mezőbe `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g`írja be a következőt: a .
3. Kattintson a **Keresés** gombra.

### <a name="view-job-streams-for-a-job"></a>Feladatadatfolyamok megtekintése egy feladathoz

Feladat hibakeresésekénél érdemes lehet a feladatstreameket is megvizsgálni. A következő lekérdezés a 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0 GUID azonosítóval rendelkező összes adatfolyamot megjeleníti:

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Korábbi feladat állapotának megtekintése

Végül érdemes lehet idővel elképzelni a feladatelőzményeket. Ezzel a lekérdezéssel idővel megkeresheti a feladatok állapotát.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Log Analytics korábbi feladatállapot-diagramja](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="removing-diagnostic-settings"></a>Diagnosztikai beállítások eltávolítása

A diagnosztikai beállítás automation-fiókból való eltávolításához futtassa a következő parancsot:

```powershell-interactive
$automationAccountId = "[resource ID of your Automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```

## <a name="summary"></a>Összefoglalás

Az Automation-feladat állapotának elküldésével és az Adatok Nak az Azure Monitor-naplókba való streamelésével jobb betekintést nyerhet az Automation-feladatok állapotába:
+ Riasztások beállítása, hogy értesítse, ha probléma merül fel.
+ Egyéni nézetek és keresési lekérdezések használatával vizualizálja a Runbook-eredményeket, a runbook-feladat állapotát és más kapcsolódó kulcsmutatókat vagy mutatókat.

Az Azure Monitor naplói nagyobb működési láthatóságot biztosítnak az Automation-feladatokszámára, és gyorsabban kezelik az incidenseket.

## <a name="next-steps"></a>További lépések

* A Log Analytics hibaelhárításával kapcsolatos segítségért olvassa el [A LogAnalytics adatgyűjtésének hibaelhárítása](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data)című témakört.
* Ha többet szeretne tudni arról, hogyan hozhat létre különböző keresési lekérdezéseket, és hogyan tekintheti át az Automation-feladatnaplókat az Azure Monitor naplóival, olvassa el [a Keresésnaplózás az Azure Monitor naplóiban című témakört.](../log-analytics/log-analytics-log-searches.md)
* A kimeneti és hibaüzenetek runbookokból való létrehozásáról és lekéréséről a [Runbook kimenetés e-mailben című](automation-runbook-output-and-messages.md)témakörben van.
* A runbook végrehajtásával, a runbook-feladatok figyelésével, illetve az egyéb technikai részletekkel kapcsolatos további tudnivalókat a [Runbook-feladatok nyomon követése](automation-runbook-execution.md) című rész tartalmazza.
* Ha többet szeretne megtudni az Azure Monitor naplóiról és adatgyűjtési forrásairól, olvassa [el az Azure Storage-adatok gyűjtése az Azure Monitor naplóinak áttekintése című témakört.](../azure-monitor/platform/collect-azure-metrics-logs.md)

