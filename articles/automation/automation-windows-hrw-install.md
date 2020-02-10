---
title: Az Azure Automation hibrid runbook-feldolgozója Windowsra
description: Ez a cikk a Azure Automation hibrid Runbook-feldolgozók telepítésével kapcsolatos információkat tartalmaz, amelyekkel a helyi adatközpontban vagy a felhőalapú környezetben Windows-alapú számítógépeken futtathatja a runbookok.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 04b5727a1c5abf8eb44ada566847456c2100b2a9
ms.sourcegitcommit: 323c3f2e518caed5ca4dd31151e5dee95b8a1578
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2020
ms.locfileid: "77111490"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Windows Hybrid Runbook Worker üzembe helyezése

A Azure Automation Hybrid Runbook Worker szolgáltatásával a runbookok közvetlenül a szerepkört üzemeltető számítógépen és a környezetben található erőforrásokon futtathatja a helyi erőforrások kezeléséhez. A runbookok tárolása és kezelése Azure Automation, majd egy vagy több kijelölt számítógépre történik. Ez a cikk azt ismerteti, hogyan telepítheti a hibrid Runbook-feldolgozót egy Windows rendszerű gépre.

> [!NOTE]
A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.3.0). Az az modul telepítési utasításait lásd: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.4.0).

## <a name="installing-the-windows-hybrid-runbook-worker"></a>A Windows Hybrid Runbook Worker telepítése

A Windows Hybrid Runbook Worker telepítéséhez és konfigurálásához a következő három módszer egyikét használhatja:

* Az Azure-beli virtuális gépek esetében a Windows rendszerhez készült [virtuálisgép-bővítmény](../virtual-machines/extensions/oms-windows.md)használatával telepíti a Windows log Analytics Agent ügynököt. A bővítmény telepíti a Log Analytics ügynököt az Azure Virtual Machines szolgáltatásban, és egy Azure Resource Manager sablonnal vagy a PowerShell-lel egy meglévő Log Analytics-munkaterületre regisztrálja a virtuális gépeket. Miután telepítette az ügynököt, a virtuális gép hozzáadható az Automation-fiókjában található hibrid Runbook Worker-csoporthoz az alábbi [manuális központi telepítési](#manual-deployment) szakaszban leírt **4. lépéssel** .

* Automatizálási runbook segítségével teljes mértékben automatizálhatja a Windows rendszerű számítógépek konfigurálásának folyamatát. Ez az ajánlott módszer a gépekhez az adatközpontban vagy más felhőalapú környezetben.

* A hibrid Runbook-feldolgozói szerepkört a nem Azure-beli virtuális gépen manuálisan telepítheti és konfigurálhatja a lépésenkénti eljárást követve.

> [!NOTE]
> A hibrid Runbook-feldolgozói szerepkört támogató kiszolgálók konfigurációjának kezeléséhez a kívánt állapot-konfigurációval (DSC) hozzá kell adnia őket DSC-csomópontként.

A Windows Hybrid Runbook Worker minimális követelményei a következők:

* Windows Server 2012 vagy újabb
* Windows PowerShell 5,1 vagy újabb ([WMF 5,1 letöltése](https://www.microsoft.com/download/details.aspx?id=54616))
* A .NET-keretrendszer 4.6.2-es vagy újabb verziója
* Két mag
* 4 GB RAM
* 443-es port (kimenő)

A hibrid Runbook-feldolgozók további hálózati követelményeinek eléréséhez lásd: [a hálózat konfigurálása](automation-hybrid-runbook-worker.md#network-planning).

További információ a DSC-vel való felügyelethez szükséges bevezetési kiszolgálókról: [bevezetési gépek a Azure Automation DSC általi felügyelethez](automation-dsc-onboarding.md).
Ha engedélyezi a [Update Management megoldást](../operations-management-suite/oms-solution-update-management.md), a log Analytics-munkaterülethez csatlakoztatott összes Windows-számítógép automatikusan hibrid Runbook-feldolgozóként van konfigurálva a megoldásban foglalt runbookok támogatásához. Azonban nincs regisztrálva az Automation-fiókban már definiált hibrid feldolgozói csoportokkal. 

A számítógép hozzáadhatók az Automation-fiókban található hibrid Runbook Worker csoporthoz az Automation-runbookok támogatásához, ha ugyanazt a fiókot használja mind a megoldáshoz, mind a hibrid Runbook-feldolgozói csoport tagságához. Ez a funkció a hibrid runbook-feldolgozó 7.2.12024.0-s verziójától érhető el.

A runbook-feldolgozó sikeres üzembe helyezése után tekintse át a [Runbookok futtatása hibrid runbook-feldolgozón](automation-hrw-run-runbooks.md) című témakört, amelyből megtudhatja, hogyan konfigurálhatja a runbookok a helyszíni adatközpontban vagy más felhőalapú környezetben lévő folyamatok automatizálására.

### <a name="automated-deployment"></a>Automatikus üzembe helyezés

A Windows hibrid feldolgozói szerepkör telepítésének és konfigurálásának automatizálásához hajtsa végre a következő lépéseket:

1. Töltse le a New-OnPremiseHybridWorker. ps1 parancsfájlt a [PowerShell-Galéria](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker) közvetlenül a hibrid Runbook-feldolgozói szerepkört futtató számítógépről vagy a környezet egy másik számítógépéről. Másolja a szkriptet a feldolgozóba. A New-OnPremiseHybridWorker. ps1 parancsfájl a következő paramétereket igényli a végrehajtás során:

   * *AAResourceGroupName* (kötelező): az Automation-fiókhoz társított erőforráscsoport neve.
   * *OMSResourceGroupName* (nem kötelező): az erőforráscsoport neve a log Analytics munkaterülethez. Ha ez az erőforráscsoport nincs megadva, a rendszer a *AAResourceGroupName* használja.
   * *SubscriptionID* (kötelező): az Automation-fiókhoz tartozó Azure-előfizetés azonosítója.
   * *TenantID* (nem kötelező): az Automation-fiókhoz társított bérlői szervezet azonosítója.
   * *WorkspaceName* (nem kötelező): a log Analytics munkaterület neve. Ha nem rendelkezik Log Analytics munkaterülettel, a szkript létrehoz és konfigurál egyet.
   * *AutomationAccountName* (kötelező): az Automation-fiók neve.
   * *HybridGroupName* (kötelező): annak a hibrid Runbook-feldolgozói csoportnak a neve, amelyet a jelen forgatókönyvet támogató runbookok célként határozhat meg.
   * *Hitelesítő adatok* (nem kötelező): az Azure-környezetbe való bejelentkezéskor használandó hitelesítő adatok.
  
   > [!NOTE]
   > A megoldások engedélyezésekor csak bizonyos régiók esetén lehet összekapcsolni egy Log Analytics-munkaterületet és egy Automation-fiókot.
   >
   > A támogatott leképezési párok listáját lásd: [az Automation-fiók és a log Analytics munkaterület-hozzárendelési területe](how-to/region-mappings.md).

2. A számítógépén nyissa meg a **Windows PowerShellt** a **kezdőképernyőn** rendszergazdai módban.
3. A PowerShell parancssori rendszerhéjból keresse meg a letöltött parancsfájlt tartalmazó mappát. Módosítsa a paraméterek értékeit:- *AutomationAccountName*, *-AAResourceGroupName*, *-OMSResourceGroupName*, *-HybridGroupName*, *-SubscriptionId*és *-WorkspaceName*. Ezután futtassa a parancsfájlt.

     > [!NOTE]
     > A parancsfájl futtatása után meg kell adnia a hitelesítést az Azure-ban. Be *kell* jelentkeznie egy olyan fiókkal, amely tagja az előfizetés-adminisztrátorok szerepkörnek, és az előfizetés közös rendszergazdája.

   ```powershell-interactive
   .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
   -OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
   -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
   ```

4. A rendszer felszólítja, hogy fogadja el a NuGet telepítését, és a rendszer felszólítja az Azure-beli hitelesítő adataival történő hitelesítésre.

5. A szkript befejezése után a **hibrid feldolgozói csoportok** lapon az új csoport és a tagok száma látható. Ha ez egy meglévő csoport, a tagok száma nő. Válassza ki a csoportot a **hibrid munkavégző csoportok** lapon a listából, és válassza a **hibrid feldolgozók** csempét. A **hibrid dolgozók** oldalon láthatja a csoport egyes tagjait.

### <a name="manual-deployment"></a>Manuális üzembe helyezés

Végezze el az első két lépést egyszer az Automation-környezethez, majd ismételje meg a hátralévő lépéseket az egyes munkavégző számítógépeken.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

#### <a name="1-create-a-log-analytics-workspace"></a>1. Log Analytics munkaterület létrehozása

Ha még nem rendelkezik Log Analytics munkaterülettel, először tekintse át a [Azure monitor log tervezési útmutatót](../azure-monitor/platform/design-logs-deployment.md) a munkaterület létrehozása előtt. 

#### <a name="2-add-the-automation-solution-to-the-log-analytics-workspace"></a>2. az Automation-megoldás hozzáadása a Log Analytics munkaterülethez

Az Automation-megoldás funkciókat biztosít a Azure Automationhoz, beleértve a hibrid Runbook-feldolgozók támogatását is. Amikor hozzáadja a megoldást a Log Analytics munkaterülethez, a automatikusan leküldi a feldolgozó összetevőket a következő lépésben telepítendő ügynök számítógépének.

Az **Automation** -megoldás munkaterülethez való hozzáadásához futtassa a következő PowerShell-parancsmagot.

```powershell-interactive
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true -DefaultProfile <IAzureContextContainer>
```

#### <a name="3-install-the-log-analytics-agent-for-windows"></a>3. Telepítse a Windows Log Analytics Agent ügynököt

A Windows Log Analytics ügynöke csatlakoztatja a számítógépeket egy Azure Monitor Log Analytics-munkaterülethez. Amikor telepíti az ügynököt a számítógépre, és összekapcsolja a munkaterülettel, automatikusan letölti a hibrid Runbook-feldolgozóhoz szükséges összetevőket.

Ha az ügynököt a számítógépre szeretné telepíteni, kövesse a [Windows rendszerű számítógépek Összekapcsolása Azure monitor naplókhoz](../log-analytics/log-analytics-windows-agent.md)című témakör útmutatását. Ezt a folyamatot több számítógép esetében is megismételheti, ha több feldolgozót ad hozzá a környezethez.

Ha az ügynök néhány perc elteltével sikeresen csatlakozott a Log Analytics munkaterülethez, a következő lekérdezés futtatásával ellenőrizheti, hogy a szívverési adatokat küld-e a munkaterületre:

```kusto
Heartbeat 
| where Category == "Direct Agent" 
| where TimeGenerated > ago(30m)
```

Az eredményül kapott keresési eredmények között meg kell jelennie a szívverési rekordoknak, amely jelzi, hogy a számítógép csatlakoztatva van, és jelentést küld a szolgáltatásnak. A szívverési rekord minden ügynöktől alapértelmezés szerint a hozzárendelt munkaterületre lesz továbbítva. Ellenőrizheti, hogy az ügynök megfelelően letöltötte-e az Automation-megoldást, ha az **AzureAutomationFiles** nevű mappában található a C:\Program Files\Microsoft monitoring Agent\Agent. A hibrid Runbook-feldolgozó verziójának megerősítéséhez keresse meg a C:\Program Files\Microsoft monitoring Agent\Agent\AzureAutomation\, és jegyezze fel a \\*Version* almappát.

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Telepítse a runbook-környezetet, és kapcsolódjon Azure Automation

Ha úgy konfigurálja az ügynököt, hogy egy Log Analytics munkaterületre jelentsen, az Automation-megoldás leküldi a **HybridRegistration** PowerShell-modult, amely tartalmazza az **Add-HybridRunbookWorker** parancsmagot. Ezzel a parancsmaggal telepítheti a számítógép runbook-környezetét, és regisztrálja azt Azure Automation használatával.

Nyisson meg egy PowerShell-munkamenetet rendszergazdai módban, és futtassa a következő parancsokat a modul importálásához:

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Ezután futtassa az **Add-HybridRunbookWorker** parancsmagot a következő szintaxis használatával:

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

A parancsmaghoz szükséges adatokat a Azure Portal **kulcsok kezelése** lapján érheti el. Nyissa meg ezt a lapot az Automation-fiók **Beállítások** lapján található **kulcsok** lehetőség kiválasztásával.

!["Kulcsok kezelése" oldal](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* A **Csoportnév** a hibrid Runbook Worker csoport neve. Ha ez a csoport már létezik az Automation-fiókban, az aktuális számítógép hozzá lesz adva. Ha ez a csoport nem létezik, a rendszer hozzáadja.
* A **végpont** az **URL-cím** bejegyzés a **kulcsok kezelése** lapon.
* A **token** az **elsődleges hozzáférési kulcs** bejegyzése a **kulcsok kezelése** lapon.

Ha részletes információkat szeretne kapni a telepítésről, használja a **-verbose** kapcsolót az **Add-HybridRunbookWorker**használatával.

#### <a name="5-install-powershell-modules"></a>5. PowerShell-modulok telepítése

A runbookok használhatja a Azure Automation-környezetben telepített modulokban meghatározott tevékenységeket és parancsmagokat. Ezeket a modulokat a rendszer nem telepíti automatikusan a helyszíni számítógépekre, ezért azokat manuálisan kell telepítenie. Ez alól kivételt képez az Azure-modul, amely alapértelmezés szerint telepítve van, és hozzáférést biztosít a Azure Automation összes Azure-szolgáltatásához és tevékenységéhez szükséges parancsmagokhoz.

Mivel a hibrid Runbook Worker funkció elsődleges célja a helyi erőforrások kezelése, valószínűleg telepítenie kell az ezeket az erőforrásokat támogató modulokat. A Windows PowerShell-modulok telepítésével kapcsolatos információkért lásd: [modulok telepítése](/powershell/scripting/developer/windows-powershell). 

A telepített moduloknak a **PSModulePath** környezeti változó által hivatkozott helyen kell lenniük, hogy a hibrid feldolgozó automatikusan importálni tudja azokat. További információ: [a PSModulePath telepítési útvonalának módosítása](/powershell/scripting/developer/windows-powershell).

## <a name="next-steps"></a>Következő lépések

* Ha szeretné megtudni, hogyan konfigurálhatja a runbookok a helyszíni adatközpontban vagy más felhőalapú környezetben lévő folyamatok automatizálására, tekintse meg a [Runbookok futtatása hibrid Runbook-feldolgozón](automation-hrw-run-runbooks.md)című témakört.
* A hibrid Runbook-feldolgozók eltávolításával kapcsolatos útmutatásért lásd: [Azure Automation hibrid Runbook-feldolgozók eltávolítása](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
* A hibrid Runbook-feldolgozók hibaelhárításával kapcsolatos további információkért lásd: [Windows Hybrid Runbook-feldolgozók hibaelhárítása](troubleshoot/hybrid-runbook-worker.md#windows)
* A Update Managementával kapcsolatos problémák elhárításával kapcsolatos további lépéseket a [Update Management: hibaelhárítás](troubleshoot/update-management.md)című témakörben talál.
