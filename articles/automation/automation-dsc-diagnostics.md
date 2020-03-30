---
title: Az Azure Automation állapotkonfigurációs jelentési adatainak továbbítása az Azure Monitor naplóiba
description: Ez a cikk bemutatja, hogyan küldhet ida-konfigurációs (DSC) jelentési adatokat az Azure Automation-állapot konfigurációjáról az Azure Monitor naplóiba, hogy további betekintést és felügyeletet biztosítson.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 578fcf4cd03a2d4fc8400b9e84f53206750a588c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77430720"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-azure-monitor-logs"></a>Az Azure Automation állapotkonfigurációs jelentési adatainak továbbítása az Azure Monitor naplóiba

Az Azure Automation State Configuration 30 napig őrzi meg a csomópont állapotadatait.
A csomópont állapotadatait elküldheti a Log Analytics-munkaterületre, ha ezeket az adatokat hosszabb ideig szeretné megőrizni.
Megfelelőségi állapot látható az Azure Portalon vagy a PowerShell, a csomópontok és az egyes DSC-erőforrások csomópontkonfigurációkban.
Az Azure Monitor naplóival a következőket teheti:

- Megfelelőségi információk beszerezni a felügyelt csomópontokat és az egyes erőforrásokat
- E-mail vagy riasztás aktiválása a megfelelőségi állapot alapján
- Speciális lekérdezések írása a felügyelt csomópontokközött
- Megfelelőségi állapot korrelálása az Automation-fiókok között
- A csomópont-megfelelőségi előzmények megjelenítése az idő múlásával

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Előfeltételek

Az Automation-állapot konfigurációs jelentéseinek az Azure Monitor-naplókba való elküldésének megkezdéséhez a következőkre van szüksége:

- Az [Azure PowerShell](/powershell/azure/overview) 2016 novemberi vagy későbbi kiadása (2.3.0-s verzió).
- Egy Azure Automation-fiókra. További információ: [Az Azure Automation bemutatkozása.](automation-intro.md)
- Log Analytics-munkaterület Automation & Control szolgáltatásajánlattal. További információ: [A Log Analytics használata az Azure Monitorban](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)című témakörben talál.
- Legalább egy Azure Automation-állapotkonfigurációs csomópont. További információ: [Bevezetési gépek az Azure Automation állapotkonfigurációja általi kezeléshez.](automation-dsc-onboarding.md)
- Az [xDscDiagnostics](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0) modul, 2.7.0.0-s vagy újabb verzió. A telepítési lépéseket az [Azure Automation kívánt állapotkonfigurációja hibaelhárítása című témakörben tésiterületen.](./troubleshoot/desired-state-configuration.md)

## <a name="set-up-integration-with-azure-monitor-logs"></a>Integráció beállítása az Azure Monitor-naplókkal

Ha meg szeretné kezdeni az adatok importálását az Azure Automation DSC-ből az Azure Monitor naplóiba, hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be Azure-fiókjába a PowerShellben. Lásd: [Bejelentkezés az Azure PowerShell használatával.](https://docs.microsoft.com/powershell/azure/authenticate-azureps)
1. Az Automation-fiók erőforrás-azonosítóját a következő PowerShell-parancsmag futtatásával szerezheti be. Ha egynél több automatizálási fiókkal rendelkezik, válassza ki a konfigurálni kívánt fiók erőforrás-azonosítóját.

   ```powershell
   # Find the ResourceId for the Automation account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. A Következő PowerShell-parancsmag futtatásával szerezheti le a Log Analytics-munkaterület erőforrás-azonosítóját. Ha egynél több munkaterülettel rendelkezik, válassza ki a konfigurálni kívánt munkaterület erőforrásazonosítót.

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. Futtassa a következő PowerShell-parancsmaszt, és `<AutomationResourceId>` `<WorkspaceResourceId>` az egyes előző lépések *ResourceId* értékeit.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

1. Ha le szeretné állítani az Azure Automation-állapot konfigurációjából származó adatok importálását az Azure Monitor naplóiba, futtassa a következő PowerShell-parancsmaszt.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
   ```

## <a name="view-the-state-configuration-logs"></a>Az állapotkonfigurációs naplók megtekintése

Miután beállította az integrációt az Azure Monitor-naplók az Automation State Configuration adatok, megtekintheti őket a **Naplók** a **Figyelés** szakaszban a bal oldali ablaktáblában az állapotkonfigurációs (DSC) lap.

![Naplók](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

A **Naplókeresés** ablaktábla megnyílik egy lekérdezési terület hatóköre az Automation-fiók erőforrás. Az Állapotkonfigurációs naplókban a DSC-műveleteket az Azure Monitor-naplókban való kereséssel keresheti meg. A DSC-műveletek rekordjai az AzureDiagnostics táblában tárolódnak. Ha például nem megfelelő csomópontokat szeretne keresni, írja be a következő lekérdezést.

```AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```
Szűrésrészletei:

* A *DscNodeStatusData* szűrése az egyes állapotkonfigurációs csomópontok műveleteinek visszaadásához.
* A *DscResourceStatusData* szűrésével az erőforrásra alkalmazott csomópontkonfigurációban meghívott dsc-erőforrások műveleteit adja vissza. 
* A *DscResourceStatusData* szűrésével hibaüzenetet ad vissza a sikertelen DSC-erőforrásokhoz.

Ha többet szeretne tudni az adatok keresésére irányuló naplólekérdezések létrehozásáról, [olvassa el a naplólekérdezések áttekintése az Azure Monitorban című témakört.](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>E-mail küldése, ha egy állapotkonfigurációmegfelelőség-ellenőrzés sikertelen

Az egyik legfontosabb ügyfélkérésünk az, hogy e-mailt vagy sms-t küldjünk, ha valami elromlik a DSC konfigurációval.

Riasztási szabály létrehozásához először hozzon létre egy naplókeresést az Állapotkonfiguráció jelentésrekordokra, amelyeknek meg kell hívniuk a riasztást. A **figyelmeztetési szabály** létrehozásához és konfigurálásához kattintson a + Új riasztási szabály gombra.

1. A Log Analytics munkaterület – áttekintés lapon kattintson a **Naplók gombra.**
1. Naplókeresési lekérdezés létrehozása a riasztáshoz a következő keresés beírásával a lekérdezésmezőbe:`Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Ha több Automation-fiókból vagy a munkaterületre való előfizetésből állított be naplókat, az értesítéseket előfizetési és Automation-fiók szerint csoportosíthatja. A DscNodeStatusData keresése során az Erőforrás mezőből származtathatja az Automation-fiók nevét.
1. A **Szabály létrehozása** képernyő megnyitásához kattintson a lap tetején a + **Új riasztási szabály** elemre. 

A riasztás konfigurálásának lehetőségeiről a [Riasztási szabály létrehozása című](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)témakörben talál további információt.

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Sikertelen DSC-erőforrások keresése az összes csomóponton

Az Azure Monitor-naplók használatának egyik előnye, hogy sikertelen ellenőrzéseket kereshet a csomópontok között.
A sikertelen DSC-erőforrások összes példányának megkeresése:

1. A Log Analytics munkaterület – áttekintés e lapon kattintson a **Naplók gombra.**
1. Naplókeresési lekérdezés létrehozása a riasztáshoz a következő keresés beírásával a lekérdezésmezőbe:`Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>A DSC-csomópont korábbi állapotának megtekintése

A DSC-csomópont állapotelőzményeinek idővel történő megjelenítéséhez használja ezt a lekérdezést:

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

Ez a lekérdezés a csomópont állapotának diagramját jeleníti meg az idő múlásával.

## <a name="azure-monitor-logs-records"></a>Az Azure Monitor naplózza a rekordokat

Az Azure Automation-diagnosztika két rekordkategóriát hoz létre az Azure Monitor naplóiban:

* Csomópont állapotadatai (DscNodeStatusData)
* Erőforrás állapotadatai (DscResourceStatusData)

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Tulajdonság | Leírás |
| --- | --- |
| TimeGenerated |A megfelelőségi ellenőrzés letöltésének dátuma és időpontja. |
| OperationName |DscNodeStatusData. |
| ResultType (Eredménytípusa) |Azt jelzi, hogy a csomópont megfelelő-e. |
| NodeName_s |A kezelt csomópont neve. |
| NodeComplianceStatus_s |Azt jelzi, hogy a csomópont megfelelő-e. |
| DscReportStatus |Azt jelzi, hogy a megfelelőségi ellenőrzés sikeresen lefutott-e. |
| ConfigurationMode (Konfigurációs mód) | A konfiguráció alkalmazásának van a csomópontra. Lehetséges értékek: <ul><li>*ApplyOnly*: A DSC alkalmazza a konfigurációt, és nem tesz tovább semmit, kivéve, ha egy új konfiguráció tolt a célcsomópontra, vagy ha egy új konfigurációt kér le egy kiszolgálóról. Az új konfiguráció kezdeti alkalmazása után a DSC nem ellenőrzi a korábban konfigurált állapotból való eltolódást. A DSC addig próbálja alkalmazni a konfigurációt, amíg sikeres nem lesz, mielőtt az *ApplyOnly* érték érvénybe lépne. </li><li>*ApplyAndMonitor*: Ez az alapértelmezett érték. Az LCM minden új konfigurációt alkalmaz. Egy új konfiguráció kezdeti alkalmazása után, ha a célcsomópont a kívánt állapottól elsodródik, a DSC jelenti az eltérést a naplókban. A DSC addig próbálja alkalmazni a konfigurációt, amíg az sikeres nem lesz, mielőtt az *ApplyAndMonitor* érték érvénybe lépne.</li><li>*ApplyAndAutoCorrect*: A DSC minden új konfigurációt alkalmaz. Egy új konfiguráció kezdeti alkalmazása után, ha a célcsomópont a kívánt állapotból sodródik, a DSC jelenti az eltérést a naplókban, majd újra alkalmazza az aktuális konfigurációt.</li></ul> |
| HostName_s | A kezelt csomópont neve. |
| IPAddress | A felügyelt csomópont IPv4-címe. |
| Kategória | DscNodeStatus. |
| Erőforrás | Az Azure Automation-fiók neve. |
| Tenant_g | GUID, amely azonosítja a bérlő a hívó. |
| NodeId_g |A felügyelt csomópontot azonosító GUID azonosító. |
| DscReportId_g |A jelentést azonosító GUID. |
| LastSeenTime_t |A jelentés utolsó megtekintésének dátuma és időpontja |
| ReportStartTime_t |A jelentés kezdésének dátuma és időpontja. |
| ReportEndTime_t |A jelentés befejezésének dátuma és időpontja. |
| NumberOfResources_d |A csomópontra alkalmazott konfigurációban meghívott DSC-erőforrások száma. |
| SourceSystem | Hogyan gyűjtötte az Azure Monitor az adatokat. Mindig "Azure" az Azure-diagnosztika. |
| ResourceId |Az Azure Automation-fiók azonosítója. |
| Eredményleírása | A művelet leírása. |
| SubscriptionId | Az Automation-fiók Azure-előfizetésazonosítója (GUID). |
| ResourceGroup | Az Automation-fiók erőforráscsoportjának neve. |
| ResourceProvider | Microsoft. Automatizálás. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId |GUID, amely a megfelelőségi jelentés korrelációs azonosítója. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Tulajdonság | Leírás |
| --- | --- |
| TimeGenerated |A megfelelőségi ellenőrzés letöltésének dátuma és időpontja. |
| OperationName |DscResourceStatusData|
| ResultType (Eredménytípusa) |Azt jelzi, hogy az erőforrás megfelelő-e. |
| NodeName_s |A kezelt csomópont neve. |
| Kategória | DscNodeStatus. |
| Erőforrás | Az Azure Automation-fiók neve. |
| Tenant_g | GUID, amely azonosítja a bérlő a hívó. |
| NodeId_g |A felügyelt csomópontot azonosító GUID azonosító. |
| DscReportId_g |A jelentést azonosító GUID. |
| DscResourceId_s |A DSC erőforráspéldány neve. |
| DscResourceName_s |A DSC-erőforrás neve. |
| DscResourceStatus_s |Azt jelzi, hogy a DSC-erőforrás megfelel-e a követelményeknek. |
| DscModuleName_s |A DSC-erőforrást tartalmazó PowerShell-modul neve. |
| DscModuleVersion_s |A PowerShell-modul, amely tartalmazza a DSC-erőforrás. |
| DscConfigurationName_s |A csomópontra alkalmazott konfiguráció neve. |
| ErrorCode_s | A hibakód, ha az erőforrás meghibásodott. |
| ErrorMessage_s |A hibaüzenet, ha az erőforrás meghibásodott. |
| DscResourceDuration_d |A DSC-erőforrás által futtatott idő másodpercben. |
| SourceSystem | Hogyan gyűjtötte az Azure Monitor az adatokat. Mindig *Azure* Azure-diagnosztika. |
| ResourceId |Megadja az Azure Automation-fiókot. |
| Eredményleírása | A művelet leírása. |
| SubscriptionId | Az Automation-fiók Azure-előfizetésazonosítója (GUID). |
| ResourceGroup | Az Automation-fiók erőforráscsoportjának neve. |
| ResourceProvider | Microsoft. Automatizálás. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId |GUID, amely a megfelelőségi jelentés korrelációs azonosítója. |

## <a name="summary"></a>Összefoglalás

Az Automation State Configuration data azure monitornaplókba küldésével jobb betekintést nyerhet az Automation State Configuration csomópontok állapotába:

- Riasztások beállítása, hogy értesítsük, ha probléma merül fel
- Egyéni nézetek és keresési lekérdezések használatával vizualizálja a Runbook-eredményeket, a runbook-feladat állapotát és más kapcsolódó kulcsmutatókat vagy mutatókat.

Az Azure Monitor naplói nagyobb működési láthatóságot biztosítnak az Automation State Configuration adataiszámára, és gyorsabban oldhatják meg az incidenseket.

## <a name="next-steps"></a>További lépések

- Áttekintést az [Azure Automation állapotkonfigurációja](automation-dsc-overview.md) című témakörben talál.
- Első lépések: [Az Azure Automation állapotkonfigurációjának első lépései](automation-dsc-getting-started.md)
- A DSC-konfigurációk összeállításáról, hogy hozzárendelhesse őket a célcsomópontokhoz, olvassa el [a Konfigurációk összeállítása az Azure Automation állapotkonfigurációjában](automation-dsc-compile.md)
- A PowerShell-parancsmagok hivatkozásáról az [Azure Automation állapotkonfigurációs parancsmagjai](/powershell/module/azurerm.automation/#automation)
- A díjszabással kapcsolatos információkért lásd: [Azure Automation State Configuration pricing](https://azure.microsoft.com/pricing/details/automation/)
- Az Azure Automation állapotkonfigurációjának folyamatos üzembe helyezési folyamatban való használatára példa: [Folyamatos üzembe helyezés az Azure Automation állapotkonfiguráció és csokoládés használatával](automation-dsc-cd-chocolatey.md)
- Ha többet szeretne tudni arról, hogyan hozhat létre különböző keresési lekérdezéseket, és hogyan tekintheti át az Automation State Configuration naplókat az Azure Monitor naplóival, olvassa el a [Keresések naplózása az Azure Monitor naplóiban című témakört.](../log-analytics/log-analytics-log-searches.md)
- Ha többet szeretne megtudni az Azure Monitor naplóiról és adatgyűjtési forrásairól, olvassa [el az Azure Storage-adatok gyűjtése az Azure Monitor naplóinak áttekintése című témakört.](../azure-monitor/platform/collect-azure-metrics-logs.md)
