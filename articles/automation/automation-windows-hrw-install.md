---
title: Az Azure Automation hibrid runbook-feldolgozója Windowsra
description: Ez a cikk tájékoztatást nyújt telepítése egy Azure Automation hibrid forgatókönyv-feldolgozót a helyi adatközpontban, illetve a felhőalapú környezetben a Windows-alapú számítógépeken a runbookok futtatásához használható.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e449e6e457c4fa568b5a4de5823014b4dcea82d0
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063952"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>A Windows hibrid forgatókönyv-feldolgozók telepítése

A hibrid forgatókönyv-feldolgozó szolgáltatás az Azure Automation segítségével közvetlenül a számítógépen, amelyen a szerepkör és a helyi erőforrások kezelése környezetben erőforrásokon a runbookok futtatásához. Runbookok által tárolt és az Azure Automationben kezelt és ezután a felhasználóikhoz kerülnek egy vagy több kijelölt számítógépekre. A cikkből megtudhatja, hogyan telepítheti a hibrid forgatókönyv-feldolgozó Windows-gépen.

## <a name="installing-the-windows-hybrid-runbook-worker"></a>A Windows hibrid forgatókönyv-feldolgozó telepítése

Telepítse és konfigurálja a Windows hibrid forgatókönyv-feldolgozók, két módszert is használhat. Az ajánlott módszer az Automation-forgatókönyv használatával történő teljesen automatizálja a Windows-számítógép konfigurálásának lépésein. A másik módszer a következő kézi telepítését és konfigurálását a szerepkör lépésenkénti útmutatót.

> [!NOTE]
> Felügyelje a konfigurációt a kiszolgálók, amelyek támogatják a hibrid forgatókönyv-feldolgozói szerepkör a kívánt állapot konfigurációs szolgáltatása (DSC), szüksége DSC-csomópontként hozzáadni.

A Windows hibrid forgatókönyv-feldolgozók minimális követelményei a következők:

* Windows Server 2012 vagy újabb.
* A Windows PowerShell 4.0-s vagy újabb ([töltse le a WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855)). Azt javasoljuk, hogy a Windows PowerShell 5.1 ([töltse le a WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616)) a nagyobb megbízhatóság.
* .NET-keretrendszer 4.6.2-es verziójához vagy újabb.
* Két magok.
* 4 GB RAM-mal.
* 443-as (kimenő) porton.

A hibrid forgatókönyv-feldolgozó további hálózati követelményei megtekinteni [a hálózat konfigurálása](automation-hybrid-runbook-worker.md#network-planning).

-Felügyelethez a DSC-bevezetési kiszolgálókkal kapcsolatos további információkért lásd: [bevezetési gépeket Azure Automation DSC általi kezelésre](automation-dsc-onboarding.md).
Ha engedélyezi a [frissítés felügyeleti megoldás](../operations-management-suite/oms-solution-update-management.md), minden Windows-számítógép, amely kapcsolódik az Azure Log Analytics-munkaterület automatikusan a hibrid forgatókönyv-feldolgozót a megoldásban forgatókönyvek támogatására van konfigurálva. Azonban az már definiálva van az Automation-fiókban található hibrid feldolgozó csoportok nincs regisztrálva. 

A számítógép az Automation-fiókban Automation-forgatókönyv támogatásához, mindaddig, amíg ugyanazt a fiókot használja a megoldás és a hibrid forgatókönyv-feldolgozó csoport tagsága is a hibrid forgatókönyv-feldolgozó csoporthoz lehet hozzáadni. Ez a funkció a hibrid runbook-feldolgozó 7.2.12024.0-s verziójától érhető el.

Miután sikeresen telepítette a forgatókönyv-feldolgozók, tekintse át a [runbookot futtatni a hibrid forgatókönyv-feldolgozó](automation-hrw-run-runbooks.md) megtudhatja, hogyan konfigurálhatja a runbook automatizálása a helyszíni adatközpontját, illetve más felhőalapú környezetben.

### <a name="automated-deployment"></a>Automatikus telepítés

A következő lépésekkel automatizálhatja a telepítése és konfigurálása a Windows hibrid feldolgozói szerepkör:

1. Töltse le a New-OnPremiseHybridWorker.ps1 parancsfájlt a [PowerShell-galériában](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/DisplayScript) közvetlenül a hibrid forgatókönyv-feldolgozó szerepkört futtató számítógépen vagy egy másik számítógép a környezetben. Másolja a parancsfájlt a dolgozó.

   A New-OnPremiseHybridWorker.ps1 parancsfájl végrehajtása közben a következő paramétereket igényel:

   * *AutomationAccountName* (kötelező): az Automation-fiók nevét.
   * *AAResourceGroupName* (kötelező): az Automation-fiók társított az erőforráscsoport nevét.
   * *OMSResourceGroupName* (nem kötelező): az Operations Management Suite-munkaterülettel az erőforráscsoport nevét. Ha ez az erőforráscsoport nem szerepel, *AAResourceGroupName* szolgál.
   * *HybridGroupName* (kötelező): a runbookokat, amely támogatja ezt a helyzetet a célként megadott hibrid forgatókönyv-feldolgozó csoport nevét.
   * *A SubscriptionID* (kötelező): az Azure-előfizetési Azonosítót, amely az Automation-fiók.
   * *WorkspaceName* (nem kötelező): A Naplóelemzési munkaterület nevét. Ha a munkaterületet nincs, a parancsfájl létrehozza, és konfigurálja az egyik.

     > [!NOTE]
     > A Log Analyticshez való integrációra támogatott csak Automation régiók jelenleg **Ausztrália délkeleti**, **USA keleti régiója 2**, **Délkelet-Ázsia**, és  **Nyugat-Európában**. Az Automation-fiók nincs egy adott helyre, ha a parancsfájl a Naplóelemzési munkaterület létrehozza, de figyelmezteti, hogy azt nem összeköt őket.

1. Nyissa meg a számítógép **Windows PowerShell** a a **Start** képernyő rendszergazdai módban.
1. A PowerShell parancssori rendszerhéj tallózással keresse meg a letöltött parancsfájlt tartalmazó mappát. Módosítsa a paraméterek értékeit *- AutomationAccountName*, *- AAResourceGroupName*, *- OMSResourceGroupName*, *- HybridGroupName*, *- SubscriptionId*, és *- WorkspaceName*. Majd futtassa a parancsfájlt.

     > [!NOTE]
     > A parancsfájl futtatása után a Azure hitelesítést kéri. Ön *kell* olyan fiókkal jelentkezzen be, amely az előfizetés-Rendszergazdák szerepkör tagja, és az előfizetés társadminisztrátoraként.

   ```powershell-interactive
   .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
   -OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
   -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
   ```

1. Vállalja, hogy telepítse a NuGet felkéri, és kéri a hitelesítő adatait az Azure szolgáltatással való hitelesítésre.

1. A parancsfájl befejezése után a **hibrid dolgozó csoportok** lapon látható az új csoport és a tagok száma. Ha egy meglévő csoportot, a tagok száma értéke akkor növekszik. A csoportot a listából kiválaszthatja a a **hibrid dolgozó csoportok** lapon, és válassza a **hibrid feldolgozók** csempére. Az a **hibrid feldolgozók** lapon megjelenik a felsorolt csoport minden tagja.

### <a name="manual-deployment"></a>Manuális telepítése

Hajtsa végre az első két lépés egyszer Automation környezetét, majd ismételje meg a fennmaradó munkavégző számítógépenként.

#### <a name="1-create-a-log-analytics-workspace"></a>1. A Naplóelemzési munkaterület létrehozása

Ha még nem rendelkezik a Naplóelemzési munkaterület, hozzon létre egyet a következő utasítások használatával [kezelése a munkaterület](../log-analytics/log-analytics-manage-access.md). Ha már rendelkezik egy meglévő munkaterülethez használhatja.

#### <a name="2-add-the-automation-solution-to-the-log-analytics-workspace"></a>2. Az Automation-megoldás hozzáadni a Naplóelemzési munkaterület

A megoldások funkciókkal bővítik ki a Log Analytics szolgáltatást. Az Automation-megoldás bővíti olyan funkciókkal, az Azure Automation, beleértve a hibrid forgatókönyv-feldolgozó támogatását. A megoldás a munkaterülethez való hozzáadásakor az automatikusan leküldéses értesítések munkavégző összetevők az ügynök számítógépre, amelyen telepíteni fogja a következő lépéssel.

Hozzáadása a **Automation** megoldást jelent a Naplóelemzési munkaterület kövesse [hozzáadása a megoldások végzi megoldás](../log-analytics/log-analytics-add-solutions.md).

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. A Microsoft Monitoring Agent telepítése

A Microsoft Monitoring Agent számítógépek szolgáltatáshoz csatlakozik. Amikor az ügynök telepítése a helyi számítógépen, és csatlakoztassa a munkaterület, automatikusan letölti a hibrid forgatókönyv-feldolgozót a szükséges összetevőket.

A helyi számítógépen az ügynök telepítéséhez kövesse [Log Analyticshez való csatlakozás Windows számítógépek](../log-analytics/log-analytics-windows-agent.md). Megismételheti a folyamatot a több számítógép számára több Worker hozzá a környezethez.

Az ügynök sikeresen csatlakozott-e a Naplóelemzési, ha az szerepel a **csatlakoztatott források** a Naplóelemzési lapján **beállítások** lap. Ellenőrizheti, hogy az ügynök megfelelően sikeresen letöltötte az Automation-megoldást, ha egy nevű mappát **AzureAutomationFiles** a C:\Program Files\Microsoft figyelési Agent\Agent. A hibrid forgatókönyv-feldolgozó verziószáma, tallózással is kikeresheti azt C:\Program Files\Microsoft figyelési Agent\Agent\AzureAutomation\, majd jegyezze fel a \\ *verzió* almappájában.

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Telepítheti a forgatókönyvek környezetét, és csatlakozzon az Azure Automation

Az ügynök Log Analyticshez való hozzáadásakor az Automation-megoldás leküldi a **HybridRegistration** PowerShell-modult, amely tartalmazza a **Add-HybridRunbookWorker** parancsmag. Ez a parancsmag segítségével telepítheti a forgatókönyvek környezetét a számítógépen, és regisztrálhatja azt az Azure Automation.

Nyisson meg egy PowerShell-munkamenetet rendszergazdai módban, és a modul importálása a következő parancsokat:

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module HybridRegistration.psd1
```

Ezután futtassa a **Add-HybridRunbookWorker** parancsmag a következő szintaxissal:

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Ez a parancsmag a szükséges adatokat kaphat a **kulcsok kezelése** oldal az Azure portálon. Nyissa meg az ezen a lapon kiválasztásával a **kulcsok** parancsát a **beállítások** lap az Automation-fiókban.

!["A kulcsok kezelése" lap](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **Csoportnév** a hibrid forgatókönyv-feldolgozó csoport neve. Ha ez a csoport már szerepel az Automation-fiók, az aktuális számítógép van felvéve. Ha ez a csoport nem létezik, kerül.
* **Végpont** van a **URL-cím** bevitelének a **kulcsok kezelése** lap.
* **Token** van a **elsődleges ELÉRÉSI kulcsot** bevitelének a **kulcsok kezelése** lap.

A telepítés részletes információt kap, használja a **-Verbose** kapcsoló **Add-HybridRunbookWorker**.

#### <a name="5-install-powershell-modules"></a>5. PowerShell-modulok telepítése

A Runbookok a tevékenységek és az Azure Automation környezetében telepített modulok definiált parancsmagokat használhatja. Ezek a modulok nem automatikusan telepít a helyszíni számítógépek, ezért telepítenie kell őket manuálisan. A kivétel: az Azure modul, amely alapértelmezés szerint telepítve van, és az összes Azure-szolgáltatások és a tevékenységek parancsmagok hozzáférést biztosít az Azure Automation.

A hibrid forgatókönyv-feldolgozó szolgáltatás elsődleges célja, helyi erőforrásokat kezeljen, mert valószínűleg telepíteni szeretné a modulokat, amelyek támogatják ezeket az erőforrásokat. Windows PowerShell-modulok telepítésével kapcsolatos információkért lásd: [modulok telepítése](http://msdn.microsoft.com/library/dd878350.aspx). 

Telepített modulok által hivatkozott helyen kell lennie a **PSModulePath** környezeti változó, hogy a hibrid feldolgozó automatikusan importálja azokat. További információkért lásd: [a PSModulePath telepítési elérési út](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx).

## <a name="troubleshoot"></a>Hibaelhárítás

A hibrid forgatókönyv-feldolgozók hibaelhárítása kapcsolatban [hibaelhárítás Windows hibrid forgatókönyv-feldolgozók](troubleshoot/hybrid-runbook-worker.md#windows)

A frissítés-kezeléssel kapcsolatos problémák elhárítása a további lépéseket lásd: [frissítéskezelés: hibaelhárítási](troubleshoot/update-management.md).

## <a name="next-steps"></a>További lépések

* A runbookok automatizálása a helyszíni adatközpontját, illetve más felhőalapú környezet konfigurálása, lásd: [runbookot futtatni a hibrid forgatókönyv-feldolgozó](automation-hrw-run-runbooks.md).
* Távolítsa el a hibrid forgatókönyv-feldolgozók konfigurálásáról lásd: [eltávolítása Azure Automation hibrid forgatókönyv-feldolgozók](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).