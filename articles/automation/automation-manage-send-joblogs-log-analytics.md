---
title: "Azure Automation-feladat adatok továbbítására OMS szolgáltatáshoz |} Microsoft Docs"
description: "Ez a cikk bemutatja, hogyan küldhet feladat állapotát és a runbook feladat adatfolyamokat a Microsoft Operations Management Suite Log Analytics képes biztosítani a további betekintést és kezelése."
services: automation
documentationcenter: 
author: eslesar
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
ms.openlocfilehash: 21923adaa8f8118995799319c1fd496a6e449faa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="forward-job-status-and-job-streams-from-automation-to-log-analytics-oms"></a>Továbbítsa feladat állapotát és a feladat adatfolyamok Automation való Naplóelemzés (OMS)
Automatizálási küldhet runbook feladat állapotát és a feladat adatfolyamokat a Microsoft Operations Management Suite (OMS) Naplóelemzési munkaterületet.  Feladat naplózza, és a feladat adatfolyamok láthatók az Azure portálon, vagy a PowerShell használatával, az egyes feladatokat, és ez lehetővé teszi egyszerű vizsgálatok végrehajtását. Most Log Analytics segítségével:

* Az automatizálási feladatok insight letöltése
* A runbook-feladat állapota (például felfüggesztett vagy sikertelen) alapuló riasztás vagy e-mail eseményindító
* Speciális lekérdezéseket írhat a feladat adatfolyamok között
* Feladatok összefüggéseket Automation-fiókok között
* A feladatelőzmények megjelenítheti az adott idő alatt     

## <a name="prerequisites-and-deployment-considerations"></a>Előfeltételek és központi telepítésével kapcsolatos megfontolások
Az automatizálási naplók értesítésküldés Naplóelemzési, az alábbiak szükségesek:

1. A November 2016 vagy újabb kiadása [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) (v2.3.0).
2. A Naplóelemzési munkaterület. További információkért lásd: [Ismerkedés a Naplóelemzési](../log-analytics/log-analytics-get-started.md). 
3. Az erőforrás-azonosítója az Azure Automation-fiókhoz

Az erőforrás-azonosítója az Azure Automation-fiók és a Naplóelemzési munkaterület megkereséséhez, futtassa a következő PowerShell:

```powershell
# Find the ResourceId for the Automation Account
Find-AzureRmResource -ResourceType "Microsoft.Automation/automationAccounts"

# Find the ResourceId for the Log Analytics workspace
Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Ha több Automation-fiókot, vagy a munkaterületek között, az előző parancs kimenetében található a *neve* meg kell konfigurálnia, és másolja a következő *ResourceId*.

Ha meg szeretné tudni a *neve* az Automation-fiók, az Azure portálon válassza ki az Automation-fiók a a **Automation-fiók** panelhez, és válassza **összes beállítás**.  A **Minden beállítás** panel **Fiókbeállítások** részénél válassza a **Tulajdonságok** lehetőséget.  A **Tulajdonságok** panelen megtalálja a keresett értékeket.<br> ![Automation-fiók tulajdonságai](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="set-up-integration-with-log-analytics"></a>Log Analytics-integráció beállítása
1. A számítógépen indítása **Windows PowerShell** a a **Start** képernyő.  
2. Másolja és illessze be a következő PowerShell és az érték szerkesztése a `$workspaceId` és `$automationAccountId`.  Az a `-Environment` paraméter érvényes értékei a *AzureCloud* vagy *AzureUSGovernment* attól függően, hogy a felhő környezet használata.     

```powershell
[cmdletBinding()]
    Param
    (
        [Parameter(Mandatory=$True)]
        [ValidateSet("AzureCloud","AzureUSGovernment")]
        [string]$Environment="AzureCloud"
    )

#Check to see which cloud environment to sign into.
Switch ($Environment)
   {
       "AzureCloud" {Login-AzureRmAccount}
       "AzureUSGovernment" {Login-AzureRmAccount -EnvironmentName AzureUSGovernment} 
   }

# if you have one Log Analytics workspace you can use the following command to get the resource id of the workspace
$workspaceId = (Get-AzureRmOperationalInsightsWorkspace).ResourceId

$automationAccountId = "/SUBSCRIPTIONS/ec11ca67-1234-421e-5678-c25/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.AUTOMATION/ACCOUNTS/DEMO" 

Set-AzureRmDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled $true

```

A parancsfájl futtatása után látni fogja az Log Analytics-rekordok új JobLogs vagy írása JobStreams 10 percen belül.

A naplók megtekintéséhez futtassa a következő lekérdezés Naplóelemzési napló keresése:`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Konfiguráció ellenőrzése
Annak ellenőrzéséhez, hogy az Automation-fiók naplókat küld a Naplóelemzési munkaterületet, ellenőrizze, hogy diagnosztika helyesen van-e beállítva a következő PowerShell-lel az Automation-fiókhoz:

```powershell
[cmdletBinding()]
    Param
    (
        [Parameter(Mandatory=$True)]
        [ValidateSet("AzureCloud","AzureUSGovernment")]
        [string]$Environment="AzureCloud"
    )

#Check to see which cloud environment to sign into.
Switch ($Environment)
   {
       "AzureCloud" {Login-AzureRmAccount}
       "AzureUSGovernment" {Login-AzureRmAccount -EnvironmentName AzureUSGovernment} 
   }
# if you have one Log Analytics workspace you can use the following command to get the resource id of the workspace
$workspaceId = (Get-AzureRmOperationalInsightsWorkspace).ResourceId

$automationAccountId = "/SUBSCRIPTIONS/ec11ca67-1234-421e-5678-c25/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.AUTOMATION/ACCOUNTS/DEMO" 

Get-AzureRmDiagnosticSetting -ResourceId $automationAccountId
```

A kimenet győződjön meg arról, hogy:
+ A *naplók*, értéke *engedélyezve* van *igaz*
+ Értékének *WorkspaceId* az erőforrás-azonosítója a Naplóelemzési munkaterület beállítása


## <a name="log-analytics-records"></a>Log Analytics-rekordok
Azure Automation diagnosztika kétféle típusú rekordok Naplóelemzési hoz létre, és címkével rendelkeznek, **típus = AzureDiagnostics**.

### <a name="job-logs"></a>Feladatnaplóit
| Tulajdonság | Leírás |
| --- | --- |
| TimeGenerated |A runbook-feladat végrehajtásának dátuma és időpontja. |
| RunbookName_s |A runbook neve. |
| Caller_s |A művelet kezdeményezője.  Lehetséges értékek: egy e-mail-cím vagy egy ütemezett feladatokat tartalmazó rendszer. |
| Tenant_g | A hívónak a bérlői azonosító GUID. |
| JobId_g |GUID, a runbook-feladat azonosítója. |
| ResultType |A runbook-feladat állapota.  Lehetséges értékek:<br>– Új<br>- Elindítva<br>- Leállítva<br>- Felfüggesztve<br>- Sikertelen<br>-Befejeződött |
| Kategória | Az adattípus besorolása.  Az Automation esetében az érték JobLogs. |
| OperationName | Meghatározza az Azure-ban végrehajtott művelet típusát.  Az automatizáláshoz értéke feladat. |
| Erőforrás | Az Automation-fiók neve |
| SourceSystem | Hogyan Naplóelemzési gyűjti az adatokat. Mindig *Azure* az Azure diagnostics. |
| ResultDescription |Ismerteti a runbook-feladat eredményállapotát.  Lehetséges értékek:<br>- A feladat elindult<br>- A feladat nem sikerült<br>- A feladat befejeződött |
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
| Caller_s |A művelet kezdeményezője.  Lehetséges értékek: egy e-mail-cím vagy egy ütemezett feladatokat tartalmazó rendszer. |
| StreamType_s |A feladatstream típusa. Lehetséges értékek:<br>- Folyamatban<br>- Kimenet<br>- Figyelmeztetés<br>- Hiba<br>- Hibakeresés<br>- Részletes |
| Tenant_g | A hívónak a bérlői azonosító GUID. |
| JobId_g |GUID, a runbook-feladat azonosítója. |
| ResultType |A runbook-feladat állapota.  Lehetséges értékek:<br>-Folyamatban |
| Kategória | Az adattípus besorolása.  Az Automation esetében az érték JobStreams. |
| OperationName | Meghatározza az Azure-ban végrehajtott művelet típusát.  Az automatizáláshoz értéke feladat. |
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
Most, hogy használatba vette az Automation-feladat naplók küldése Naplóelemzési, nézzük meg, mi mindent, ezek a naplók Naplóelemzési belül.

A naplók megtekintéséhez futtassa a következő lekérdezést:`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>E-mailt küld, ha egy runbook-feladat sikertelen lesz, vagy felfüggesztése
Egy felső ügyfelünk kéri van arra, hogy a szöveg vagy egy e-mailt küldeni, ha probléma merül fel a runbook-feladatok.   

A riasztási szabályt létrehozni, akkor először hozzon létre egy napló keressen rá a runbook feladat azt jelzi, hogy a riasztás kell meghívnia.  Kattintson a **riasztás** gombra kattintva hozza létre és konfigurálja a riasztási szabályt.

1. A napló elemzés áttekintése lapon kattintson **naplófájl-keresési**.
2. A riasztás napló keresési lekérdezés létrehozásához írja be a következő keresést a lekérdezés mezőbe: `Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobLogs (ResultType=Failed OR ResultType=Suspended)` szerint is csoportosíthatók a RunbookName által használatával:`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobLogs (ResultType=Failed OR ResultType=Suspended) | measure Count() by RunbookName_s`   

   Ha állított be naplók egynél több Automation-fiók vagy előfizetés a munkaterületet, csoportosíthatók a a riasztások előfizetés és az Automation-fiók.  Automation-fiók nevét a JobLogs keresése erőforrás mezője származtatható.  
3. Lehetőségre a **riasztási szabály hozzáadása** kattintson **riasztási** az oldal tetején. További beállítások konfigurálása a riasztás további információkért lásd: [Naplóelemzési riasztások](../log-analytics/log-analytics-alerts.md#alert-rules).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Található összes feladatot, amely hibákkal fejeződött be
Mellett hibák riasztást küld, ha a runbook-feladatok nem okozó hibát tartalmaz található. Ezekben az esetekben PowerShell egy hibafolyam eredményez, de a nem okozó hibákat nem indítják el a feladat felfüggesztése, vagy sikertelen.    

1. A Naplóelemzési munkaterületet, kattintson **naplófájl-keresési**.
2. A lekérdezés mezőbe írja be a `Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobStreams StreamType_s=Error | measure count() by JobId_g` majd **keresési**.

### <a name="view-job-streams-for-a-job"></a>Nézet feladat adatfolyamok feladat
Ha egy feladat hibakeresés is érdemes lehet megismerhetők a feladat adatfolyamokat.  A következő lekérdezés a GUID 2ebd22ea-e05e-4eb9 – 9d 76-d73cbd4356e0 egyetlen feladat az adatfolyamokat jeleníti meg:   

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobStreams JobId_g="2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort TimeGenerated | select ResultDescription`

### <a name="view-historical-job-status"></a>Korábbi feladat állapotának megtekintése
Végül érdemes lehet a feladatelőzményekben megjelenítheti az adott idő alatt.  Ez a lekérdezés segítségével keresse meg a feladatok állapotának adott idő alatt.

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobLogs NOT(ResultType="started") | measure Count() by ResultType interval 1hour`  
<br> ![OMS korábbi feladat állapota diagram](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="summary"></a>Összefoglalás
Az Automation feladat állapotát és az adatfolyam adatokat küld a Naplóelemzési, által az automatizálási feladatok állapotának jobb betekintést kaphat:
+ Értesítést küldenek, ha probléma van a riasztások beállítása
+ Egyéni nézetei és a keresési lekérdezések segítségével a runbook eredményeinek képi megjelenítése, runbook-feladat állapotát, és egyéb kapcsolódó fő mutatók vagy metrikákat.  

A Naplóelemzési az Automation-feladat működési áttekinthetősége biztosít, és segít a cím incidensek gyorsabb.  

## <a name="next-steps"></a>Következő lépések
* A különböző keresési lekérdezések összeállításával és az Automation-feladatnaplók Log Analytics-szel történő megtekintésével kapcsolatos további tudnivalókat a [Naplókeresések a Log Analytics-ben](../log-analytics/log-analytics-log-searches.md) című rész tartalmazza
* Szeretné megtudni, hogyan hozhat létre és runbook-kimenet és a hiba üzeneteket beolvasni, lásd: [Runbook kimenet és üzenetek](automation-runbook-output-and-messages.md)
* A runbook végrehajtásával, a runbook-feladatok figyelésével, illetve az egyéb technikai részletekkel kapcsolatos további tudnivalókat a [Runbook-feladatok nyomon követése](automation-runbook-execution.md) című rész tartalmazza
* Az OMS Log Analytics használatával és adatgyűjtési forrásokkal kapcsolatos további tudnivalókat lásd: az [Azure-tárfiókbeli adatok Log Analytics-ben történő gyűjtésének az áttekintése](../log-analytics/log-analytics-azure-storage.md)
