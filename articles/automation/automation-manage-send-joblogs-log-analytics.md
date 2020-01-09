---
title: Azure Automation-feladat adatainak továbbítása az Azure Monitor-naplói felé
description: Ez a cikk azt ismerteti, hogyan lehet elküldeni a feladatok állapotát és a runbook, hogy Azure Monitor naplókat a további betekintés és felügyelet biztosításához.
services: automation
ms.subservice: process-automation
ms.date: 02/05/2019
ms.topic: conceptual
ms.openlocfilehash: beb69edc57b5a13db0f6d2e5e1536804f3472aff
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75421912"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>Feladatok állapotának és a feladatok adatfolyamának továbbítása az Automationből Azure Monitor naplókba

Az Automation elküldheti a runbook feladatok állapotát és a feladatok streamjét a Log Analytics munkaterületre. Ez a folyamat nem jár a munkaterület összekapcsolásával, és teljesen független. A feladatok naplói és a feladatok adatfolyamai láthatók a Azure Portalban, vagy a PowerShell-lel az egyes feladatokhoz, és ez lehetővé teszi az egyszerű vizsgálatok végrehajtását. A Azure Monitor naplók mostantól a következőket teheti:

* Információk szerzése az Automation-feladatokról.
* E-mail vagy riasztás elindítása a runbook-feladatok állapota (például sikertelen vagy felfüggesztett) alapján.
* Speciális lekérdezések futtatása több feladatstreamre.
* Feladatok korrelációjának megállapítása több Automation-fiókra kiterjedően.
* Feladatelőzmények időbeli megjelenítése.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-deployment-considerations"></a>Előfeltételek és üzembe helyezési megfontolások

Az Automation-naplók Azure Monitor naplókba való küldésének megkezdéséhez a következőkre lesz szüksége:

* [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)legújabb kiadása.
* Egy Log Analytics-munkaterület. További információ: Ismerkedés [a Azure monitor-naplókkal](../log-analytics/log-analytics-get-started.md).
* A Azure Automation-fiók ResourceId.

A Azure Automation fiók ResourceId megkeresése:

```powershell-interactive
# Find the ResourceId for the Automation Account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

A Log Analytics munkaterülethez tartozó ResourceId megkereséséhez futtassa a következő PowerShell-t:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Ha több Automation-fiókkal vagy-munkaterülettel rendelkezik, az előző parancsok kimenetében keresse meg a konfigurálni kívánt *nevet* , és másolja ki a *ResourceId*értékét.

Ha meg kell találnia az Automation-fiókja *nevét* , a Azure Portal válassza ki az Automation-fiókját az **Automation-fiók** panelen, és válassza a **minden beállítás**lehetőséget. A **Minden beállítás** panel **Fiókbeállítások** részénél válassza a **Tulajdonságok** lehetőséget.  A **Tulajdonságok** panelen megtalálja a keresett értékeket.<br> ![Automation-fiók tulajdonságai](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Integráció beállítása Azure Monitor naplókhoz

1. A számítógépen indítsa el a **Windows PowerShellt** a **kezdőképernyőn** .
2. Futtassa a következő PowerShellt, és szerkessze a `[your resource id]` értékét, és `[resource id of the log analytics workspace]` az előző lépés értékeivel.

   ```powershell-interactive
   $workspaceId = "[resource id of the log analytics workspace]"
   $automationAccountId = "[resource id of your automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

A szkript futtatása után egy óráig is eltarthat, mielőtt megkezdi a rekordok megtekintését Azure Monitor az új JobLogs vagy JobStreams naplójában.

A naplók megjelenítéséhez futtassa a következő lekérdezést a log Analytics naplójának keresésében: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Konfiguráció ellenőrzése

Annak ellenőrzéséhez, hogy az Automation-fiókja naplókat küld a Log Analytics munkaterületre, ellenőrizze, hogy a diagnosztika megfelelően van-e konfigurálva az Automation-fiókban a következő PowerShell használatával:

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

A kimenetben ügyeljen arra, hogy:

* A *naplók*területen az *engedélyezve* érték értéke *true (igaz*).
* A *munkaterület azonosítója* értéke a log Analytics munkaterület ResourceId van beállítva.

## <a name="azure-monitor-log-records"></a>Naplóbejegyzések Azure Monitor

Azure Automation diagnosztika két típusú rekordot hoz létre Azure Monitor naplókban, és **AzureDiagnostics**van megjelölve. A következő lekérdezések a frissített lekérdezési nyelvet használják a naplók Azure Monitor. Az örökölt lekérdezési nyelv és az új Azure Kusto lekérdezési nyelv közötti gyakori lekérdezésekkel kapcsolatos információkért tekintse meg a [régebbi verziókat az Azure Kusto Query Language-adatlap](https://docs.loganalytics.io/docs/Learn/References/Legacy-to-new-to-Azure-Log-Analytics-Language)

### <a name="job-logs"></a>Feladatok naplói

| Tulajdonság | Leírás |
| --- | --- |
| TimeGenerated |A runbook-feladat végrehajtásának dátuma és időpontja. |
| RunbookName_s |A runbook neve. |
| Caller_s |A művelet kezdeményezője. Lehetséges értékek: egy e-mail-cím vagy egy ütemezett feladatokat tartalmazó rendszer. |
| Tenant_g | A hívó bérlőjét azonosító GUID. |
| JobId_g |GUID, a runbook-feladat azonosítója. |
| ResultType |A runbook-feladat állapota. Lehetséges értékek:<br>– Új<br>-Létrehozva<br>- Elindítva<br>- Leállítva<br>- Felfüggesztve<br>- Sikertelen<br>-Befejezett |
| Kategória | Az adattípus besorolása. Az Automation esetében az érték JobLogs. |
| OperationName | Meghatározza az Azure-ban végrehajtott művelet típusát. Az Automation esetében az érték a job. |
| Erőforrás | Az Automation-fiók neve |
| SourceSystem | Hogyan gyűjtöttük össze az adatokat Azure Monitor naplókat. Mindig *Azure* az Azure Diagnostics szolgáltatáshoz. |
| ResultDescription |Ismerteti a runbook-feladat eredményállapotát. Lehetséges értékek:<br>- A feladat elindult<br>- A feladat nem sikerült<br>- A feladat befejeződött |
| CorrelationId |GUID, a runbook-feladat korrelációs azonosítója. |
| ResourceId |Megadja a runbook Azure Automation fiókjának erőforrás-azonosítóját. |
| SubscriptionId | Az Automation-fiókhoz tartozó Azure-előfizetés azonosítója (GUID). |
| ResourceGroup | Az Automation-fiókhoz tartozó erőforráscsoport neve. |
| ResourceProvider | Microsoft. AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |


### <a name="job-streams"></a>Feladatok adatfolyamai
| Tulajdonság | Leírás |
| --- | --- |
| TimeGenerated |A runbook-feladat végrehajtásának dátuma és időpontja. |
| RunbookName_s |A runbook neve. |
| Caller_s |A művelet kezdeményezője. Lehetséges értékek: egy e-mail-cím vagy egy ütemezett feladatokat tartalmazó rendszer. |
| StreamType_s |A feladatstream típusa. Lehetséges értékek:<br>\- Folyamatban<br>- Kimenet<br>- Figyelmeztetés<br>- Hiba<br>- Hibakeresés<br>- Részletes |
| Tenant_g | A hívó bérlőjét azonosító GUID. |
| JobId_g |GUID, a runbook-feladat azonosítója. |
| ResultType |A runbook-feladat állapota. Lehetséges értékek:<br>– Folyamatban |
| Kategória | Az adattípus besorolása. Az Automation esetében az érték JobStreams. |
| OperationName | Meghatározza az Azure-ban végrehajtott művelet típusát. Az Automation esetében az érték a job. |
| Erőforrás | Az Automation-fiók neve |
| SourceSystem | Hogyan gyűjtöttük össze az adatokat Azure Monitor naplókat. Mindig *Azure* az Azure Diagnostics szolgáltatáshoz. |
| ResultDescription |A runbook kimeneti streamjét tartalmazza. |
| CorrelationId |GUID, a runbook-feladat korrelációs azonosítója. |
| ResourceId |Megadja a runbook Azure Automation fiókjának erőforrás-azonosítóját. |
| SubscriptionId | Az Automation-fiókhoz tartozó Azure-előfizetés azonosítója (GUID). |
| ResourceGroup | Az Automation-fiókhoz tartozó erőforráscsoport neve. |
| ResourceProvider | Microsoft. AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>Az Automation-naplók megtekintése Azure Monitor naplókban

Most, hogy elindította az Automation-feladatok naplóinak küldését Azure Monitor naplókra, lássuk, mit tehet a naplókban a Azure Monitor-naplókon belül.

A naplók megtekintéséhez futtassa a következő lekérdezést: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>E-mail küldése runbook-feladatok sikertelensége vagy felfüggesztése esetén
Az egyik legfelső szintű ügyfél arra kéri, hogy küldjön egy e-mailt vagy szöveget, ha valami nem stimmel egy runbook-feladatokkal.

Riasztási szabály létrehozásához először létre kell hoznia egy naplót a runbook-feladatokhoz, amelyeknek meg kell hívniuk a riasztást. A riasztási szabály létrehozásához és konfigurálásához kattintson a **riasztás** gombra.

1. A Log Analytics munkaterület áttekintés lapján kattintson a **naplók megtekintése**elemre.
2. Hozzon létre egy naplóbeli keresési lekérdezést a riasztáshoz, ehhez írja be a következő keresést a lekérdezés mezőbe: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")` a RunbookName az alábbi paranccsal is csoportosíthatja: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Ha több Automation-fiókból vagy-előfizetésből állít be naplókat a munkaterületre, a riasztásokat az előfizetés és az Automation-fiók alapján csoportosíthatja. Az Automation-fiók neve megtalálható a JobLogs keresésének erőforrás mezőjében.
3. A **szabály létrehozása** képernyő megnyitásához kattintson az **+ új riasztási szabály** elemre az oldal tetején. A riasztás konfigurálásával kapcsolatos további információkért lásd: [riasztások naplózása az Azure-ban](../azure-monitor/platform/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>A hibákkal befejezett összes feladat megkeresése
A hibákkal kapcsolatos riasztások mellett megkeresheti, hogy egy runbook-feladatokhoz ne kelljen megszakítást okozó hiba. Ezekben az esetekben a PowerShell hibát okozó adatfolyamot hoz létre, de a megszakítás nélküli hibák miatt a feladat felfüggesztése vagy meghibásodása sikertelen lesz.

1. A Log Analytics munkaterületen kattintson a **naplók**elemre.
2. A lekérdezés mezőbe írja be a `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g`, majd kattintson a **Keresés** gombra.

### <a name="view-job-streams-for-a-job"></a>Feladatokhoz tartozó feladatok adatfolyamának megtekintése
A feladatok hibakeresése során érdemes megtekinteni a feladatok folyamait is. A következő lekérdezés a GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0 rendelkező egyetlen feladatokhoz tartozó összes adatfolyamot megjeleníti:

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Korábbi feladatok állapotának megtekintése
Végezetül érdemes lehet megjeleníteni a feladatok előzményeit az idő múlásával. Ezt a lekérdezést használhatja a feladatok állapotának megkeresésére az idő múlásával.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Log Analytics korábbi feladatok állapotának diagramja](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="remove-diagnostic-settings"></a>Diagnosztikai beállítások eltávolítása

Ha el szeretné távolítani a diagnosztikai beállítást az Automation-fiókból, futtassa a következő parancsokat:

```powershell-interactive
$automationAccountId = "[resource id of your automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```

## <a name="summary"></a>Összefoglalás

Az Automation-feladatok állapotának és az adatfolyam-adatok Azure Monitor naplókba küldésével jobban megismerheti az automatizálási feladatai állapotát:
+ Riasztások beállítása, amelyekkel értesítést küldhet, ha probléma merül fel.
+ Egyéni nézetek és keresési lekérdezések használatával megjelenítheti a runbook eredményeit, a runbook-feladatok állapotát, valamint az egyéb kapcsolódó kulcsfontosságú mutatókat vagy metrikákat.

Azure Monitor naplók nagyobb működési láthatóságot biztosítanak az automatizálási feladatokhoz, és gyorsabban segíthetnek az incidensek kezelésében.

## <a name="next-steps"></a>Következő lépések

* A Log Analytics hibaelhárításával kapcsolatban lásd: [Hibaelhárítás, hogy a log Analytics miért nem gyűjti az adatgyűjtést](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data).
* Ha többet szeretne megtudni a különböző keresési lekérdezések létrehozásáról és az Automation-feladatok naplóinak Azure Monitor-naplókkal való áttekintéséről, tekintse meg a következő témakört: [keresések Azure monitor naplókban](../log-analytics/log-analytics-log-searches.md).
* A runbookok származó kimeneti és hibaüzenetek létrehozásáról és lekéréséről lásd: [Runbook output and messages](automation-runbook-output-and-messages.md).
* A runbook végrehajtásával, a runbook-feladatok figyelésével, illetve az egyéb technikai részletekkel kapcsolatos további tudnivalókat a [Runbook-feladatok nyomon követése](automation-runbook-execution.md) című rész tartalmazza.
* Ha többet szeretne megtudni Azure Monitor a naplókról és az adatgyűjtési forrásokról, tekintse meg [Az Azure Storage-adatok gyűjtése Azure monitor naplók áttekintését](../azure-monitor/platform/collect-azure-metrics-logs.md)ismertető cikket.

