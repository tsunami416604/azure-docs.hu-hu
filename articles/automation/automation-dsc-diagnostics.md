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
ms.openlocfilehash: badd8ba676ef25c33a5034bb04d616faeb4ef1b0
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392104"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-azure-monitor-logs"></a>Az Azure Automation állapotkonfigurációs jelentési adatainak továbbítása az Azure Monitor naplóiba

Az Azure Automation State Configuration 30 napig őrzi meg a csomópont állapotadatait. A csomópont állapotadatait elküldheti a Log Analytics-munkaterületre, ha ezeket az adatokat hosszabb ideig szeretné megőrizni. Megfelelőségi állapot látható az Azure Portalon vagy a PowerShell, a csomópontok és az egyes DSC-erőforrások csomópontkonfigurációkban. 

Az Azure Monitor naplói nagyobb működési láthatóságot biztosítnak az Automation State Configuration adataiszámára, és gyorsabban oldhatják meg az incidenseket. Az Azure Monitor naplóival a következőket teheti:

- Megfelelőségi információk beszerezni a felügyelt csomópontok és az egyes erőforrások.
- E-mail vagy riasztás aktiválása a megfelelőségi állapot alapján.
- Speciális lekérdezések írása a felügyelt csomópontok között.
- Korrelálja a megfelelőségi állapotot az Automation-fiókok között.
- Egyéni nézetek és keresési lekérdezések segítségével vizualizálhatja a Runbook-eredményeket, a runbook-feladat állapotát és más kapcsolódó kulcsjelzőket vagy mutatókat.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](automation-update-azure-modules.md)

## <a name="prerequisites"></a>Előfeltételek

Az Automation-állapot konfigurációs jelentéseinek az Azure Monitor-naplókba való elküldésének megkezdéséhez a következőkre van szüksége:

- Az [Azure PowerShell](/powershell/azure/overview) 2016 novemberi vagy későbbi kiadása (2.3.0-s verzió).
- Egy Azure Automation-fiókra. További információ: [Az Azure Automation bemutatkozása.](automation-intro.md)
- Log Analytics-munkaterület Automation & Control szolgáltatásajánlattal. További információ: [A Log Analytics használata az Azure Monitorban](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)című témakörben talál.
- Legalább egy Azure Automation-állapotkonfigurációs csomópont. További információ: [Bevezetési gépek az Azure Automation állapotkonfigurációja általi kezeléshez.](automation-dsc-onboarding.md)
- Az [xDscDiagnostics](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0) modul, 2.7.0.0-s vagy újabb verzió. A telepítési lépéseket az [Azure Automation kívánt állapotkonfigurációja hibaelhárítása című témakörben tésiterületen.](./troubleshoot/desired-state-configuration.md)

## <a name="set-up-integration-with-azure-monitor-logs"></a>Integráció beállítása az Azure Monitor-naplókkal

Ha meg szeretné kezdeni az adatok importálását az Azure Automation-állapot konfigurációjából az Azure Monitor naplóiba, hajtsa végre az alábbi lépéseket:

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

1. Futtassa a következő PowerShell-parancsmaszt, és az `<AutomationResourceId>` `<WorkspaceResourceId>` `ResourceId` előző lépések értékeit.

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

A Naplókeresés ablaktábla megnyílik egy lekérdezési terület hatóköre az Automation-fiók erőforrás. Az Állapotkonfigurációs naplókban a DSC-műveleteket az Azure Monitor-naplókban való kereséssel keresheti meg. A DSC-műveletek rekordjai a `AzureDiagnostics` táblában tárolódnak. Ha például nem megfelelő csomópontokat szeretne keresni, írja be a következő lekérdezést.

```AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```

Szűrésrészletei:

* Szűrje `DscNodeStatusData` az egyes állapotkonfigurációs csomópontok műveleteinek visszaadásához.
* Szűrje `DscResourceStatusData` a visszatérési műveleteket az erőforrásra alkalmazott csomópontkonfigurációban meghívott dsc-erőforrásokhoz. 
* Szűrés `DscResourceStatusData` a sikertelen DSC-erőforrások hibainformációinak visszaadására.

Ha többet szeretne tudni az adatok keresésére irányuló naplólekérdezések létrehozásáról, [olvassa el a naplólekérdezések áttekintése az Azure Monitorban című témakört.](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>E-mail küldése, ha egy állapotkonfigurációmegfelelőség-ellenőrzés sikertelen

Az egyik legfontosabb ügyfélkérésünk az, hogy e-mailt vagy sms-t küldjünk, ha valami elromlik a DSC konfigurációval.

Riasztási szabály létrehozásához először hozzon létre egy naplókeresést az Állapotkonfiguráció jelentésrekordokra, amelyeknek meg kell hívniuk a riasztást. A **riasztási szabály** létrehozásához és konfigurálásához kattintson az Új riasztási szabály gombra.

1. A Log Analytics munkaterület – áttekintés lapon kattintson a **Naplók gombra.**
1. Naplókeresési lekérdezés létrehozása a riasztáshoz a következő keresés beírásával a lekérdezésmezőbe:`Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Ha több Automation-fiókból vagy a munkaterületre való előfizetésből állított be naplókat, az értesítéseket előfizetési és Automation-fiók szerint csoportosíthatja. A rekordok keresésének `Resource` mezőjéből származtathatja az `DscNodeStatusData` Automation-fiók nevét.
1. A **Szabály létrehozása** képernyő megnyitásához kattintson a lap tetején az **Új riasztási szabály** elemre. 

A riasztás konfigurálásának lehetőségeiről a [Riasztási szabály létrehozása című](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)témakörben talál további információt.

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Sikertelen DSC-erőforrások keresése az összes csomóponton

Az Azure Monitor-naplók használatának egyik előnye, hogy sikertelen ellenőrzéseket kereshet a csomópontok között. A sikertelen DSC-erőforrások összes példányának megkeresése:

1. A Log Analytics munkaterület – áttekintés e lapon kattintson a **Naplók gombra.**
1. Naplókeresési lekérdezés létrehozása a riasztáshoz a következő keresés beírásával a lekérdezésmezőbe:`Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>A DSC-csomópont korábbi állapotának megtekintése

A DSC-csomópont állapotelőzményeinek idővel történő megjelenítéséhez használja ezt a lekérdezést:

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

Ez a lekérdezés a csomópont állapotának diagramját jeleníti meg az idő múlásával.

## <a name="azure-monitor-logs-records"></a>Az Azure Monitor naplózza a rekordokat

Az Azure Automation-diagnosztika két rekordkategóriát hoz létre az Azure Monitor naplóiban:

* Csomópont állapotadatai`DscNodeStatusData`( )
* Erőforrás állapotadatai`DscResourceStatusData`( )

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Tulajdonság | Leírás |
| --- | --- |
| TimeGenerated |A megfelelőségi ellenőrzés letöltésének dátuma és időpontja. |
| OperationName |`DscNodeStatusData`. |
| ResultType (Eredménytípusa) |Érték, amely azt jelzi, hogy a csomópont megfelelő-e. |
| NodeName_s |A kezelt csomópont neve. |
| NodeComplianceStatus_s |Állapotérték, amely megadja, hogy a csomópont megfelelő-e. |
| DscReportStatus |Állapotérték, amely jelzi, hogy a megfelelőségi ellenőrzés sikeresen lefutott-e. |
| ConfigurationMode (Konfigurációs mód) | A konfiguráció nak a csomópontra való alkalmazásához használt mód. Lehetséges értékek: <ul><li>`ApplyOnly`: A DSC alkalmazza a konfigurációt, és nem tesz tovább semmit, kivéve, ha egy új konfiguráció tolt a célcsomópontra, vagy ha egy új konfigurációt lekért egy kiszolgálóról. Az új konfiguráció kezdeti alkalmazása után a DSC nem ellenőrzi a korábban konfigurált állapotból való eltolódást. A DSC addig próbálja alkalmazni a `ApplyOnly` konfigurációt, amíg az sikeres nem lesz, mielőtt az érték érvénybe lépne. </li><li>`ApplyAndMonitor`: Ez az alapértelmezett érték. Az LCM minden új konfigurációt alkalmaz. Egy új konfiguráció kezdeti alkalmazása után, ha a célcsomópont a kívánt állapottól elsodródik, a DSC jelenti az eltérést a naplókban. A DSC addig próbálja alkalmazni a `ApplyAndMonitor` konfigurációt, amíg az sikeres nem lesz, mielőtt az érték érvénybe lépne.</li><li>`ApplyAndAutoCorrect`: A DSC minden új konfigurációt alkalmaz. Egy új konfiguráció kezdeti alkalmazása után, ha a célcsomópont a kívánt állapotból sodródik, a DSC jelenti az eltérést a naplókban, majd újra alkalmazza az aktuális konfigurációt.</li></ul> |
| HostName_s | A kezelt csomópont neve. |
| IPAddress | A felügyelt csomópont IPv4-címe. |
| Kategória | `DscNodeStatus`. |
| Erőforrás | Az Azure Automation-fiók neve. |
| Tenant_g | GUID, amely azonosítja a bérlő a hívó. |
| NodeId_g | A felügyelt csomópontot azonosító GUID azonosító. |
| DscReportId_g | A jelentést azonosító GUID. |
| LastSeenTime_t | A jelentés utolsó megtekintésének dátuma és időpontja |
| ReportStartTime_t | A jelentés kezdésének dátuma és időpontja. |
| ReportEndTime_t | A jelentés befejezésének dátuma és időpontja. |
| NumberOfResources_d | A csomópontra alkalmazott konfigurációban meghívott DSC-erőforrások száma. |
| SourceSystem | A forrásrendszer azonosítja, hogy az Azure Monitor naplók gyűjtötte az adatokat. Mindig `Azure` az Azure-diagnosztika. |
| ResourceId |Az Azure Automation-fiók erőforrás-azonosítója. |
| Eredményleírása | A művelet erőforrásleírása. |
| SubscriptionId | Az Automation-fiók Azure-előfizetésazonosítója (GUID). |
| ResourceGroup | Az Automation-fiók erőforráscsoportjának neve. |
| ResourceProvider | Microsoft. Automatizálás. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId | A megfelelőségi jelentés korrelációs azonosítóját tartalmazó GUID. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Tulajdonság | Leírás |
| --- | --- |
| TimeGenerated |A megfelelőségi ellenőrzés letöltésének dátuma és időpontja. |
| OperationName |`DscResourceStatusData`.|
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
| SourceSystem | Hogyan gyűjtötte az Azure Monitor az adatokat. Mindig `Azure` az Azure-diagnosztika. |
| ResourceId |Az Azure Automation-fiók azonosítója. |
| Eredményleírása | A művelet leírása. |
| SubscriptionId | Az Automation-fiók Azure-előfizetésazonosítója (GUID). |
| ResourceGroup | Az Automation-fiók erőforráscsoportjának neve. |
| ResourceProvider | Microsoft. Automatizálás. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId |GUID, amely a megfelelőségi jelentés korrelációs azonosítója. |


## <a name="next-steps"></a>További lépések

- Áttekintést az [Azure Automation állapotkonfigurációja című témakörben talál.](automation-dsc-overview.md)
- Első lépések: [Az Azure Automation állapotkonfigurációjának első lépései.](automation-dsc-getting-started.md)
- A DSC-konfigurációk összeállításáról a célcsomópontokhoz való hozzárendelésről a [Konfigurációk összeállítása az Azure Automation állapotkonfigurációjában](automation-dsc-compile.md)témakörben olvashat.
- A PowerShell-parancsmag referencia, lásd: [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Díjszabási információkért lásd: [Azure Automation state configuration fortaring.](https://azure.microsoft.com/pricing/details/automation/)
- Az Azure Automation state configuration használatával kapcsolatos példa folyamatos üzembe helyezési folyamatban: [Folyamatos üzembe helyezés az Azure Automation állapotkonfiguráció és a Csokoládé használata című témakörben.](automation-dsc-cd-chocolatey.md)
- Ha többet szeretne tudni arról, hogyan hozhat létre különböző keresési lekérdezéseket, és hogyan tekintheti át az Automation State Configuration naplókat az Azure Monitor naplóival, olvassa el [a Naplókeresések az Azure Monitor naplóiban című témakört.](../log-analytics/log-analytics-log-searches.md)
- Ha többet szeretne megtudni az Azure Monitor naplóiról és adatgyűjtési forrásairól, olvassa [el az Azure Storage-adatok gyűjtése az Azure Monitor naplóinak áttekintése című témakört.](../azure-monitor/platform/collect-azure-metrics-logs.md)
