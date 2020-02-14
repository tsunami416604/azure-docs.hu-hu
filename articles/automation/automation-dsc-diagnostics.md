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
ms.openlocfilehash: 69801909c6bc8d215ca7dd3ccb7ac349201e8774
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198564"
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
- Egy Azure Automation-fiókra. További információ: [Bevezetés a Azure Automationba](automation-intro.md).
- Egy Log Analytics munkaterületen egy Automation & Control szolgáltatási ajánlattal. További információ: [Bevezetés a log Analytics használatába Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).
- Legalább egy Azure Automation állapot-konfigurációs csomópont. További információ: [bevezetési gépek felügyeletre Azure Automation állapot-konfiguráció alapján](automation-dsc-onboarding.md).
- A [xDscDiagnostics](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0) modul 2.7.0.0 vagy újabb verzió. A telepítési lépésekért lásd: [Azure Automation a kívánt állapot konfigurációjának hibaelhárítása](./troubleshoot/desired-state-configuration.md#steps-to-troubleshoot-desired-state-configuration-dsc).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Integráció beállítása Azure Monitor naplókhoz

Ha szeretné megkezdeni az adatok importálását a Azure Automation DSC-ből Azure Monitor naplókba, hajtsa végre a következő lépéseket:

1. Jelentkezzen be az Azure-fiókjába a PowerShellben. Lásd: [bejelentkezés Azure PowerShellsal](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
1. Szerezze be az Automation-fiók erőforrás-AZONOSÍTÓját az alábbi PowerShell-parancsmag futtatásával. Ha egynél több Automation-fiókkal rendelkezik, válassza ki a konfigurálni kívánt fiók erőforrás-AZONOSÍTÓját.

   ```powershell
   # Find the ResourceId for the Automation account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. A következő PowerShell-parancsmag futtatásával szerezze be Log Analytics munkaterület erőforrás-AZONOSÍTÓját. Ha egynél több munkaterülettel rendelkezik, válassza ki a konfigurálni kívánt munkaterület erőforrás-AZONOSÍTÓját.

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. Futtassa a következő PowerShell-parancsmagot, és cserélje le a `<AutomationResourceId>` és a `<WorkspaceResourceId>`t az előző lépések *ResourceId* értékeire.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

1. Ha le szeretné állítani az adatok importálását Azure Automation állapotból Azure Monitor naplókba, futtassa a következő PowerShell-parancsmagot.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
   ```

## <a name="view-the-state-configuration-logs"></a>Az állapot-konfigurációs naplók megtekintése

Miután beállította az integrációt Azure Monitor naplókkal az Automation-állapot konfigurációs adataihoz, megtekintheti őket az állapot-konfiguráció (DSC) lap bal oldali ablaktáblájának **figyelés** szakaszában található **naplók** lehetőség kiválasztásával.

![Naplók](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

Megnyílik a **napló keresése** ablaktábla az Automation-fiók erőforrására vonatkozó lekérdezési régióval. A DSC-műveletekhez tartozó állapot-konfigurációs naplók kereséséhez keresse meg Azure Monitor naplókat. A DSC-műveletek rekordjait a AzureDiagnostics táblában tárolja a rendszer. Ha például meg szeretné keresni a nem megfelelő csomópontokat, írja be a következő lekérdezést.

```AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```
Szűrés részletei:

* Szűrés a *DscNodeStatusData* az egyes állapot-konfigurációs csomópontok műveleteinek visszaküldéséhez.
* A *DscResourceStatusData* szűrésével visszatérhet az adott erőforrásra alkalmazott csomópont-konfigurációban megnevezett összes DSC-erőforrásra vonatkozó műveletekre. 
* Szűrje a *DscResourceStatusData* , és adja vissza a hibás DSC-erőforrások hibájának adatait.

Ha többet szeretne megtudni a naplófájlok adatainak megkereséséről, tekintse meg a [Azure monitorban található naplók áttekintését](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>E-mail küldése, ha az állapot-konfiguráció megfelelőségének ellenőrzése sikertelen

Az egyik legfontosabb ügyfelünk, hogy e-mail vagy szöveg küldésére van lehetőség, ha a DSC-konfigurációval valamilyen hiba történik.

Riasztási szabály létrehozásához először létre kell hoznia egy naplót az állapot-konfigurációs jelentés azon rekordjaihoz, amelyeknek meg kell hívniuk a riasztást. Kattintson az **+ új riasztási szabály** gombra a riasztási szabály létrehozásához és konfigurálásához.

1. A Log Analytics munkaterület áttekintés lapján kattintson a **naplók**elemre.
1. Hozzon létre egy naplóbeli keresési lekérdezést a riasztáshoz úgy, hogy beírja a következő keresést a lekérdezés mezőbe: `Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Ha több Automation-fiókból vagy-előfizetésből állított be naplókat a munkaterületre, a riasztásokat az előfizetés és az Automation-fiók alapján csoportosíthatja. Származtatja az Automation-fiók nevét a DscNodeStatusData keresési erőforrás mezőjéből.
1. A **szabály létrehozása** képernyő megnyitásához kattintson az **+ új riasztási szabály** elemre az oldal tetején. 

A riasztás konfigurálásának lehetőségeiről további információt a [riasztási szabály létrehozása](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)című témakörben talál.

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Nem sikerült a DSC-erőforrások keresése az összes csomóponton

A Azure Monitor naplók használatának egyik előnye, hogy a sikertelen ellenőrzéseket a csomópontokon is megkeresheti.
A meghibásodott DSC-erőforrások összes példányának megkeresése:

1. A Log Analytics munkaterület áttekintése lapon kattintson a **naplók**elemre.
1. Hozzon létre egy naplóbeli keresési lekérdezést a riasztáshoz úgy, hogy beírja a következő keresést a lekérdezés mezőbe: `Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>A DSC-csomópont korábbi állapotának megtekintése

A DSC-csomóponti állapot előzményeinek időbeli megjelenítéséhez használhatja ezt a lekérdezést:

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

A lekérdezés megjeleníti a csomópont állapotának diagramját az idő múlásával.

## <a name="azure-monitor-logs-records"></a>Azure Monitor rekordok naplózása

Azure Automation diagnosztika két típusú rekordot hoz létre a Azure Monitor naplókban:

* Node status-adat (DscNodeStatusData)
* Erőforrás-állapotadatok (DscResourceStatusData)

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Tulajdonság | Leírás |
| --- | --- |
| TimeGenerated |Az a dátum és idő, amikor a megfelelőségi ellenőrzés futott. |
| OperationName |DscNodeStatusData. |
| ResultType |Azt határozza meg, hogy a csomópont megfelelő-e. |
| NodeName_s |A felügyelt csomópont neve. |
| NodeComplianceStatus_s |Azt határozza meg, hogy a csomópont megfelelő-e. |
| DscReportStatus |Azt jelzi, hogy sikeresen lefutott-e a megfelelőség ellenőrzése. |
| ConfigurationMode | A konfiguráció alkalmazása a csomópontra. Lehetséges értékek: <ul><li>*ApplyOnly*: a DSC alkalmazza a konfigurációt, és még nem tesz semmit, kivéve, ha új konfigurációt küld a cél csomópontra, vagy amikor egy új konfigurációt végez egy kiszolgálóról. Új konfiguráció kezdeti alkalmazása után a DSC nem keres eltolódást egy előzőleg konfigurált állapotból. A DSC addig próbálkozik a konfiguráció alkalmazásával, amíg a *ApplyOnly* érték érvénybe lép. </li><li>*ApplyAndMonitor*: ez az alapértelmezett érték. Az LCD ChipOnGlas minden új konfigurációt alkalmaz. Egy új konfiguráció kezdeti alkalmazása után, ha a cél csomópont a kívánt állapotba sodródik, a DSC a naplókban lévő eltérést jelenti. A DSC addig próbálkozik a konfiguráció alkalmazásával, amíg a *ApplyAndMonitor* érték érvénybe lép.</li><li>*ApplyAndAutoCorrect*: a DSC minden új konfigurációt alkalmaz. Egy új konfiguráció kezdeti alkalmazása után, ha a cél csomópont a kívánt állapotba sodródik, a DSC a naplókban jelzi a következetlenségeket, majd újra alkalmazza az aktuális konfigurációt.</li></ul> |
| HostName_s | A felügyelt csomópont neve. |
| IP-cím | A felügyelt csomópont IPv4-címe. |
| Kategória | DscNodeStatus. |
| Erőforrás | A Azure Automation fiók neve. |
| Tenant_g | A hívó bérlőjét azonosító GUID. |
| NodeId_g |A felügyelt csomópontot azonosító GUID. |
| DscReportId_g |A jelentést azonosító GUID. |
| LastSeenTime_t |A jelentés legutóbbi megtekintésének dátuma és időpontja. |
| ReportStartTime_t |A jelentés elindításának dátuma és időpontja. |
| ReportEndTime_t |A jelentés befejezésének dátuma és időpontja. |
| NumberOfResources_d |A csomópontra alkalmazott konfigurációban meghívott DSC-erőforrások száma. |
| SourceSystem | Hogyan gyűjtöttük össze az adatokat Azure Monitor naplókat. Az Azure Diagnostics mindig "Azure". |
| ResourceId |A Azure Automation fiók azonosítója. |
| ResultDescription | A művelet leírása. |
| SubscriptionId | Az Automation-fiókhoz tartozó Azure-előfizetés azonosítója (GUID). |
| ResourceGroup | Az Automation-fiókhoz tartozó erőforráscsoport neve. |
| ResourceProvider | Microsoft. Automation. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId |A megfelelőségi jelentés korrelációs azonosítójának GUID azonosítója. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Tulajdonság | Leírás |
| --- | --- |
| TimeGenerated |Az a dátum és idő, amikor a megfelelőségi ellenőrzés futott. |
| OperationName |DscResourceStatusData|
| ResultType |Azt határozza meg, hogy az erőforrás megfelelő-e. |
| NodeName_s |A felügyelt csomópont neve. |
| Kategória | DscNodeStatus. |
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
| ResourceProvider | Microsoft. Automation. |
| ResourceType | AUTOMATIONACCOUNTS. |
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
