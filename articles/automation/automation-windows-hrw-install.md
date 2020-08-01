---
title: Windows Hybrid Runbook Worker üzembe helyezése Azure Automation
description: Ez a cikk azt ismerteti, hogyan helyezhet üzembe egy hibrid Runbook-feldolgozót, amely a helyi adatközpontban vagy a felhőalapú környezetben lévő Windows-alapú gépeken futtatott runbookok futtatására használható.
services: automation
ms.subservice: process-automation
ms.date: 06/24/2020
ms.topic: conceptual
ms.openlocfilehash: 634f200280a85ff865741cd03905101ff1e5c19f
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2020
ms.locfileid: "87448045"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Windows Hybrid Runbook Worker üzembe helyezése

A Azure Automation Hybrid Runbook Worker szolgáltatásával a runbookok közvetlenül a szerepkört üzemeltető gépen és a környezet erőforrásain keresztül futtathatja a helyi erőforrások kezeléséhez. A Azure Automation a runbookok tárolja és felügyeli, majd egy vagy több kijelölt gépre továbbítja azokat. Ez a cikk bemutatja, hogyan helyezhet üzembe hibrid Runbook-feldolgozót egy Windows rendszerű gépen, hogyan távolíthatja el a feldolgozót, és hogyan távolíthat el egy hibrid Runbook-feldolgozói csoportot.

A runbook-feldolgozó sikeres üzembe helyezése után tekintse át a [Runbookok futtatása hibrid runbook-feldolgozón](automation-hrw-run-runbooks.md) című témakört, amelyből megtudhatja, hogyan konfigurálhatja a runbookok a helyszíni adatközpontban vagy más felhőalapú környezetben lévő folyamatok automatizálására.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik a következőkkel.

### <a name="a-log-analytics-workspace"></a>Log Analytics munkaterület

A hibrid Runbook-feldolgozói szerepkör a szerepkör telepítéséhez és konfigurálásához Azure Monitor Log Analytics munkaterülettől függ. [Azure Resource Manager](../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace), a [PowerShell](../azure-monitor/scripts/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)vagy a [Azure Portal](../azure-monitor/learn/quick-create-workspace.md)segítségével hozhatja létre.

Ha nem rendelkezik Azure Monitor Log Analytics munkaterülettel, tekintse át a [Azure monitor log tervezési útmutatót](../azure-monitor/platform/design-logs-deployment.md) a munkaterület létrehozása előtt.

Ha van munkaterülete, de nem kapcsolódik az Automation-fiókjához, az automatizálási funkció lehetővé teszi a Azure Automation funkcióinak hozzáadását, beleértve a hibrid Runbook-feldolgozó támogatását is. Ha engedélyezi a Log Analytics munkaterület Azure Automation funkciójának egyikét, különösen a [Update Management](update-management/update-mgmt-overview.md) vagy a [change Tracking és a leltárt](change-tracking.md), a rendszer automatikusan leküldi a munkavégző összetevőket az ügynök számítógépére.

   A Update Management szolgáltatás munkaterülethez való hozzáadásához futtassa a következő PowerShell-parancsmagot:

```powershell-interactive
   Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "Updates" -Enabled $true
```

   A Change Tracking és a leltár funkciónak a munkaterülethez való hozzáadásához futtassa a következő PowerShell-parancsmagot:

```powershell-interactive
   Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "ChangeTracking" -Enabled $true
```

### <a name="log-analytics-agent"></a>Log Analytics-ügynök

A hibrid Runbook feldolgozói szerepkörhöz a [log Analytics ügynök](../azure-monitor/platform/log-analytics-agent.md) szükséges a támogatott Windows operációs rendszerhez.

### <a name="supported-windows-operating-system"></a>Támogatott Windows operációs rendszer

A Windows Hybrid Runbook Worker a Windows operációs rendszer következő verzióit támogatja hivatalosan:

* Windows Server 2019
* Windows Server 2016, 1709-es és 1803-es verzió
* Windows Server 2012, 2012 R2
* Windows Server 2008 SP2 (x64), 2008 R2
* Windows 10 Enterprise (több munkamenetet is beleértve) és Pro
* Windows 8 Enterprise és Pro
* Windows 7 SP1

### <a name="minimum-requirements"></a>Minimális követelmények

A Windows Hybrid Runbook Worker minimális követelményei a következők:

* Windows PowerShell 5,1 vagy újabb ([WMF 5,1 letöltése](https://www.microsoft.com/download/details.aspx?id=54616))
* A .NET-keretrendszer 4.6.2-es vagy újabb verziója
* Két mag
* 4 GB RAM
* 443-es port (kimenő)

### <a name="network-configuration"></a>Hálózati konfiguráció

A hibrid Runbook-feldolgozók további hálózati követelményeinek eléréséhez lásd: [a hálózat konfigurálása](automation-hybrid-runbook-worker.md#network-planning).

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Gép felvétele hibrid Runbook Worker-csoportba

Az Automation-fiókban felveheti a munkavégző gépet egy hibrid Runbook Worker-csoportba. Vegye figyelembe, hogy az Automation runbookok is támogatnia kell, ha ugyanazt a fiókot használja, mint a Azure Automation funkció és a hibrid Runbook Worker csoport tagsága. Ez a funkció a hibrid runbook-feldolgozó 7.2.12024.0-s verziójától érhető el.

>[!NOTE]
>A Azure Automation [Update Management](update-management/update-mgmt-overview.md) engedélyezése lehetővé teszi, hogy a log Analytics munkaterülethez csatlakoztatott Windows-gépeket a hibrid Runbook-feldolgozóként automatikusan konfigurálja az operációs rendszer frissítéseinek kezeléséhez. Ez a feldolgozó azonban nincs regisztrálva az Automation-fiókban már definiált hibrid Runbook-feldolgozói csoportokkal.

## <a name="enabling-machines-for-management-with-azure-automation-state-configuration"></a>Gépek Azure Automation állapot-konfigurációval való felügyeletének engedélyezése

További információ a gépek Azure Automation állapot-konfigurációval való felügyeletének engedélyezéséről: [a gépek Azure Automation állapot-konfiguráció általi felügyeletének engedélyezése](automation-dsc-onboarding.md).

> [!NOTE]
> A hibrid Runbook-feldolgozói szerepkört a kívánt állapot-konfigurációval (DSC) támogató gépek konfigurációjának kezeléséhez a gépeket DSC-csomópontként kell hozzáadnia.

## <a name="windows-hybrid-runbook-worker-installation-options"></a>A Windows Hybrid Runbook Worker telepítési beállításai

A Windows Hybrid Runbook Worker telepítéséhez és konfigurálásához az alábbi módszerek egyikét használhatja.

* Azure-beli virtuális gépek esetén a Windows rendszerhez készült [virtuálisgép-bővítmény](../virtual-machines/extensions/oms-windows.md)használatával telepítse a Windows log Analytics Agent ügynököt. A bővítmény telepíti a Log Analytics ügynököt az Azure Virtual Machines szolgáltatásban, és egy Azure Resource Manager sablonnal vagy a PowerShell használatával regisztrálja a virtuális gépeket egy meglévő Log Analytics-munkaterületen. Miután telepítette az ügynököt, a virtuális gép hozzáadhatók az Automation-fiókjában lévő hibrid Runbook Worker csoportjához.

* Nem Azure-beli virtuális gépek esetén telepítse a Windows Log Analytics Agent ügynököt a Windows [rendszerű számítógépek összekötése a Azure monitor](../azure-monitor/platform/agent-windows.md) cikkhez című témakörben ismertetett központi telepítési beállítások használatával. Ezt a folyamatot többször is megismételheti, ha több feldolgozót ad hozzá a környezethez. Az ügynök telepítése után a virtuális gépek hozzáadhatók az Automation-fiókjában lévő hibrid Runbook Worker csoportjához.

* Egy megadott PowerShell-szkripttel teljes mértékben [automatizálhatja](#automated-deployment) egy vagy több Windows rendszerű gép konfigurálásának folyamatát. Ez az ajánlott módszer az adatközpontban vagy más felhőalapú környezetben található gépekhez.

## <a name="automated-deployment"></a>Automatikus üzembe helyezés

A célszámítógépen hajtsa végre a következő lépéseket a Windows hibrid feldolgozói szerepkör telepítésének és konfigurálásának automatizálásához a PowerShell-parancsfájl **New-OnPremiseHybridWorker.ps1**használatával. A szkript a következő lépéseket hajtja végre:

* Telepíti a szükséges modulokat.
* Bejelentkezik az Azure-fiókjával
* Ellenőrzi a megadott erőforráscsoport és Automation-fiók létezését
* Az Automation-fiók attribútumaira mutató hivatkozásokat hoz létre
* Ha nincs megadva, létrehoz egy Azure Monitor Log Analytics munkaterületet
* A Azure Automation megoldás engedélyezése a munkaterületen
* Töltse le és telepítse a Windows Log Analytics Agent ügynököt
* A gép regisztrálása hibrid Runbook-feldolgozóként

### <a name="step-1---download-the-powershell-script"></a>1. lépés – a PowerShell-parancsfájl letöltése

Töltse le a **New-OnPremiseHybridWorker.ps1** szkriptet a [PowerShell-galériaból](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker). A parancsfájl letöltése után másolja vagy futtassa azt a célszámítógépen. A **New-OnPremiseHybridWorker.ps1** szkript a végrehajtás során az alább ismertetett paramétereket használja.

| Paraméter | Állapot | Leírás |
| --------- | ------ | ----------- |
| `AAResourceGroupName` | Kötelező | Az Automation-fiókhoz társított erőforráscsoport neve. |
| `AutomationAccountName` | Kötelező | Az Automation-fiók neve.
| `Credential` | Választható | Az Azure-környezetbe való bejelentkezéskor használandó hitelesítő adatok. |
| `HybridGroupName` | Kötelező | Annak a hibrid Runbook-feldolgozó csoportnak a neve, amelyet a jelen forgatókönyvet támogató runbookok célként határoz meg. |
| `OMSResourceGroupName` | Választható | Az Log Analytics munkaterület erőforráscsoport neve. Ha ez az erőforráscsoport nincs megadva, a rendszer a értéket `AAResourceGroupName` használja. |
| `SubscriptionID` | Kötelező | Az Automation-fiókhoz társított Azure-előfizetés azonosítója. |
| `TenantID` | Választható | Az Automation-fiókhoz társított bérlői szervezet azonosítója. |
| `WorkspaceName` | Választható | A Log Analytics munkaterület neve. Ha nem rendelkezik Log Analytics munkaterülettel, a szkript létrehoz és konfigurál egyet. |

> [!NOTE]
> A szolgáltatások engedélyezésekor Azure Automation csak bizonyos régiókat támogat, amelyekkel összekapcsolhat egy Log Analytics-munkaterületet és egy Automation-fiókot. A támogatott leképezési párok listáját lásd: [az Automation-fiók és a log Analytics munkaterület-hozzárendelési területe](how-to/region-mappings.md).

### <a name="step-2---open-windows-powershell-command-line-shell"></a>2. lépés – a Windows PowerShell parancssori rendszerhéj megnyitása

A **Start menüben** kattintson a **Start**gombra, írja be a **PowerShell**parancsot, kattintson a jobb gombbal a **Windows PowerShell**elemre, majd kattintson a **Futtatás rendszergazdaként**parancsra.

### <a name="step-3---run-the-powershell-script"></a>3. lépés – a PowerShell-szkript futtatása

A PowerShell parancssori felületén keresse meg a letöltött parancsfájlt tartalmazó mappát. Módosítsa a paraméterek (,,,, `AutomationAccountName` `AAResourceGroupName` `OMSResourceGroupName` `HybridGroupName` `SubscriptionID` és `WorkspaceName` ) értékeit. Ezután futtassa a parancsfájlt.

A parancsfájl futtatása után meg kell adnia a hitelesítést az Azure-ban. Be kell jelentkeznie egy olyan fiókkal, amely tagja az előfizetés-adminisztrátorok szerepkörnek, és az előfizetés közös rendszergazdája.

```powershell-interactive
.\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <nameOfAutomationAccount> -AAResourceGroupName <nameOfResourceGroup>`
-OMSResourceGroupName <nameOfOResourceGroup> -HybridGroupName <nameOfHRWGroup> `
-SubscriptionID <subscriptionId> -WorkspaceName <nameOfLogAnalyticsWorkspace>
```

### <a name="step-4---install-nuget"></a>4. lépés – a NuGet telepítése

A rendszer felszólítja, hogy fogadja el a NuGet telepítését, valamint az Azure-beli hitelesítő adataival történő hitelesítést. Ha nem rendelkezik a legújabb NuGet-verzióval, letöltheti az [elérhető NuGet-terjesztési verziókról](https://www.nuget.org/downloads).

### <a name="step-5---verify-the-deployment"></a>5. lépés – az üzemelő példány ellenőrzése

A szkript befejezése után az Automation-fiók hibrid feldolgozói csoportok lapja az új csoportot és a tagok számát jeleníti meg. Ha ez egy meglévő csoport, a tagok száma nő. Válassza ki a csoportot a hibrid munkavégző csoportok lapon a listából, és válassza a **hibrid feldolgozók** csempét. A hibrid dolgozók oldalon láthatja a csoport egyes tagjainak listáját.

## <a name="manual-deployment"></a>Manuális üzembe helyezés

Windows Hybrid Runbook Worker telepítéséhez és konfigurálásához hajtsa végre az alábbi lépéseket.

### <a name="step-1---verify-agent-is-reporting-to-workspace"></a>1. lépés – az ügynök ellenőrzése a munkaterületre

A Windows Log Analytics ügynöke csatlakoztatja a gépeket egy Azure Monitor Log Analytics-munkaterülethez. Amikor telepíti az ügynököt a gépre, és összekapcsolja a munkaterülettel, automatikusan letölti a hibrid Runbook-feldolgozóhoz szükséges összetevőket.

Ha az ügynök néhány perc elteltével sikeresen csatlakozott a Log Analytics munkaterülethez, a következő lekérdezés futtatásával ellenőrizheti, hogy a rendszer a szívverési adatokat küldi a munkaterületre.

```kusto
Heartbeat 
| where Category == "Direct Agent"
| where TimeGenerated > ago(30m)
```

A keresési eredmények között meg kell jelennie a gép szívverési rekordjainak, ami azt jelzi, hogy csatlakoztatva van, és a szolgáltatáshoz jelent jelentést. Alapértelmezés szerint minden ügynök egy szívverési rekordot továbbít a hozzárendelt munkaterülethez. Az ügynök telepítésének és telepítésének befejezéséhez kövesse az alábbi lépéseket.

1. Engedélyezze a szolgáltatásnak az ügynök számítógép hozzáadását. Update Management-és Azure-beli virtuális gépek esetében lásd: az [Automation-fiók Update Managementának engedélyezése](update-management/update-mgmt-enable-automation-account.md), [a Update Management a Azure Portal böngészésével](update-management/update-mgmt-enable-portal.md), a [Update Management runbook engedélyezése](update-management/update-mgmt-enable-runbook.md)vagy az [Azure-beli virtuális gépről való Update Management engedélyezése](update-management/update-mgmt-enable-vm.md). Change Tracking-és Azure-beli virtuális gépek esetében lásd: Azure-beli [virtuális gépek engedélyezése](automation-enable-changes-from-auto-acct.md#enable-azure-vms)és nem Azure-beli virtuális gépek esetén lásd: [számítógépek engedélyezése a munkaterületen](automation-enable-changes-from-auto-acct.md#enable-machines-in-the-workspace).

2. A hibrid Runbook-feldolgozó verziójának megerősítéséhez keresse meg `C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\` és jegyezze fel a **verzió** almappát.

### <a name="step-2---install-the-runbook-environment-and-connect-to-azure-automation"></a>2. lépés – a runbook-környezet telepítése és a Azure Automationhoz való kapcsolódás

Ha úgy konfigurálja az ügynököt, hogy egy Log Analytics munkaterületre jelentsen, az Azure Automation szolgáltatás leküldi a `HybridRegistration` parancsmagot tartalmazó PowerShell-modult `Add-HybridRunbookWorker` . Ezzel a parancsmaggal telepítheti a runbook környezetet a gépre, és regisztrálhatja Azure Automation.

Nyisson meg egy PowerShell-munkamenetet rendszergazdai módban, és futtassa a következő parancsokat a modul importálásához.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Most futtassa a `Add-HybridRunbookWorker` parancsmagot a következő szintaxis használatával.

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -Url <Url> -Key <String>
```

A paraméterekhez `Url` és az `Key` Automation-fiók Keys ( **kulcsok** ) lapjához szükséges információkat lekérheti. A lap bal oldalán található **Fiókbeállítások** szakaszban válassza a **kulcsok** lehetőséget.

![Kulcsok kezelése lap](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* A `Url` paraméter esetében másolja az **URL-cím**értékét.

* A `Key` paraméter esetében másolja az **elsődleges elérési kulcs**értékét.

* A `GroupName` paraméterhez használja a Hybrid Runbook Worker Group nevét. Ha ez a csoport már létezik az Automation-fiókban, az aktuális gép hozzá lesz adva. Ha ez a csoport nem létezik, a rendszer hozzáadja.

* Ha szükséges, állítsa be a `Verbose` paramétert a telepítés részleteinek fogadására.

### <a name="step-3----install-powershell-modules"></a>3. lépés – PowerShell-modulok telepítése

A runbookok a Azure Automation környezetében telepített modulokban meghatározott tevékenységeket és parancsmagokat is használhatják. Mivel ezeket a modulokat a rendszer nem telepíti automatikusan a helyszíni gépekre, manuálisan kell telepítenie azokat. A kivétel az Azure-modul. Ez a modul alapértelmezés szerint telepítve van, és hozzáférést biztosít a Azure Automation összes Azure-szolgáltatásának és tevékenységének parancsmagokhoz.

Mivel a hibrid Runbook-feldolgozó elsődleges célja a helyi erőforrások kezelése, valószínűleg telepítenie kell azokat a modulokat, amelyek támogatják ezeket az erőforrásokat, különösen a `PowerShellGet` modult. A Windows PowerShell-modulok telepítésével kapcsolatos információkért lásd: [Windows PowerShell](/powershell/scripting/developer/windows-powershell).

A telepített moduloknak a környezeti változó által hivatkozott helyen kell lenniük, `PSModulePath` hogy a hibrid feldolgozó automatikusan importálni tudja őket. További információkért lásd: [modulok telepítése a PSModulePath-ben](/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7).

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-windows-machine"></a><a name="remove-windows-hybrid-runbook-worker"></a>A hibrid Runbook-feldolgozó eltávolítása egy helyszíni Windows-gépről

1. A Azure Portal nyissa meg az Automation-fiókját.

2. A **Fiókbeállítások**területen válassza a **kulcsok** lehetőséget, és jegyezze fel az **URL-cím** és az **elsődleges elérési kulcs**értékét.

3. Nyisson meg egy PowerShell-munkamenetet rendszergazdai módban, és futtassa az alábbi parancsot az URL-cím és az elsődleges elérési kulcs értékeivel. Az `Verbose` eltávolítási folyamat részletes naplójához használja a paramétert. Ha el szeretné távolítani az elavult gépeket a hibrid feldolgozói csoportból, használja a választható `machineName` paramétert.

```powershell-interactive
Remove-HybridRunbookWorker -Url <URL> -Key <primaryAccessKey> -MachineName <computerName>
```

## <a name="remove-a-hybrid-worker-group"></a>Hibridfeldolgozó-csoport eltávolítása

Hibrid Runbook-feldolgozó csoport eltávolításához először el kell távolítania a hibrid Runbook-feldolgozót minden olyan gépről, amely tagja a csoportnak. Ezután a következő lépésekkel távolítsa el a csoportot:

1. Nyissa meg az Automation-fiókot a Azure Portal.

2. Válassza a **hibrid munkavégző csoportok** lehetőséget a **folyamat automatizálása**alatt. Válassza ki a törölni kívánt csoportot. Megjelenik az adott csoport tulajdonságok lapja.

   ![Tulajdonságok lap](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. A kiválasztott csoport Tulajdonságok lapján válassza a **Törlés**lehetőséget. Egy üzenet arra kéri, hogy erősítse meg ezt a műveletet. Válassza az **Igen** lehetőséget, ha biztos benne, hogy folytatni kívánja.

   ![Megerősítő üzenet](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Ez a folyamat több másodpercig is eltarthat. Az **Értesítések** menüpont alatt nyomon követheti a folyamat előrehaladását.

## <a name="next-steps"></a>További lépések

* Ha szeretné megtudni, hogyan konfigurálhatja a runbookok a helyszíni adatközpontban vagy más felhőalapú környezetben lévő folyamatok automatizálására, tekintse meg a [Runbookok futtatása hibrid Runbook-feldolgozón](automation-hrw-run-runbooks.md)című témakört.

* A hibrid Runbook-feldolgozók hibaelhárításával kapcsolatos további információkért lásd: [hibrid Runbook-feldolgozói problémák elhárítása](troubleshoot/hybrid-runbook-worker.md#general).
