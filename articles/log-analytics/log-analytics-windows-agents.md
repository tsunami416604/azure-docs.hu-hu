---
title: "Windows rendszerű számítógépek csatlakozzon az Azure Naplóelemzés szolgáltatáshoz |} Microsoft Docs"
description: "Ez a cikk bemutatja a lépéseket a Windows-számítógépekhez a helyszíni-infrastruktúrájában szolgáltatáshoz való kapcsolódáshoz a Naplóelemzési egy testre szabott verzióját a Microsoft Monitoring Agent (MMA) használatával."
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 932f7b8c-485c-40c1-98e3-7d4c560876d2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/03/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aaf3e596f8c287c60531a6911c5797b3de26e570
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2017
---
# <a name="connect-windows-computers-to-the-log-analytics-service-in-azure"></a>Windows számítógépek csatlakoztatása a Log Analytics szolgáltatás az Azure-ban

Ez a cikk bemutatja egy testre szabott verzióját a Microsoft Monitoring Agent (MMA) használatával kapcsolódnak a helyszíni-infrastruktúrájában Windows rendszerű számítógépek OMS-munkaterület lépéseit. Szeretné telepíteni, és az összes kívánt számítógépekre ügynökök kapcsolódjon ahhoz adatokat küldeni a Naplóelemzés szolgáltatás és a megjelenítheti és az adatok a bevezetni. Minden ügynök több munkaterületeket is tud jelentéseket.

Telepítés, parancssor, ügynökök telepítése vagy a Szükségeskonfiguráció-konfiguráló (DSC) az Azure Automationben.  

>[!NOTE]
Az Azure-ban futó virtuális gépek, akkor telepítés használatával leegyszerűsítheti a [virtuálisgép-bővítmény](../virtual-machines/windows/extensions-oms.md).

Internetkapcsolattal rendelkező számítógépeken a az ügynök szeretnék adatokat küldeni a OMS használja az internethez. Azok a számítógépek, amelyek nem rendelkeznek internetkapcsolattal, használhatja a proxy vagy az [OMS átjáró](log-analytics-oms-gateway.md).

Csatlakozás a Windows rendszerű számítógépek OMS egyszerű három egyszerű lépéseket követve:

1. Az ügynök telepítési fájl letöltését az OMS-portálon
2. Telepítse az ügynököt, a kiválasztott módszer használatával
3. Konfigurálni az ügynököt, vagy adja hozzá a további munkaterületek, ha szükséges

Az alábbi ábrán látható a Windows rendszerű számítógépek és az OMS közötti kapcsolat, telepíteni és konfigurálni az ügynököket után.

![OMS-közvetlen-ügynök – diagram](./media/log-analytics-windows-agents/oms-direct-agent-diagram.png)

Ha az IT-biztonsági házirendeknek nem engedélyezi a számítógépek a hálózat csatlakozik az internethez, konfigurálhatja a számítógépeket csatlakoztatni az OMS-átjáró. További információt és az OMS szolgáltatáshoz az OMS-átjárón keresztül kapcsolatba lépni a kiszolgálók konfigurálásával kapcsolatos lépéseket, [számítógépeket csatlakoztatni az OMS Szolgáltatáshoz az OMS-átjáró](log-analytics-oms-gateway.md).

## <a name="system-requirements-and-required-configuration"></a>Rendszerkövetelmények és a szükséges konfigurációval
Telepítéséhez, és ügynökök telepítésére, tekintse át a következő részleteket győződjön meg arról, hogy számítógépe megfelel a követelményeknek.

- Az OMS MMA csak telepítheti a Windows Server 2008 SP 1 vagy újabb rendszerű számítógépek vagy Windows 7 SP1 vagy újabb.
- Azure-előfizetés szükséges.  További információkért lásd: [Ismerkedés a Naplóelemzési](log-analytics-get-started.md).
- Minden Windows-számítógép csatlakozni az internethez, HTTPS-kapcsolaton keresztül vagy az OMS-átjárónak képesnek kell lennie. Lehet, hogy a kapcsolat közvetlen, egy proxyn keresztül, vagy az OMS-átjárón keresztül.
- Az OMS MMA telepíthető önálló számítógépek, kiszolgálók és virtuális gépek. Ha azt szeretné, az Azure által üzemeltetett virtuális gépeket csatlakoztatni az OMS Szolgáltatáshoz, lásd: [Log Analyticshez való csatlakozás Azure virtuális gépek](log-analytics-azure-vm-extension.md).
- Az ügynök a különböző erőforrások 443-as TCP-port használatára van szüksége.

### <a name="network"></a>Network (Hálózat)

Windows-ügynökök, és csatlakozhat, és regisztrálja az OMS szolgáltatással a hálózati erőforrások eléréséhez, beleértve a portszámot és a tartomány URL-címek kell rendelkezniük.

- Proxykiszolgálók esetében biztosítania kell, hogy a megfelelő proxykiszolgáló-erőforrások konfigurálva vannak az ügynök beállításaiban.
- Az interneten való hozzáférés korlátozása tűzfalak, vagy a hálózati mérnökök kell konfigurálni a tűzfalat, hogy a hozzáférés engedélyezése az OMS Szolgáltatáshoz. Az ügynök beállításait nem kell módosítania.

Az alábbi táblázat a kommunikációhoz szükséges erőforrásokat tartalmazza.

>[!NOTE]
>A következő erőforrások említse meg az Operational Insights, amelyet a rendszer Naplóelemzési előző nevét.

| Ügynök erőforrása | Portok | HTTPS-ellenőrzés kihagyása |
|---|---|---|
| *.ods.opinsights.azure.com | 443 | Igen |
| *.oms.opinsights.azure.com | 443 | Igen |
| *.blob.core.windows.net | 443 | Igen |
| *.azure-automation.net | 443 | Igen |



## <a name="download-the-agent-setup-file-from-oms"></a>Töltse le az ügynököt telepítő fájl az OMS Szolgáltatáshoz
1. Az a [OMS-portálon](https://www.mms.microsoft.com), az a **áttekintése** lapján kattintson a **beállítások** csempére.  Kattintson a **csatlakoztatott források** fülre az oldal tetején.  
    ![Csatlakoztatott adatforrások lap](./media/log-analytics-windows-agents/oms-direct-agent-connected-sources.png)
2. Kattintson a **Windows-kiszolgálók** majd **Windows-ügynök letöltése** alkalmazandó a számítógép processzor típusa a telepítőfájl letöltéséhez.
3. Jobb oldalán **munkaterület azonosítója**, a Másolás ikonra, és illessze be az azonosítója a Jegyzettömbbe.
4. Jobb oldalán **elsődleges kulcs**, a Másolás ikonra, és illessze be a kulcsot a Jegyzettömbbe.     

## <a name="install-the-agent-using-setup"></a>Telepítse az ügynököt, a telepítőprogram használatával
1. A telepítőprogramot az ügynök telepítése a kezelni kívánt számítógépeken.
2. Az üdvözlőoldalon kattintson **következő**.
3. A licencfeltételek oldalon olvassa el a licencfeltételeket, és kattintson a **elfogadom**.
4. A célmappa lapon módosítani vagy hagyja az alapértelmezett telepítési mappáját, majd kattintson **következő**.
5. Az ügynök telepítésének beállításai lapon választhat csatlakoztassa az ügynököt az Azure Naplóelemzés (OMS), Operations Manager, vagy a választási lehetőségek üresen hagyhatja, ha az ügynök később konfigurálni szeretné. Kattintson a **Tovább** gombra.   
    - Ha úgy döntött, hogy csatlakozzon az Azure Naplóelemzés (OMS), illessze be a **munkaterület azonosítója** és **Munkaterületkulcsot (elsődleges kulcs)** másolja a Jegyzettömbbe az előző eljárásban, és kattintson **következő**.  
        ![illessze be a munkaterület azonosítója és az elsődleges kulcs](./media/log-analytics-windows-agents/connect-workspace.png)
    - Ha úgy döntött, hogy az Operations Manager csatlakozni, írja be a **felügyeleti csoport neve**, **felügyeleti kiszolgáló** nevét, és **felügyeleti kiszolgáló portszáma**, és kattintson a **következő**. Az ügynök Műveletfiókjához oldalon válassza ki azt a helyi rendszer fiók vagy a helyi tartományi fiókot, és kattintson a **következő**.  
        ![felügyeleti csoport konfigurációja](./media/log-analytics-windows-agents/oms-mma-om-setup01.png)![a műveleti fiók](./media/log-analytics-windows-agents/oms-mma-om-setup02.png)

6. A rendszer készen áll a telepítés oldal, ellenőrizze a megadott beállításokat, majd **telepítése**.
7. A konfiguráció sikeresen befejeződött lapon, és kattintson **Befejezés**.
8. Amikor végzett, a **Microsoft-Figyelőügynök** megjelenik **Vezérlőpult**. Tekintse át a hiba a konfiguráció, és ellenőrizze, hogy az ügynök csatlakozik-e az Operational Insights (OMS). Csatlakoztatva az OMS-be, amikor az ügynök jeleníti meg a következő üzenet: **a Microsoft Monitoring Agent sikeresen csatlakozott-e a Microsoft Operations Management Suite szolgáltatással.**

## <a name="configure-proxy-settings"></a>Proxybeállítások konfigurálása

Az alábbi eljárás segítségével konfigurálhatja a proxybeállításokat a Microsoft Monitoring Agent számára a Vezérlőpult használatával. Ezzel az eljárással kiszolgálónként kell. Ha sok kiszolgálót kell konfigurálnia, akkor érdemes lehet parancsfájl használatával automatizálni a folyamatot. Ha ez az eset áll fenn, tekintse meg a következő eljárást [A proxybeállítások konfigurálása a Microsoft Monitoring Agent számára parancsfájl használatával](#to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script) című szakaszban.

### <a name="to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-control-panel"></a>A proxybeállítások konfigurálása a Microsoft Monitoring Agent számára a Vezérlőpult használatával
1. Nyissa meg **Vezérlőpultot**.
2. Válassza a **Microsoft Monitoring Agent** lehetőséget.
3. Kattintson a **Proxy Settings** (Proxybeállítások) fülre.  
    ![proxybeállítások lap](./media/log-analytics-windows-agents/proxy-direct-agent-proxy.png)
4. Válassza a **Use a proxy server** (Use a proxy server) lehetőséget, majd adja meg az URL-címet és portszámot, amennyiben szükséges – hasonlóan az itt látható példához. Ha a proxykiszolgáló hitelesítést igényel, írja be a felhasználónevet és jelszót a proxykiszolgáló eléréséhez.


### <a name="verify-agent-connectivity-to-oms"></a>Ellenőrizze az ügynök kapcsolatairól az OMS-be

Könnyen ellenőrizheti, hogy az ügynököket az alábbi eljárást követve OMS kommunikáló:

1.  A Windows-ügynökkel rendelkező számítógépen nyissa meg a Vezérlőpultot.
2.  Nyissa meg a Microsoft Monitoring Agent szolgáltatásnál.
3.  Kattintson az Azure Naplóelemzés (OMS) fülre.
4.  Az Állapot oszlopban láthatja, hogy az ügynök sikeresen csatlakoztatva az Operations Management Suite szolgáltatással.

![ügynök csatlakoztatva](./media/log-analytics-windows-agents/mma-connected.png)


### <a name="to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script"></a>A proxybeállítások konfigurálása a Microsoft Monitoring Agent számára parancsfájl használatával
Másolja ki az alábbi mintát, frissítse az Ön környezetére jellemző adatokkal, mentse PS1 fájlnévkiterjesztéssel, és futtassa a parancsfájlt minden olyan számítógépen, amely közvetlenül csatlakozik az OMS szolgáltatáshoz.

    param($ProxyDomainName="http://proxy.contoso.com:80", $cred=(Get-Credential))

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



## <a name="install-the-agent-using-the-command-line"></a>Telepítse az ügynököt, a parancssor használatával
- Módosítsa, majd az alábbi példa használatával telepítse az ügynököt, a parancssor használatával. A példa a teljes beavatkozás nélküli telepítés hajt végre.

    >[!NOTE]
    Ha azt szeretné, az ügynök frissítése, hogy szeretné használni, a parancsprogram-kezelési API Naplóelemzési. Az ügynök frissítése a következő szakaszban talál.

    ```dos
    MMASetup-AMD64.exe /Q:A /R:N /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"
    ```

Az ügynök IExpress használja, mint a önkibontó használatával a `/c` parancsot. Megtekintheti a következő parancssori kapcsolók [parancssori kapcsolók a IExpress](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages) majd frissítse a példa az igényeinek.

|MMA vonatkozó beállítások                   |Megjegyzések         |
|---------------------------------------|--------------|
|ADD_OPINSIGHTS_WORKSPACE               | 1 = konfigurálása az ügynök számára, hogy a munkaterület                |
|OPINSIGHTS_WORKSPACE_ID                | Munkaterületének azonosítóját (guid) a munkaterülethez való hozzáadása                    |
|OPINSIGHTS_WORKSPACE_KEY               | Kezdetben a való hitelesítéshez szükséges a munkaterület kulcsát |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | Adja meg a felhőalapú környezetben, ahol a munkaterület <br> 0 = azure kereskedelmi cloud (alapértelmezett) <br> 1 = azure Government |
|OPINSIGHTS_PROXY_URL               | A proxy használatára URI |
|OPINSIGHTS_PROXY_USERNAME               | Egy hitelesített proxykiszolgálót eléréséhez felhasználónév |
|OPINSIGHTS_PROXY_PASSWORD               | Egy hitelesített proxykiszolgálót eléréséhez szükséges jelszót. |

>[!NOTE]
Szerezze meg a parancssori hosszkorlátot IExpress elkerüléséhez az ügynök telepítéséhez nincs konfigurálva munkaterület, és parancsfájl segítségével konfigurációjának megadása a munkaterületen.

>[!NOTE]
Ha egy `Command line option syntax error.` használata esetén a `OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE` paraméter, használhatja az alábbi megkerülő megoldást:
```dos
MMASetup-AMD64.exe /C /T:.\MMAExtract
cd .\MMAExtract
setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1
```

## <a name="add-a-workspace-using-a-script"></a>Egy parancsfájl segítségével munkaterület hozzáadása
A Naplóelemzési ügynök parancsfájlkezelési API használata a következő példa egy munkaterület hozzáadása:

```PowerShell
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

A munkaterület az Azure-ban az Amerikai Egyesült államokbeli kormányzati, használja az alábbi parancsfájl-mintában:
```PowerShell
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
Ha már használta a parancssorból vagy parancsfájlból korábban az ügynök telepítéséhez és konfigurálásához a `EnableAzureOperationalInsights` le lett cserélve `AddCloudWorkspace`.

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Telepítse az ügynököt az Azure Automation DSC használata

Az alábbi mintaparancsfájl segítségével telepítse az ügynököt az Azure Automation DSC használata. A példa telepíti a 64 bites ügynök azonosítja a `URI` érték. A 32 bites azáltal, hogy az URI azonosítóját is használhatja. Az URI-azonosítók mindkét-verziók a következők:

- A Windows 64 bites ügynök – https://go.microsoft.com/fwlink/?LinkId=828603
- A Windows 32 bites ügynök – https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
A művelet és a parancsfájl például nem frissíti a meglévő ügynököt.

1. Modul importálása a xPSDesiredStateConfiguration DSC [http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) az Azure Automation.  
2.  Azure Automation változó eszközök létrehozása *OPSINSIGHTS_WS_ID* és *OPSINSIGHTS_WS_KEY*. Állítsa be *OPSINSIGHTS_WS_ID* a OMS Naplóelemzési munkaterület azonosítója és a set *OPSINSIGHTS_WS_KEY* az elsődleges kulcsot a munkaterületet.
3.  A következő parancsfájlt, és mentse MMAgent.ps1
4.  Módosítsa, majd az alábbi példa használatával telepítse az ügynököt az Azure Automation DSC használata. Azure Automation MMAgent.ps1 importálja az Azure Automation-felület vagy a parancsmag használatával.
5.  Rendelje hozzá egy csomópontot a konfigurációt. 15 percen belül a csomópont ellenőrzi a konfigurációját, és az MMA a rendszer előkészítésre továbbít a csomópont.

```PowerShell
Configuration MMAgent
{
    $OIPackageLocalPath = "C:\MMASetup-AMD64.exe"
    $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
    $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"


    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    Node OMSnode {
        Service OIService
        {
            Name = "HealthService"
            State = "Running"
            DependsOn = "[Package]OI"
        }

        xRemoteFile OIPackage {
            Uri = "https://go.microsoft.com/fwlink/?LinkId=828603"
            DestinationPath = $OIPackageLocalPath
        }

        Package OI {
            Ensure = "Present"
            Path  = $OIPackageLocalPath
            Name = "Microsoft Monitoring Agent"
            ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
            Arguments = '/C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
            DependsOn = "[xRemoteFile]OIPackage"
        }
    }
}


```

### <a name="get-the-latest-productid-value"></a>A legújabb ProductId érték

A `ProductId value` a MMAgent.ps1 a parancsfájl minden ügynök verzió egyedi. Minden ügynök frissített verziójának közzétételekor ProductId értéke megváltozik. Így a ProductId a jövőben változik, a használatával egy egyszerű parancsprogram verziója található. Miután egy tesztkiszolgálón telepített ügynök legfrissebb, a következő parancsfájl segítségével telepített ProductId értékének beolvasása. Legújabb ProductId értékének használatával frissítheti a MMAgent.ps1 értékét.

```PowerShell
$InstalledApplications  = Get-ChildItem hklm:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall


foreach ($Application in $InstalledApplications)

{

     $Key = Get-ItemProperty $Application.PSPath

     if ($Key.DisplayName -eq "Microsoft Monitoring Agent")

     {

        $Key.DisplayName

        Write-Output ("Product ID is: " + $Key.PSChildName.Substring(1,$Key.PSChildName.Length -2))

     }

}  
```

## <a name="configure-an-agent-manually-or-add-additional-workspaces"></a>Az ügynök manuális konfigurálásához, vagy adja hozzá a további munkaterületek
Ha telepített ügynökök, de nem állította be, vagy ha azt szeretné, hogy több munkaterületekhez való, az alábbi információk segítségével engedélyezze az ügynököt, vagy konfigurálja azt újra. Az ügynök konfigurálását követően regisztrálja az ügynököt szolgáltatással, és megkapja a szükséges konfigurációs adatokat és a felügyeleti csomagok, amelyekben a megoldással kapcsolatos információkat.

1. A Microsoft Monitoring Agent telepítése után nyissa meg a **Vezérlőpult**.
2. Nyissa meg **Microsoft-Figyelőügynök** , majd a **Azure Naplóelemzés (OMS)** fülre.   
3. Kattintson a **Hozzáadás** megnyitásához a **hozzáadni a Naplóelemzési munkaterület** mezőbe.
4. Beillesztés a **munkaterület azonosítója** és **Munkaterületkulcsot (elsődleges kulcs)** , amely a Jegyzettömbbe az előző eljárás a munkaterület hozzáadása, és kattintson a kívánt másoltuk **OK**.  
    ![Operational insights szolgáltatás konfigurálása](./media/log-analytics-windows-agents/add-workspace.png)

Adatgyűjtés az ügynök által felügyelt számítógépek, OMS által felügyelt számítógépek száma megjelennek az OMS-portálon a a **csatlakoztatott források** lapján **beállítások** , **kiszolgálók**.


## <a name="to-disable-an-agent"></a>Az ügynök letiltásához
1. Az ügynök telepítése után nyissa meg a **Vezérlőpult**.
2. Nyissa meg a Microsoft Monitoring Agent, és kattintson a **Azure Naplóelemzés (OMS)** fülre.
3. Válasszon egy munkaterületet, és kattintson a **eltávolítása**. Ismételje meg ezt a lépést minden egyéb munkaterületekkel.


## <a name="optionally-configure-agents-to-report-to-an-operations-manager-management-group"></a>Nem kötelező lépésként beállíthatja az ügynökök számára, hogy az Operations Manager felügyeleti csoport

Az Operations Manager az informatikai infrastruktúrát használ, ha az Operations Manager ügynök is használhatja az MMA ügynök.

### <a name="to-configure-mma-agents-to-report-to-an-operations-manager-management-group"></a>MMA ügynökök számára, hogy az Operations Manager felügyeleti csoport konfigurálása
1.  Nyissa meg a számítógép, amelyen az ügynök telepítve van, **Vezérlőpult**.  
2.  Nyissa meg **Microsoft-Figyelőügynök** , majd a **Operations Manager** fülre.  
    ![A Microsoft figyelési ügynök Operations Manager lap](./media/log-analytics-windows-agents/om-mg01.png)
3.  Ha az Operations Manager-kiszolgáló Active Directory integrációja a, kattintson a **az AD DS-ből felügyeleti csoport-hozzárendelések automatikus frissítése**.
4.  Kattintson a **Hozzáadás** megnyitásához a **felügyeleti csoport hozzáadása** párbeszédpanel megnyitásához.  
    ![A Microsoft Monitoring Agent egy felügyeleti csoport hozzáadása](./media/log-analytics-windows-agents/oms-mma-om02.png)
5.  A **felügyeleti csoport neve** mezőbe írja be a felügyeleti csoport neve.
6.  Az a **elsődleges felügyeleti kiszolgáló** mezőbe írja be az elsődleges felügyeleti kiszolgáló számítógépneve.
7.  Az a **felügyeleti kiszolgáló portszáma** mezőbe írja be a TCP-portszámot.
8.  A **Ügynökműveleti fiók**, válassza a helyi rendszerfiókot vagy egy helyi tartományi fiók.
9.  Kattintson a **OK** bezárásához a **felügyeleti csoport hozzáadása** párbeszédpanel megnyitásához, majd kattintson **OK** bezárásához a **Microsoft Monitoring Agent tulajdonságai** párbeszédpanel megnyitásához.


## <a name="next-steps"></a>Következő lépések

- A funkciók hozzáadásával és az adatgyűjtéssel kapcsolatban lásd: [Add Log Analytics solutions from the Solutions Gallery](log-analytics-add-solutions.md) (Log Analytics-megoldások hozzáadása a megoldástárból).
