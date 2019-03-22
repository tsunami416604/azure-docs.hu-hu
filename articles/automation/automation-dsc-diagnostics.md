---
title: Azure Automation Állapotkonfiguráció számára az Azure Monitor naplóira továbbítása
description: Ez a cikk bemutatja, hogyan küldhet a Desired State Configuration (DSC) az Azure Monitor naplóira Azure Automation Állapotkonfiguráció jelentésadatait, hogy további elemzés és felügyeleti.
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8898280e887392591873f1fc832bfd0c105689fe
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58097286"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-azure-monitor-logs"></a>Azure Automation Állapotkonfiguráció számára az Azure Monitor naplóira továbbítása

Azure Automation konfigurációs csomópont állapota adatait 30 napig őrzi meg.
Ha szeretné megőrizni az adatokat hosszabb ideig, csomópont adatokat küldhet a Log Analytics-munkaterület.
Megfelelőségi állapot jelenik meg az Azure Portalon vagy a PowerShell-lel, csomópontok, és az egyes DSC-erőforrásokhoz a csomópont-konfigurációkat.
Az Azure Monitor-naplók segítségével:

- Megfelelőségi információk lekérése a felügyelt csomópontok és az egyéni erőforrások
- Aktiválja az e-mailben vagy a megfelelőségi állapot alapján riasztása
- Speciális lekérdezések írása a felügyelt csomópontok között
- Megfelelőségi állapot összekapcsolása az Automation-fiókok
- A csomópont megfelelőségi előzmények megjelenítése időbeli alakulása

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Előfeltételek

Az Automation Állapotkonfiguráció jelentések küldése az Azure Monitor naplóira indításához lesz szüksége:

- A November 2016 vagy újabb kiadását [Azure PowerShell-lel](/powershell/azure/overview) (v2.3.0).
- Egy Azure Automation-fiókra. További információkért lásd: [Bevezetés az Azure Automation használatába](automation-offering-get-started.md)
- A Log Analytics-munkaterület- **automatizálás és vezérlés** szolgáltatásajánlat. További információkért lásd: [Ismerkedés az Azure Monitor naplóira](../log-analytics/log-analytics-get-started.md).
- Legalább egy Azure Automation konfigurációs csomópont. További információkért lásd: [gépek előkészítése kezelésre, az Azure Automation állapot konfigurációja](automation-dsc-onboarding.md)

## <a name="set-up-integration-with-azure-monitor-logs"></a>Integráció beállítása az Azure Monitor naplóira

Azure Automation DSC adatok importálása az Azure Monitor naplóira a kezdéshez kövesse az alábbi lépéseket:

1. Jelentkezzen be az Azure PowerShell-fiókjával. Lásd: [bejelentkezés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-4.0.0)
1. Első a _ResourceId_ az automation-fiók az alábbi PowerShell-parancs futtatásával: (Ha egynél több automation-fiókkal rendelkezik, válassza ki a _ResourceID_ a konfigurálni kívánt fiók).

   ```powershell
   # Find the ResourceId for the Automation Account
   Get-AzureRmResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. Első a _ResourceId_ a Log Analytics munkaterület a következő PowerShell-parancs futtatásával: (Ha rendelkezik több munkaterülettel, válassza ki a _ResourceID_ a konfigurálni kívánt munkaterület).

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzureRmResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. Futtassa a következő PowerShell-parancsot, és cserélje le `<AutomationResourceId>` és `<WorkspaceResourceId>` az a _ResourceId_ értékeit az előző lépések:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Categories 'DscNodeStatus'
   ```

Ha szeretné leállítani az Azure Automation konfigurációs adatok importálása az Azure Monitor naplóira, futtassa a következő PowerShell-parancsot:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Categories 'DscNodeStatus'
```

## <a name="view-the-state-configuration-logs"></a>Konfigurációs naplók megtekintése

Az Automation konfigurációs adatok az Azure Monitor naplóira-integráció beállítása után egy **naplóbeli keresés** gomb jelenik meg a **DSC-csomópontok** az automation-fiók panelén. Kattintson a **naplóbeli keresés** gombra kattintva megtekintheti a DSC-csomópont adatait a naplókat.

![Log search gomb](media/automation-dsc-diagnostics/log-search-button.png)

A **naplóbeli keresés** panel megnyílik, és megjelenik egy **DscNodeStatusData** művelet esetében minden egyes konfiguráció csomópontot, és a egy **DscResourceStatusData** egyes művelete[ DSC-erőforrás](/powershell/dsc/resources) meghívta a csomópont-konfigurációt a csomóponton a alkalmazni.

A **DscResourceStatusData** művelet olyan DSC-erőforrások, nem sikerült hiba adatait tartalmazza.

Kattintson az egyes műveletek a listában, hogy a művelet az adatok megtekintéséhez.

A naplók szerint az Azure Monitor-naplókban keresést is megtekintheti.
Lásd: [keresse meg a naplókeresések segítségével adatokat](../log-analytics/log-analytics-log-searches.md).
Írja be a következőt a konfigurációs naplók keresése: `Type=AzureDiagnostics ResourceProvider='MICROSOFT.AUTOMATION' Category='DscNodeStatus'`

A lekérdezés is szűkítheti a művelet neve. Például:`Type=AzureDiagnostics ResourceProvider='MICROSOFT.AUTOMATION' Category='DscNodeStatus' OperationName='DscNodeStatusData'`

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>E-mail küldése, amikor egy konfigurációs megfelelőségének ellenőrzése sikertelen

Az ügyfelek legfontosabb kérelmek egyike arra, hogy küldjön egy e-mailt vagy egy szöveges, ha probléma merül fel a DSC-konfiguráció.

Riasztási szabályt létrehozni, akkor először létrehozni egy naplókeresést, amely a riasztást kell meghívnia Állapotkonfiguráció jelentés rekordjára vonatkozóan. Kattintson a **+ Új riasztási szabály** gombot létrehozni és konfigurálni a riasztási szabályt.

1. A Log Analytics munkaterület áttekintése oldalon kattintson **naplók**.
1. Hozzon létre egy naplóbeli keresési lekérdezés a riasztás a következő keresési beírni a szöveget a lekérdezési mezőtől:  `Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Ha meg van naplók egynél több Automation-fiókot vagy előfizetést a munkaterületre, előfizetése és az Automation-fiók által a riasztásokat csoportosíthatók.  
   Automation-fiók nevét a erőforrás mező DscNodeStatusData keresési származtatható.  
1. Megnyitásához a **létrehozás szabály** kattintson **+ Új riasztási szabály** az oldal tetején. A beállítások konfigurálása a riasztás további információkért lásd: [riasztási szabályt létrehozni](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Az összes csomópont között sikertelen DSC-erőforrások keresése

Az Azure Monitor naplóira használatának egyik előnye az, hogy a csomópontok között sikertelen ellenőrzések kereshet.
DSC-erőforrások, amelyek nem sikerült az összes példányát található.

1. A Log Analytics munkaterület áttekintése oldalon kattintson **naplók**.
1. Hozzon létre egy naplóbeli keresési lekérdezés a riasztás a következő keresési beírni a szöveget a lekérdezési mezőtől:  `Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>Korábbi DSC csomópont állapotának megtekintése

Végül, előfordulhat, hogy szeretné megjeleníteni a DSC-csomópont állapotelőzményei idővel.  
Ez a lekérdezés segítségével keresse meg a DSC-csomópont állapota állapota idővel.

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`  

Ekkor megjelenik egy diagram, a csomópont állapota idővel.

## <a name="azure-monitor-logs-records"></a>Az Azure Monitor rekordok naplózása

Diagnosztika az Azure Automation szolgáltatást két kategóriája rögzíti az Azure Monitor naplóira hoz létre.

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Tulajdonság | Leírás |
| --- | --- |
| TimeGenerated |Dátum és időpont, amikor a megfelelőségi ellenőrzés futtatta. |
| OperationName |DscNodeStatusData |
| ResultType |A csomópont-e megfelelő. |
| NodeName_s |A felügyelt csomópont neve. |
| NodeComplianceStatus_s |A csomópont-e megfelelő. |
| DscReportStatus |Hogy a megfelelőségi ellenőrzés sikeresen lefutott. |
| ConfigurationMode | Hogyan a konfiguráció alkalmazása a csomópontra. Lehetséges értékek a következők __"ApplyOnly"__,__"ApplyandMonitior"__, és __"ApplyandAutoCorrect"__. <ul><li>__ApplyOnly__: DSC konfigurációjának alkalmazására szolgál, és nem módosítja a további, kivéve, ha egy új konfigurációt a célcsomópont, vagy ha egy kiszolgálóról kéri le egy új konfiguráció leküldésekor. Új konfiguráció kezdeti léptetés DSC nem ellenőrzi az előzőleg konfigurált állapotba való eltéréseket. DSC megkísérli a alkalmazni a konfigurációt, egészen addig, amíg a sikeres előtt __ApplyOnly__ lép érvénybe. </li><li> __ApplyAndMonitor__: Ez az alapértelmezett érték. Az LCM vonatkozik minden új konfigurációt. Új konfiguráció, kezdeti alkalmazása után a a célcsomópont drifts a kívánt állapotból, ha DSC jelentések naplók az eltérést. DSC megkísérli a alkalmazni a konfigurációt, egészen addig, amíg a sikeres előtt __ApplyAndMonitor__ lép érvénybe.</li><li>__ApplyAndAutoCorrect__: DSC vonatkozik minden új konfigurációt. Kezdeti alkalmazását követően az új konfiguráció a célcsomópont drifts a kívánt állapotból, ha DSC-jelentések a naplókban az eltérés, és majd újra alkalmazza arra a jelenlegi konfiguráció.</li></ul> |
| HostName_s | A felügyelt csomópont neve. |
| IP-cím | A kezelt csomópontok IPv4-címét. |
| Kategória | DscNodeStatus |
| Erőforrás | Az Azure Automation-fiók neve. |
| Tenant_g | A hívónak a bérlői azonosító GUID. |
| NodeId_g |A kezelt csomópontok azonosító GUID. |
| DscReportId_g |GUID, a jelentés azonosítja. |
| LastSeenTime_t |Dátum és időpont, amikor a jelentés legutóbb megtekintve. |
| ReportStartTime_t |Dátum és időpont, amikor a jelentés lett elindítva. |
| ReportEndTime_t |Dátum és időpont, amikor a jelentés befejeződött. |
| NumberOfResources_d |A DSC-erőforrások számát neve az a csomópont a alkalmazni a konfigurációt. |
| SourceSystem | Hogyan naplózza az Azure Monitor összegyűjti az adatokat. Mindig *Azure* Azure Diagnostics. |
| ResourceId |Itt adható meg az Azure Automation-fiókot. |
| ResultDescription | Ez a művelet leírását. |
| SubscriptionId | Az Azure-előfizetés azonosítóját (GUID) az Automation-fiókhoz. |
| ResourceGroup | Az Automation-fiókot az erőforráscsoport neve. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |GUID, a megfelelőségi jelentés korrelációs azonosítója. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Tulajdonság | Leírás |
| --- | --- |
| TimeGenerated |Dátum és időpont, amikor a megfelelőségi ellenőrzés futtatta. |
| OperationName |DscResourceStatusData|
| ResultType |Az erőforrás-e megfelelő. |
| NodeName_s |A felügyelt csomópont neve. |
| Kategória | DscNodeStatus |
| Erőforrás | Az Azure Automation-fiók neve. |
| Tenant_g | A hívónak a bérlői azonosító GUID. |
| NodeId_g |A kezelt csomópontok azonosító GUID. |
| DscReportId_g |GUID, a jelentés azonosítja. |
| DscResourceId_s |A DSC-erőforrás példány nevét. |
| DscResourceName_s |A DSC-erőforrás neve. |
| DscResourceStatus_s |A DSC-erőforrás-e meg a szabályzatnak. |
| DscModuleName_s |A PowerShell-modult, amely tartalmazza a DSC-erőforrás neve. |
| DscModuleVersion_s |A PowerShell-modult, amely tartalmazza a DSC-erőforrás verzióját. |
| DscConfigurationName_s |A konfigurációt, alkalmazza a csomópont neve. |
| ErrorCode_s | Ha az erőforráshoz nem sikerült. a hibakód. |
| ErrorMessage_s |A hibaüzenet, ha az erőforráshoz nem sikerült. |
| DscResourceDuration_d |Az idő másodpercben, ameddig a DSC-erőforrás futott. |
| SourceSystem | Hogyan naplózza az Azure Monitor összegyűjti az adatokat. Mindig *Azure* Azure Diagnostics. |
| ResourceId |Itt adható meg az Azure Automation-fiókot. |
| ResultDescription | Ez a művelet leírását. |
| SubscriptionId | Az Azure-előfizetés azonosítóját (GUID) az Automation-fiókhoz. |
| ResourceGroup | Az Automation-fiókot az erőforráscsoport neve. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |GUID, a megfelelőségi jelentés korrelációs azonosítója. |

## <a name="summary"></a>Összegzés

Az Automation állapota konfigurációs adatokat küld az Azure Monitor naplóira, állapotát, az Automation Állapotkonfiguráció csomópontok által nagyobb betekintést kaphat:

- Riasztások beállítása arra az esetre, ha a probléma
- Egyéni nézetek és a keresési lekérdezések segítségével a runbook eredményeinek képi megjelenítése, forgatókönyv-feladat állapota, és egyéb kapcsolódó legfontosabb mutatók vagy a metrikákat.  

Az Azure Monitor naplóira áttekinthet működési az Automation konfigurációs adatait, és gyorsabban segíthet cím incidensek.

## <a name="next-steps"></a>További lépések

- Áttekintéséhez lásd: [Azure Automation Állapotkonfiguráció](automation-dsc-overview.md)
- Első lépésként lásd [Ismerkedés az Azure Automation Állapotkonfiguráció](automation-dsc-getting-started.md)
- DSC-konfigurációk fordítása, így hozzárendelheti azokat a célcsomópontokat kapcsolatos további információkért lásd: [összeállítása az Azure Automation Állapotkonfiguráció konfigurációk](automation-dsc-compile.md)
- PowerShell-parancsmagok leírása, lásd: [konfiguráló Azure Automation-parancsmagok](/powershell/module/azurerm.automation/#automation)
- Díjszabási információkért tekintse meg a [Azure Automation State Configuration díjszabása](https://azure.microsoft.com/pricing/details/automation/)
- Folyamatos üzembe helyezés folyamatban lévő Azure Automation Állapotkonfiguráció használatának példájáért lásd: [folyamatos üzembe helyezés használatával az Azure Automation Állapotkonfiguráció és a chocolatey-t](automation-dsc-cd-chocolatey.md)
- Más keresési lekérdezéseket hozhat létre, és tekintse át az Automation állapota konfigurációs naplók az Azure Monitor-naplókkal kapcsolatos további tudnivalókért lásd: [Naplókereséseket a Azure Monitor naplóira](../log-analytics/log-analytics-log-searches.md)
- Az Azure Monitor naplóira és adatgyűjtési forrásokkal kapcsolatos további tudnivalókért lásd: [gyűjtése Azure-tárfiókbeli adatok az Azure monitorban naplók áttekintése](../azure-monitor/platform/collect-azure-metrics-logs.md)
