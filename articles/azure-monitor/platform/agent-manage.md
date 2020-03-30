---
title: Az Azure Log Analytics-ügynök kezelése
description: Ez a cikk ismerteti a különböző felügyeleti feladatokat, amelyeket általában a számítógépen telepített Log Analytics Windows vagy Linux-ügynök életciklusa során fog végrehajtani.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/14/2019
ms.openlocfilehash: 4d0ceacd37748e9761903d02fd7e052d70b10e15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275099"
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>Windows és Linux rendszerhez készült Log Analytics-ügynök kezelése és karbantartása

A Log Analytics Windows vagy Linux ügynök az Azure Monitorban való üzembe helyezése után előfordulhat, hogy újra kell konfigurálnia az ügynököt, frissítenie kell, vagy el kell távolítania a számítógépről, ha elérte a lehívási szakaszt az életciklusa során. Ezeket a rutinkarbantartási feladatokat egyszerűen kezelheti manuálisan vagy automatizálással, ami csökkenti a működési hibákat és a költségeket.

## <a name="upgrading-agent"></a>Ügynök frissítése

A Windows és Linux Log Analytics-ügynök manuálisan vagy automatikusan frissíthető a legújabb kiadásra a ttól függően, hogy a virtuális gép a központi telepítési forgatókönyvés környezet a virtuális gép fut. Az ügynök frissítéséhez a következő módszerek használhatók.

| Környezet | Telepítési módszer | Frissítési módszer |
|--------|----------|-------------|
| Azure VM | Log Analytics ügynök virtuálisgép-bővítmény Windows/Linux hoz | Az ügynök alapértelmezés szerint automatikusan frissül, kivéve, ha az Azure Resource Manager-sablont úgy állította be, hogy letiltsa a tulajdonság *autoUpgradeMinorVersion* tulajdonságát **hamisértékre**állítva. |
| Egyéni Azure virtuálisgép-lemezképek | A Log Analytics ügynök kézi telepítése Windows/Linux rendszeren | A virtuális gépek frissítése az ügynök legújabb verziójára a Windows telepítőcsomagot vagy a Linux önkicsomagoló és telepíthető rendszerhéj-kötegköteget futtató parancssorból történik.|
| Nem Azure-beli virtuális gépek | A Log Analytics ügynök kézi telepítése Windows/Linux rendszeren | A virtuális gépek frissítése az ügynök legújabb verziójára a Windows telepítőcsomagot vagy a Linux önkicsomagoló és telepíthető rendszerhéj-kötegköteget futtató parancssorból történik. |

### <a name="upgrade-windows-agent"></a>Frissítés Windows-ügynök 

Ha a Windows virtuális gép en lévő ügynököt a Log Analytics virtuálisgép-bővítménysel nem telepített legújabb verzióra szeretné frissíteni, vagy\<a\>parancssorból, a parancsfájlból vagy más automatizálási megoldásból, vagy az MMASetup platform .msi telepítővarázslójával fut.  

A Windows-ügynök legújabb verzióját letöltheti a Log Analytics-munkaterületről a következő lépések végrehajtásával.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

2. Az Azure Portalon kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza **a Log Analytics-munkaterületek lehetőséget.**

3. A Log Analytics-munkaterületek listájában jelölje ki a munkaterületet.

4. A Log Analytics-munkaterületen válassza a **Speciális beállítások**lehetőséget, majd a **Csatlakoztatott források**lehetőséget, és végül **a Windows-kiszolgálók lehetőséget.**

5. A **Windows-kiszolgálók** lapon válassza ki a **Windows-ügynök letöltésének** megfelelő letöltési verzióját a Windows operációs rendszer processzorarchitektúrájától függően.

>[!NOTE]
>A Windows Log Analytics-ügynök frissítése során nem támogatja a jelentésként szolgáló munkaterület konfigurálását vagy újrakonfigurálását. Az ügynök konfigurálásához a munkaterület hozzáadása vagy eltávolítása csoportban felsorolt támogatott módszerek egyikét kell [követnie.](#adding-or-removing-a-workspace)
>

#### <a name="to-upgrade-using-the-setup-wizard"></a>Frissítés a Telepítő varázslóval

1. Jelentkezzen be a számítógépre rendszergazdai jogosultságokkal rendelkező fiókkal.

2. A telepítővarázsló elindításához hajtsa végre az **MMASetup-\<platform\>.exe** parancsát.

3. A Telepítő varázsló első lapján kattintson a **Tovább gombra.**

4. A **Microsoft Monitoring Agent setup (Figyelési ügynök) párbeszédpanelen** kattintson az **Elfogadom** a licencszerződés elfogadása elemre.

5. A **Microsoft Monitoring Agent beállítása** párbeszédpanelen kattintson a **Frissítés** lehetőségre. Az állapotlapon megjelenik a frissítés állapota.

6. Amikor a **Microsoft Monitoring Agent konfigurációja sikeresen befejeződött.** megjelenik a lap, kattintson a **Befejezés gombra.**

#### <a name="to-upgrade-from-the-command-line"></a>Frissítés a parancssorból

1. Jelentkezzen be a számítógépre rendszergazdai jogosultságokkal rendelkező fiókkal.

2. Az ügynök telepítőfájljainak kibontásához `MMASetup-<platform>.exe /c` egy rendszergazda jogú parancssorból, és megkéri a fájlok kibontásának elérési útját. Másik lehetőségként megadhatja az elérési utat `MMASetup-<platform>.exe /c /t:<Full Path>`az argumentumok megadásával.

3. Futtassa a következő parancsot, amelyben a D:\ a frissítési naplófájl helye.

    ```dos
    setup.exe /qn /l*v D:\logs\AgentUpgrade.log AcceptEndUserLicenseAgreement=1
    ```

### <a name="upgrade-linux-agent"></a>Linux-ügynök frissítése 

A korábbi verziókról (>1.0.0-47) való frissítés támogatott. A telepítés végrehajtása `--upgrade` a paranccsal frissíti az ügynök összes összetevőjét a legújabb verzióra.

Futtassa a következő parancsot az ügynök frissítéséhez.

`sudo sh ./omsagent-*.universal.x64.sh --upgrade`

## <a name="adding-or-removing-a-workspace"></a>Munkaterület hozzáadása vagy eltávolítása

### <a name="windows-agent"></a>Windows-ügynök
Az ebben a szakaszban ismertetett lépésekre akkor van szükség, ha nem csak a Windows-ügynököt szeretné újrakonfigurálni, hogy egy másik munkaterületnek jelentsen, vagy hogy eltávolítsa a munkaterületet a konfigurációjából, hanem akkor is, ha az ügynököt úgy szeretné konfigurálni, hogy több munkaterületnek jelentsen (általában többirányúnak nevezik). A Windows-ügynök több munkaterületnek történő jelentésre való konfigurálása csak az ügynök kezdeti beállítása és az alább leírt módszerek használata után hajtható végre.    

#### <a name="update-settings-from-control-panel"></a>Beállítások frissítése a Vezérlőpultról

1. Jelentkezzen be a számítógépre rendszergazdai jogosultságokkal rendelkező fiókkal.

2. Nyissa **meg a Vezérlőpultot**.

3. Válassza a **Microsoft Monitoring Agent** lehetőséget, majd kattintson az Azure Log **Analytics** fülre.

4. Ha eltávolít egy munkaterületet, jelölje ki, majd kattintson **az Eltávolítás gombra.** Ismételje meg ezt a lépést bármely más munkaterület esetében, amelynek azt szeretné, hogy az ügynök ne jelentsen.

5. Ha munkaterületet ad hozzá, kattintson a **Hozzáadás** gombra, és a **Naplóelemzési munkaterület hozzáadása** párbeszédpanelen illessze be a munkaterület-azonosítót és a munkaterületi kulcsot (elsődleges kulcs). Ha a gépnek egy Azure Government-felhőbeli Log Analytics-munkaterületnek kell jelentenie, válassza az Azure US Government lehetőséget az Azure Cloud legördülő listából.

6. Kattintson az **OK** gombra a módosítások mentéséhez.

#### <a name="remove-a-workspace-using-powershell"></a>Munkaterület eltávolítása a PowerShell használatával

```powershell
$workspaceId = "<Your workspace Id>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.RemoveCloudWorkspace($workspaceId)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-commercial-using-powershell"></a>Munkaterület hozzáadása az Azure-reklámban a PowerShell használatával

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>Munkaterület hozzáadása az Azure for US Government szolgáltatásban a PowerShell használatával

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>Ha korábban a parancssort vagy parancsfájlt használta az `EnableAzureOperationalInsights` ügynök telepítéséhez `RemoveCloudWorkspace`vagy konfigurálásához, a program lecserélte a y- ra. `AddCloudWorkspace`
>

### <a name="linux-agent"></a>Linux ügynök
A következő lépések bemutatják, hogyan konfigurálhatja újra a Linux-ügynököt, ha úgy dönt, hogy egy másik munkaterületre regisztrálja, vagy eltávolít egy munkaterületet a konfigurációból.

1. Annak ellenőrzéséhez, hogy egy munkaterületre van-e regisztrálva, futtassa a következő parancsot:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    A következő példához hasonló állapotot kell visszaadnia:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

    Fontos, hogy az állapot azt is mutatja, hogy az ügynök fut, különben az ügynök újrakonfigurálásának következő lépései nem fejeződnek be sikeresen.

2. Ha már regisztrálva van egy munkaterülettel, távolítsa el a regisztrált munkaterületet a következő parancs futtatásával. Ellenkező esetben, ha nincs regisztrálva, folytassa a következő lépéssel.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -X`

3. Ha másik munkaterülettel szeretne regisztrálni, futtassa a következő parancsot:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <shared key> [-d <top level domain>]`
    
4. A módosítások érvénybe lépésének ellenőrzéséhez futtassa a következő parancsot:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    A következő példához hasonló állapotot kell visszaadnia:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

A módosítások életbe léptetéséhez az ügynökszolgáltatást nem kell újraindítani.

## <a name="update-proxy-settings"></a>Proxybeállítások frissítése
Ha azt szeretné beállítani, hogy az ügynök a telepítés után egy proxykiszolgálón vagy [a Log Analytics-átjárón](gateway.md) keresztül kommunikáljon a szolgáltatással, a feladat végrehajtásához használja az alábbi módszerek egyikét.

### <a name="windows-agent"></a>Windows-ügynök

#### <a name="update-settings-using-control-panel"></a>Beállítások frissítése a Vezérlőpulton

1. Jelentkezzen be a számítógépre rendszergazdai jogosultságokkal rendelkező fiókkal.

2. Nyissa **meg a Vezérlőpultot**.

3. Válassza a **Microsoft Monitoring Agent lehetőséget,** majd kattintson a **Proxybeállítások** fülre.

4. Kattintson a **Proxykiszolgáló használata** elemre, és adja meg a proxykiszolgáló vagy átjáró URL-címét és portszámát. Ha a proxykiszolgáló vagy a Log Analytics-átjáró hitelesítést igényel, írja be a felhasználónevet és jelszót a hitelesítéshez, majd kattintson az **OK** gombra.

#### <a name="update-settings-using-powershell"></a>Beállítások frissítése a PowerShell használatával

Másolja a következő PowerShell-kódot, frissítse azt a környezetére vonatkozó információkkal, és mentse ps1 fájlnévkiterjesztéssel. Futtassa a parancsfájlt minden olyan számítógépen, amely közvetlenül csatlakozik az Azure Monitor Log Analytics munkaterületéhez.

```powershell
param($ProxyDomainName="https://proxy.contoso.com:30443", $cred=(Get-Credential))

# First we get the Health Service configuration object. We need to determine if we
#have the right update rollup with the API we need. If not, no need to run the rest of the script.
$healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

$proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

if (!$proxyMethod)
{
    Write-Output 'Health Service proxy API not present, will not update settings.'
    return
}

Write-Output "Clearing proxy settings."
$healthServiceSettings.SetProxyInfo('', '', '')

$ProxyUserName = $cred.username

Write-Output "Setting proxy to $ProxyDomainName with proxy username $ProxyUserName."
$healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)
```

### <a name="linux-agent"></a>Linux ügynök
Hajtsa végre a következő lépéseket, ha a Linux-számítógépek proxykiszolgálón vagy Log Analytics-átjárón keresztül kell kommunikálniuk. A proxykonfiguráció értékének szintaxisa a következő: `[protocol://][user:password@]proxyhost[:port]`. A *proxyhost* tulajdonság a proxykiszolgáló teljes tartománynevét vagy IP-címét fogadja el.

1. Szerkessze az `/etc/opt/microsoft/omsagent/proxy.conf` fájlt a következő parancsok futtatásával, és módosítsa az értékeket a saját konkrét beállításaira.

    ```
    proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
    sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf
    ```

2. Indítsa újra az ügynököt a következő parancs futtatásával:

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ```

## <a name="uninstall-agent"></a>Ügynök eltávolítása
Az alábbi eljárások egyikével távolítsa el a Windows vagy Linux ügynököt a parancssori vagy beállítási varázsló segítségével.

### <a name="windows-agent"></a>Windows-ügynök

#### <a name="uninstall-from-control-panel"></a>Eltávolítás a Vezérlőpultról
1. Jelentkezzen be a számítógépre rendszergazdai jogosultságokkal rendelkező fiókkal.

2. A **Vezérlőpulton**kattintson a **Programok és szolgáltatások gombra.**

3. A **Programok és szolgáltatások menüben**kattintson a Microsoft Monitoring **Agent**elemre, majd az **Eltávolítás gombra,** majd az **Igen**gombra.

>[!NOTE]
>Az Ügynök beállítása varázsló is futtatható dupla kattintással **MMASetup-\<platform\>.exe**, amely letölthető egy munkaterületről az Azure Portalon.

#### <a name="uninstall-from-the-command-line"></a>Eltávolítás a parancssorból
Az ügynök letöltött fájlja az IExpress programmal létrehozott önálló telepítőcsomag. Az ügynök és a támogató fájlok telepítőprogramja a csomagban található, és ki kell bontani annak érdekében, hogy a következő példában látható parancssorból megfelelően eltávolíthassuk őket.

1. Jelentkezzen be a számítógépre rendszergazdai jogosultságokkal rendelkező fiókkal.

2. Az ügynök telepítőfájljainak kibontásához `extract MMASetup-<platform>.exe` egy rendszergazda jogú parancssorból, és megkéri a fájlok kibontásának elérési útját. Másik lehetőségként megadhatja az elérési utat `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>`az argumentumok megadásával. Az IExpress által támogatott parancssori kapcsolókról az [IExpress parancssori kapcsolóiban](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages) talál további információt, majd az igényeinek megfelelően frissítse a példát.

3. A parancssorba `%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb`írja be a következőt: .

### <a name="linux-agent"></a>Linux ügynök
Az ügynök eltávolításához futtassa az alábbi parancsot a Linux rendszerű számítógépen. A *--purge* argumentum teljesen eltávolítja az ügynököt és annak konfigurációját.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>Ügynök konfigurálása az Operations Manager felügyeleti csoportjának való jelentéshez

### <a name="windows-agent"></a>Windows-ügynök
Hajtsa végre az alábbi lépéseket, hogy konfigurálja a Windows Log Analytics ügynökét, hogy jelentsen egy System Center Operations Manager felügyeleti csoportnak.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. Jelentkezzen be a számítógépre rendszergazdai jogosultságokkal rendelkező fiókkal.

2. Nyissa **meg a Vezérlőpultot**.

3. Kattintson a **Microsoft Monitoring Agent fülre,** majd az **Operations Manager** fülre.

4. Ha az Operations Manager-kiszolgálók integrálódtak az Active Directoryval, kattintson **a Felügyeleti csoport hozzárendeléseinek automatikus frissítése az Active Directoryból**elemre.

5. A **Hozzáadás** gombra kattintva nyissa meg a **Felügyeleti csoport hozzáadása** párbeszédpanelt.

6. A **Felügyeleti csoport neve** mezőbe írja be a felügyeleti csoport nevét.

7. Az **Elsődleges felügyeleti kiszolgáló** mezőbe írja be az elsődleges felügyeleti kiszolgáló számítógépnevét.

8. A **Felügyeleti kiszolgáló portja** mezőbe írja be a TCP-port számát.

9. Az **Ügynöki műveletfiók csoportban**válassza a Helyi rendszer fiókot vagy egy helyi tartományi fiókot.

10. Kattintson az **OK** gombra a **Felügyeleti csoport hozzáadása** párbeszédpanel bezárásához, majd a Microsoft Monitoring Agent **tulajdonságai** párbeszédpanel bezárásához kattintson az **OK** gombra.

### <a name="linux-agent"></a>Linux ügynök
Hajtsa végre az alábbi lépéseket a Log Analytics-ügynök Linuxhoz konfigurálásához, hogy jelentsen egy System Center Operations Manager felügyeleti csoportnak.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. A fájl szerkesztése`/etc/opt/omi/conf/omiserver.conf`

2. Győződjön meg arról, hogy az 1270-es porttal `httpsport=` kezdődő sor határozza meg. Mint:`httpsport=1270`

3. Indítsa újra az OMI-kiszolgálót:`sudo /opt/omi/bin/service_control restart`

## <a name="next-steps"></a>További lépések

- Tekintse át [a Linux-ügynök hibaelhárítását,](agent-linux-troubleshoot.md) ha problémákat tapasztal a Linux-ügynök telepítése vagy kezelése során.

- Tekintse át [a Windows-ügynök hibaelhárítása](agent-windows-troubleshoot.md) című témakört, ha problémákat tapasztal a Windows-ügynök telepítése vagy kezelése során.
