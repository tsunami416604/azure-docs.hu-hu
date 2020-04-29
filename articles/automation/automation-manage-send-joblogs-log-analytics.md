---
title: Azure Automation-feladat adatainak továbbítása az Azure Monitor-naplói felé
description: Ez a cikk azt ismerteti, hogyan lehet elküldeni a feladatok állapotát és a runbook, hogy Azure Monitor naplókat a további betekintés és felügyelet biztosításához.
services: automation
ms.subservice: process-automation
ms.date: 02/05/2019
ms.topic: conceptual
ms.openlocfilehash: a9f4e641e60d6cf1c481c445767422e8b4df683b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457688"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>Feladatok állapotának és a feladatok adatfolyamának továbbítása az Automationből Azure Monitor naplókba

Az Automation elküldheti a runbook feladatok állapotát és a feladatok streamjét a Log Analytics munkaterületre. Ez a folyamat nem jár a munkaterület összekapcsolásával, és teljesen független. A feladatok naplói és a feladatok adatfolyamai láthatók a Azure Portalban, vagy a PowerShell-lel az egyes feladatokhoz, és ez lehetővé teszi az egyszerű vizsgálatok végrehajtását. A Azure Monitor naplók mostantól a következőket teheti:

* Betekintést nyerhet az Automation-feladatok állapotával.
* E-mail vagy riasztás elindítása a runbook-feladatok állapota (például sikertelen vagy felfüggesztett) alapján.
* Speciális lekérdezések írása a feladatok adatfolyamai között.
* A feladatok összekapcsolása az Automation-fiókok között.
* Egyéni nézeteket és keresési lekérdezéseket használhat a runbook eredményeinek, a runbook-feladatok állapotának, valamint az egyéb kapcsolódó kulcsfontosságú mutatók vagy mérőszámok megjelenítéséhez.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az az modul telepítési útmutatója a hibrid Runbook-feldolgozón: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Az Automation-fiók esetében a modulokat a legújabb verzióra frissítheti a [Azure Automation Azure PowerShell moduljainak frissítésével](automation-update-azure-modules.md).

## <a name="prerequisites-and-deployment-considerations"></a>Előfeltételek és üzembe helyezési megfontolások

Az Automation-naplók Azure Monitor naplókba való küldésének megkezdéséhez a következőkre lesz szüksége:

* [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)legújabb kiadása.
* Egy Log Analytics-munkaterület. További információ: Ismerkedés [a Azure monitor-naplókkal](../log-analytics/log-analytics-get-started.md).
* A Azure Automation-fiók erőforrás-azonosítója.

Használja az alábbi parancsot a Azure Automation-fiók erőforrás-AZONOSÍTÓjának megkereséséhez:

```powershell-interactive
# Find the ResourceId for the Automation account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

A Log Analytics munkaterület erőforrás-AZONOSÍTÓjának megkereséséhez futtassa a következő PowerShell-parancsot:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Ha több Automation-fiókkal vagy-munkaterülettel rendelkezik az előző parancsok kimenetében, keresse meg az erőforrás-azonosító értékének konfigurálásához és másolásához szükséges nevet.

1. A Azure Portal válassza ki az Automation-fiókját az **Automation-fiók** panelen, és válassza a **minden beállítás**lehetőséget. 
2. A **minden beállítás** panel **Fiókbeállítások**területén válassza a **Tulajdonságok**elemet.  
3. A **Tulajdonságok** panelen jegyezze fel az alább látható tulajdonságokat.

    ![Automation-fiók tulajdonságai](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="azure-monitor-log-records"></a>Naplóbejegyzések Azure Monitor

Azure Automation diagnosztika két típusú rekordot hoz létre a Azure Monitor naplókban, címkézve `AzureDiagnostics`. A következő részekben szereplő táblázatok a Azure Automation által generált és a naplók keresési eredményei között megjelenő adattípusokra vonatkozó példák.

### <a name="job-logs"></a>Feladatnaplók

| Tulajdonság | Leírás |
| --- | --- |
| TimeGenerated |A runbook-feladat végrehajtásának dátuma és időpontja. |
| RunbookName_s |A runbook neve. |
| Caller_s |A műveletet kezdeményező hívó. Lehetséges értékek: egy e-mail-cím vagy egy ütemezett feladatokat tartalmazó rendszer. |
| Tenant_g | A hívó bérlőjét azonosító GUID. |
| JobId_g |A runbook-feladatot azonosító GUID. |
| ResultType |A runbook-feladat állapota. Lehetséges értékek:<br>– Új<br>-Létrehozva<br>- Elindítva<br>- Leállítva<br>- Felfüggesztve<br>- Sikertelen<br>-Befejezett |
| Kategória | Az adattípus besorolása. Az Automation esetében az érték JobLogs. |
| OperationName | Az Azure-ban végrehajtott művelet típusa. Az Automation esetében az érték a job. |
| Erőforrás | Az Automation-fiók neve |
| SourceSystem | A rendszer, amely Azure Monitor naplózza az adatok gyűjtését. Az érték mindig Azure az Azure Diagnostics szolgáltatáshoz. |
| ResultDescription |A runbook-feladatok eredményének állapota. Lehetséges értékek:<br>- A feladat elindult<br>- A feladat nem sikerült<br>- A feladat befejeződött |
| CorrelationId |A runbook-feladatokhoz tartozó korrelációs GUID |
| ResourceId |A runbook Azure Automation fiókjának erőforrás-azonosítója. |
| SubscriptionId | Az Automation-fiókhoz tartozó Azure-előfizetés GUID-azonosítója. |
| ResourceGroup | Az Automation-fiókhoz tartozó erőforráscsoport neve. |
| ResourceProvider | Az erőforrás-szolgáltató. Az érték a MICROSOFT. Automation. |
| ResourceType | Az erőforrás típusa. Az érték a AUTOMATIONACCOUNTS. |

### <a name="job-streams"></a>Feladatstreamek
| Tulajdonság | Leírás |
| --- | --- |
| TimeGenerated |A runbook-feladat végrehajtásának dátuma és időpontja. |
| RunbookName_s |A runbook neve. |
| Caller_s |A műveletet kezdeményező hívó. Lehetséges értékek: egy e-mail-cím vagy egy ütemezett feladatokat tartalmazó rendszer. |
| StreamType_s |A feladatstream típusa. Lehetséges értékek:<br>- Folyamatban<br>- Kimenet<br>- Figyelmeztetés<br>- Hiba<br>- Hibakeresés<br>- Részletes |
| Tenant_g | A hívó bérlőjét azonosító GUID. |
| JobId_g |A runbook-feladatot azonosító GUID. |
| ResultType |A runbook-feladat állapota. Lehetséges értékek:<br>– Folyamatban |
| Kategória | Az adattípus besorolása. Az Automation esetében az érték JobStreams. |
| OperationName | Az Azure-ban végrehajtott művelet típusa. Az Automation esetében az érték a job. |
| Erőforrás | Az Automation-fiók neve. |
| SourceSystem | A rendszer, amely Azure Monitor naplózza az adatok gyűjtését. Az érték mindig Azure az Azure Diagnostics szolgáltatáshoz. |
| ResultDescription |A runbook kimeneti streamjét tartalmazó leírás. |
| CorrelationId |A runbook-feladatokhoz tartozó korrelációs GUID |
| ResourceId |A runbook Azure Automation fiókjának erőforrás-azonosítója. |
| SubscriptionId | Az Automation-fiókhoz tartozó Azure-előfizetés GUID-azonosítója. |
| ResourceGroup | Az Automation-fiókhoz tartozó erőforráscsoport neve. |
| ResourceProvider | Az erőforrás-szolgáltató. Az érték a MICROSOFT. Automation. |
| ResourceType | Az erőforrás típusa. Az érték a AUTOMATIONACCOUNTS. |

## <a name="setting-up-integration-with-azure-monitor-logs"></a>Azure Monitor naplókkal való integráció beállítása

1. A számítógépen indítsa el a Windows PowerShellt a **kezdőképernyőn** .
2. Futtassa a következő PowerShell-parancsokat, és szerkessze a `[your resource ID]` és `[resource ID of the log analytics workspace]` az értékeket az előző szakasz értékeivel.

   ```powershell-interactive
   $workspaceId = "[resource ID of the log analytics workspace]"
   $automationAccountId = "[resource ID of your Automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

A szkript futtatása után egy órát is igénybe vehet, mielőtt megkezdi a rekordok megjelenítését az új `JobLogs` vagy `JobStreams` megírt naplók Azure monitor.

A naplók megtekintéséhez futtassa a következő lekérdezést a log Analytics naplójának keresésében:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Konfiguráció ellenőrzése

Annak ellenőrzéséhez, hogy az Automation-fiókja naplókat küld a Log Analytics munkaterületre, ellenőrizze, hogy a diagnosztika megfelelően van-e konfigurálva az Automation-fiókon az alábbi PowerShell-parancs használatával.

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

A kimenetben győződjön meg a következőket:

* A `Logs`alatt a értéke true `Enabled` (igaz).
* `WorkspaceId`a Log Analytics munkaterület `ResourceId` értékére van beállítva.

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>Az Automation-naplók megtekintése Azure Monitor naplókban

Most, hogy elindította az Automation-feladatok naplóinak küldését Azure Monitor naplókra, lássuk, mit tehet a naplókban a Azure Monitor-naplókon belül.

A naplók megtekintéséhez futtassa a következő lekérdezést:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>E-mail küldése runbook-feladatok sikertelensége vagy felfüggesztése esetén

Az egyik legfelső szintű ügyfél arra kéri, hogy küldjön egy e-mailt vagy szöveget, ha valami nem stimmel egy runbook-feladatokkal.

Riasztási szabály létrehozásához először hozzon létre egy naplót a runbook-feladatokhoz, amelyeknek meg kell hívniuk a riasztást. A riasztási szabály létrehozásához és konfigurálásához kattintson a **riasztás** gombra.

1. A Log Analytics munkaterület áttekintés lapján kattintson a **naplók megtekintése**elemre.
2. Hozzon létre egy naplóbeli keresési lekérdezést a riasztáshoz úgy, hogy beírja a következő keresést a lekérdezés mezőbe:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>A runbook neve a használatával is csoportosítható:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Ha több Automation-fiókból vagy-előfizetésből állít be naplókat a munkaterületre, a riasztásokat az előfizetés és az Automation-fiók alapján csoportosíthatja. Az Automation-fiók neve a keresés `Resource` mezőjében található. `JobLogs`
3. A **szabály létrehozása** képernyő megnyitásához kattintson az oldal tetején található **új riasztási szabály** elemre. A riasztás konfigurálásával kapcsolatos további információkért lásd: [riasztások naplózása az Azure-ban](../azure-monitor/platform/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>A hibákkal befejezett összes feladat megkeresése

A hibákkal kapcsolatos riasztások mellett megkeresheti, hogy egy runbook-feladatokhoz ne kelljen megszakítást okozó hiba. Ezekben az esetekben a PowerShell egy hibát okozó adatfolyamot hoz létre, de a megszakítás nélküli hibák miatt a feladat felfüggesztése vagy meghibásodása sikertelen lesz.

1. A Log Analytics munkaterületen kattintson a **naplók**elemre.
2. A lekérdezés mezőbe írja be a `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g`következőt:.
3. Kattintson a **Keresés** gombra.

### <a name="view-job-streams-for-a-job"></a>Feladatokhoz tartozó feladatok adatfolyamának megtekintése

A feladatok hibakeresése során érdemes megtekinteni a feladatok folyamait is. A következő lekérdezés a GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0 rendelkező egyetlen feladatokhoz tartozó összes adatfolyamot megjeleníti:

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Korábbi feladatok állapotának megtekintése

Végezetül érdemes lehet megjeleníteni a feladatok előzményeit az idő múlásával. Ezt a lekérdezést használhatja a feladatok állapotának megkeresésére az idő múlásával.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Log Analytics korábbi feladatok állapotának diagramja](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="removing-diagnostic-settings"></a>Diagnosztikai beállítások eltávolítása

Ha el szeretné távolítani a diagnosztikai beállítást az Automation-fiókból, futtassa a következő parancsot:

```powershell-interactive
$automationAccountId = "[resource ID of your Automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```
## <a name="next-steps"></a>További lépések

* A Log Analytics hibaelhárításával kapcsolatban lásd: [Hibaelhárítás, hogy a log Analytics miért nem gyűjti az adatgyűjtést](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data).
* Ha többet szeretne megtudni a különböző keresési lekérdezések létrehozásáról és az Automation-feladatok naplóinak Azure Monitor-naplókkal való áttekintéséről, tekintse meg a következő témakört: [keresések Azure monitor naplókban](../log-analytics/log-analytics-log-searches.md).
* A runbookok származó kimeneti és hibaüzenetek létrehozásáról és lekéréséről lásd: [Runbook output and messages](automation-runbook-output-and-messages.md).
* A runbook végrehajtásával, a runbook-feladatok figyelésével, illetve az egyéb technikai részletekkel kapcsolatos további tudnivalókat a [Runbook-feladatok nyomon követése](automation-runbook-execution.md) című rész tartalmazza.
* Ha többet szeretne megtudni Azure Monitor a naplókról és az adatgyűjtési forrásokról, tekintse meg [Az Azure Storage-adatok gyűjtése Azure monitor naplók áttekintését](../azure-monitor/platform/collect-azure-metrics-logs.md)ismertető cikket.