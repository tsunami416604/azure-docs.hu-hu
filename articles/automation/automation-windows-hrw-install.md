---
title: Windows Hybrid Runbook Worker üzembe helyezése Azure Automation
description: Ez a cikk azt ismerteti, hogyan helyezhet üzembe egy hibrid Runbook-feldolgozót, amely a helyi adatközpontban vagy a felhőalapú környezetben lévő Windows-alapú gépeken futtatott runbookok futtatására használható.
services: automation
ms.subservice: process-automation
ms.date: 11/24/2020
ms.topic: conceptual
ms.openlocfilehash: e77a90617d79dab8c71cdf0c7a6a4fb21e052fbd
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182784"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Windows Hybrid Runbook Worker üzembe helyezése

A Azure Automation User Hybrid Runbook Worker funkciójának használatával közvetlenül az Azure-beli vagy nem Azure-beli gépen futtathatja a runbookok, beleértve az [Azure arc-kompatibilis kiszolgálókon](../azure-arc/servers/overview.md)regisztrált kiszolgálókat is. A szerepkört üzemeltető gépről vagy kiszolgálóról a runbookok közvetlenül, illetve a környezetben lévő erőforrásokkal is futtathatja a helyi erőforrások kezeléséhez.

A Azure Automation a runbookok tárolja és felügyeli, majd egy vagy több kijelölt gépre továbbítja azokat. Ez a cikk azt ismerteti, hogyan helyezhet üzembe egy hibrid Runbook-feldolgozót egy Windows rendszerű gépen, hogyan távolíthatja el a feldolgozót, és hogyan távolíthat el egy hibrid Runbook-feldolgozói csoportot.

A runbook-feldolgozó sikeres üzembe helyezése után tekintse át a [Runbookok futtatása hibrid runbook-feldolgozón](automation-hrw-run-runbooks.md) című témakört, amelyből megtudhatja, hogyan konfigurálhatja a runbookok a helyszíni adatközpontban vagy más felhőalapú környezetben lévő folyamatok automatizálására.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik a következőkkel.

### <a name="a-log-analytics-workspace"></a>Log Analytics munkaterület

A hibrid Runbook-feldolgozói szerepkör a szerepkör telepítéséhez és konfigurálásához Azure Monitor Log Analytics munkaterülettől függ. [Azure Resource Manager](../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace), a [PowerShell](../azure-monitor/scripts/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)vagy a [Azure Portal](../azure-monitor/learn/quick-create-workspace.md)segítségével hozhatja létre.

Ha nem rendelkezik Azure Monitor Log Analytics munkaterülettel, tekintse át a [Azure monitor log tervezési útmutatót](../azure-monitor/platform/design-logs-deployment.md) a munkaterület létrehozása előtt.

### <a name="log-analytics-agent"></a>Log Analytics-ügynök

A hibrid Runbook feldolgozói szerepkörhöz a [log Analytics ügynök](../azure-monitor/platform/log-analytics-agent.md) szükséges a támogatott Windows operációs rendszerhez. Az Azure-on kívül üzemeltetett kiszolgálók vagy gépek esetében a Log Analytics-ügynököt az [Azure arc-kompatibilis kiszolgálók](../azure-arc/servers/overview.md)használatával telepítheti.

### <a name="supported-windows-operating-system"></a>Támogatott Windows operációs rendszer

A hibrid Runbook Worker szolgáltatás a következő operációs rendszereket támogatja:

* Windows Server 2019
* Windows Server 2016, 1709-es és 1803-es verzió
* Windows Server 2012, 2012 R2
* Windows Server 2008 SP2 (x64), 2008 R2
* Windows 10 Enterprise (több munkamenetet is beleértve) és Pro
* Windows 8 Enterprise és Pro
* Windows 7 SP1

### <a name="minimum-requirements"></a>Minimális követelmények

A Windows rendszer és a felhasználó hibrid Runbook-feldolgozói minimális követelményei a következők:

* Windows PowerShell 5,1 ([WMF 5,1 letöltése](https://www.microsoft.com/download/details.aspx?id=54616)). A PowerShell Core nem támogatott.
* A .NET-keretrendszer 4.6.2-es vagy újabb verziója
* Két mag
* 4 GB RAM
* 443-es port (kimenő)

### <a name="network-configuration"></a>Hálózati konfiguráció

A hibrid Runbook-feldolgozók hálózati követelményeivel kapcsolatban lásd: [a hálózat konfigurálása](automation-hybrid-runbook-worker.md#network-planning).

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Gép felvétele hibrid Runbook Worker-csoportba

A munkavégző gépet hozzáadhatja egy hibrid Runbook Worker-csoporthoz az egyik Automation-fiókban. A Update Management által felügyelt, rendszerhibrid Runbook-feldolgozót futtató gépekhez hozzáadhatók egy hibrid Runbook Worker csoportjához. Azonban ugyanazt az Automation-fiókot kell használnia mind a Update Management, mind a hibrid Runbook-feldolgozói csoporttagság esetében.

>[!NOTE]
>Azure Automation [Update Management](./update-management/overview.md) automatikusan telepíti a hibrid Runbook-feldolgozót egy olyan Azure-beli vagy nem Azure-beli gépen, amely engedélyezve van a Update Management számára. Ez a feldolgozó azonban nincs regisztrálva az Automation-fiókjában található bármely hibrid Runbook Worker-csoportban. A runbookok ezen gépeken való futtatásához hozzá kell adnia őket egy hibrid Runbook-feldolgozói csoporthoz. A csoportba való felvételhez kövesse a [manuális üzembe helyezés](#manual-deployment) szakaszának 6. lépését.

## <a name="enable-for-management-with-azure-automation-state-configuration"></a>Felügyelet engedélyezése Azure Automation állapot-konfigurációval

További információ a gépek Azure Automation állapot-konfigurációval való felügyeletének engedélyezéséről: [a gépek Azure Automation állapot-konfiguráció általi felügyeletének engedélyezése](automation-dsc-onboarding.md).

> [!NOTE]
> A hibrid Runbook-feldolgozói szerepkört a kívánt állapot-konfigurációval (DSC) támogató gépek konfigurációjának kezeléséhez a gépeket DSC-csomópontként kell hozzáadnia.

## <a name="installation-options"></a>Telepítési lehetőségek

A Windows felhasználói hibrid Runbook-feldolgozók telepítéséhez és konfigurálásához az alábbi módszerek egyikét használhatja.

* Egy megadott PowerShell-szkripttel teljes mértékben [automatizálhatja](#automated-deployment) egy vagy több Windows rendszerű gép konfigurálásának folyamatát. Ez az ajánlott módszer az adatközpontban vagy más felhőalapú környezetben található gépekhez.
* Manuálisan importálja az Automation-megoldást, telepítse a Windows Log Analytics Agent ügynököt, és konfigurálja a feldolgozói szerepkört a gépen.

## <a name="automated-deployment"></a>Automatikus üzembe helyezés

Az automatikus telepítési módszer a PowerShell-parancsfájl **New-OnPremiseHybridWorker.ps1** segítségével automatizálja és konfigurálja a Windows Hybrid Runbook feldolgozói szerepkört. A következőket hajtja végre:

* Telepíti a szükséges modulokat.
* Bejelentkezik az Azure-fiókjával
* Ellenőrzi a megadott erőforráscsoport és Automation-fiók létezését
* Az Automation-fiók attribútumaira mutató hivatkozásokat hoz létre
* Ha nincs megadva, létrehoz egy Azure Monitor Log Analytics munkaterületet
* A Azure Automation megoldás engedélyezése a munkaterületen
* Töltse le és telepítse a Windows Log Analytics Agent ügynököt
* A gép regisztrálása hibrid Runbook-feldolgozóként

A következő lépések végrehajtásával telepítse a szerepkört a Windows rendszerű gépre a szkript használatával.

1. Töltse le a **New-OnPremiseHybridWorker.ps1** szkriptet a [PowerShell-galériaból](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker). A parancsfájl letöltése után másolja vagy futtassa azt a célszámítógépen. A **New-OnPremiseHybridWorker.ps1** parancsfájl a következő paramétereket használja a végrehajtás során.

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

2. Nyisson meg egy emelt szintű 64-bites PowerShell-parancssort.

3. A PowerShell-parancssorban keresse meg a letöltött parancsfájlt tartalmazó mappát. Módosítsa a paraméterek (,,,, `AutomationAccountName` `AAResourceGroupName` `OMSResourceGroupName` `HybridGroupName` `SubscriptionID` és `WorkspaceName` ) értékeit. Ezután futtassa a parancsfájlt.

    A parancsfájl futtatása után meg kell adnia a hitelesítést az Azure-ban. Be kell jelentkeznie egy olyan fiókkal, amely tagja az **előfizetés-adminisztrátorok** szerepkörnek, és az előfizetés közös rendszergazdája.

    ```powershell-interactive
    $NewOnPremiseHybridWorkerParameters = @{
      AutomationAccountName = <nameOfAutomationAccount>
      AAResourceGroupName   = <nameOfResourceGroup>
      OMSResourceGroupName  = <nameOfResourceGroup>
      HybridGroupName       = <nameOfHRWGroup>
      SubscriptionID        = <subscriptionId>
      WorkspaceName         = <nameOfLogAnalyticsWorkspace>
    }
    .\New-OnPremiseHybridWorker.ps1 @NewOnPremiseHybridWorkerParameters
    ```

4. A rendszer felszólítja, hogy fogadja el a NuGet telepítését, valamint az Azure-beli hitelesítő adataival történő hitelesítést. Ha nem rendelkezik a legújabb NuGet-verzióval, letöltheti az [elérhető NuGet-terjesztési verziókról](https://www.nuget.org/downloads).

5. A parancsfájl befejeződése után ellenőrizze a telepítést. Az Automation-fiók **Hybrid Runbook Worker** groups lapján a **felhasználó hibrid Runbook-feldolgozói csoport** lapon az új csoport és a tagok száma látható. Ha ez egy meglévő csoport, a tagok száma nő. A listából kiválaszthatja a kívánt csoportot a lap bal oldali menüjében a **hibrid feldolgozók** kiválasztása lehetőséggel. A **hibrid dolgozók** oldalon láthatja a csoport egyes tagjainak listáját.

## <a name="manual-deployment"></a>Manuális üzembe helyezés

Windows Hybrid Runbook Worker telepítéséhez és konfigurálásához hajtsa végre az alábbi lépéseket.

1. Engedélyezze a Azure Automation megoldást a Log Analytics munkaterületen, és futtassa a következő parancsot egy emelt szintű PowerShell-parancssorban vagy a [Azure Portal](https://portal.azure.com)Cloud Shellban.

    ```powershell
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resourceGroupName> -WorkspaceName <workspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
    ```

2. Telepítse a Log Analytics ügynököt a célszámítógépen.

    * Azure-beli virtuális gépek esetén a Windows rendszerhez készült [virtuálisgép-bővítmény](../virtual-machines/extensions/oms-windows.md)használatával telepítse a Windows log Analytics Agent ügynököt. A bővítmény telepíti a Log Analytics ügynököt az Azure Virtual Machines szolgáltatásban, és egy meglévő Log Analytics-munkaterületre regisztrálja a virtuális gépeket. Azure Resource Manager sablon, PowerShell vagy Azure Policy használatával hozzárendelheti az [üzembe helyezési log Analytics-ügynököt a *Linux* vagy a *Windows rendszerű* virtuális gépek](../governance/policy/samples/built-in-policies.md#monitoring) beépített házirendjéhez. Miután telepítette az ügynököt, a gép hozzáadhatók az Automation-fiókjában lévő hibrid Runbook Worker csoportjához.
    
    * A nem Azure-beli gépek esetében az Log Analytics-ügynököt az [Azure arc-kompatibilis kiszolgálók](../azure-arc/servers/overview.md)használatával telepítheti. Az arc-kompatibilis kiszolgálók a következő módszerekkel támogatják a Log Analytics ügynök telepítését:
    
        - A VM-bővítmények keretrendszer használata.
        
            Ez a funkció az Azure arc-kompatibilis kiszolgálókon lehetővé teszi, hogy az Log Analytics Agent virtuálisgép-bővítményt egy nem Azure-beli Windows-és/vagy Linux-kiszolgálóra telepítse. A virtuálisgép-bővítmények a következő módszerekkel kezelhetők a hibrid gépeken vagy az arc-kompatibilis kiszolgálók által felügyelt kiszolgálókon:
        
            - A [Azure Portal](../azure-arc/servers/manage-vm-extensions-portal.md)
            - Az [Azure CLI](../azure-arc/servers/manage-vm-extensions-cli.md)
            - [Azure PowerShell](../azure-arc/servers/manage-vm-extensions-powershell.md)
            - Azure [Resource Manager-sablonok](../azure-arc/servers/manage-vm-extensions-template.md)
        
        - Azure Policy használata.
        
            Ezzel a módszerrel a Azure Policy [log Analytics Agent üzembe helyezése Linux vagy a Windows Azure arc gépek](../governance/policy/samples/built-in-policies.md#monitoring) beépített házirendjének használatával naplózhatja, hogy az ív használatára képes kiszolgáló rendelkezik-e a log Analytics ügynökkel. Ha az ügynök nincs telepítve, az automatikusan szervizelési feladat használatával telepíti azt. Ha azt tervezi, hogy Azure Monitor for VMs használatával figyeli a gépeket, Ehelyett használja a [Azure monitor for VMS engedélyezése](../governance/policy/samples/built-in-initiatives.md#monitoring) kezdeményezést az log Analytics-ügynök telepítéséhez és konfigurálásához.

    Javasoljuk, hogy Azure Policy használatával telepítse a Windows vagy Linux rendszerhez készült Log Analytics-ügynököt.

3. Ellenőrizze, hogy az ügynök jelentést tesz-e a munkaterületre

    A Windows Log Analytics ügynöke csatlakoztatja a gépeket egy Azure Monitor Log Analytics-munkaterülethez. Amikor telepíti az ügynököt a gépre, és összekapcsolja a munkaterülettel, automatikusan letölti a hibrid Runbook-feldolgozóhoz szükséges összetevőket.

    Ha az ügynök néhány perc elteltével sikeresen csatlakozott a Log Analytics munkaterülethez, a következő lekérdezés futtatásával ellenőrizheti, hogy a rendszer a szívverési adatokat küldi a munkaterületre.

    ```kusto
    Heartbeat 
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    A keresési eredmények között meg kell jelennie a gép szívverési rekordjainak, ami azt jelzi, hogy csatlakoztatva van, és a szolgáltatáshoz jelent jelentést. Alapértelmezés szerint minden ügynök egy szívverési rekordot továbbít a hozzárendelt munkaterülethez. Az ügynök telepítésének és telepítésének befejezéséhez kövesse az alábbi lépéseket.

4. Erősítse meg a hibrid Runbook-feldolgozó verzióját a Log Analytics ügynököt futtató gépen, keresse meg `C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\` és jegyezze fel a **verzió** almappát. Ez a mappa néhány perc múlva megjelenik a gépen, miután a megoldás engedélyezve van a munkaterületen.

5. Telepítse a runbook-környezetet, és kapcsolódjon a Azure Automationhoz. Ha úgy konfigurálja az ügynököt, hogy egy Log Analytics munkaterületre jelentsen, és importálja az **Automation** -megoldást, a megoldás leküldi a `HybridRegistration` PowerShell-modult. Ez a modul tartalmazza a `Add-HybridRunbookWorker` parancsmagot. Ezzel a parancsmaggal telepítheti a runbook környezetet a gépre, és regisztrálhatja Azure Automation.

    Nyisson meg egy PowerShell-munkamenetet rendszergazdai módban, és futtassa a következő parancsokat a modul importálásához.

    ```powershell-interactive
    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module .\HybridRegistration.psd1
    ```

6. Futtassa a `Add-HybridRunbookWorker` parancsmagot a következő paraméterek értékeinek megadásával: `Url` `Key` , és `GroupName` .

    ```powershell-interactive
    Add-HybridRunbookWorker –GroupName <String> -Url <Url> -Key <String>
    ```

    A paraméterekhez `Url` és az `Key` Automation-fiók Keys ( **kulcsok** ) lapjához szükséges információkat lekérheti. A lap bal oldalán található **Fiókbeállítások** szakaszban válassza a **kulcsok** lehetőséget.

    ![Kulcsok kezelése lap](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * A `Url` paraméter esetében másolja az **URL-cím** értékét.

    * A `Key` paraméter esetében másolja az **elsődleges elérési kulcs** értékét.

    * A `GroupName` paraméterhez használja a Hybrid Runbook Worker Group nevét. Ha ez a csoport már létezik az Automation-fiókban, az aktuális gép hozzá lesz adva. Ha ez a csoport nem létezik, a rendszer hozzáadja.

    * Ha szükséges, állítsa be a `Verbose` paramétert a telepítés részleteinek fogadására.

7. A parancs befejezése után ellenőrizze a telepítést. Az Automation-fiók **Hybrid Runbook Worker** groups lapján a **felhasználó hibrid Runbook-feldolgozói csoport** lapon az új vagy a meglévő csoport és a tagok száma látható. Ha ez egy meglévő csoport, a tagok száma nő. A listából kiválaszthatja a kívánt csoportot a lap bal oldali menüjében a **hibrid feldolgozók** kiválasztása lehetőséggel. A **hibrid dolgozók** oldalon láthatja a csoport egyes tagjainak listáját.

## <a name="install-powershell-modules"></a>PowerShell-modulok telepítése

A runbookok a Azure Automation környezetében telepített modulokban meghatározott tevékenységeket és parancsmagokat is használhatják. Mivel ezeket a modulokat a rendszer nem telepíti automatikusan a helyszíni gépekre, manuálisan kell telepítenie azokat. A kivétel az Azure-modul. Ez a modul alapértelmezés szerint telepítve van, és hozzáférést biztosít a Azure Automation összes Azure-szolgáltatásának és tevékenységének parancsmagokhoz.

Mivel a hibrid Runbook-feldolgozó elsődleges célja a helyi erőforrások kezelése, valószínűleg telepítenie kell azokat a modulokat, amelyek támogatják ezeket az erőforrásokat, különösen a `PowerShellGet` modult. A Windows PowerShell-modulok telepítésével kapcsolatos információkért lásd: [Windows PowerShell](/powershell/scripting/developer/windows-powershell).

A telepített moduloknak a környezeti változó által hivatkozott helyen kell lenniük, `PSModulePath` hogy a hibrid feldolgozó automatikusan importálni tudja őket. További információkért lásd: [modulok telepítése a PSModulePath-ben](/powershell/scripting/developer/module/installing-a-powershell-module).

## <a name="remove-the-hybrid-runbook-worker"></a><a name="remove-windows-hybrid-runbook-worker"></a>A hibrid Runbook Worker eltávolítása

1. A Azure Portal nyissa meg az Automation-fiókját.

2. A **Fiókbeállítások** területen válassza a **kulcsok** lehetőséget, és jegyezze fel az **URL-cím** és az **elsődleges elérési kulcs** értékét.

3. Nyisson meg egy PowerShell-munkamenetet rendszergazdai módban, és futtassa az alábbi parancsot az URL-cím és az elsődleges elérési kulcs értékeivel. Az `Verbose` eltávolítási folyamat részletes naplójához használja a paramétert. Ha el szeretné távolítani az elavult gépeket a hibrid feldolgozói csoportból, használja a választható `machineName` paramétert.

```powershell-interactive
Remove-HybridRunbookWorker -Url <URL> -Key <primaryAccessKey> -MachineName <computerName>
```

## <a name="remove-a-hybrid-worker-group"></a>Hibridfeldolgozó-csoport eltávolítása

Hibrid Runbook-feldolgozó csoport eltávolításához először el kell távolítania a hibrid Runbook-feldolgozót minden olyan gépről, amely tagja a csoportnak. Ezután a következő lépésekkel távolítsa el a csoportot:

1. Nyissa meg az Automation-fiókot a Azure Portal.

2. Válassza a **hibrid munkavégző csoportok** lehetőséget a **folyamat automatizálása** alatt. Válassza ki a törölni kívánt csoportot. Megjelenik az adott csoport tulajdonságok lapja.

   ![Tulajdonságok lap](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. A kiválasztott csoport Tulajdonságok lapján válassza a **Törlés** lehetőséget. Egy üzenet arra kéri, hogy erősítse meg ezt a műveletet. Válassza az **Igen** lehetőséget, ha biztos benne, hogy folytatni kívánja.

   ![Megerősítő üzenet](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Ez a folyamat több másodpercig is eltarthat. Az **Értesítések** menüpont alatt nyomon követheti a folyamat előrehaladását.

## <a name="next-steps"></a>Következő lépések

* Ha szeretné megtudni, hogyan konfigurálhatja a runbookok a helyszíni adatközpontban vagy más felhőalapú környezetben lévő folyamatok automatizálására, tekintse meg a [Runbookok futtatása hibrid Runbook-feldolgozón](automation-hrw-run-runbooks.md)című témakört.

* A hibrid Runbook-feldolgozók hibaelhárításával kapcsolatos további információkért lásd: [hibrid Runbook-feldolgozói problémák elhárítása](troubleshoot/hybrid-runbook-worker.md#general).