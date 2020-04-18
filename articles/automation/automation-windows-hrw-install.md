---
title: Az Azure Automation hibrid runbook-feldolgozója Windowsra
description: Ez a cikk egy Azure Automation-hibrid runbook-feldolgozó telepítésével kapcsolatos információkat tartalmaz, amelyekkel runbookokat futtathat Windows-alapú számítógépeken a helyi adatközpontban vagy felhőbeli környezetben.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 53dfe07ebd4925c96290db140b6e613c38eef564
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617332"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Windows hibrid runbook-feldolgozó központi telepítése

Az Azure Automation hibrid Runbook-feldolgozó szolgáltatásával runbookokat futtathat közvetlenül a szerepkört üzemeltető számítógépen és a környezetben lévő erőforrásokkal a helyi erőforrások kezeléséhez. Az Azure Automation tárolja és kezeli a runbookokat, majd egy vagy több kijelölt számítógépre kézbesíti azokat. Ez a cikk bemutatja, hogyan telepíthet hibrid runbook-feldolgozót Windows-gépen.

Miután sikeresen üzembe helyezett egy runbook-feldolgozót, tekintse át [a Runbookok futtatásának futtatását egy hibrid Runbook-feldolgozón](automation-hrw-run-runbooks.md) című útmutatót, amelyből megtudhatja, hogyan konfigurálhatja a runbookokat a folyamatok automatizálására a helyszíni adatközpontban vagy más felhőalapú környezetben.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](automation-update-azure-modules.md)

## <a name="windows-hybrid-runbook-worker-installation-and-configuration"></a>A Windows hibrid runbook feldolgozó jának telepítése és konfigurálása

A Windows hibrid runbook-feldolgozó telepítéséhez és konfigurálásához az alábbi módszerek egyikét használhatja.

* Az Azure virtuális gépek esetén telepítse a Windows Log Analytics-ügynökét a [Windows virtuálisgép-bővítmény használatával.](../virtual-machines/extensions/oms-windows.md) A bővítmény telepíti a Log Analytics-ügynököt az Azure virtuális gépeken, és virtuális gépeket foglal be egy meglévő Log Analytics-munkaterületre egy Azure Resource Manager-sablon vagy PowerShell használatával. Az ügynök telepítése után a virtuális gép hozzáadható egy hibrid Runbook-feldolgozó csoporthoz az Automation-fiókban. Tekintse meg a [3.](#manual-deployment)

* Az Automation runbook okaként teljesen automatizálhatja a Windows-számítógépek konfigurálásának folyamatát. Ez az ajánlott módszer az adatközpontban vagy más felhőkörnyezetben lévő gépekhez.

* Kövesse a lépésről-lépésre lépésként manuálisan telepítse és konfigurálja a hibrid Runbook feldolgozó szerepkört a nem Azure-beli virtuális gépen.

> [!NOTE]
> A hibrid runbook-feldolgozószerepkört a kívánt állapotkonfigurációval (DSC) támogató kiszolgálók konfigurációjának kezeléséhez a kiszolgálókat DSC-csomópontként kell hozzáadnia.

### <a name="minimum-requirements-for-windows-hybrid-runbook-worker"></a>A Windows hibrid runbook-feldolgozóminimális követelményei

A Windows hibrid runbook-feldolgozóminimális követelményei a következők:

* Windows Server 2012 vagy újabb
* Windows PowerShell 5.1-es vagy újabb verzió[(wmf 5.1 letöltés)](https://www.microsoft.com/download/details.aspx?id=54616)
* A .NET-keretrendszer 4.6.2-es vagy újabb verziója
* Két mag
* 4 GB RAM
* 443-as port (kimenő)

### <a name="network-configuration"></a>Hálózati konfiguráció

A hibrid runbook-feldolgozó további hálózati követelményeit a [Hálózat konfigurálása](automation-hybrid-runbook-worker.md#network-planning)című témakörben kaphatja.

### <a name="server-onboarding-for-management-with-automation-dsc"></a>Kiszolgáló bevezetés az Automation DSC-vel való felügyelethez

A DSC-vel való felügyeletre szolgáló bevezetési kiszolgálókról az [Azure Automation DSC általi kezeléshez szolgáló bevezetési gépek című](automation-dsc-onboarding.md)témakörben talál további információt.

Az [Update Management megoldás](../operations-management-suite/oms-solution-update-management.md) engedélyezése automatikusan konfigurálja a Log Analytics-munkaterülethez hibrid Runbook-feldolgozóként csatlakoztatott Windows-számítógépeket a megoldásban található runbookok támogatásához. Ez a dolgozó azonban nincs regisztrálva az Automation-fiókban már definiált hibrid Runbook-feldolgozó-csoportokkal.

### <a name="addition-of-the-computer-to-a-hybrid-runbook-worker-group"></a>A számítógép hozzáadása hibrid Runbook-feldolgozó csoporthoz

A feldolgozó számítógép hozzáadása egy hibrid Runbook feldolgozó csoport az Automation-fiókban. Vegye figyelembe, hogy támogatnia kell az Automation runbookok, mindaddig, amíg ugyanazt a fiókot használja mind a megoldás, és a hibrid Runbook feldolgozó csoporttagság. Ez a funkció a hibrid runbook-feldolgozó 7.2.12024.0-s verziójától érhető el.

## <a name="automated-deployment"></a>Automatikus üzembe helyezés

A célgépen hajtsa végre a következő lépéseket a Windows hibrid feldolgozószerepkör telepítésének és konfigurálásának automatizálásához.

### <a name="step-1---download-the-powershell-script"></a>1. lépés - A PowerShell-parancsfájl letöltése

Töltse le az **Új-OnPremiseHybridWorker.ps1** parancsfájlt a [PowerShell-galériából.](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker) A letöltésnek közvetlenül a hibrid Runbook-feldolgozó szerepkört futtató számítógépről vagy a környezet egy másik számítógépéről kell származnia. Miután letöltötte a parancsfájlt, másolja azt a dolgozóba. A **New-OnPremiseHybridWorker.ps1** parancsfájl az alábbiakban leírt paramétereket használja a végrehajtás során.

| Paraméter | status | Leírás |
| --------- | ------ | ----------- |
| `AAResourceGroupName` | Kötelező | Az Automation-fiókhoz társított erőforráscsoport neve. |
| `AutomationAccountName` | Kötelező | Az Automation-fiók neve.
| `Credential` | Optional | Az Azure-környezetbe való bejelentkezéskor használandó hitelesítő adatok. |
| `HybridGroupName` | Kötelező | A hibrid Runbook-feldolgozó csoport nevét, amelyet a forgatókönyvet támogató runbookok célként ad meg. |
| `OMSResourceGroupName` | Optional | A Log Analytics-munkaterület erőforráscsoportjának neve. Ha ez az erőforráscsoport nincs megadva, a függvény használja a `AAResourceGroupName` függvény értékét. |
| `SubscriptionID` | Kötelező | Az Automation-fiókhoz társított Azure-előfizetés azonosítója. |
| `TenantID` | Optional | Az Automation-fiókhoz társított bérlői szervezet azonosítója. |
| `WorkspaceName` | Optional | A Log Analytics munkaterület neve. Ha nem rendelkezik Log Analytics-munkaterülettel, a parancsfájl létrehoz és konfigurál egyet. |

> [!NOTE]
> A megoldások engedélyezésekor az Azure Automation csak bizonyos régiókat támogat a Log Analytics-munkaterület és az Automation-fiók összekapcsolásához. A támogatott leképezési párok listáját az [Automation-fiók és a Log Analytics-munkaterület Régióleképezés](how-to/region-mappings.md)e.

### <a name="step-2---open-windows-powershell-command-line-shell"></a>2. lépés – A Windows PowerShell parancssori rendszerhéjának megnyitása

Nyissa meg a **Windows PowerShellt** a **kezdőképernyőről** rendszergazdai módban.

### <a name="step-3---run-the-powershell-script"></a>3. lépés - A PowerShell-parancsfájl futtatása

A PowerShell parancssorában keresse meg a letöltött parancsfájlt tartalmazó mappát. Módosítsa a `AutomationAccountName`paraméterek , `AAResourceGroupName`, `OMSResourceGroupName` `HybridGroupName`, `SubscriptionID`, `WorkspaceName`, és értékét. Akkor futtassa le a forgatókönyvet.

A parancsfájl futtatása után a rendszer kéri a hitelesítést az Azure-ral. Olyan fiókkal kell bejelentkeznie, amely az Előfizetés-rendszergazdák szerepkör tagja és az előfizetés társrendszergazdája.

```powershell-interactive
.\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
-OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
-SubscriptionID <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
```

### <a name="step-4---install-nuget"></a>4. lépés - NuGet telepítése

A rendszer kéri, hogy egyezzen bele a NuGet telepítésébe, és hitelesítse magát az Azure-hitelesítő adataival. Ha nem rendelkezik a legújabb NuGet verzióval, akkor azt az [Elérhető NuGet terjesztési verziók](https://www.nuget.org/downloads)ból szerezheti be.

### <a name="step-5---verify-the-deployment"></a>5. lépés - A telepítés ellenőrzése

A parancsfájl befejezése után a hibrid feldolgozócsoportok lap az új csoportot és a tagok számát jeleníti meg. Ha egy meglévő csoportról van szó, a tagok száma növekszik. Kiválaszthatja a csoportot a hibrid feldolgozócsoportok lapon lévő listából, és kiválaszthatja a **Hibrid feldolgozók** csempét. A hibrid dolgozók lapon láthatja a csoport minden egyes tagja szerepel.

## <a name="manual-deployment"></a>Manuális üzembe helyezés

A célgépen hajtsa végre az első két lépést egyszer az Automation-környezetben. Ezután hajtsa végre a fennmaradó lépéseket az egyes feldolgozó számítógépek.

### <a name="step-1---create-a-log-analytics-workspace"></a>1. lépés – Log Analytics-munkaterület létrehozása

Ha még nem rendelkezik a Log Analytics-munkaterület, tekintse át az [Azure Monitor Napló tervezési útmutató](../azure-monitor/platform/design-logs-deployment.md) létrehozása előtt a munkaterületet.

### <a name="step-2---add-the-automation-solution-to-the-log-analytics-workspace"></a>2. lépés – Az Automatizálási megoldás hozzáadása a Log Analytics-munkaterülethez

Az Automation-megoldás funkciókat ad az Azure Automationhez, beleértve a hibrid Runbook-feldolgozó támogatását is. Amikor hozzáadja a megoldást a Log Analytics-munkaterülethez, az automatikusan lenyomja az ügynökszámítógépre a következő lépésben leírtak szerint telepített munkavégző összetevőket.

Az Automation-megoldás hozzáadása a munkaterülethez, futtassa a következő PowerShell-parancsmas.

```powershell-interactive
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true -DefaultProfile <IAzureContextContainer>
```

### <a name="step-3---install-the-log-analytics-agent-for-windows"></a>3. lépés – A Windows Log Analytics-ügynökének telepítése

A Windows Log Analytics-ügynöke számítógépeket csatlakoztat egy Azure Monitor Log Analytics-munkaterülethez. Amikor telepíti az ügynököt a számítógépre, és csatlakoztatja a munkaterülethez, automatikusan letölti a hibrid Runbook-feldolgozóhoz szükséges összetevőket.

Az ügynök számítógépre való telepítéséhez kövesse a [Windows-számítógépek csatlakoztatása az Azure Monitor naplóihoz](../log-analytics/log-analytics-windows-agent.md)című témakör utasításait. Ezt a folyamatot több számítógép esetén is megismételheti, ha több dolgozót szeretne hozzáadni a környezethez.

Ha az ügynök néhány perc múlva sikeresen csatlakozott a Log Analytics-munkaterülethez, a következő lekérdezés futtatásával ellenőrizheti, hogy szívverési adatokat küld-e a munkaterületre.

```kusto
Heartbeat 
| where Category == "Direct Agent" 
| where TimeGenerated > ago(30m)
```

A keresési eredmények között meg kell jelennie a számítógép szívverési rekordjainak, jelezve, hogy csatlakoztatva van, és jelentést kell tennie a szolgáltatásnak. Alapértelmezés szerint minden ügynök továbbít egy szívverésrekordot a hozzárendelt munkaterületre. 

Az ügynök telepítésének és telepítésének befejezéséhez kövesse az alábbi lépéseket.

1. Engedélyezze a megoldást az ügynökgép fedélzetére. Lásd: [Fedélzeti gépek a munkaterületen](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-automation-account#onboard-machines-in-the-workspace).
2. Ellenőrizze, hogy az ügynök megfelelően töltötte-e le az Automation-megoldást. 
3. A hibrid runbook-feldolgozó verziójának megerősítéséhez keresse meg a **C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation** mappát, és jegyezze fel a verzióalmappát. **version**

### <a name="step-4---install-the-runbook-environment-and-connect-to-azure-automation"></a>4. lépés – Telepítse a runbook-környezetet, és csatlakozzon az Azure Automationhez

Amikor konfigurálja az ügynököt, hogy jelentsen egy Log Analytics-munkaterületnek, az Automation-megoldás lenyomja a `HybridRegistration` PowerShell-modult, amely a `Add-HybridRunbookWorker` parancsmast tartalmazza. Ezzel a parancsmaggal telepítse a runbook-környezetet a számítógépre, és regisztrálja az Azure Automation használatával.

Nyisson meg egy PowerShell-munkamenetet Rendszergazda módban, és futtassa a következő parancsokat a modul importálásához.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Most futtassa a `Add-HybridRunbookWorker` parancsmagát az alábbi szintaxissal.

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

A parancsmaghoz szükséges információkat az Azure Portalon a Kulcsok kezelése lapon szerezheti be. Nyissa meg ezt a lapot az Automation-fiók Beállítások lapján a **Kulcsok** lehetőséget választva.

![Kulcsok kezelése lap](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* A `GroupName` paraméterhez használja a hibrid Runbook feldolgozó csoport nevét. Ha ez a csoport már létezik az Automation-fiókban, az aktuális számítógép hozzáadódik hozzá. Ha ez a csoport nem létezik, akkor hozzáadjuk.
* A `EndPoint` paraméterhez használja a Kulcsok kezelése lapon található **URL-bejegyzést.**
* A `Token` paraméterhöz használja a Kulcsok kezelése lap **ELSŐDLEGES HOZZÁFÉRÉSI KULCS** bejegyzését.
* Ha szükséges, `Verbose` állítsa be a paramétert, hogy megkapja a telepítés részleteit.

### <a name="step-5----install-powershell-modules"></a>5. lépés – PowerShell-modulok telepítése

A Runbookok az Azure Automation-környezetben telepített modulokban definiált tevékenységek és parancsmagok bármelyikét használhatják. Mivel ezek a modulok nem kerülnek automatikusan üzembe helyezésre a helyszíni számítógépekre, manuálisan kell telepíteniőket. A kivétel az Azure-modul. Ez a modul alapértelmezés szerint telepítve van, és hozzáférést biztosít a parancsmagok hoz minden Azure-szolgáltatások és tevékenységek az Azure Automation.

Mivel a hibrid Runbook-feldolgozó szolgáltatás elsődleges célja a helyi erőforrások kezelése, valószínűleg telepítenie kell `PowerShellGet` az ezeket az erőforrásokat támogató modulokat, különösen a modult. A Windows PowerShell-modulok telepítéséről a [Windows PowerShell című témakörben](https://docs.microsoft.com/powershell/scripting/developer/windows-powershell)talál további információt.

A telepített moduloknak a `PSModulePath` környezeti változó által hivatkozott helyen kell lenniük, hogy a hibrid feldolgozó automatikusan importálhassa őket. További információt a [Modulok telepítése a PSModulePath alkalmazásban című témakörben talál.](https://docs.microsoft.com/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7)

## <a name="next-steps"></a>További lépések

* Ha tudni szeretné, hogyan konfigurálhatja a runbookokat a folyamatok automatizálására a helyszíni adatközpontban vagy más felhőbeli környezetben, olvassa [el a Runbookok futtatása hibrid runbook-feldolgozón](automation-hrw-run-runbooks.md)című témakört.
* A hibrid runbook-feldolgozók eltávolításáról az [Azure Automation hibrid runbook-feldolgozóinak eltávolítása](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker)című témakörben talál.
* A hibrid runbook-feldolgozók hibaelhárításáról a [Windows hibrid runbook-feldolgozók hibaelhárítása](troubleshoot/hybrid-runbook-worker.md#windows)című témakörben olvashat.
* A frissítéskezelési problémák elhárításának további lépéseit a [Frissítéskezelés: hibaelhárítás](troubleshoot/update-management.md)című témakörben tésiterületen.
