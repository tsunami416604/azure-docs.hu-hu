---
title: Az Azure Log Analytics-ügynök felügyelete |} A Microsoft Docs
description: Ez a cikk ismerteti a különböző felügyeleti feladatok, amelyek általában a Log Analytics Windows vagy Linux-ügynök üzembe helyezett egy gépen élettartama során fogja elvégezni.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: magoedte
ms.openlocfilehash: 1809cc50f3ad3c285e0b69bc6e383a2c7c398238
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65139249"
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>Kezelésével és karbantartásával a Log Analytics-ügynököket Windows és Linux rendszerekhez

A Log Analytics Windows vagy Linux-ügynök az Azure monitorban kezdeti telepítés után szükség lehet konfigurálja újra az ügynököt, frissíteni, vagy ha elérte életciklusa szakasza a használatból való kivonást egyaránt eltávolítja azt a számítógépről. Könnyedén felügyelheti a rendszeres karbantartási műveletek manuálisan, illetve az automation, ami csökkenti a működési hiba és a költségeket.

## <a name="upgrading-agent"></a>Ügynök frissítése

A Log Analytics-ügynököket Windows és Linux rendszerhez készült frissíthetők a legfrissebb kiadás manuális vagy automatikus üzembe helyezési forgatókönyv és a virtuális gép fut-e a környezetben. Az alábbi módszerek segítségével frissítse az ügynököt.

| Környezet | A telepítési mód | Frissítési eljárás |
|--------|----------|-------------|
| Azure VM | Log Analytics ügynök Virtuálisgép-bővítmény Windows/Linux rendszerhez készült | Az ügynök automatikusan frissül alapértelmezés szerint, ha nem konfigurálta az Azure Resource Manager-sablon tulajdonság beállításával vesznek *autoUpgradeMinorVersion* való **hamis**. |
| Egyéni Azure-beli Virtuálisgép-rendszerképek | Manuális Windows/Linux rendszerhez készült Log Analytics-ügynök telepítése | Virtuális gépek frissítése az ügynök legújabb verziójára kell a Windows installer-csomag vagy a Linux önkicsomagoló és telepíthető shell script csomag futtatása a parancssorból kell elvégezni.|
| Nem Azure-beli virtuális gépek | Manuális Windows/Linux rendszerhez készült Log Analytics-ügynök telepítése | Virtuális gépek frissítése az ügynök legújabb verziójára kell a Windows installer-csomag vagy a Linux önkicsomagoló és telepíthető shell script csomag futtatása a parancssorból kell elvégezni. |

### <a name="upgrade-windows-agent"></a>Windows-ügynökök frissítése 

A Windows virtuális gép-ügynök nincs telepítve a Log Analytics Virtuálisgép-bővítmény használata a legújabb verzióra frissítéséhez, futtathatja a parancssorból, szkript vagy más megoldás, illetve a MMASetup - használatával\<platform\>beállítása .msi A varázsló.  

Letöltheti a Windows-ügynök legújabb verzióját a Log Analytics-munkaterület az alábbi lépések végrehajtásával.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Az Azure Portalon kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza ki **Log Analytics-munkaterületek**.

3. A Log Analytics-munkaterületek listájában válassza ki a munkaterületet.

4. A Log Analytics munkaterületén válassza **speciális beállítások**, majd **csatlakoztatott források**, és végül **Windows kiszolgálók**.

5. Az a **Windows kiszolgálók** lapon, válassza ki a megfelelő **Windows-ügynök letöltése** verziót töltse le a Windows operációs rendszer a processzor architektúrájától függően.

>[!NOTE]
>A Log Analytics-ügynököket Windows esetében a frissítés során nem támogatja konfigurálásával vagy egy munkaterületet, hogy újrakonfigurálása. Az ügynök konfigurálásához kövesse a felsorolt támogatott módszerek egyikét kell [hozzáadása és eltávolítása a munkaterület](#adding-or-removing-a-workspace).
>

#### <a name="to-upgrade-using-the-setup-wizard"></a>Frissítése a telepítővarázslóval

1. Jelentkezzen be a számítógépre egy olyan fiókkal, amely rendszergazdai jogosultságokkal rendelkezik.

2. Hajtsa végre **MMASetup -\<platform\>.exe** a telepítővarázsló elindításához.

3. A telepítővarázsló első lapján kattintson a **tovább**.

4. Az a **Microsoft Monitoring Agent beállítása** párbeszédpanelen kattintson a **elfogadom** fogadja el a licencszerződést.

5. Az a **Microsoft Monitoring Agent beállítása** párbeszédpanelen kattintson a **frissítése**. Az állapotlapon megjelenik a frissítés állapotát.

6. Ha a **Microsoft-Figyelőügynök konfigurálása sikeresen befejeződött.** Kattintson a lap jelenik meg, **Befejezés**.

#### <a name="to-upgrade-from-the-command-line"></a>A parancssorból frissítése

1. Jelentkezzen be a számítógépre egy olyan fiókkal, amely rendszergazdai jogosultságokkal rendelkezik.

2. Egy rendszergazda jogú parancssorból futtassa az ügynök telepítési fájljait, kibontásához `MMASetup-<platform>.exe /c` és a rendszer felkéri a elérési útján a fájlok kibontásához. Másik lehetőségként megadhatja az elérési út az argumentumoknak `MMASetup-<platform>.exe /c /t:<Full Path>`.

3. Futtassa a következő parancsot, ahol D:\ a frissítési naplófájl helyét.

    ```dos
    setup.exe /qn /l*v D:\logs\AgentUpgrade.log AcceptEndUserLicenseAgreement=1
    ```

### <a name="upgrade-linux-agent"></a>Linux-ügynökök frissítése 

Frissítés a korábbi verziók (> 1.0.0-47) használata támogatott. A telepítést végez a `--upgrade` parancsot az ügynök összes összetevőjét frissíti a legújabb verzióra.

Futtassa a következő parancsot az ügynököt.

`sudo sh ./omsagent-*.universal.x64.sh --upgrade`

## <a name="adding-or-removing-a-workspace"></a>Hozzáadása vagy eltávolítása a munkaterület

### <a name="windows-agent"></a>Windows-ügynök
A jelen szakaszban ismertetett lépések szükségesek, ha azt szeretné csak nem konfigurálja újra a Windows-ügynök számára, hogy egy másik munkaterületet, vagy munkaterület eltávolítása a konfigurációs, hanem amikor szeretné konfigurálni az ügynököt (gyakran több munkaterületnek néven többkiszolgálós). A Windows-ügynök több munkaterületeknek való jelentés érdekében konfigurálása csak az ügynök és az alább ismertetett módszerek használatával a kezdeti telepítés után végezheti el.    

#### <a name="update-settings-from-control-panel"></a>A Vezérlőpult-beállítások frissítése

1. Jelentkezzen be a számítógépre egy olyan fiókkal, amely rendszergazdai jogosultságokkal rendelkezik.

2. Nyissa meg **Vezérlőpultot**.

3. Válassza ki **Microsoft Monitoring Agent** és kattintson a **Azure Log Analytics** fülre.

4. Ha eltávolítja egy munkaterületet, jelölje ki azt, és kattintson a **eltávolítása**. Ismételje meg ezt a lépést bármely más azt szeretné, hogy az ügynök a továbbiakban a jelentéskészítés munkaterületen.

5. Ha ad hozzá egy munkaterületet, kattintson a **Hozzáadás** és az a **adja hozzá a Log Analytics-munkaterület** párbeszédpanelen illessze be a munkaterület Azonosítóját és a Munkaterületkulcsot (elsődleges kulcs). Ha az Azure Government-felhőbeli Log Analytics-munkaterületet a kell jelentenie, válassza ki Azure US Government az Azure Cloud legördülő listából.

6. Kattintson az **OK** gombra a módosítások mentéséhez.

#### <a name="remove-a-workspace-using-powershell"></a>Egy PowerShell-lel munkaterület eltávolítása

```powershell
$workspaceId = "<Your workspace Id>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.RemoveCloudWorkspace($workspaceId)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-commercial-using-powershell"></a>Munkaterület hozzáadása az Azure-ban kereskedelmi PowerShell-lel

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>Munkaterület hozzáadása az Azure-ban az USA kormányzati szerveinek biztosított PowerShell-lel

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>Ha már használta a parancssorból vagy parancsfájlból korábban telepíteni vagy konfigurálni az ügynököt `EnableAzureOperationalInsights` értéke le lett cserélve `AddCloudWorkspace` és `RemoveCloudWorkspace`.
>

### <a name="linux-agent"></a>Linux-ügynök
A következő lépések bemutatják, hogyan konfigurálja újra a Linux-ügynök, ha úgy dönt, hogy regisztrálja őket egy másik munkaterületet, vagy eltávolíthatnak munkaterületeket a konfigurációban.

1. Annak ellenőrzéséhez, hogy regisztrálva van egy munkaterületet, futtassa a következő parancsot:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    Állapot kell visszaadnia az alábbi példához hasonló:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

    Fontos, hogy a is állapota az ügynök fut, ellenkező esetben a következő lépések végrehajtásával konfigurálja újra az ügynök nem lesz sikeres.

2. Ha már regisztrálva van egy munkaterülethez, távolítsa el a regisztrált munkaterület a következő parancs futtatásával. Ellenkező esetben, ha nincs regisztrálva van, folytassa a következő lépéssel.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -X`

3. Egy másik munkaterülettel regisztrálásához futtassa a következő parancsot:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <shared key> [-d <top level domain>]`
    
4. A módosítások érvénybe tartott ellenőrzéséhez futtassa a következő parancsot:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    Állapot kell visszaadnia az alábbi példához hasonló:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

Az ügynök szolgáltatást nem kell ahhoz, hogy a módosítások érvénybe léptetéséhez újra kell indítani.

## <a name="update-proxy-settings"></a>Proxy-beállítások frissítése
A szolgáltatás egy proxykiszolgálón keresztül kommunikáljon az ügynök konfigurálásához vagy [Log Analytics-átjáró](gateway.md) az üzembe helyezést követően használja a feladat végrehajtásához a következő módszerek egyikét.

### <a name="windows-agent"></a>Windows-ügynök

#### <a name="update-settings-using-control-panel"></a>A Vezérlőpulton beállításainak frissítése

1. Jelentkezzen be a számítógépre egy olyan fiókkal, amely rendszergazdai jogosultságokkal rendelkezik.

2. Nyissa meg **Vezérlőpultot**.

3. Válassza ki **Microsoft Monitoring Agent** és kattintson a **proxybeállítások** fülre.

4. Kattintson a **Proxykiszolgáló használata** elemre, és adja meg a proxykiszolgáló vagy átjáró URL-címét és portszámát. Ha a proxykiszolgáló vagy a Log Analytics-átjáró hitelesítést igényel, írja be a felhasználónevet és jelszót a hitelesítéshez, majd kattintson az **OK** gombra.

#### <a name="update-settings-using-powershell"></a>PowerShell-lel beállításainak frissítése

Másolja az alábbi minta PowerShell-kódot, frissítse az adott környezetre jellemző információkat, és mentse PS1 fájlnévkiterjesztéssel. Futtassa a szkriptet minden olyan számítógépen, amely közvetlenül csatlakozik az Azure Monitor Log Analytics-munkaterületen.

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

### <a name="linux-agent"></a>Linux-ügynök
Ha Linux rendszerű számítógépek keresztül kell kommunikálniuk-proxykiszolgálót vagy a Log Analytics-átjáró, hajtsa végre az alábbi lépéseket. A proxykonfiguráció értékének szintaxisa a következő: `[protocol://][user:password@]proxyhost[:port]`. A *proxyhost* tulajdonság a proxykiszolgáló teljes tartománynevét vagy IP-címét fogadja el.

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
Használja az alábbi eljárások egyikét a Windows vagy Linux agent eltávolítása a parancssor vagy a telepítő varázsló használatával.

### <a name="windows-agent"></a>Windows-ügynök

#### <a name="uninstall-from-control-panel"></a>Távolítsa el a Vezérlőpult
1. Jelentkezzen be a számítógépre egy olyan fiókkal, amely rendszergazdai jogosultságokkal rendelkezik.

2. A **Vezérlőpult**, kattintson a **programok és szolgáltatások**.

3. A **programok és szolgáltatások**, kattintson a **Microsoft Monitoring Agent**, kattintson a **Eltávolítás**, és kattintson a **Igen**.

>[!NOTE]
>Az ügynök telepítővarázslójával duplán kattintva is elindítható **MMASetup -\<platform\>.exe**, amely letölthető a munkaterület az Azure Portalon érhető el.

#### <a name="uninstall-from-the-command-line"></a>Távolítsa el a parancssorból
A letöltött fájlt az ügynök a csomag egy önálló telepítő IExpress hoztak létre. A telepítőprogram az ügynök és a fájlokat a csomagban található, és szeretne kinyerni annak érdekében, hogy megfelelően eltávolítsa a parancssorból az alábbi példában látható.

1. Jelentkezzen be a számítógépre egy olyan fiókkal, amely rendszergazdai jogosultságokkal rendelkezik.

2. Egy rendszergazda jogú parancssorból futtassa az ügynök telepítési fájljait, kibontásához `extract MMASetup-<platform>.exe` és a rendszer felkéri a elérési útján a fájlok kibontásához. Másik lehetőségként megadhatja az elérési út az argumentumoknak `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>`. A parancssori kapcsolók IExpress által támogatott további információkért lásd: [IExpress használható parancssori kapcsolók](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages) , majd frissítse a példát, hogy illeszkedjen az igényeihez.

3. A parancssorba írja be a `%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb`.

### <a name="linux-agent"></a>Linux-ügynök
Az ügynök eltávolításához futtassa az alábbi parancsot a Linux rendszerű számítógépen. A *--purge* argumentum teljesen eltávolítja az ügynököt és annak konfigurációját.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>Az ügynök számára, hogy az Operations Manager felügyeleti csoport konfigurálása

### <a name="windows-agent"></a>Windows-ügynök
Hajtsa végre az alábbi lépéseket a Log Analytics-ügynököket a Windows számára, hogy a System Center Operations Manager felügyeleti csoport konfigurálása.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. Jelentkezzen be a számítógépre egy olyan fiókkal, amely rendszergazdai jogosultságokkal rendelkezik.

2. Nyissa meg **Vezérlőpultot**.

3. Kattintson a **Microsoft Monitoring Agent** és kattintson a **az Operations Manager** fülre.

4. Ha az Operations Manager-kiszolgáló Active Directory-integráció, kattintson a **AD DS-től származó felügyeleticsoport-hozzárendelések automatikus frissítése**.

5. Kattintson a **Hozzáadás** megnyitásához a **adja hozzá a felügyeleti csoport** párbeszédpanel bezárásához.

6. A **felügyeleti csoport neve** mezőbe írja be a felügyeleti csoport neve.

7. Az a **elsődleges felügyeleti kiszolgáló** mezőbe írja be az elsődleges felügyeleti kiszolgáló számítógépnevét.

8. Az a **felügyeleti kiszolgáló portja** mezőbe írja be a TCP-port száma.

9. A **Ügynökműveleti fiók**, válassza a helyi rendszerfiókot vagy egy helyi tartományi fiókot.

10. Kattintson a **OK** gombra kattintva zárja be a **adja hozzá a felügyeleti csoport** párbeszédpanelen, majd kattintson **OK** gombra kattintva zárja be a **Microsoft Monitoring Agent tulajdonságai** párbeszédpanel bezárásához.

### <a name="linux-agent"></a>Linux-ügynök
A következő lépésekkel konfigurálhatja, hogy a System Center Operations Manager felügyeleti csoport Linuxhoz készült Log Analytics-ügynököket.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. A fájl szerkesztése `/etc/opt/omi/conf/omiserver.conf`

2. Ellenőrizze, hogy a sor elején `httpsport=` az 1270-es portot határozza meg. Például: `httpsport=1270`

3. Indítsa újra az OMI-kiszolgáló: `sudo /opt/omi/bin/service_control restart`

## <a name="next-steps"></a>További lépések

Felülvizsgálat [a Linux-ügynök hibaelhárítási](agent-linux-troubleshoot.md) Ha problémák merülnek fel telepítése vagy az ügynök kezelése során.
