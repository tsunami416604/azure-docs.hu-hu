---
title: Azure Automation DSC Log Analyticshez való jelentéskészítéshez szükséges adatok továbbítása
description: Ez a cikk bemutatja, hogyan küldhetők szükséges konfiguráló (DSC) jelentéskészítéshez szükséges adatok további elemzéséhez és a felügyeleti szolgáltatáshoz.
services: automation
ms.service: automation
ms.component: dsc
author: georgewallace
ms.author: gwallace
ms.date: 06/12/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 822d0e285e6f1cc9907625d7928dff3d9bf66921
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36218955"
---
# <a name="forward-azure-automation-dsc-reporting-data-to-log-analytics"></a>Azure Automation DSC Log Analyticshez való jelentéskészítéshez szükséges adatok továbbítása

Automation DSC csomópont állapota adatokat küldhet a Naplóelemzési munkaterület.  
Megfelelőségi állapota látható az Azure portálon, vagy a PowerShell használatával, a csomópontok számára, és az egyedi DSC erőforrások a csomópont-konfigurációt. Log Analytics segítségével:

* A megfelelőségi adatok lekérése a felügyelt csomópontok és az egyes erőforrások
* Indítás, egy e-mailek vagy a riasztás a megfelelőségi állapot alapján
* Speciális lekérdezéseket írhat a felügyelt csomópontokon
* Megfelelőségi állapot összefüggéseket Automation-fiókok között
* A csomópont megfelelőségi előzményei megjelenítheti az adott idő alatt

## <a name="prerequisites"></a>Előfeltételek

Indítsa el az Automation DSC-jelentéseket küld a Naplóelemzési, az alábbiak szükségesek:

* A November 2016 vagy újabb kiadása [Azure PowerShell](/powershell/azure/overview) (v2.3.0).
* Egy Azure Automation-fiókra. További információkért lásd: [Ismerkedés az Azure Automation szolgáltatással](automation-offering-get-started.md)
* A Naplóelemzési munkaterület egy **Automation & vezérlő** szolgáltatásajánlat. További információkért lásd: [Ismerkedés a Naplóelemzési](../log-analytics/log-analytics-get-started.md).
* Legalább egy Azure Automation DSC-csomópont. További információkért lásd: [bevezetési gépeket Azure Automation DSC általi kezelésre](automation-dsc-onboarding.md)

## <a name="set-up-integration-with-log-analytics"></a>Log Analytics-integráció beállítása

Adatok importálása az Azure Automation DSC Log Analyticshez való elindításához kövesse az alábbi lépéseket:

1. Jelentkezzen be az Azure PowerShell-fiókjával. Lásd: [jelentkezzen be az Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-4.0.0)
1. Beolvasása a _ResourceId_ , az automation-fiók a következő PowerShell-parancs futtatásával: (Ha egynél több automation-fiók, válassza a _ResourceID_ a konfigurálni kívánt fiókhoz).

  ```powershell
  # Find the ResourceId for the Automation Account
  Find-AzureRmResource -ResourceType "Microsoft.Automation/automationAccounts"
  ```
1. Beolvasása a _ResourceId_ a Naplóelemzési munkaterület a következő PowerShell-parancs futtatásával: (Ha egynél több munkaterületen, válassza a _ResourceID_ a konfigurálni kívánt munkaterület).

  ```powershell
  # Find the ResourceId for the Log Analytics workspace
  Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces"
  ```
1. Futtassa a következő PowerShell-parancsot cseréje `<AutomationResourceId>` és `<WorkspaceResourceId>` rendelkező a _ResourceId_ értékeit az előző lépéseket:

  ```powershell
  Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Categories "DscNodeStatus"
  ```

Ha le kívánja állítani a adatok importálását az Azure Automation DSC Log Analyticshez való, futtassa a következő PowerShell-parancsot.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Categories "DscNodeStatus"
```

## <a name="view-the-dsc-logs"></a>A DSC-naplók megtekintése

Az Automation DSC adatok Log Analyticshez való integráció beállítása után a **naplófájl-keresési** gomb megjelenik a **DSC-csomópontok** panelen található az automation-fiók. Kattintson a **naplófájl-keresési** gombra kattintva megtekintheti a DSC-csomópont adatait a naplókat.

![Napló Keresés gomb](media/automation-dsc-diagnostics/log-search-button.png)

A **naplófájl-keresési** panel nyílik meg, és megjelenik egy **DscNodeStatusData** művelete minden DSC-csomópont, és egy **DscResourceStatusData** minden művelet [DSC erőforrás](https://msdn.microsoft.com/powershell/dsc/resources) adott csomóponton alkalmazott a csomópont-konfigurációnak a neve.

A **DscResourceStatusData** művelet nem sikerült DSC erőforrásokat hiba adatait tartalmazza.

Kattintson az adott műveletre vonatkozó adatok megjelenítéséhez a listában lévő egyes műveletek.

A naplók megtekintéséhez által [Log Analyticshez megkeresése. Lásd: [található adatokat, és napló keresések](../log-analytics/log-analytics-log-searches.md).
Írja be a következő lekérdezés futtatásával a DSC-napló keresése: `Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category = "DscNodeStatus"`

A művelet neve is szűkítheti a lekérdezést. For example: `Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category = "DscNodeStatus" OperationName = "DscNodeStatusData"

### <a name="send-an-email-when-a-dsc-compliance-check-fails"></a>E-mailt küld, ha a DSC-megfelelőségi ellenőrzés sikertelen lesz.

A felső ügyfelek kéréseire egyik arra, hogy a szöveg vagy egy e-mailt küldeni, ha probléma merül fel a DSC-konfiguráció.   

A riasztási szabályt létrehozni, akkor először hozzon létre egy napló keressen rá a DSC jelentés azt jelzi, hogy a riasztás kell meghívnia.  Kattintson a **+ új riasztást szabály** gombra kattintva hozza létre és konfigurálja a riasztási szabályt.

1. A napló elemzés áttekintése lapon kattintson **naplófájl-keresési**.
1. A riasztás napló keresési lekérdezés létrehozásához írja be a következő keresést a lekérdezés mezőbe:  `Type=AzureDiagnostics Category=DscNodeStatus NodeName_s=DSCTEST1 OperationName=DscNodeStatusData ResultType=Failed`

  Ha állított be naplók egynél több Automation-fiók vagy előfizetés a munkaterületet, csoportosíthatók a a riasztások előfizetés és az Automation-fiók.  
  Automation-fiók nevét a DscNodeStatusData keresése erőforrás mezője származtatható.  
1. Lehetőségre a **létrehozás szabály** kattintson **+ Új riasztási szabály** az oldal tetején. A riasztás konfigurálása lehetőségekről további információkért lásd: [hozzon létre egy riasztási rulelert](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Sikertelen a DSC-erőforrások található összes csomópont

Egy Naplóelemzési előnye, hogy a csomópontok közötti sikertelen ellenőrzést kereshet.
Találja DSC-erőforrások, melyeknél nem sikerült az összes példányát.

1. A napló elemzés áttekintése lapon kattintson **naplófájl-keresési**.
1. A riasztás napló keresési lekérdezés létrehozásához írja be a következő keresést a lekérdezés mezőbe:  `Type=AzureDiagnostics Category=DscNodeStatus OperationName=DscResourceStatusData ResultType=Failed`

### <a name="view-historical-dsc-node-status"></a>Korábbi DSC csomópont állapotának megtekintése

Végül érdemes lehet a DSC-csomópont állapotelőzmény megjelenítheti az adott idő alatt.  
Ez a lekérdezés segítségével keresse meg a DSC-csomópont állapotát állapotának adott idő alatt.

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`  

A csomópont állapota a diagram megjeleníti adott idő alatt.

## <a name="log-analytics-records"></a>Log Analytics-rekordok

Azure Automation diagnosztika rekordok két kategóriába Naplóelemzési hoz létre.

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Tulajdonság | Leírás |
| --- | --- |
| TimeGenerated |Dátum és idő, amikor a megfelelőségi ellenőrzés futtatta. |
| OperationName |DscNodeStatusData |
| ResultType |Hogy a csomópont nem megfelelő. |
| NodeName_s |A felügyelt csomópont neve. |
| NodeComplianceStatus_s |Hogy a csomópont nem megfelelő. |
| DscReportStatus |Ellenőrizze, hogy a megfelelőségi sikeresen lefutott. |
| ConfigurationMode | A konfiguráció alkalmazásának a módját a csomópontra. A lehetséges értékek: __"ApplyOnly"__,__"ApplyandMonitior"__, és __"ApplyandAutoCorrect"__. <ul><li>__ApplyOnly__: DSC konfigurációjának alkalmazására szolgál, és nincs semmi hatása további, kivéve, ha az új konfiguráció célcsomóponton, vagy ha egy kiszolgáló új konfigurációt van lekért fejlesztőre. Az új konfiguráció első alkalmazása után DSC nem ellenőrzi a korábban konfigurált állapotból eltéréseket. A konfiguráció alkalmazásához, amíg az nem lesz sikeres, mielőtt megpróbálja DSC __ApplyOnly__ lép érvénybe. </li><li> __ApplyAndMonitor__: Ez az az alapértelmezett érték. A LCM alkalmazza minden új konfigurációt. Az új konfiguráció első alkalmazása után a célcsomóponton drifts kívánt állapotból, ha DSC jelent a naplókban az eltérés. A konfiguráció alkalmazásához, amíg az nem lesz sikeres, mielőtt megpróbálja DSC __ApplyAndMonitor__ lép érvénybe.</li><li>__ApplyAndAutoCorrect__: DSC alkalmazza minden új konfigurációt. Az új konfiguráció első alkalmazása után a célcsomópont drifts kívánt állapotból, ha DSC jelent a naplókban az eltérés, és majd újra alkalmazza a jelenlegi konfiguráció.</li></ul> |
| HostName_s | A felügyelt csomópont neve. |
| IP-cím | A felügyelt csomóponthoz IPv4-címét. |
| Kategória | DscNodeStatus |
| Erőforrás | Az Azure Automation-fiók neve. |
| Tenant_g | A hívónak a bérlői azonosító GUID. |
| NodeId_g |A felügyelt csomóponthoz azonosító GUID. |
| DscReportId_g |A jelentés azonosító GUID. |
| LastSeenTime_t |Dátum és idő, amikor a jelentés utolsó tekinthetők. |
| ReportStartTime_t |Dátum és idő, amikor a jelentés elindítása. |
| ReportEndTime_t |Dátum és idő, amikor a jelentés befejeződött. |
| NumberOfResources_d |A konfiguráció a csomóponton alkalmazott nevű DSC erőforrások száma. |
| SourceSystem | Hogyan Naplóelemzési gyűjti az adatokat. Mindig *Azure* az Azure diagnostics. |
| ResourceId |Adja meg az Azure Automation-fiók. |
| ResultDescription | Ez a művelet leírását. |
| SubscriptionId | Az Azure-előfizetés azonosítója (GUID) az Automation-fiókhoz. |
| ResourceGroup | Az erőforráscsoport neve az Automation-fiók. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |A megfelelőségi jelentés korrelációs azonosítója GUID. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Tulajdonság | Leírás |
| --- | --- |
| TimeGenerated |Dátum és idő, amikor a megfelelőségi ellenőrzés futtatta. |
| OperationName |DscResourceStatusData|
| ResultType |Az erőforrás-e megfelelő. |
| NodeName_s |A felügyelt csomópont neve. |
| Kategória | DscNodeStatus |
| Erőforrás | Az Azure Automation-fiók neve. |
| Tenant_g | A hívónak a bérlői azonosító GUID. |
| NodeId_g |A felügyelt csomóponthoz azonosító GUID. |
| DscReportId_g |A jelentés azonosító GUID. |
| DscResourceId_s |A DSC erőforráspéldány neve. |
| DscResourceName_s |A DSC-erőforrás nevét. |
| DscResourceStatus_s |A DSC-erőforrás e megfelel a szabályzatnak. |
| DscModuleName_s |A PowerShell-modult, amely tartalmazza a DSC-erőforrás neve. |
| DscModuleVersion_s |A PowerShell-modult, amely tartalmazza a DSC-erőforrás verzióját. |
| DscConfigurationName_s |A csomóponton alkalmazott konfiguráció neve. |
| ErrorCode_s | Ha az erőforrás nem sikerült a hibakód. |
| ErrorMessage_s |A hibaüzenet, ha az erőforrás nem sikerült. |
| DscResourceDuration_d |Az idő másodpercben, ameddig a DSC-erőforrás futott. |
| SourceSystem | Hogyan Naplóelemzési gyűjti az adatokat. Mindig *Azure* az Azure diagnostics. |
| ResourceId |Adja meg az Azure Automation-fiók. |
| ResultDescription | Ez a művelet leírását. |
| SubscriptionId | Az Azure-előfizetés azonosítója (GUID) az Automation-fiókhoz. |
| ResourceGroup | Az erőforráscsoport neve az Automation-fiók. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |A megfelelőségi jelentés korrelációs azonosítója GUID. |

## <a name="summary"></a>Összegzés

Az Automation DSC adatokat küld a Naplóelemzési, az Automation DSC-csomópontok által állapotának jobb betekintést kaphat:

* Értesítést küldenek, ha probléma van a riasztások beállítása
* Egyéni nézetei és a keresési lekérdezések segítségével a runbook eredményeinek képi megjelenítése, runbook-feladat állapotát, és egyéb kapcsolódó fő mutatók vagy metrikákat.  

A Naplóelemzési az Automation DSC adatait működési áttekinthetősége biztosít, és gyorsabban segít a cím incidensek.  

## <a name="next-steps"></a>További lépések

* Különböző keresési lekérdezések összeállításához, és tekintse át a Automation DSC a Naplóelemzési kapcsolatos további tudnivalókért lásd: [Log Analytics-e jelentkezni a keresések](../log-analytics/log-analytics-log-searches.md)
* Azure Automation DSC használatával kapcsolatos további tudnivalókért lásd: [Ismerkedés az Azure Automation DSC](automation-dsc-getting-started.md)
* Log Analytics és a gyűjtemény adatforrások kapcsolatos további információkért lásd: [gyűjtése Azure storage adatok a Naplóelemzési – áttekintés](../log-analytics/log-analytics-azure-storage.md)

