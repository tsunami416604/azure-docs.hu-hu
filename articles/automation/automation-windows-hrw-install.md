---
title: Az Azure Automation hibrid runbook-feldolgozója Windowsra
description: Ez a cikk a Azure Automation hibrid Runbook-feldolgozók telepítésével kapcsolatos információkat tartalmaz, amelyekkel a helyi adatközpontban vagy a felhőalapú környezetben Windows-alapú számítógépeken futtathatja a runbookok.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: fc6d3bbe1580c4e6f7064c957a9d420555296231
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372570"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Windows Hybrid Runbook Worker üzembe helyezése

A Azure Automation Hybrid Runbook Worker szolgáltatásával a runbookok közvetlenül a szerepkört üzemeltető számítógépen és a környezetben található erőforrásokon futtathatja a helyi erőforrások kezeléséhez. Azure Automation tárolja és kezeli a runbookok, majd egy vagy több kijelölt számítógéphez továbbítja azokat. Ez a cikk bemutatja, hogyan helyezhet üzembe egy hibrid Runbook-feldolgozót egy Windows rendszerű gépen.

A runbook-feldolgozó sikeres üzembe helyezése után tekintse át a [Runbookok futtatása hibrid runbook-feldolgozón](automation-hrw-run-runbooks.md) című témakört, amelyből megtudhatja, hogyan konfigurálhatja a runbookok a helyszíni adatközpontban vagy más felhőalapú környezetben lévő folyamatok automatizálására.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az az modul telepítési útmutatója a hibrid Runbook-feldolgozón: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Az Automation-fiók esetében a modulokat a legújabb verzióra frissítheti a [Azure Automation Azure PowerShell moduljainak frissítésével](automation-update-azure-modules.md).

## <a name="windows-hybrid-runbook-worker-installation-and-configuration"></a>Windows hibrid Runbook Worker telepítése és konfigurálása

A Windows Hybrid Runbook Worker telepítéséhez és konfigurálásához az alábbi módszerek egyikét használhatja.

* Azure-beli virtuális gépek esetén a Windows rendszerhez készült [virtuálisgép-bővítmény](../virtual-machines/extensions/oms-windows.md)használatával telepítse a Windows log Analytics Agent ügynököt. A bővítmény telepíti a Log Analytics ügynököt az Azure Virtual Machines szolgáltatásban, és egy Azure Resource Manager sablonnal vagy a PowerShell használatával regisztrálja a virtuális gépeket egy meglévő Log Analytics-munkaterületen. Miután telepítette az ügynököt, a virtuális gép hozzáadható az Automation-fiókjában található hibrid Runbook Worker-csoporthoz a [manuális telepítés](#manual-deployment) szakasz 4. lépésével.

* Automatizálási runbook segítségével teljes mértékben automatizálhatja a Windows rendszerű számítógépek konfigurálásának folyamatát. Ez az ajánlott módszer az adatközpontban vagy más felhőalapú környezetben található gépekhez.

* A hibrid Runbook-feldolgozói szerepkört a nem Azure-beli virtuális gépen manuálisan telepítheti és konfigurálhatja a lépésenkénti eljárást követve.

> [!NOTE]
> A hibrid Runbook-feldolgozói szerepkört támogató kiszolgálók konfigurációjának kezeléséhez a kívánt állapot-konfigurációval (DSC) hozzá kell adnia a kiszolgálókat DSC-csomópontként.

### <a name="minimum-requirements-for-windows-hybrid-runbook-worker"></a>A Windows Hybrid Runbook Worker minimális követelményei

A Windows Hybrid Runbook Worker minimális követelményei a következők:

* Windows Server 2012 vagy újabb
* Windows PowerShell 5,1 vagy újabb ([WMF 5,1 letöltése](https://www.microsoft.com/download/details.aspx?id=54616))
* A .NET-keretrendszer 4.6.2-es vagy újabb verziója
* Két mag
* 4 GB RAM
* 443-es port (kimenő)

### <a name="network-configuration"></a>Hálózati konfiguráció

A hibrid Runbook-feldolgozók további hálózati követelményeinek eléréséhez lásd: [a hálózat konfigurálása](automation-hybrid-runbook-worker.md#network-planning).

### <a name="server-onboarding-for-management-with-automation-dsc"></a>Kiszolgáló bevezetését Automation DSC

További információ a DSC-vel való felügyelethez szükséges bevezetési kiszolgálókról: [bevezetési gépek a Azure Automation DSC általi felügyelethez](automation-dsc-onboarding.md).

A [Update Management megoldás](../operations-management-suite/oms-solution-update-management.md) engedélyezése automatikusan konfigurálja a log Analytics-munkaterülethez csatlakoztatott Windows-számítógépeket hibrid Runbook-feldolgozóként a megoldásban foglalt runbookok támogatásához. Ez a feldolgozó azonban nincs regisztrálva az Automation-fiókban már definiált hibrid Runbook-feldolgozói csoportokkal.

### <a name="addition-of-the-computer-to-a-hybrid-runbook-worker-group"></a>A számítógép hozzáadása egy hibrid Runbook Worker-csoporthoz

Az Automation-fiókban felveheti a munkavégző számítógépet egy hibrid Runbook Worker-csoportba. Vegye figyelembe, hogy az Automation runbookok is támogatnia kell, ha ugyanazt a fiókot használja mind a megoldáshoz, mind a hibrid Runbook Worker Group-tagsághoz. Ez a funkció a hibrid runbook-feldolgozó 7.2.12024.0-s verziójától érhető el.

## <a name="automated-deployment"></a>Automatikus üzembe helyezés

A célszámítógépen hajtsa végre a következő lépéseket a Windows hibrid feldolgozói szerepkör telepítésének és konfigurálásának automatizálásához.

### <a name="step-1---download-the-powershell-script"></a>1\. lépés – a PowerShell-parancsfájl letöltése

Töltse le a **New-OnPremiseHybridWorker. ps1** parancsfájlt a [PowerShell-Galéria](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker). A letöltést közvetlenül a hibrid Runbook-feldolgozói szerepkört futtató számítógépről vagy a környezet egy másik számítógépéről kell letölteni. Ha letöltötte a parancsfájlt, másolja a munkavégzőbe. A **New-OnPremiseHybridWorker. ps1** parancsfájl az alább leírt paramétereket használja a végrehajtás során.

| Paraméter | status | Leírás |
| --------- | ------ | ----------- |
| *AAResourceGroupName* | Kötelező | Az Automation-fiókhoz társított erőforráscsoport neve. |
| *AutomationAccountName* | Kötelező | Az Automation-fiók neve.
| *Hitelesítőadat* | Optional | Az Azure-környezetbe való bejelentkezéskor használandó hitelesítő adatok. |
| *HybridGroupName* | Kötelező | Annak a hibrid Runbook-feldolgozó csoportnak a neve, amelyet a jelen forgatókönyvet támogató runbookok célként határoz meg. |
| *OMSResourceGroupName* | Optional | Az Log Analytics munkaterület erőforráscsoport neve. Ha ez az erőforráscsoport nincs megadva, a rendszer a *AAResourceGroupName* értékét használja. |
| *SubscriptionID* | Kötelező | Az Automation-fiókhoz társított Azure-előfizetés azonosítója. |
| *TenantID* | Optional | Az Automation-fiókhoz társított bérlői szervezet azonosítója. |
| *WorkspaceName* | Optional | A Log Analytics munkaterület neve. Ha nem rendelkezik Log Analytics munkaterülettel, a szkript létrehoz és konfigurál egyet. |

> [!NOTE]
> A megoldások engedélyezésekor Azure Automation csak bizonyos régiókat támogat, Log Analytics munkaterület és egy Automation-fiók összekapcsolásához. A támogatott leképezési párok listáját lásd: [az Automation-fiók és a log Analytics munkaterület-hozzárendelési területe](how-to/region-mappings.md).

### <a name="step-2---open-windows-powershell-command-line-shell"></a>2\. lépés – a Windows PowerShell parancssori rendszerhéj megnyitása

Nyissa meg a **Windows PowerShellt** a **kezdőképernyőn** rendszergazdai módban.

### <a name="step-3---run-the-powershell-script"></a>3\. lépés – a PowerShell-szkript futtatása

A PowerShell parancssori felületén keresse meg a letöltött parancsfájlt tartalmazó mappát. Módosítsa a *AutomationAccountName*, a *AAResourceGroupName*, a *OMSResourceGroupName*, a *HybridGroupName*, a *SubscriptionID*és a *WorkspaceName*paraméterek értékeit. Ezután futtassa a parancsfájlt.

A parancsfájl futtatása után meg kell adnia a hitelesítést az Azure-ban. Be kell jelentkeznie egy olyan fiókkal, amely tagja az előfizetés-adminisztrátorok szerepkörnek, és az előfizetés közös rendszergazdája.

```powershell-interactive
.\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
-OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
-SubscriptionID <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
```

### <a name="step-4---install-nuget"></a>4\. lépés – a NuGet telepítése

A rendszer felszólítja, hogy fogadja el a NuGet telepítését, valamint az Azure-beli hitelesítő adataival történő hitelesítést. Ha nem rendelkezik a legújabb NuGet-verzióval, az [elérhető NuGet-terjesztési verziókról](https://www.nuget.org/downloads)szerezheti be.

### <a name="step-5---verify-the-deployment"></a>5\. lépés – az üzemelő példány ellenőrzése

A szkript befejezése után a hibrid feldolgozói csoportok lapon az új csoport és a tagok száma látható. Ha ez egy meglévő csoport, a tagok száma nő. Válassza ki a csoportot a hibrid munkavégző csoportok lapon a listából, és válassza a **hibrid feldolgozók** csempét. A hibrid dolgozók oldalon láthatja a csoport egyes tagjainak listáját.

## <a name="manual-deployment"></a>Manuális üzembe helyezés

A célszámítógépen végezze el az első két lépést egyszer az Automation-környezethez. Ezután hajtsa végre a fennmaradó lépéseket az egyes munkavégző számítógépeken.

### <a name="step-1---create-a-log-analytics-workspace"></a>1\. lépés – Log Analytics munkaterület létrehozása

Ha még nem rendelkezik Log Analytics munkaterülettel, tekintse át a [Azure monitor log tervezési útmutatót](../azure-monitor/platform/design-logs-deployment.md) a munkaterület létrehozása előtt.

### <a name="step-2---add-the-automation-solution-to-the-log-analytics-workspace"></a>2\. lépés – az Automation-megoldás hozzáadása a Log Analytics munkaterülethez

Az Automation-megoldás funkciókkal bővíti a Azure Automation, beleértve a hibrid Runbook-feldolgozó támogatását is. Amikor hozzáadja a megoldást a Log Analytics munkaterülethez, a automatikusan leküldi az ügynököt a következő lépésben leírtaknak megfelelően telepített Worker-összetevőknek.

Az Automation-megoldás munkaterülethez való hozzáadásához futtassa a következő PowerShell-parancsmagot.

```powershell-interactive
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true -DefaultProfile <IAzureContextContainer>
```

### <a name="step-3---install-the-log-analytics-agent-for-windows"></a>3\. lépés – a Windows Log Analytics ügynökének telepítése

A Windows Log Analytics ügynöke csatlakoztatja a számítógépeket egy Azure Monitor Log Analytics-munkaterülethez. Amikor telepíti az ügynököt a számítógépre, és összekapcsolja a munkaterülettel, automatikusan letölti a hibrid Runbook-feldolgozóhoz szükséges összetevőket.

Ha az ügynököt a számítógépre szeretné telepíteni, kövesse a [Windows rendszerű számítógépek Összekapcsolása Azure monitor naplókhoz](../log-analytics/log-analytics-windows-agent.md)című témakör útmutatását. Ezt a folyamatot több számítógép esetében is megismételheti, ha több feldolgozót ad hozzá a környezethez.

Ha az ügynök néhány perc elteltével sikeresen csatlakozott a Log Analytics munkaterülethez, a következő lekérdezés futtatásával ellenőrizheti, hogy a rendszer a szívverési adatokat küldi a munkaterületre.

```kusto
Heartbeat 
| where Category == "Direct Agent" 
| where TimeGenerated > ago(30m)
```

A keresési eredmények között meg kell jelennie a számítógép szívverési rekordjainak, ami azt jelzi, hogy csatlakoztatva van, és jelentést küld a szolgáltatásnak. Alapértelmezés szerint minden ügynök egy szívverési rekordot továbbít a hozzárendelt munkaterülethez. Ellenőrizheti, hogy az ügynök megfelelően letöltötte-e az Automation-megoldást, ha az **AzureAutomationFiles** nevű mappában található a **C:\Program Files\Microsoft monitoring Agent\Agent**. A hibrid Runbook-feldolgozó verziójának megerősítéséhez keresse meg a **C:\Program Files\Microsoft monitoring Agent\Agent\AzureAutomation** , és jegyezze fel a **verzió** almappát.

### <a name="step-4---install-the-runbook-environment-and-connect-to-azure-automation"></a>4\. lépés – a runbook-környezet telepítése és a Azure Automationhoz való kapcsolódás

Ha úgy konfigurálja az ügynököt, hogy egy Log Analytics munkaterületre jelentsen, az Automation-megoldás leküldi a **HybridRegistration** PowerShell-modult, amely tartalmazza az **Add-HybridRunbookWorker** parancsmagot. Ezzel a parancsmaggal telepítheti a számítógép runbook-környezetét, és regisztrálhatja Azure Automation.

Nyisson meg egy PowerShell-munkamenetet rendszergazdai módban, és futtassa a következő parancsokat a modul importálásához.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Most futtassa az **Add-HybridRunbookWorker** parancsmagot a következő szintaxis használatával.

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

A parancsmaghoz szükséges adatokat a Azure Portal kulcsok kezelése lapján érheti el. Az Automation-fiók beállítások lapján található **kulcsok** lehetőség kiválasztásával nyissa meg ezt a lapot.

![Kulcsok kezelése lap](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* A *Csoportnév* paraméternél használja a hibrid Runbook Worker csoport nevét. Ha ez a csoport már létezik az Automation-fiókban, az aktuális számítógép hozzá lesz adva. Ha ez a csoport nem létezik, a rendszer hozzáadja.
* A *végpont* paraméternél használja az **URL-címet** a kulcsok kezelése lapon.
* A *jogkivonat* paraméternél használja a kulcsok kezelése lap **elsődleges hozzáférési kulcs** bejegyzését.
* Ha szükséges, állítsa be a *részletes* paramétert a telepítés részleteinek fogadásához.

### <a name="step-5----install-powershell-modules"></a>5\. lépés – PowerShell-modulok telepítése

A runbookok a Azure Automation környezetében telepített modulokban meghatározott tevékenységeket és parancsmagokat is használhatják. Mivel ezeket a modulokat a rendszer nem telepíti automatikusan a helyszíni számítógépekre, manuálisan kell telepítenie azokat. A kivétel az Azure-modul. Ez a modul alapértelmezés szerint telepítve van, és hozzáférést biztosít a Azure Automation összes Azure-szolgáltatásának és tevékenységének parancsmagokhoz.

Mivel a hibrid Runbook-feldolgozó funkció elsődleges célja a helyi erőforrások kezelése, valószínűleg telepítenie kell azokat a modulokat, amelyek támogatják ezeket az erőforrásokat, különösen a **PowerShellGet** modult. A Windows PowerShell-modulok telepítésével kapcsolatos információkért lásd: [Windows PowerShell](https://docs.microsoft.com/powershell/scripting/developer/windows-powershell).

A telepített moduloknak a *PSModulePath* környezeti változó által hivatkozott helyen kell lenniük, hogy a hibrid feldolgozó automatikusan importálni tudja azokat. További információkért lásd: [modulok telepítése a PSModulePath-ben](https://docs.microsoft.com/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7).

## <a name="next-steps"></a>Következő lépések

* Ha szeretné megtudni, hogyan konfigurálhatja a runbookok a helyszíni adatközpontban vagy más felhőalapú környezetben lévő folyamatok automatizálására, tekintse meg a [Runbookok futtatása hibrid Runbook-feldolgozón](automation-hrw-run-runbooks.md)című témakört.
* A hibrid Runbook-feldolgozók eltávolítására vonatkozó utasításokért lásd: [Azure Automation hibrid Runbook-feldolgozók eltávolítása](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
* A hibrid Runbook-feldolgozók hibaelhárításával kapcsolatos további információkért lásd: [Windows Hybrid Runbook-feldolgozók hibaelhárítása](troubleshoot/hybrid-runbook-worker.md#windows).
* Az Update Management problémáinak hibaelhárításával kapcsolatos további lépéseket a [Update Management: hibaelhárítás](troubleshoot/update-management.md)című témakörben talál.
