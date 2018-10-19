---
title: Az Azure Log Analytics-ügynök felügyelete |} A Microsoft Docs
description: Ez a cikk ismerteti a különböző felügyeleti feladatok, amelyek általában a, a Microsoft Monitoring Agent (MMA) gépen telepített életciklusa alatt fogja elvégezni.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/30/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: eaf6aa538a4733528b52b1417c2d53318064e068
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2018
ms.locfileid: "49405396"
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>Kezelésével és karbantartásával a Log Analytics-ügynököket Windows és Linux rendszerekhez

A Windows vagy Linux-ügynök a Log Analytics kezdeti telepítés után szükség lehet konfigurálja újra az ügynököt, vagy ha elérte életciklusa szakasza a használatból való kivonást egyaránt eltávolítja azt a számítógépről.  Könnyedén felügyelheti a rendszeres karbantartási műveletek manuálisan, illetve az automation, ami csökkenti a működési hiba és a költségeket.

## <a name="adding-or-removing-a-workspace"></a>Hozzáadása vagy eltávolítása a munkaterület 

### <a name="windows-agent"></a>Windows-ügynök

#### <a name="update-settings-from-control-panel"></a>A Vezérlőpult-beállítások frissítése

1. Jelentkezzen be a számítógépre egy olyan fiókkal, amely rendszergazdai jogosultságokkal rendelkezik.
2. Nyissa meg **Vezérlőpultot**.
3. Válassza ki **Microsoft Monitoring Agent** és kattintson a **Azure Log Analytics** fülre.
4. Ha eltávolítja egy munkaterületet, jelölje ki azt, és kattintson a **eltávolítása**. Ismételje meg ezt a lépést bármely más azt szeretné, hogy az ügynök a továbbiakban a jelentéskészítés munkaterületen.
5. Ha ad hozzá egy munkaterületet, kattintson a **Hozzáadás** és az a **adja hozzá a Log Analytics-munkaterület** párbeszédpanelen illessze be a munkaterület Azonosítóját és a Munkaterületkulcsot (elsődleges kulcs). Ha az Azure Government-felhőbeli Log Analytics-munkaterületet a kell jelentenie, válassza ki Azure US Government az Azure Cloud legördülő listából. 
6. Kattintson az **OK** gombra a módosítások mentéséhez.

#### <a name="remove-a-workspace-using-powershell"></a>Egy PowerShell-lel munkaterület eltávolítása 

```PowerShell
$workspaceId = "<Your workspace Id>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.RemoveCloudWorkspace($workspaceId)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-commercial-using-powershell"></a>Munkaterület hozzáadása az Azure-ban kereskedelmi PowerShell-lel 

```PowerShell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>”
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>Munkaterület hozzáadása az Azure-ban az USA kormányzati szerveinek biztosított PowerShell-lel 

```PowerShell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>”
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>Ha már használta a parancssorból vagy parancsfájlból korábban telepíteni vagy konfigurálni az ügynököt `EnableAzureOperationalInsights` értéke le lett cserélve `AddCloudWorkspace` és `RemoveCloudWorkspace`.
>

### <a name="linux-agent"></a>Linux-ügynök
A következő lépések bemutatják, hogyan konfigurálja újra a Linux-ügynök, ha úgy dönt, hogy regisztrálja az egy másik munkaterületet, vagy eltávolítja a munkaterület az a konfiguráció.  

1.  Annak ellenőrzéséhez, hogy regisztrálva van egy munkaterületet, futtassa a következő parancsot.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l` 

    Kell visszaadnia egy állapota a következőhöz hasonló- 

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

    Fontos, hogy a is állapota az ügynök fut, ellenkező esetben a következő lépések végrehajtásával konfigurálja újra az ügynök nem lesz sikeres.  

2. Ha már regisztrálva van egy munkaterülethez, távolítsa el a regisztrált munkaterület a következő parancs futtatásával.  Ellenkező esetben, ha nincs regisztrálva van, folytassa a következő lépéssel.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -X`  
    
3. Egy másik munkaterülettel regisztrálásához futtassa a parancsot `/opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <shared key> [-d <top level domain>]` 
4. A módosítások érinthetik tartott ellenőrzéséhez futtassa a parancsot.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l` 

    Kell visszaadnia egy állapota a következőhöz hasonló- 

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

Az ügynök szolgáltatást nem kell ahhoz, hogy a módosítások érvénybe léptetéséhez újra kell indítani.

## <a name="update-proxy-settings"></a>Proxy-beállítások frissítése 
A szolgáltatás egy proxykiszolgálón keresztül kommunikáljon az ügynök konfigurálásához vagy [Log Analytics-átjáró](log-analytics-oms-gateway.md) az üzembe helyezést követően használja a feladat végrehajtásához a következő módszerek egyikét.

### <a name="windows-agent"></a>Windows-ügynök

#### <a name="update-settings-using-control-panel"></a>A Vezérlőpulton beállításainak frissítése

1. Jelentkezzen be a számítógépre egy olyan fiókkal, amely rendszergazdai jogosultságokkal rendelkezik.
2. Nyissa meg **Vezérlőpultot**.
3. Válassza ki **Microsoft Monitoring Agent** és kattintson a **proxybeállítások** fülre.
4. Kattintson a **proxykiszolgálóval** , és adja meg az URL-címét és portszámát a proxy-kiszolgáló vagy átjáró. Ha a proxykiszolgáló vagy a Log Analytics-átjáró hitelesítést igényel, írja be a felhasználónevet és jelszót a hitelesítéshez, és kattintson a **OK**. 

#### <a name="update-settings-using-powershell"></a>PowerShell-lel beállításainak frissítése 

Másolja az alábbi minta PowerShell-kódot, frissítse az adott környezetre jellemző információkat, és mentse PS1 fájlnévkiterjesztéssel.  Futtassa a szkriptet minden olyan számítógépen, amely közvetlenül csatlakozik a Log Analytics szolgáltatásnak.

```PowerShell
param($ProxyDomainName="https://proxy.contoso.com:30443", $cred=(Get-Credential))

# First we get the Health Service configuration object.  We need to determine if we
#have the right update rollup with the API we need.  If not, no need to run the rest of the script.
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
Ha Linux rendszerű számítógépek keresztül kell kommunikálniuk-proxykiszolgálót vagy a Log Analytics-átjáró, hajtsa végre az alábbi lépéseket.  A proxykonfiguráció értékének szintaxisa a következő: `[protocol://][user:password@]proxyhost[:port]`.  A *proxyhost* tulajdonság a proxykiszolgáló teljes tartománynevét vagy IP-címét fogadja el.

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
A következő eljárások valamelyikével parancssori vagy a telepítő varázsló segítségével Windows vagy Linux-ügynök eltávolítása.

### <a name="windows-agent"></a>Windows-ügynök

#### <a name="uninstall-from-control-panel"></a>Távolítsa el a Vezérlőpult
1. Jelentkezzen be a számítógépre egy olyan fiókkal, amely rendszergazdai jogosultságokkal rendelkezik.  
2. A **Vezérlőpult**, kattintson a **programok és szolgáltatások**.
3. A **programok és szolgáltatások**, kattintson a **Microsoft Monitoring Agent**, kattintson a **Eltávolítás**, és kattintson a **Igen**.

>[!NOTE]
>Az ügynök telepítővarázslójával duplán kattintva is elindítható **MMASetup -<platform>.exe**, amely letölthető a munkaterület az Azure Portalon érhető el.

#### <a name="uninstall-from-the-command-line"></a>Távolítsa el a parancssorból
A letöltött fájlt az ügynök a csomag egy önálló telepítő IExpress hoztak létre.  A telepítőprogram az ügynök és a fájlokat a csomagban található, és szeretne kinyerni annak érdekében, hogy megfelelően eltávolítsa a parancssorból az alábbi példában látható. 

1. Jelentkezzen be a számítógépre egy olyan fiókkal, amely rendszergazdai jogosultságokkal rendelkezik.  
2. Egy rendszergazda jogú parancssorból futtassa az ügynök telepítési fájljait, kibontásához `extract MMASetup-<platform>.exe` és a rendszer felkéri a elérési útján a fájlok kibontásához.  Másik lehetőségként megadhatja az elérési út az argumentumoknak `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>`.  Az IExpress által támogatott parancssori swtiches további információkért lásd: [IExpress használható parancssori kapcsolók](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages) , majd frissítse a példát, hogy illeszkedjen az igényeihez.
3. A parancssorba írja be a `%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb`.  

### <a name="linux-agent"></a>Linux-ügynök
Az ügynök eltávolításához futtassa az alábbi parancsot a Linux rendszerű számítógépen.  A *--purge* argumentum teljesen eltávolítja az ügynököt és annak konfigurációját.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>Az ügynök számára, hogy az Operations Manager felügyeleti csoport konfigurálása

### <a name="windows-agent"></a>Windows-ügynök
Hajtsa végre az alábbi lépéseket a Log Analytics-ügynököket a Windows számára, hogy a System Center Operations Manager felügyeleti csoport konfigurálása.

[!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)] 

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

[!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

1. A fájl szerkesztése `/etc/opt/omi/conf/omiserver.conf`
2. Ellenőrizze, hogy a sor elején `httpsport=` az 1270-es portot határozza meg. Például: `httpsport=1270`
3. Indítsa újra az OMI-kiszolgáló: `sudo /opt/omi/bin/service_control restart`

## <a name="next-steps"></a>További lépések

Felülvizsgálat [a Linux-ügynök hibaelhárítási](log-analytics-agent-linux-support.md) Ha problémák merülnek fel telepítése vagy az ügynök kezelése során.  