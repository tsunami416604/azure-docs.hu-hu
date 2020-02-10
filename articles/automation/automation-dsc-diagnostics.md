---
title: Azure Automation állapot-konfiguráció jelentési adatAzure Monitor naplókba való továbbítása
description: Ez a cikk bemutatja, hogyan küldheti el a kívánt állapot-konfiguráció (DSC) jelentéskészítési adatait Azure Automation állapot-konfigurációból Azure Monitor naplókba, hogy további betekintést és felügyeletet nyújtson.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: bbc9048452c5361306dd05e712090543bb1066ce
ms.sourcegitcommit: 323c3f2e518caed5ca4dd31151e5dee95b8a1578
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2020
ms.locfileid: "77111534"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-azure-monitor-logs"></a>Azure Automation állapot-konfiguráció jelentési adatAzure Monitor naplókba való továbbítása

Azure Automation állapot-konfiguráció 30 napig őrzi meg a csomópontok állapotát.
Ha inkább hosszabb ideig szeretné megőrizni ezeket az adatait, elküldheti a csomóponti állapot adatait a Log Analytics munkaterületre.
A megfelelőségi állapot látható a Azure Portal vagy a PowerShell-lel, a csomópontok és a csomópont-konfigurációk egyes DSC-erőforrásai esetében.
Azure Monitor naplók segítségével:

- A felügyelt csomópontok és az egyes erőforrások megfelelőségi adatainak beolvasása
- E-mail vagy riasztás elindítása a megfelelőségi állapot alapján
- Speciális lekérdezések írása a felügyelt csomópontok között
- A megfelelőségi állapot korrelációja az Automation-fiókok között
- A csomópont megfelelőségi előzményeinek megjelenítése az idő függvényében

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Előfeltételek

Az Automation-állapotra vonatkozó konfigurációs jelentések Azure Monitor naplókba való küldésének megkezdéséhez a következőkre lesz szüksége:

- A [Azure PowerShell](/powershell/azure/overview) november 2016-es vagy újabb kiadása (v 2.3.0).
- Egy Azure Automation-fiókra. További információ: [Első lépések Azure Automation](automation-offering-get-started.md)
- Egy Log Analytics munkaterületen egy **Automation & Control** szolgáltatási ajánlattal. További információ: Ismerkedés [a Azure monitor-naplókkal](../log-analytics/log-analytics-get-started.md).
- Legalább egy Azure Automation állapot-konfigurációs csomópont. További információ: [bevezetési gépek felügyeletre Azure Automation állapot-konfiguráció alapján](automation-dsc-onboarding.md)
- A [xDscDiagnostics](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0) modul 2.7.0.0 vagy újabb verzió. A telepítési lépésekért lásd: [DSC-naplók megtekintése a csomóponton](./troubleshoot/desired-state-configuration.md#steps-to-troubleshoot-desired-state-configuration-dsc).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Integráció beállítása Azure Monitor naplókhoz

Ha szeretné megkezdeni az adatok importálását a Azure Automation DSC-ből Azure Monitor naplókba, hajtsa végre a következő lépéseket:

1. Jelentkezzen be az Azure-fiókjába a PowerShellben. Lásd: [Bejelentkezés a Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps)
1. A következő PowerShell-parancs futtatásával szerezheti be az Automation-fiók _ResourceId_ : (ha egynél több Automation-fiókkal rendelkezik, válassza ki a konfigurálni kívánt fiókhoz tartozó _ResourceId_ ).

   ```powershell
   # Find the ResourceId for the Automation Account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. Szerezze be a Log Analytics munkaterület _ResourceId_ a következő PowerShell-parancs futtatásával: (ha egynél több munkaterülettel rendelkezik, válassza ki a konfigurálni kívánt munkaterület _ResourceId_ ).

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. Futtassa a következő PowerShell-parancsot, és cserélje le a `<AutomationResourceId>` és a `<WorkspaceResourceId>`t az előző lépések _ResourceId_ értékeire:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

Ha le szeretné állítani az adatok importálását Azure Automation állapotból Azure Monitor naplókba, futtassa a következő PowerShell-parancsot:

```powershell
Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
```

## <a name="view-the-state-configuration-logs"></a>Az állapot-konfigurációs naplók megtekintése

Miután beállította az integrációt Azure Monitor naplókkal az Automation-állapot konfigurációs adataihoz **, az állapot** konfigurálása (DSC) lap bal oldali paneljének **naplók** elemét választva megtekintheti őket.  

![Naplók](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

Megnyílik a **naplók keresése** panel, és megjelenik egy **DscNodeStatusData** művelet az egyes állapot-konfigurációs csomópontokhoz, valamint egy **DscResourceStatusData** -művelet minden olyan [DSC-erőforráshoz](/powershell/scripting/dsc/resources/resources) , amelyet az adott csomópontra alkalmazott csomópont-konfigurációban hívnak.

A **DscResourceStatusData** művelet a sikertelen DSC-erőforrások hibáit tartalmazza.

A művelethez tartozó információk megtekintéséhez kattintson a listában szereplő egyes műveletekre.

A naplókat Azure Monitor naplók keresésével is megtekintheti. Lásd: [az adatkeresés a naplók használatával](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview). Adja meg a következő lekérdezést az állapot-konfigurációs naplók megkereséséhez.

```
AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>E-mail küldése, ha az állapot-konfiguráció megfelelőségének ellenőrzése sikertelen

Az egyik legfontosabb ügyfelünk, hogy e-mail vagy szöveg küldésére van lehetőség, ha a DSC-konfigurációval valamilyen hiba történik.

Riasztási szabály létrehozásához először létre kell hoznia egy naplót az állapot-konfigurációs jelentés azon rekordjaihoz, amelyeknek meg kell hívniuk a riasztást. Kattintson az **+ új riasztási szabály** gombra a riasztási szabály létrehozásához és konfigurálásához.

1. A Log Analytics munkaterület áttekintés lapján kattintson a **naplók**elemre.
1. Hozzon létre egy naplóbeli keresési lekérdezést a riasztáshoz úgy, hogy beírja a következő keresést a lekérdezés mezőbe: `Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Ha több Automation-fiókból vagy-előfizetésből állított be naplókat a munkaterületre, a riasztásokat az előfizetés és az Automation-fiók alapján csoportosíthatja.
   Az Automation-fiók neve a DscNodeStatusData keresési erőforrás mezőjéből származtatható.
1. A **szabály létrehozása** képernyő megnyitásához kattintson az **+ új riasztási szabály** elemre az oldal tetején. A riasztás konfigurálásának lehetőségeiről további információt a [riasztási szabály létrehozása](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)című témakörben talál.

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Nem sikerült a DSC-erőforrások keresése az összes csomóponton

A Azure Monitor naplók használatának egyik előnye, hogy a sikertelen ellenőrzéseket a csomópontokon is megkeresheti.
A sikertelen DSC-erőforrások összes példányának megkeresése.

1. A Log Analytics munkaterület áttekintés lapján kattintson a **naplók**elemre.
1. Hozzon létre egy naplóbeli keresési lekérdezést a riasztáshoz úgy, hogy beírja a következő keresést a lekérdezés mezőbe: `Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>A DSC-csomópont korábbi állapotának megtekintése

Végezetül érdemes lehet megjeleníteni a DSC-csomópontok állapotának előzményeit az idő múlásával.
Ezzel a lekérdezéssel megkeresheti a DSC-csomópontok állapotát az idő múlásával.

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

Ekkor a csomópont állapotának diagramja jelenik meg az idő múlásával.

## <a name="azure-monitor-logs-records"></a>Azure Monitor rekordok naplózása

A Azure Automation diagnosztika két kategóriát hoz létre a Azure Monitor naplókban.

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Tulajdonság | Leírás |
| --- | --- |
| TimeGenerated |Az a dátum és idő, amikor a megfelelőségi ellenőrzés futott. |
| OperationName |DscNodeStatusData |
| ResultType |Azt határozza meg, hogy a csomópont megfelelő-e. |
| NodeName_s |A felügyelt csomópont neve. |
| NodeComplianceStatus_s |Azt határozza meg, hogy a csomópont megfelelő-e. |
| DscReportStatus |Azt jelzi, hogy sikeresen lefutott-e a megfelelőség ellenőrzése. |
| ConfigurationMode | A konfiguráció alkalmazása a csomópontra. A lehetséges értékek a következők: __"ApplyOnly"__ , __"ApplyandMonitior"__ és __"ApplyandAutoCorrect"__ . <ul><li>__ApplyOnly__: a DSC alkalmazza a konfigurációt, és még nem tesz semmit, kivéve, ha új konfigurációt küld a cél csomópontra, vagy amikor egy új konfigurációt végez egy kiszolgálóról. Új konfiguráció kezdeti alkalmazása után a DSC nem keres eltolódást egy előzőleg konfigurált állapotból. A DSC addig próbálkozik a konfiguráció alkalmazásával, amíg a __ApplyOnly__ érvénybe lép. </li><li> __ApplyAndMonitor__: ez az alapértelmezett érték. Az LCD ChipOnGlas minden új konfigurációt alkalmaz. Egy új konfiguráció kezdeti alkalmazása után, ha a cél csomópont a kívánt állapotba sodródik, a DSC a naplókban lévő eltérést jelenti. A DSC addig próbálkozik a konfiguráció alkalmazásával, amíg a __ApplyAndMonitor__ érvénybe lép.</li><li>__ApplyAndAutoCorrect__: a DSC minden új konfigurációt alkalmaz. Egy új konfiguráció kezdeti alkalmazása után, ha a cél csomópont a kívánt állapotba sodródik, a DSC a naplókban jelzi a következetlenségeket, majd újra alkalmazza az aktuális konfigurációt.</li></ul> |
| HostName_s | A felügyelt csomópont neve. |
| IP-cím | A felügyelt csomópont IPv4-címe. |
| Kategória | DscNodeStatus |
| Erőforrás | A Azure Automation fiók neve. |
| Tenant_g | A hívó bérlőjét azonosító GUID. |
| NodeId_g |A felügyelt csomópontot azonosító GUID. |
| DscReportId_g |A jelentést azonosító GUID. |
| LastSeenTime_t |A jelentés legutóbbi megtekintésének dátuma és időpontja. |
| ReportStartTime_t |A jelentés elindításának dátuma és időpontja. |
| ReportEndTime_t |A jelentés befejezésének dátuma és időpontja. |
| NumberOfResources_d |A csomópontra alkalmazott konfigurációban meghívott DSC-erőforrások száma. |
| SourceSystem | Hogyan gyűjtöttük össze az adatokat Azure Monitor naplókat. Mindig *Azure* az Azure Diagnostics szolgáltatáshoz. |
| ResourceId |Megadja a Azure Automation fiókot. |
| ResultDescription | A művelet leírása. |
| SubscriptionId | Az Automation-fiókhoz tartozó Azure-előfizetés azonosítója (GUID). |
| ResourceGroup | Az Automation-fiókhoz tartozó erőforráscsoport neve. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |A megfelelőségi jelentés korrelációs AZONOSÍTÓjának GUID azonosítója. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Tulajdonság | Leírás |
| --- | --- |
| TimeGenerated |Az a dátum és idő, amikor a megfelelőségi ellenőrzés futott. |
| OperationName |DscResourceStatusData|
| ResultType |Azt határozza meg, hogy az erőforrás megfelelő-e. |
| NodeName_s |A felügyelt csomópont neve. |
| Kategória | DscNodeStatus |
| Erőforrás | A Azure Automation fiók neve. |
| Tenant_g | A hívó bérlőjét azonosító GUID. |
| NodeId_g |A felügyelt csomópontot azonosító GUID. |
| DscReportId_g |A jelentést azonosító GUID. |
| DscResourceId_s |A DSC-erőforrás példányának neve. |
| DscResourceName_s |A DSC-erőforrás neve. |
| DscResourceStatus_s |Azt jelzi, hogy a DSC-erőforrás megfelel-e. |
| DscModuleName_s |A DSC-erőforrást tartalmazó PowerShell-modul neve. |
| DscModuleVersion_s |A DSC-erőforrást tartalmazó PowerShell-modul verziója. |
| DscConfigurationName_s |A csomópontra alkalmazott konfiguráció neve. |
| ErrorCode_s | A hibakód, ha az erőforrás sikertelen volt. |
| ErrorMessage_s |A hibaüzenet, ha az erőforrás sikertelen volt. |
| DscResourceDuration_d |Az az idő (másodpercben), ameddig a DSC-erőforrás futott. |
| SourceSystem | Hogyan gyűjtöttük össze az adatokat Azure Monitor naplókat. Mindig *Azure* az Azure Diagnostics szolgáltatáshoz. |
| ResourceId |Megadja a Azure Automation fiókot. |
| ResultDescription | A művelet leírása. |
| SubscriptionId | Az Automation-fiókhoz tartozó Azure-előfizetés azonosítója (GUID). |
| ResourceGroup | Az Automation-fiókhoz tartozó erőforráscsoport neve. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |A megfelelőségi jelentés korrelációs AZONOSÍTÓjának GUID azonosítója. |

## <a name="summary"></a>Összegzés

Az Automation-állapot konfigurációs adatainak Azure Monitor naplókba való elküldésével jobban betekintést nyerhet az Automation-állapot konfigurációs csomópontjainak állapotára:

- Riasztások beállítása, hogy értesítést kapjon, ha probléma merül fel
- Egyéni nézetek és keresési lekérdezések használatával megjelenítheti a runbook eredményeit, a runbook-feladatok állapotát, valamint az egyéb kapcsolódó kulcsfontosságú mutatókat vagy metrikákat.

Azure Monitor naplók nagyobb működési láthatóságot biztosítanak az Automation-állapot konfigurációs adataihoz, és gyorsabban segíthetnek az incidensek kezelésében.

## <a name="next-steps"></a>Következő lépések

- Az áttekintést lásd: [Azure Automation állapot konfigurálása](automation-dsc-overview.md)
- Első lépésként tekintse meg [az Azure Automation állapot konfigurációjának megismerése](automation-dsc-getting-started.md) című témakört.
- A DSC-konfigurációk fordításának megismeréséhez, hogy hozzá lehessen rendelni azokat a célcsoportokhoz, tekintse meg a [konfigurációk fordítása Azure Automation állapot konfigurációjában](automation-dsc-compile.md) című témakört.
- A PowerShell-parancsmagok ismertetése: [Azure Automation állapot-konfigurációs parancsmagok](/powershell/module/azurerm.automation/#automation)
- A díjszabással kapcsolatos információkért lásd: [Azure Automation állapot konfigurációjának díjszabása](https://azure.microsoft.com/pricing/details/automation/)
- Ha szeretné megtekinteni a Azure Automation állapot konfigurációjának folyamatos üzembe helyezési folyamatban való használatát, tekintse meg a [folyamatos üzembe helyezést a Azure Automation állapot-konfiguráció és a csokoládés használatával](automation-dsc-cd-chocolatey.md) .
- Ha többet szeretne megtudni a különböző keresési lekérdezések létrehozásáról és az Automation-állapot konfigurációs naplófájljainak Azure Monitor naplókkal való áttekintéséről, tekintse meg a naplók keresése [a Azure monitor naplókban](../log-analytics/log-analytics-log-searches.md) című témakört.
- Ha többet szeretne megtudni Azure Monitor a naplókról és az adatgyűjtési forrásokról, tekintse meg [Az Azure Storage-adatok gyűjtése Azure monitor naplók áttekintését](../azure-monitor/platform/collect-azure-metrics-logs.md) ismertető cikket.
