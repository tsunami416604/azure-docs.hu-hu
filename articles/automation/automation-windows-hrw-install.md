---
title: Az Azure Automation hibrid runbook-feldolgozója Windowsra
description: Ez a cikk egy Azure Automation hibrid Runbook-feldolgozó, amellyel runbookok futtatása a Windows-alapú számítógépeken a helyi adatközpontban vagy a felhőalapú környezet telepítése információkat biztosít.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 62c171381201f3cedee869aba301859a7047f5ce
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46294908"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Egy Windows hibrid Runbook-feldolgozó üzembe helyezése

Az Azure Automation hibrid Runbook-feldolgozó szolgáltatása segítségével a runbookok futtatása, közvetlenül a számítógépen, amelyen a szerepkör és a helyi erőforrások kezelése a környezetben az erőforrásokon. Runbookok tárolt és kezelt az Azure Automation és egy vagy több kijelölt számítógépekre kézbesítését. Ez a cikk egy Windows-gépen a hibrid Runbook-feldolgozó telepítését ismerteti.

## <a name="installing-the-windows-hybrid-runbook-worker"></a>A Windows a hibrid Runbook Worker telepítése

Telepítése és konfigurálása a Windows hibrid Runbook-feldolgozók, két módszert használhatja. Az ajánlott módszer az Automation-runbook segítségével teljesen automatizálja a Windows-számítógép konfigurálásának folyamatán. A második módszer által követett kézi telepítését és konfigurálását a szerepkör lépésenkénti útmutatót.

> [!NOTE]
> A kiszolgálók, amelyek támogatják a hibrid forgatókönyv-feldolgozói szerepkör Desired State Configuration (DSC) rétegen-konfigurációjának kezeléséhez, hozzá kell őket DSC-csomópontok.

A Windows hibrid Runbook-feldolgozók minimális követelményei a következők:

* A Windows Server 2012 vagy újabb.
* Windows PowerShell 5.1-es vagy újabb ([töltse le a WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616)).
* .NET-keretrendszer 4.6.2-es vagy újabb.
* Két magot.
* 4 GB RAM.
* 443-as (kimenő) porton.

A hibrid Runbook-feldolgozó több hálózati követelményei lekéréséhez lásd: [a hálózat konfigurálása](automation-hybrid-runbook-worker.md#network-planning).

A DSC-kezelés bevezetési kiszolgálókkal kapcsolatos további információkért lásd: [gépek előkészítése kezelésre, amelyet az Azure Automation DSC](automation-dsc-onboarding.md).
Ha engedélyezi a [Update Management megoldás](../operations-management-suite/oms-solution-update-management.md), bármely Windows-számítógép, az Azure Log Analytics-munkaterülethez csatlakozó automatikusan van beállítva egy hibrid Runbook-feldolgozó az ebben a megoldásban lévő runbookok támogatása érdekében. Azonban hogy nincs regisztrálva a bármely Hibridfeldolgozó-csoportok az Automation-fiók már definiálva. 

A számítógép egy hibrid Runbook-feldolgozó csoporthoz az Automation-fiókban az Automation-runbookok támogatása, mindaddig, amíg használja ugyanazt a fiókot a megoldás és a hibrid Runbook-feldolgozó csoport tagsága is hozzáadhat. Ez a funkció a hibrid runbook-feldolgozó 7.2.12024.0-s verziójától érhető el.

Miután sikeresen telepített runbook-feldolgozó, tekintse át a [runbookok futtatása hibrid Runbook-feldolgozók](automation-hrw-run-runbooks.md) megtudhatja, hogyan konfigurálhatja a helyszíni adatközpont vagy egyéb felhőalapú környezetben lévő folyamatok automatizálásához a runbookokban.

### <a name="automated-deployment"></a>Automatikus telepítés

A következő lépésekkel automatizálhatja a telepítés és konfigurálás Windows hibrid feldolgozói szerepkör:

1. Töltse le a New-OnPremiseHybridWorker.ps1 szkriptet a [PowerShell-galériából](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/DisplayScript) közvetlenül a hibrid Runbook-feldolgozó szerepkört futtató számítógépről vagy egy másik számítógép a környezetben. Másolja a szkriptet a feldolgozóval.

   A New-OnPremiseHybridWorker.ps1 parancsfájl végrehajtása során van szükség a következő paraméterekkel:

   * *AutomationAccountName* (kötelező): az Automation-fiók nevére.
   * *AAResourceGroupName* (kötelező): az Automation-fiókhoz társított az erőforráscsoport nevét.
   * *OMSResourceGroupName* (nem kötelező): a Log Analytics-munkaterületet az erőforráscsoport nevét. Ha ez az erőforráscsoport nincs megadva, *AAResourceGroupName* szolgál.
   * *HybridGroupName* (kötelező): a runbookokat, amelyek támogatják az ebben a forgatókönyvben a célként megadott hibrid forgatókönyv-feldolgozó csoport nevét.
   * *SubscriptionID* (kötelező): az Azure előfizetés-azonosító, amely az Automation-fiókot.
   * *WorkspaceName* (nem kötelező): A Log Analytics-munkaterület neve. Ha nem rendelkezik a Log Analytics-munkaterületen, a parancsfájl létrehozza, és konfigurál egyet.

     > [!NOTE]
     > A Log Analytics-integráció támogatott csak Automation régiók jelenleg **Délkelet-Ausztrália**, **USA keleti RÉGIÓJA 2**, **Délkelet-Ázsia**, és  **Nyugat-Európa**. Ha az Automation-fiók nem ezen régiók egyikében, a parancsfájl egy Log Analytics-munkaterületet hoz létre, de figyelmeztetést jelenít meg, hogy azt nem kapcsolja őket össze.

2. Nyissa meg a számítógépen, **Windows PowerShell** származó a **Start** képernyő rendszergazdai módban.
3. A PowerShell parancssori rendszerhéjból keresse meg a letöltött szkript tartalmazó mappát. Módosítsa az értékeket a paraméterekhez *– AutomationAccountName*, *- AAResourceGroupName*, *- OMSResourceGroupName*, *- HybridGroupName*, *- SubscriptionId*, és *- WorkspaceName*. Ezután futtassa a szkriptet.

     > [!NOTE]
     > A szkript futtatása után az Azure-hitelesítésre kéri. Ön *kell* jelentkezzen be egy olyan fiókkal, amely tagja az előfizetés-Adminisztrátorok szerepkörhöz, és az előfizetés társadminisztrátorának.

   ```powershell-interactive
   .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
   -OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
   -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
   ```

4. Kéri, hogy fogadja el a NuGet telepítése, és kéri, hogy a saját Azure hitelesítő adatait.

5. A szkript befejezése után a **hibrid Feldolgozócsoportok** lap az új csoport és a tagok száma látható. Ha egy meglévő csoportot, a tagok száma értéke akkor növekszik. A csoportot a listából kiválaszthatja a a **hibrid Feldolgozócsoportok** lapon, és válassza a **hibrid feldolgozók** csempére. Az a **hibrid feldolgozók** lapon láthatja a felsorolt csoport minden tagja.

### <a name="manual-deployment"></a>Manuális telepítés

Hajtsa végre az első két egyszer lépést az automatizálási környezet, és ismételje meg a fennmaradó feldolgozó számítógépenként.

#### <a name="1-create-a-log-analytics-workspace"></a>1. Log Analytics-munkaterület létrehozása

Ha még nem rendelkezik a Log Analytics-munkaterületet, hozzon létre egyet utasításai szerint [a munkaterület kezelése](../log-analytics/log-analytics-manage-access.md). Egy meglévő munkaterületet is használhatja, ha már rendelkezik ilyennel.

#### <a name="2-add-the-automation-solution-to-the-log-analytics-workspace"></a>2. Az Automation-megoldás a Log Analytics-munkaterület hozzáadása

A megoldások funkciókkal bővítik ki a Log Analytics szolgáltatást. Az Automation-megoldás funkciókkal bővíti az Azure Automation, beleértve a hibrid Runbook-feldolgozó támogatását. Amikor a megoldás ad hozzá a munkaterülethez, leküldi a megfelelő feldolgozó összetevőinek automatikusan az ügynökszámítógép, amely a következő lépésben telepíteni fogja.

Hozzáadása a **Automation** megoldás a Log Analytics-munkaterület utasításai [hozzáadása egy megoldást a használatával, kövesse a megoldástárban](../log-analytics/log-analytics-add-solutions.md).

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. A Microsoft Monitoring Agent telepítése

A Microsoft Monitoring Agent számítógép csatlakozik a Log Analytics. Amikor az ügynök telepítése a helyi számítógépen, és csatlakoztassa a munkaterülethez, automatikusan letölti a hibrid Runbook-feldolgozó szükséges összetevőket.

Az ügynök telepítéséhez a helyi számítógépen, utasításai [a Log Analyticshez való csatlakozáshoz Windows számítógépek](../log-analytics/log-analytics-windows-agent.md). Ez a folyamat feldolgozó hozzáadása a környezethez több számítógép esetén megismételheti.

Az ügynök sikeresen csatlakozott a Log Analytics szolgáltatásba, amikor az megjelenik a **csatlakoztatott források** a Log Analytics lapján **beállítások** lapot. Ellenőrizheti, hogy az ügynök megfelelően töltött le az Automation-megoldás nevű mappát, ha **AzureAutomationFiles** a C:\Program Files\Microsoft Monitoring Agent\Agent. Ellenőrizze a hibrid Runbook-feldolgozó verziója, megnyithatja a C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\, és jegyezze fel a \\ *verzió* almappába.

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. A runbook-környezet telepítése, és csatlakozzon az Azure Automation

Az ügynök a Log Analyticshez való hozzáadásakor az Automation-megoldás leküldi a **HybridRegistration** PowerShell-modult, amely tartalmazza a **Add-HybridRunbookWorker** parancsmagot. Ez a parancsmag használatával telepítse a forgatókönyv-környezetet a számítógépre, és regisztrálja az Azure Automation szolgáltatással.

Nyisson meg egy PowerShell-munkamenetet rendszergazdai módban, és futtassa a következő parancsok futtatásával importálja a modult:

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module HybridRegistration.psd1
```

Ezután futtassa a **Add-HybridRunbookWorker** parancsmag a következő szintaxis használatával:

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

A parancsmag a szükséges információkat szerezhet a **kulcsok kezelése** oldal az Azure Portalon. Nyissa meg az ezen a lapon kiválasztásával a **kulcsok** parancsát a **beállítások** lap az Automation-fiókban.

!["A kulcsok kezelése" lap](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **GroupName** a hibrid Runbook-feldolgozó csoport neve. Ez a csoport már létezik az Automation-fiókot, ha az aktuális számítógépet adnak hozzá. Ha ez a csoport nem létezik, az bekerül.
* **Végpont** van a **URL-cím** a bejegyzés a **kulcsok kezelése** lapot.
* **Token** van a **elsődleges ELÉRÉSI kulcs** a bejegyzés a **kulcsok kezelése** lapot.

A telepítés részletes információt kap, használja a **-Verbose** felülettel **Add-HybridRunbookWorker**.

#### <a name="5-install-powershell-modules"></a>5. PowerShell-modulok telepítéséhez

Runbookok a tevékenységeket és az Azure Automation környezetében telepített modulok meghatározott parancsmagok bármelyikét használhatja. Ezek a modulok nem automatikusan települnek az a helyszíni számítógépeken, ezért kell manuálisan telepítenie. A kivétel ez alól az Azure-modul, amely alapértelmezés szerint telepítve van, és az összes Azure-szolgáltatások és a tevékenységek parancsmagok hozzáférést biztosít az Azure Automation.

Mivel az elsődleges célja, hogy a hibrid Runbook-feldolgozó szolgáltatás a helyi erőforrások kezelésére, nagy valószínűséggel telepítenie kell a modulokat, amelyek támogatják ezeket az erőforrásokat. Windows PowerShell-modul telepítéséről további információkért lásd: [modulok telepítése](http://msdn.microsoft.com/library/dd878350.aspx). 

Telepített modulok által hivatkozott helyen kell lennie a **PSModulePath** környezeti változót, hogy a hibrid feldolgozó automatikusan importálhatja őket. További információkért lásd: [módosítása a PSModulePath telepítési útvonal](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx).

## <a name="troubleshoot"></a>Hibaelhárítás

Hogyan háríthatók el a hibrid Runbook-feldolgozók kapcsolatban lásd: [hibaelhárítása Windows hibrid Runbook-feldolgozók](troubleshoot/hybrid-runbook-worker.md#windows)

Az Update Management hibáinak elhárítása a további lépésekért lásd: [az Update Management: hibaelhárítási](troubleshoot/update-management.md).

## <a name="next-steps"></a>További lépések

* A runbookok automatizálhatja a folyamatokat a helyszíni adatközpont vagy egyéb felhőalapú környezetbe történő konfigurálásával kapcsolatban lásd: [runbookok futtatása hibrid Runbook-feldolgozók](automation-hrw-run-runbooks.md).
* A hibrid Runbook-feldolgozóinak eltávolítása útmutatásért lásd: [távolítsa el az Azure Automation hibrid Runbook-feldolgozók](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).