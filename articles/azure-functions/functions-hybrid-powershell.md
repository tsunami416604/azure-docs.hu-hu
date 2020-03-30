---
title: Távoli helyszíni erőforrások kezelése PowerShell-függvények használatával
description: Megtudhatja, hogyan konfigurálhatja a hibrid kapcsolatok az Azure Relay-ben egy PowerShell-függvényalkalmazás helyszíni erőforrásokhoz való csatlakoztatásához, amelyek ezután a helyszíni erőforrás távoli kezelésére használhatók.
author: eamono
ms.topic: conceptual
ms.date: 9/5/2019
ms.author: eamono
ms.openlocfilehash: 36fc4c873dccfe9fa814bddccd829ed04207f095
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74226937"
---
# <a name="managing-hybrid-environments-with-powershell-in-azure-functions-and-app-service-hybrid-connections"></a>Hibrid környezetek kezelése a PowerShell használatával az Azure Functionsben és az App Service hybrid kapcsolatokban

Az Azure App Service Hybrid Connections szolgáltatás lehetővé teszi a hozzáférést a más hálózatokban lévő erőforrásokhoz. Erről a funkcióról a Hibrid kapcsolatok dokumentációjában olvashat [bővebben.](../app-service/app-service-hybrid-connections.md) Ez a cikk azt ismerteti, hogy miként használhatja ezt a funkciót a helyszíni kiszolgálót célzó PowerShell-függvények futtatásához. Ez a kiszolgáló ezután a helyszíni környezetben lévő összes erőforrás kezelésére használható egy Azure PowerShell-függvényből.


## <a name="configure-an-on-premises-server-for-powershell-remoting"></a>Helyszíni kiszolgáló konfigurálása a PowerShell-távszolgáltatás-alapú szolgáltatásokhoz

A következő parancsfájl lehetővé teszi a PowerShell-távszolgáltatást, és létrehoz egy új tűzfalszabályt és egy WinRM https-figyelőt. Tesztelési célokra önaláírt tanúsítványt használ. Éles környezetben azt javasoljuk, hogy aláírt tanúsítványt használjon.

```powershell
# For configuration of WinRM, see
# https://docs.microsoft.com/windows/win32/winrm/installation-and-configuration-for-windows-remote-management.

# Enable PowerShell remoting.
Enable-PSRemoting -Force

# Create firewall rule for WinRM. The default HTTPS port is 5986.
New-NetFirewallRule -Name "WinRM HTTPS" `
                    -DisplayName "WinRM HTTPS" `
                    -Enabled True `
                    -Profile "Any" `
                    -Action "Allow" `
                    -Direction "Inbound" `
                    -LocalPort 5986 `
                    -Protocol "TCP"

# Create new self-signed-certificate to be used by WinRM.
$Thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME  -CertStoreLocation Cert:\LocalMachine\My).Thumbprint

# Create WinRM HTTPS listener.
$Cmd = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:COMPUTERNAME ""; CertificateThumbprint=""$Thumbprint""}"
cmd.exe /C $Cmd
```

## <a name="create-a-powershell-function-app-in-the-portal"></a>PowerShell-függvényalkalmazás létrehozása a portálon

Az App Service hibrid kapcsolatok szolgáltatás csak alapszintű, standard és elkülönített díjszabási csomagokban érhető el. Amikor létrehozza a függvényalkalmazást a PowerShell segítségével, hozzon létre vagy válasszon ki egy ilyen csomagok.

1. Az [Azure Portalon](https://portal.azure.com)válassza a **+ Erőforrás létrehozása lehetőséget** a bal oldali menüben, majd válassza a Függvény alkalmazás **lehetőséget.**

1. Az **Üzemeltetési csomag**esetén válassza az App **Service-csomag**lehetőséget, majd az App **Service-csomag/hely lehetőséget.**

1. Válassza **az Új létrehozása**lehetőséget, írja be az App **Service-csomag** nevét, válasszon egy **helyet** egy önhöz közeli [régióban](https://azure.microsoft.com/regions/) vagy más szolgáltatások közelében, amelyekhez a funkciók hozzáférhetnek, majd válassza **a Tarifacsomag**lehetőséget.

1. Válassza az S1 Standard csomagot, majd válassza **az Alkalmaz**lehetőséget.

1. Válassza az **OK gombot** a terv létrehozásához, majd konfigurálja a **többi függvényalkalmazás-beállításokat** a táblázatban megadott a következő képernyőkép után:

    ![PowerShell Core függvényalkalmazás](./media/functions-hybrid-powershell/create-function-powershell-app.png)  

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Alkalmazás neve** | Globálisan egyedi név | Az új függvényalkalmazást azonosító név. Érvényes karakterek: `a-z`, `0-9` és `-`.  | 
    | **Előfizetés** | Az Ön előfizetése | Az előfizetés, amelyben létrehozta az új függvényalkalmazást. |
    | **Erőforráscsoport** |  myResourceGroup | Az új erőforráscsoport neve, amelyben létrehozza a függvényalkalmazást. A javasolt értéket is használhatja. |
    | **OS** | Előnyben részesített operációs rendszer | Válassza a Windowst. |
    | **Futtatókörnyezet verme** | Elsődleges nyelv | Válassza a PowerShell Core lehetőséget. |
    | **Tárterület** |  Globálisan egyedi név |  Hozzon létre egy tárfiókot a függvényalkalmazás számára. A tárfióknevek nek 3 és 24 karakter között kell lenniük, és csak számokat és kisbetűket tartalmazhatnak. Meglévő fiókot is használhat.
    | **Application Insights** | Alapértelmezett | Létrehoz egy Application Insights-erőforrást ugyanazzal az *alkalmazásnévvel* a legközelebbi támogatott régióban. A beállítás kibontásával módosíthatja az **új erőforrás nevét,** vagy választhat egy másik **helyet** egy [Azure-földrajzi](https://azure.microsoft.com/global-infrastructure/geographies/) régióban, ahol tárolni szeretné az adatokat. |

1. A beállítások ellenőrzése után válassza a **Létrehozás gombot.**

1. Válassza az **Értesítés** ikont a portál jobb felső sarkában, és várja meg a "Sikeres telepítés" üzenetet.

1. Az új függvényalkalmazás megtekintéséhez válassza az **Erőforrás megnyitása** lehetőséget. Kiválaszthatja **a Rögzítés az irányítópulton**lehetőséget is. A rögzítés megkönnyíti a függvényalkalmazás-erőforráshoz való visszatérést az irányítópultról.

## <a name="create-a-hybrid-connection-for-the-function-app"></a>Hibrid kapcsolat létrehozása a függvényalkalmazáshoz

A hibrid kapcsolatok a függvényalkalmazás hálózati szakaszából vannak konfigurálva:

1. Válassza a **Platform funkciók** lapját a függvényalkalmazásban, majd a **Hálózat lehetőséget.** 
   ![Alkalmazás – áttekintés a platformhálózatokhoz](./media/functions-hybrid-powershell/app-overview-platform-networking.png)  
1. Válassza **a Hibrid kapcsolatok végpontjainak konfigurálása lehetőséget.**
   ![Hálózat](./media/functions-hybrid-powershell/select-network-feature.png)  
1. Válassza **a Hibrid kapcsolat hozzáadása**lehetőséget.
   ![Hibrid kapcsolat](./media/functions-hybrid-powershell/hybrid-connection-overview.png)  
1. Adja meg a hibrid kapcsolat adatait a következő képernyőkép után. Lehetősége van arra, hogy az **Endpoint Host** beállítás megfeleljen a helyszíni kiszolgáló állomásnevének, hogy később könnyebben megjegyezhető legyen a kiszolgáló, amikor távoli parancsokat futtat. A port megegyezik a kiszolgálón korábban definiált alapértelmezett Windows távfelügyeleti szolgáltatásporttal.
  ![Hibrid kapcsolat hozzáadása](./media/functions-hybrid-powershell/add-hybrid-connection.png)  

    **Hibrid kapcsolat neve**: ContosoHybridOnPremisesServer
    
    **Végpontgazda**: finance1
    
    **Végpont port:** 5986
    
    **Servicebus névtér**: Új létrehozása
    
    **Hely**: Válasszon egy elérhető helyet
    
    **Név**: contosopowershellhybrid

5. A hibrid kapcsolat létrehozásához válassza az **OK gombot.**

## <a name="download-and-install-the-hybrid-connection"></a>A hibrid kapcsolat letöltése és telepítése

1. A **Csatlakozáskezelő letöltése** lehetőséget választva helyileg szeretné menteni az .msi fájlt a számítógépre.
![Telepítő letöltése](./media/functions-hybrid-powershell/download-hybrid-connection-installer.png)  
1. Másolja az .msi fájlt a helyi számítógépről a helyszíni kiszolgálóra.
1. Futtassa a hibrid csatlakozáskezelő telepítőjét a szolgáltatás helyszíni kiszolgálóra való telepítéséhez.
![Hibrid kapcsolat telepítése](./media/functions-hybrid-powershell/hybrid-installation.png)  
1. A portálról nyissa meg a hibrid kapcsolatot, majd másolja az átjáró kapcsolati karakterláncát a vágólapra.
![Hibrid kapcsolati karakterlánc másolása](./media/functions-hybrid-powershell/copy-hybrid-connection.png)  
1. Nyissa meg a hibrid csatlakozáskezelő felhasználói felületét a helyszíni kiszolgálón.
![Hibrid kapcsolat felhasználói felülete](./media/functions-hybrid-powershell/hybrid-connection-ui.png)  
1. Jelölje ki a **Kézi bevitel** gombot, és illessze be a kapcsolati karakterláncot a vágólapról.
![Kapcsolat beillesztése](./media/functions-hybrid-powershell/enter-manual-connection.png)  
1. Indítsa újra a hibrid kapcsolatkezelőt a PowerShellből, ha nem jelenik meg csatlakoztatottként.
    ```powershell
    Restart-Service HybridConnectionManager
    ```

## <a name="create-an-app-setting-for-the-password-of-an-administrator-account"></a>Alkalmazásbeállítás létrehozása rendszergazdai fiók jelszavához

1. Válassza a **Platform funkciók** lapját a függvényalkalmazásban.
1. Az **Általános beállítások csoportban**válassza a **Konfiguráció**lehetőséget.
![Platform konfigurációjának kiválasztása](./media/functions-hybrid-powershell/select-configuration.png)  
1. **Az Új alkalmazásbeállítás kibontásával** új beállítást hozhat létre a jelszóhoz.
1. Nevezze el a _ContosoUserPassword_beállítást, és írja be a jelszót.
1. Válassza **az OK gombot,** majd mentse a jelszót a függvényalkalmazásban.
![Alkalmazásbeállítás hozzáadása a jelszóhoz](./media/functions-hybrid-powershell/add-appsetting-password.png)  

## <a name="create-a-function-http-trigger-to-test"></a>Függvény létrehozása http trigger teszteléshez

1. Hozzon létre egy új HTTP-eseményindító függvényt a függvényalkalmazásból.
![Új HTTP-eseményindító létrehozása](./media/functions-hybrid-powershell/create-http-trigger-function.png)  
1. Cserélje le a PowerShell-kódot a sablonból a következő kódra:

    ```powershell
    # Input bindings are passed in via param block.
    param($Request, $TriggerMetadata)
    
    # Write to the Azure Functions log stream.
    Write-Output "PowerShell HTTP trigger function processed a request."
    
    # Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword.
    $UserName = "ContosoUser"
    $securedPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
    $Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)
    
    # This is the name of the hybrid connection Endpoint.
    $HybridEndpoint = "finance1"
    
    $Script = {
        Param(
            [Parameter(Mandatory=$True)]
            [String] $Service
        )
        Get-Service $Service
    }
    
    Write-Output "Scenario 1: Running command via Invoke-Command"
    Invoke-Command -ComputerName $HybridEndpoint `
                   -Credential $Credential `
                   -Port 5986 `
                   -UseSSL `
                   -ScriptBlock $Script `
                   -ArgumentList "*" `
                   -SessionOption (New-PSSessionOption -SkipCACheck)
    ```

3. A funkció teszteléséhez válassza a **Mentés** és **futtatás** lehetőséget.
![A függvényalkalmazás tesztelése](./media/functions-hybrid-powershell/test-function-hybrid.png)  

## <a name="managing-other-systems-on-premises"></a>Egyéb rendszerek helyszíni kezelése

A csatlakoztatott helyszíni kiszolgáló segítségével csatlakozhat más kiszolgálókhoz és felügyeleti rendszerekhez a helyi környezetben. Ez lehetővé teszi az adatközpont-műveletek kezelését az Azure-ból a PowerShell-függvények használatával. A következő parancsfájl regisztrál egy PowerShell-konfigurációs munkamenetet, amely a megadott hitelesítő adatok alatt fut. Ezeknek a hitelesítő adatoknak a távoli kiszolgálók rendszergazdájának kell lenniük. Ezután ezzel a konfigurációval a helyi kiszolgáló vagy adatközpont más végpontjaihoz is hozzáférhet.

```powershell
# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword.
$UserName = "ContosoUser"
$SecuredPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
$Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)

# This is the name of the hybrid connection Endpoint.
$HybridEndpoint = "finance1"

# The remote server that will be connected to run remote PowerShell commands on
$RemoteServer = "finance2".

Write-Output "Use hybrid connection server as a jump box to connect to a remote machine"

# We are registering an endpoint that runs under credentials ($Credential) that has access to the remote server.
$SessionName = "HybridSession"
$ScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $SessionName)

    if (-not (Get-PSSessionConfiguration -Name $SessionName -ErrorAction SilentlyContinue))
    {
        Register-PSSessionConfiguration -Name $SessionName -RunAsCredential $Using:Credential
    }
}

Write-Output "Registering session on hybrid connection jumpbox"
Invoke-Command -ComputerName $HybridEndpoint `
               -Credential $Credential `
               -Port 5986 `
               -UseSSL `
               -ScriptBlock $ScriptCommand `
               -ArgumentList $SessionName `
               -SessionOption (New-PSSessionOption -SkipCACheck)

# Script to run on the jump box to run against the second machine.
$RemoteScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $ComputerName)
        # Write out the hostname of the hybrid connection server.
        hostname
        # Write out the hostname of the remote server.
        Invoke-Command -ComputerName $ComputerName -Credential $Using:Credential -ScriptBlock {hostname} `
                        -UseSSL -Port 5986 -SessionOption (New-PSSessionOption -SkipCACheck)
}

Write-Output "Running command against remote machine via jumpbox by connecting to the PowerShell configuration session"
Invoke-Command -ComputerName $HybridEndpoint `
               -Credential $Credential `
               -Port 5986 `
               -UseSSL `
               -ScriptBlock $RemoteScriptCommand `
               -ArgumentList $RemoteServer `
               -SessionOption (New-PSSessionOption -SkipCACheck) `
               -ConfigurationName $SessionName
```

Cserélje le a parancsfájl ban szereplő alábbi változókat a környezetből származó megfelelő értékekre:
* $HybridEndpoint
* $RemoteServer

Az előző két forgatókönyvben csatlakozhat és kezelheti a helyszíni környezeteket a PowerShell használatával az Azure Functions és a hibrid kapcsolatok használatával. Javasoljuk, hogy tudjon meg többet a [hibrid kapcsolatokról](../app-service/app-service-hybrid-connections.md) és [a PowerShell funkcióiról.](./functions-reference-powershell.md)

Az Azure [virtuális hálózataival](./functions-create-vnet.md) is csatlakozhat a helyszíni környezethez az Azure Functions segítségével.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [További információ a PowerShell-függvények használatával kapcsolatban](functions-reference-powershell.md)
