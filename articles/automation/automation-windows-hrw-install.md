---
title: Az Azure Automation hibrid runbook-feldolgozója Windowsra
description: Ez a cikk tájékoztatást nyújt telepítése egy Azure Automation hibrid forgatókönyv-feldolgozó, amely lehetővé teszi runbookok futtatását a helyi adatközpontban, illetve a felhőalapú környezetben a Windows-alapú számítógépeken.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 9ac6423c6b08aa2a86eda5b0560c8b10e7082284
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="how-to-deploy-a-windows-hybrid-runbook-worker"></a>A Windows hibrid forgatókönyv-feldolgozók központi telepítése

A hibrid forgatókönyv-feldolgozó szolgáltatás az Azure Automation lehetővé teszi a runbookok futtatásához, közvetlenül a szerepkört futtató számítógépen és a helyi erőforrások kezelése környezetben erőforrásokon. Runbookok által tárolt és az Azure Automationben kezelt és ezután a felhasználóikhoz kerülnek egy vagy több kijelölt számítógépekre. Ez a következő cikket: decribes a hibrid forgatókönyv-feldolgozó telepítése Windows-gépen.

## <a name="installing-the-windows-hybrid-runbook-worker"></a>A Windows hibrid forgatókönyv-feldolgozó telepítése

Telepítése és konfigurálása a Windows hibrid forgatókönyv-feldolgozók, kétféleképpen érhető el. Az ajánlott módszer használ egy Automation-runbook teljesen automatizálja a Windows-számítógép konfigurálásához szükséges. A másik módszer a következő kézi telepítését és konfigurálását a szerepkör lépésenkénti útmutatót.

> [!NOTE]
> A kiszolgálók, a hibrid forgatókönyv-feldolgozói szerepkör a kívánt állapot konfigurációs szolgáltatása (DSC) támogató konfigurációjának kezelésére, szüksége DSC-csomópontként hozzáadni.

Az alábbiakban a Windows hibrid forgatókönyv-feldolgozók minimális követelményeinek.

* Windows Server 2012 vagy újabb.
* A Windows PowerShell 4.0-s vagy újabb rendszer szükséges ([töltse le a WMF 4.0-s](https://www.microsoft.com/download/details.aspx?id=40855)). A Windows PowerShell 5.1 ([letöltése WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616)) megbízhatóbbak ajánlott.
* .NET-keretrendszer 4.6.2-es verziójához vagy újabb.
* Legalább két magszámra vonatkozó követelménynek.
* Egy legalább 4 GB RAM-mal.
* Port 443-as (kimenő)

A hibrid forgatókönyv-feldolgozó további hálózati követelményei, olvassa el [a hálózat konfigurálása](automation-hybrid-runbook-worker.md#network-planning).

További információ a bevezetési azokat a-kezeléshez a DSC, lásd: [bevezetési gépeket Azure Automation DSC általi kezelésre](automation-dsc-onboarding.md).
Ha engedélyezi a [frissítés felügyeleti megoldás](../operations-management-suite/oms-solution-update-management.md), a Naplóelemzési munkaterület kapcsolódó minden Windows-számítógép automatikusan a hibrid forgatókönyv-feldolgozót a megoldásban forgatókönyvek támogatására van konfigurálva. Azonban nincs regisztrálva a hibrid feldolgozó csoportokkal már definiálva van az Automation-fiókban. A számítógép Automation-forgatókönyv támogatásához, mindaddig, amíg az megoldás és a hibrid forgatókönyv-feldolgozó csoport tagsága is ugyanazt a fiókot használ az Automation-fiók hibrid forgatókönyv-feldolgozó csoporthoz lehet hozzáadni. Ez a funkció a hibrid runbook-feldolgozó 7.2.12024.0-s verziójától érhető el.

Miután sikeresen telepítette a forgatókönyv-feldolgozók, tekintse át a [runbookot futtatni a hibrid forgatókönyv-feldolgozó](automation-hrw-run-runbooks.md) megtudhatja, hogyan konfigurálhatja a runbook automatizálása a helyszíni adatközpontját, illetve más felhőalapú környezetben.

### <a name="automated-deployment"></a>Automatikus telepítés

A következő lépésekkel automatizálhatja a telepítése és konfigurálása a Windows hibrid feldolgozói szerepkör.

1. Töltse le a *New-OnPremiseHybridWorker.ps1* parancsfájl a [PowerShell-galériában](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/DisplayScript) közvetlenül a hibrid forgatókönyv-feldolgozó szerepkört futtató számítógépen vagy egy másik számítógép a környezetben, és másolja a dolgozó.

   A *New-OnPremiseHybridWorker.ps1* parancsfájl paraméterei a következők végrehajtása során:

   * *AutomationAccountName* (kötelező) – az Automation-fiók nevét.
   * *AAResourceGroupName* (kötelező) – az Automation-fiók társított az erőforráscsoport neve
   * *OMSResourceGroupName* (nem kötelező) – az OMS-munkaterület az erőforráscsoport nevét. Ha nincs megadva, a AAResourceGroupName szolgál.
   * *HybridGroupName* (kötelező) – a runbookok támogató ebben a forgatókönyvben a célként megadott hibrid forgatókönyv-feldolgozó csoport nevét.
   * *A SubscriptionID* (kötelező) – az Azure-előfizetési Azonosítót, amely az Automation-fiók.
   * *WorkspaceName* (nem kötelező) – a Naplóelemzési munkaterület nevét. Ha nem rendelkezik a Naplóelemzési munkaterület, a parancsfájl létrehozza és konfigurálja az egyik.

     > [!NOTE]
     > A Log Analyticshez való integrációra támogatott csak Automation régiók jelenleg - **Ausztrália délkeleti**, **USA keleti régiója 2**, **Délkelet-Ázsia**, és  **Nyugat-Európában**. Az Automation-fiók nincs egy adott helyre, ha a parancsfájl a Naplóelemzési munkaterület hoz létre, de figyelmeztetést kap, hogy azt nem összeköt őket.

1. A számítógépen indítása **Windows PowerShell** a a **Start** képernyő rendszergazdai módban.
1. Az PowerShell parancssori rendszerhéj lépjen abba a mappába, amely tartalmazza a parancsfájl letöltése, és végrehajtja a paraméterek értékeinek módosítása *- AutomationAccountName*, *- AAResourceGroupName*, *- OMSResourceGroupName*, *- HybridGroupName*, *- SubscriptionId*, és *- WorkspaceName*.

     > [!NOTE]
     > A parancsfájl végrehajtása után a Azure hitelesítést kéri. Ön **kell** olyan fiókkal jelentkezzen be, amely az előfizetés-Rendszergazdák szerepkör tagja, és az előfizetés társadminisztrátoraként.

   ```powershell-interactive
   .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
   -OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
   -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
   ```

1. Vállalja, hogy a telepítés megkezdésére **NuGet** és a hitelesítést az Azure hitelesítő adatait kéri.

1. A parancsfájl befejezése után a hibrid dolgozó csoportok lapon megjelenik az új csoport és a tagok száma, vagy ha egy meglévő csoportot, a tagok száma növeli. A csoportot a listából kiválaszthatja a a **hibrid dolgozó csoportok** lapon, és válassza a **hibrid feldolgozók** csempére. Az a **hibrid feldolgozók** lapon megjelenik a felsorolt csoport minden tagja.

### <a name="manual-deployment"></a>Manuális telepítése

Hajtsa végre az első két lépés egyszer Automation környezetét, majd ismételje meg a fennmaradó munkavégző számítógépenként.

#### <a name="1-create-log-analytics-workspace"></a>1. Log Analytics-munkaterület létrehozása

Ha még nem rendelkezik a Naplóelemzési munkaterület, majd hozzon létre egyet a következő útmutatást [kezelése a munkaterület](../log-analytics/log-analytics-manage-access.md). Ha már rendelkezik egy meglévő munkaterülethez használhatja.

#### <a name="2-add-automation-solution-to-log-analytics-workspace"></a>2. Automation-megoldás hozzáadni a Naplóelemzési munkaterület

A megoldások funkciókkal bővítik ki a Log Analytics szolgáltatást. Az Automation-megoldás bővíti olyan funkciókkal, az Azure Automation, beleértve a hibrid forgatókönyv-feldolgozó támogatását. A megoldás a munkaterület hozzáadásakor automatikusan leküldi munkavégző összetevők az ügynök számítógépre, amelyen telepíteni fogja a következő lépéssel.

Kövesse [hozzáadása a megoldások végzi megoldás](../log-analytics/log-analytics-add-solutions.md) hozzáadása a **Automation** a Naplóelemzési munkaterület megoldást.

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. A Microsoft Monitoring Agent telepítése

A Microsoft Monitoring Agent számítógépek szolgáltatáshoz csatlakozik. Telepítse az ügynököt a helyi számítógépen, és csatlakoztassa a munkaterület, automatikusan letölti a hibrid forgatókönyv-feldolgozó szükséges összetevőket.

Kövesse az utasításokat, [Log Analyticshez való csatlakozás Windows számítógépek](../log-analytics/log-analytics-windows-agent.md) az ügynök telepítéséhez a helyi számítógépen. Megismételheti a folyamatot a több számítógép számára több Worker hozzá a környezethez.

Az ügynök sikeresen csatlakozott-e a Naplóelemzési, ha az szerepel a **csatlakoztatott források** a Naplóelemzési lapján **beállítások** lap. Ellenőrizheti, hogy az ügynök megfelelően sikeresen letöltötte az Automation-megoldást, ha egy nevű mappát **AzureAutomationFiles** a C:\Program Files\Microsoft figyelési Agent\Agent. A hibrid forgatókönyv-feldolgozó verziójával megerősítéséhez lépjen C:\Program Files\Microsoft figyelési Agent\Agent\AzureAutomation\, majd jegyezze fel a \\ *verzió* almappájában.

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Telepítheti a forgatókönyvek környezetét, és csatlakozzon az Azure Automation

Az ügynök Log Analyticshez való hozzáadásakor az Automation-megoldás leküldi a **HybridRegistration** PowerShell-modult, amely tartalmazza a **Add-HybridRunbookWorker** parancsmag. Ez a parancsmag segítségével telepítheti a forgatókönyvek környezetét a számítógépen, és regisztrálhatja azt az Azure Automation.

Nyisson meg egy PowerShell-munkamenetet rendszergazdai módban, és a következő parancsok futtatásával importálja a modult.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module HybridRegistration.psd1
```

Ezután futtassa a **Add-HybridRunbookWorker** parancsmag a következő szintaxis használatával:

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Ez a parancsmag a szükséges adatokat kaphat a **kulcsok kezelése** oldal az Azure portálon. Nyissa meg az ezen a lapon kiválasztásával a **kulcsok** parancsát a **beállítások** lap az Automation-fiókban.

![Hibrid forgatókönyv-feldolgozó – áttekintés](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **Csoportnév** a hibrid forgatókönyv-feldolgozó csoport neve. Ha ez a csoport már létezik az automation-fiókban, az aktuális számítógépet hozzáadni. Ha még nem létezik, majd kerül.
* **Végpont** van a **URL-cím** mező mellett a **kulcsok kezelése** lap.
* **Token** van a **elsődleges elérési kulcsot** a a **kulcsok kezelése** lap.

Használja a **-Verbose** kapcsoló **Add-HybridRunbookWorker** telepítésével kapcsolatos részletes adatokat fogadhat.

#### <a name="5-install-powershell-modules"></a>5. PowerShell-modulok telepítése

A Runbookok a tevékenységek és az Azure Automation-környezetbe telepített modulban definiált parancsmagokat használhatja. Ezek a modulok nem automatikusan telepít a helyszíni számítógépek, ezért telepítenie kell őket manuálisan. A kivétel: az Azure modult, amely az összes Azure-szolgáltatások és a tevékenységek parancsmagok való hozzáférés biztosításához az Azure Automation alapértelmezés szerint telepítve van.

Mivel az elsődleges célja, hogy a hibrid forgatókönyv-feldolgozó szolgáltatás helyi erőforrásokat kezeljen, valószínűleg telepíteni szeretné a modulokat, amelyek támogatják ezeket az erőforrásokat. Olvassa el a [modulok telepítése](http://msdn.microsoft.com/library/dd878350.aspx) Windows PowerShell-modul telepítéséről további információt. Telepített modulok PSModulePath környezeti változó által hivatkozott, hogy a hibrid feldolgozó automatikusan importált helyen kell lennie. További információkért lásd: [a PSModulePath telepítési elérési út](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx).

## <a name="troubleshooting"></a>Hibaelhárítás

A hibrid forgatókönyv-feldolgozó attól függ, hogy a Microsoft figyelési ügynök kommunikáljon az Automation-fiók regisztrálása a munkavégző, runbook-feladatok fogadása és jelentse állapotát. A dolgozó regisztrálása meghiúsul, ha az alábbiakban néhány a hiba lehetséges okai:

### <a name="the-microsoft-monitoring-agent-is-not-running"></a>A Microsoft Monitoring Agent nem fut.

Ha a Microsoft figyelési ügynök Windows szolgáltatás nem fut, ez megakadályozza, hogy a hibrid forgatókönyv-feldolgozó Azure Automation kommunikál. Ellenőrizze az ügynök fut-e a következő parancs beírásával PowerShell: `Get-Service healthservice`. Ha a szolgáltatás leáll, adja meg a következő parancsot a PowerShell elindítani a szolgáltatást: `Start-Service healthservice`.

### <a name="event-4502-in-operations-manager-log"></a>Az esemény 4502 Operations Manager

Az a **alkalmazások és szolgáltatások Logs\Operations kezelője** Eseménynapló, látni esemény 4502 és EventMessage tartalmazó **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**következő leírást: *a szolgáltatás által bemutatott tanúsítványt \<wsid\>. oms.opinsights.azure.com nem a Microsoft-szolgáltatásokhoz használt hitelesítésszolgáltató állította ki. Lépjen kapcsolatba a hálózati rendszergazdával, ha a proxy, amely elfogja a TLS/SSL-kommunikáció futnak. A kb3126513 jelű további információkat talál a csatlakozási problémák.*

Ezt okozhatja a proxy vagy a hálózati tűzfal blokkolja a Microsoft Azure-kommunikációt. Ellenőrizze, hogy a számítógép *.azure-automation.net kimenő hozzáféréssel rendelkezzen a 443-as porton.

Naplók minden hibridfeldolgozó C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes, helyileg tárolja. Ellenőrizheti, hogy vannak-e bármilyen figyelmeztetés vagy írni hibaesemények a **alkalmazások és szolgáltatások Logs\Microsoft-SMA\Operations** és **alkalmazások és szolgáltatások Logs\Operations kezelője** esemény naplózása, amelyek azt jelzi, a kapcsolattal vagy más probléma érdekében, hogy a szerepkört az Azure Automation vagy probléma befolyásolja a normál műveletek során.

A frissítés-kezeléssel kapcsolatos problémák elhárítása a további lépéseket lásd: [felügyelete – hibaelhárítás](automation-update-management.md#troubleshooting)

## <a name="next-steps"></a>Következő lépések

* Felülvizsgálati [runbookot futtatni a hibrid forgatókönyv-feldolgozó](automation-hrw-run-runbooks.md) megtudhatja, hogyan konfigurálhatja a runbook automatizálása a helyszíni adatközpontját, illetve más felhőalapú környezetben.
* Távolítsa el a hibrid forgatókönyv-feldolgozók konfigurálásáról lásd: [eltávolítása Azure Automation hibrid forgatókönyv-feldolgozók](automation-hybrid-runbook-worker.md#removing-hybrid-runbook-worker)