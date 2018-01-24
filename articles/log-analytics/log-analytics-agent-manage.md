---
title: "A Azure Log Analytics Agent kezelése |} Microsoft Docs"
description: "Ez a cikk ismerteti a különböző felügyeleti feladatok, amelyek végrehajtják általában az a Microsoft Monitoring Agent (MMA) egy gépen központilag telepített életciklusa folyamán."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2018
ms.author: magoedte
ms.openlocfilehash: a17418142fb5f52a93d7a56cb2e6e6e97a250002
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2018
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>Kezelését és karbantartását a Log Analyticshez ügynök Windows és Linux rendszerekhez

A Windows vagy Linux-ügynök Naplóelemzési a kezdeti telepítés után szükség lehet konfigurálja újra a helyzettől függően az ügynök, vagy ha elérte a használatból való kivonást szakasza életciklus eltávolítja azt a számítógépről.  A szokásos karbantartási feladatok manuálisan, illetve az automation, ami csökkenti a működési hiba és a is könnyedén kezelhető.

## <a name="adding-or-removing-a-workspace"></a>Hozzáadásával vagy eltávolításával a munkaterület 

### <a name="windows-agent"></a>Windows-ügynök

#### <a name="update-settings-from-control-panel"></a>A Vezérlőpult-beállítások frissítése

1. Jelentkezzen be a számítógépre egy olyan fiókkal, amely rendszergazdai jogosultságokkal rendelkezik.
2. Nyissa meg **Vezérlőpultot**.
3. Válassza ki **Microsoft-Figyelőügynök** , majd a **Azure Naplóelemzés (OMS)** fülre.
4. Ha a munkaterületet távolítani, válassza ki azt, és kattintson **eltávolítása**. Ismételje meg ezt a lépést minden egyéb munkaterülethez azt szeretné, hogy az ügynök a továbbiakban nem küld jelentéseket.
5. Ha a munkaterületet hozzá, kattintson a **Hozzáadás** és az a **hozzáadni a Naplóelemzési munkaterület** párbeszédpanelen illessze be a munkaterület azonosítója és Munkaterületkulcsot (elsődleges kulcs). A számítógép egy Naplóelemzési munkaterületet Azure Government felhőben jelentse, ha az Azure-felhő legördülő listából válassza ki az Azure Amerikai Egyesült államokbeli kormányzati. 
6. Kattintson az **OK** gombra a módosítások mentéséhez.

#### <a name="remove-a-workspace-using-powershell"></a>Távolítsa el a PowerShell használatával munkaterület 

```PowerShell
$workspaceId = "<Your workspace Id>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.RemoveCloudWorkspace($workspaceId)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-commercial-using-powershell"></a>A munkaterület hozzáadása az Azure kereskedelmi PowerShell használatával 

```PowerShell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>”
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>A munkaterület hozzáadása az Azure-ban az Amerikai Egyesült államokbeli kormányzati PowerShell használatával 

```PowerShell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>”
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>Ha már használta a parancssorból vagy parancsfájlból korábban az ügynök telepítéséhez és konfigurálásához a `EnableAzureOperationalInsights` le lett cserélve `AddCloudWorkspace` és `RemoveCloudWorkspace`.
>

## <a name="update-proxy-settings"></a>Proxykiszolgáló-beállításainak frissítése 
Az ügynököt, hogy a szolgáltatás egy proxyn keresztül történő kommunikációhoz konfigurálandó vagy [OMS átjáró](log-analytics-oms-gateway.md) telepítést követően a következő módszerek valamelyikével befejezheti a feladatot.

### <a name="windows-agent"></a>Windows-ügynök

#### <a name="update-settings-using-control-panel"></a>Használja a Vezérlőpult-beállítások frissítése

1. Jelentkezzen be a számítógépre egy olyan fiókkal, amely rendszergazdai jogosultságokkal rendelkezik.
2. Nyissa meg **Vezérlőpultot**.
3. Válassza ki **Microsoft-Figyelőügynök** , majd a **proxybeállítások** fülre.
4. Kattintson a **proxykiszolgálót használni** és URL-címét és portszámát a proxy- vagy az átjáró. Ha a proxykiszolgáló vagy az OMS-átjáró megköveteli a hitelesítést, írja be a felhasználónév és jelszó hitelesítéséhez, és kattintson a **OK**. 

#### <a name="update-settings-using-powershell"></a>PowerShell-lel beállításainak frissítése 

Másolja az alábbi PowerShell-példakód, frissítse a környezetre vonatkozó információkkal, és mentse a PS1 fájlnév-kiterjesztésűeket.  Futtassa a parancsfájlt, amely közvetlenül kapcsolódik a Naplóelemzés szolgáltatás minden számítógépen.

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
Hajtsa végre az alábbi lépéseket, ha a Linux rendszerű számítógépek kell kommunikációja áthaladjon a proxykiszolgáló vagy az OMS átjáró szolgáltatáshoz.  A proxykonfiguráció értékének szintaxisa a következő: `[protocol://][user:password@]proxyhost[:port]`.  A *proxyhost* tulajdonság a proxykiszolgáló IP-címet vagy egy teljesen minősített tartománynevét fogad el.

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
A Windows vagy Linux ügynök eltávolítása a parancssori vagy a telepítő varázsló segítségével használja a következő eljárások egyikét.

### <a name="windows-agent"></a>Windows-ügynök

#### <a name="uninstall-from-control-panel"></a>Távolítsa el a Vezérlőpult
1. Jelentkezzen be a számítógépre egy olyan fiókkal, amely rendszergazdai jogosultságokkal rendelkezik.  
2. A **Vezérlőpult**, kattintson a **programok és szolgáltatások**.
3. A **programok és szolgáltatások**, kattintson a **Microsoft-Figyelőügynök**, kattintson a **Eltávolítás**, és kattintson a **Igen**.

>[!NOTE]
>Az ügynök telepítése varázsló is futtatható duplán kattintva **MMASetup -<platform>.exe**, elérhető letöltésre egy olyan munkaterületről, az Azure portálon.

#### <a name="uninstall-from-the-command-line"></a>Távolítsa el a parancssorból
Az ügynök a letöltött fájl csomag egy önálló telepítő IExpress létre.  A telepítőprogram az ügynök és a fájlokat a csomagban található, és ahhoz, hogy megfelelően távolítsa el a parancssorból az alábbi példában látható módon kinyerni kell. 

1. Jelentkezzen be a számítógépre egy olyan fiókkal, amely rendszergazdai jogosultságokkal rendelkezik.  
2. Egy rendszergazda jogú parancssorból futtassa a telepítési fájlokat, kibontani `extract MMASetup-<platform>.exe` és felszólítja a fájlokat az elérési úthoz.  Másik lehetőségként az elérési utat is megadhat úgy, hogy az argumentumok `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>`.  Az IExpress által támogatott parancssori swtiches további információkért lásd: [parancssori kapcsolók a IExpress](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages) majd frissítse a példa az igényeinek.
3. Írja be a parancssorba `%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb`.  

### <a name="linux-agent"></a>Linux-ügynök
Az ügynök eltávolításához hajtsa végre az alábbi lépéseket.

1. Töltse le a linuxos ügynök [univerzális parancsfájlját](https://github.com/Microsoft/OMS-Agent-for-Linux/releases) a számítógépre.
2. Futtassa a csomag .sh fájlját a *--purge* argumentummal a számítógépen, ami teljesen eltávolítja az ügynököt és annak konfigurációját.

    `sudo sh ./omsagent-<version>.universal.x64.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>Hogy az Operations Manager felügyeleti csoport konfigurálása

### <a name="windows-agent"></a>Windows-ügynök
Hajtsa végre a következő lépésekkel állíthatja be az OMS ügynök a Windows számára, hogy egy System Center Operations Manager felügyeleti csoportban. 

1. Jelentkezzen be a számítógépre egy olyan fiókkal, amely rendszergazdai jogosultságokkal rendelkezik.
2. Nyissa meg **Vezérlőpultot**. 
3. Kattintson a **Microsoft-Figyelőügynök** , majd a **Operations Manager** fülre.
4. Ha az Operations Manager-kiszolgáló Active Directory integrációja a, kattintson a **az AD DS-ből felügyeleti csoport-hozzárendelések automatikus frissítése**.
5. Kattintson a **Hozzáadás** megnyitásához a **felügyeleti csoport hozzáadása** párbeszédpanel megnyitásához.
6. A **felügyeleti csoport neve** mezőbe írja be a felügyeleti csoport nevét.
7. Az a **elsődleges felügyeleti kiszolgáló** mezőbe írja be az elsődleges felügyeleti kiszolgáló számítógépneve.
8. Az a **felügyeleti kiszolgáló portszáma** mezőbe írja be a TCP-portszámot.
9. A **Ügynökműveleti fiók**, válassza a helyi rendszerfiókot vagy egy helyi tartományi fiók.
10. Kattintson a **OK** bezárásához a **felügyeleti csoport hozzáadása** párbeszédpanel megnyitásához, majd kattintson **OK** bezárásához a **Microsoft Monitoring Agent tulajdonságai** párbeszédpanel megnyitásához.

### <a name="linux-agent"></a>Linux-ügynök
Hajtsa végre a következő lépésekkel állíthatja be az OMS-ügynököt, hogy egy System Center Operations Manager felügyeleti csoport linuxos. 

1. A fájl szerkesztése`/etc/opt/omi/conf/omiserver.conf`
2. Győződjön meg arról, hogy a kezdődő `httpsport=` határozza meg az 1270-es port. Például:`httpsport=1270`
3. Indítsa újra az OMI-kiszolgálón:`sudo /opt/omi/bin/service_control restart`

## <a name="next-steps"></a>További lépések

Felülvizsgálati [a Linux-ügynök hibaelhárítási](log-analytics-agent-linux-support.md) Ha hibát tapasztal telepítése vagy az ügynök kezelése során.  