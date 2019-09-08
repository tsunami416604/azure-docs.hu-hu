---
title: Távoli helyszíni erőforrások kezelése a PowerShell-Azure Functions használatával
description: Megtudhatja, hogyan konfigurálhat Hibrid kapcsolatok a Azure Relayban egy PowerShell-függvény alkalmazás helyszíni erőforrásokhoz való összekapcsolásához, amelyet aztán a helyszíni erőforrás távoli kezelésére használhat.
author: eamono
manager: gailey777
ms.service: azure-functions
ms.topic: conceptual
ms.date: 9/5/2019
ms.author: eamono
ms.openlocfilehash: 0acbe0892be50e9e1747e5839101110d4adcebcf
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70775415"
---
# <a name="managing-hybrid-environments-with-powershell-in-azure-functions-and-app-service-hybrid-connections"></a>Hibrid környezetek kezelése a PowerShell-lel Azure Functions és App Service Hibrid kapcsolatok

A Azure App Service Hibrid kapcsolatok lehetővé teszi a más hálózatokban lévő erőforrásokhoz való hozzáférést. Erről a képességről a [hibrid kapcsolatok](../app-service/app-service-hybrid-connections.md) dokumentációjában olvashat bővebben. Az alábbi információk azt ismertetik, hogyan használhatja ezt a képességet a helyszíni kiszolgálóval megcélzott PowerShell-függvények futtatásához. Ezzel a kiszolgálóval a helyszíni környezetben lévő összes erőforrást egy Azure PowerShell függvényből lehet kezelni.


## <a name="configure-an-on-premises-server-for-powershell-remoting"></a>Helyszíni kiszolgáló konfigurálása a PowerShell táveléréshez

Az alábbi szkript lehetővé teszi a PowerShell távelérését, létrehoz egy új tűzfalszabály-szabályt és egy WinRM HTTPS-figyelőt. Tesztelési célból a rendszer önaláírt tanúsítványt használ. Az éles üzemhez aláírt tanúsítványt ajánlott használni.

```powershell
# For configuration of WinRM, please see
# https://docs.microsoft.com/windows/win32/winrm/installation-and-configuration-for-windows-remote-management

# Enable PowerShell remoting
Enable-PSRemoting -Force

# Create firewall rule for WinRM. The default HTTPS port is 5986 
New-NetFirewallRule -Name "WinRM HTTPS" `
                    -DisplayName "WinRM HTTPS" `
                    -Enabled True `
                    -Profile "Any" `
                    -Action "Allow" `
                    -Direction "Inbound" `
                    -LocalPort 5986 `
                    -Protocol "TCP"

# Create new self-signed-certificate to be used by WinRM
$Thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME  -CertStoreLocation Cert:\LocalMachine\My).Thumbprint

# Create WinRM HTTPS listener
$Cmd = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:COMPUTERNAME ""; CertificateThumbprint=""$Thumbprint""}"
cmd.exe /C $Cmd
```

## <a name="create-a-powershell-function-app-in-the-portal"></a>PowerShell-függvény alkalmazás létrehozása a portálon

A App Service Hibrid kapcsolatok csak az alapszintű, a standard és az elkülönített díjszabási csomagokban érhetők el. Ha a Function alkalmazást a PowerShell-lel hozza létre, hozzon létre vagy válasszon ki egyet az alábbi csomagok közül.

1. Nyissa meg az [Azure Portal](https://portal.azure.com).

1. Válassza az **+ erőforrás létrehozása** lehetőséget a bal oldali oldalon, majd válassza a **Function app**elemet.

1. A **üzemeltetési csomag**területen válassza a **app Service terv**, majd a **app Service terv/hely**lehetőséget.

1. Válassza az **új létrehozása**elemet, írja be a **app Servicei csomag** nevét, válasszon egy **helyet** az Ön közelében lévő [régióban](https://azure.microsoft.com/regions/) vagy a funkciókhoz hozzáférő egyéb szolgáltatások közelében, majd válassza az **árképzési szintet**.

1. Válassza ki az S1 standard csomagot, majd kattintson az **alkalmaz**gombra.

1. A terv létrehozásához kattintson az **OK gombra** , majd használja a fennmaradó Function App-beállításokat a rendszerkép alatti táblázatban megadott módon. 

    ![PowerShell Core Function-alkalmazás](./media/functions-hybrid-powershell/create-function-powershell-app.png)  

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Alkalmazás neve** | Globálisan egyedi név | Az új függvényalkalmazást azonosító név. Érvényes karakterek: `a-z`, `0-9` és `-`.  | 
    | **Előfizetés** | Az Ön előfizetése | Az előfizetés, amelyben létrehozta az új függvényalkalmazást. |
    | **Erőforráscsoport** |  myResourceGroup | Az új erőforráscsoport neve, amelyben létrehozza a függvényalkalmazást. Használhatja a javasolt értéket is. |
    | **OS** | Előnyben részesített operációs rendszer | Válassza a Windows lehetőséget. |
    | **Futtatókörnyezet verme** | Elsődleges nyelv | Válassza a PowerShell mag lehetőséget. |
    | **Storage** |  Globálisan egyedi név |  Hozzon létre egy tárfiókot a függvényalkalmazás számára. A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat. Meglévő fiókot is használhat.
    | **Application Insights** | Alapértelmezett | Létrehoz egy Application Insights erőforrást ugyanahhoz az *alkalmazáshoz* a legközelebbi támogatott régióban. A beállítás kibontásával módosíthatja az **új erőforrás nevét** , vagy kiválaszthat egy másik **helyet** egy olyan [Azure-földrajzban](https://azure.microsoft.com/global-infrastructure/geographies/) , ahol az adatait tárolni szeretné. |

1. A Beállítások ellenőrzése után válassza a **Létrehozás**lehetőséget.

1. Válassza a portál jobb felső sarkában található Értesítések ikont, és várja meg az **üzembe helyezés sikerességét** jelző üzenetet.

1. Az új függvényalkalmazás megtekintéséhez válassza az **Erőforrás megnyitása** lehetőséget. Kiválaszthatja **a rögzítés az irányítópulton**lehetőséget is. A rögzítéssel egyszerűbbé válik a Function app-erőforráshoz való visszatérés az irányítópultról.

## <a name="create-a-hybrid-connection-for-the-function-app"></a>Hibrid kapcsolatok létrehozása a Function alkalmazáshoz

A hibrid kapcsolatok konfigurálása a Function alkalmazás hálózatkezelési szakasza alapján történik.

1. Válassza ki a Function alkalmazás platform lapját, és válassza a hálózatkezelés lehetőséget.
![Az alkalmazások áttekintése a platform hálózatkezeléséhez](./media/functions-hybrid-powershell/app-overview-platform-networking.png)  
1. Válassza a hibrid kapcsolatok végpontok konfigurálása lehetőséget.
![Hálózat](./media/functions-hybrid-powershell/select-network-feature.png)  
1. Válassza a hibrid kapcsolatok hozzáadása lehetőséget.
![Hibrid kapcsolatok](./media/functions-hybrid-powershell/hybrid-connection-overview.png)  
1. Adja meg a hibrid kapcsolatok adatait az alább látható módon. A végponti gazdagép opcionálisan egyeztetheti a helyszíni kiszolgáló állomásnevét, így a távoli parancsok futtatásakor később könnyebben megjegyezheti a kiszolgálót. A port a korábban a kiszolgálón definiált alapértelmezett Rendszerfelügyeleti webszolgáltatás-portnak felel meg.
![Hibrid kapcsolatok hozzáadása](./media/functions-hybrid-powershell/add-hybrid-connection.png)  

    **Hibrid kapcsolatok neve** ContosoHybridOnPremisesServer
    
    **Végponti gazdagép** finance1
    
    5986-es **végponti port**
    
    **Servicebus-névtér** Új létrehozása
    
    **Tartózkodási hely** Válasszon ki egy elérhető helyet
    
    **Név** contosopowershellhybrid

5. A hibrid kapcsolatok létrehozásához kattintson az OK gombra.

## <a name="download-and-install-the-hybrid-connection"></a>A hibrid kapcsolatok letöltése és telepítése

1. Kattintson a Csatlakozáskezelő letöltése ikonra az. msi fájl helyi mentéséhez a számítógépen.
![Telepítő letöltése](./media/functions-hybrid-powershell/download-hybrid-connection-installer.png)  
1. Másolja az. msi fájlt a helyi számítógépről a helyszíni kiszolgálóra.
1. A hibrid kapcsolódási telepítő futtatásával telepítse a szolgáltatást a helyszíni kiszolgálóra.
![Hibrid kapcsolatok telepítése](./media/functions-hybrid-powershell/hybrid-installation.png)  
1. A portálon nyissa meg a hibrid kapcsolatokat, és másolja az átjáró kapcsolódási karakterláncát a vágólapra.
![Hibrid kapcsolatok karakterláncának másolása](./media/functions-hybrid-powershell/copy-hybrid-connection.png)  
1. Nyissa meg a hibridkapcsolat-kezelő felhasználói felületet a helyszíni kiszolgálón.
![Hibrid kapcsolatok felhasználói felületének megnyitása](./media/functions-hybrid-powershell/hybrid-connection-ui.png)  
1. Jelölje be a "manuális bevitel" gombot, és illessze be a kapcsolatok karakterláncát a vágólapról.
![Összekötő beillesztése](./media/functions-hybrid-powershell/enter-manual-connection.png)  
1. Indítsa újra a hibrid Csatlakozáskezelőt a PowerShellből, ha nem jelenik meg a kapcsolat.
```powershell
Restart-Service HybridConnectionManager
```

## <a name="create-an-app-setting-for-the-password-of-an-administrator-account"></a>Alkalmazás-beállítás létrehozása rendszergazdai fiók jelszavához

1. Válassza ki a platform fület a Function alkalmazásból
1. Válassza ki a konfigurációt az általános beállítások ![szakaszban, válassza a platform konfiguráció elemet.](./media/functions-hybrid-powershell/select-configuration.png)  
1. Válassza az új Alkalmazásbeállítás lehetőséget új beállítás létrehozásához a jelszóhoz
1. Nevezze el a beállítás ContosoUserPassword, és adja meg a jelszót.
1. Válassza az OK, majd a mentés lehetőséget a jelszó tárolásához a ![függvény alkalmazás hozzáadása beállítás a jelszóhoz lehetőségre.](./media/functions-hybrid-powershell/add-appsetting-password.png)  

## <a name="create-a-function-http-trigger-to-test"></a>Function http-trigger létrehozása teszteléshez

1. Új http-trigger függvény létrehozása a függvény alkalmazás ![új http-trigger létrehozása](./media/functions-hybrid-powershell/create-http-trigger-function.png)  
1. Cserélje le a PowerShell-kódot a sablonból a következő kódra:

    ```powershell
    # Input bindings are passed in via param block.
    param($Request, $TriggerMetadata)
    
    # Write to the Azure Functions log stream.
    Write-Output "PowerShell HTTP trigger function processed a request."
    
    # Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword
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

3. Kattintson a Mentés és Futtatás gombra a Function ![test Function alkalmazás teszteléséhez](./media/functions-hybrid-powershell/test-function-hybrid.png)  

## <a name="managing-other-systems-on-premises"></a>Más rendszerek helyszíni kezelése

A csatlakoztatott helyszíni kiszolgáló használatával kapcsolódhat a helyi környezetben lévő más kiszolgálókhoz és felügyeleti rendszerekhez. Ez lehetővé teszi, hogy az Azure-beli adatközpont-műveleteket a PowerShell-függvények használatával kezelhesse. A következő parancsfájl egy PowerShell-konfigurációs munkamenetet regisztrál, amely a megadott hitelesítő adatok alatt fut. Ezeknek a hitelesítő adatoknak rendszergazdai jogosultságokkal kell rendelkezniük a távoli kiszolgálókon. Ezt a konfigurációt használhatja a helyi kiszolgáló vagy az adatközpont többi végpontjának eléréséhez.

```powershell
# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword
$UserName = "ContosoUser"
$SecuredPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
$Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)

# This is the name of the hybrid connection Endpoint.
$HybridEndpoint = "finance1"

# The remote server that will be conected to run remote PowerShell commands on
$RemoteServer = "finance2"

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

# Script to run on the jump box to run against the second machine
$RemoteScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $ComputerName)
        # Write out the hostname of the hybrid connection server
        hostname
        # Write out the hostname of the remote server
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

Cserélje le ezeket a változókat a fenti szkriptre a környezetében lévő értékekkel:
* $HybridEndpoint
* $RemoteServer

A fenti két forgatókönyv lehetővé teszi a helyszíni környezetek összekapcsolását és kezelését a PowerShell használatával Azure Functions és Hibrid kapcsolatokban. A dokumentációban szereplő függvények [hibrid kapcsolatok](../app-service/app-service-hybrid-connections.md) vagy [PowerShell-](./functions-reference-powershell.md) lel kapcsolatos további információkat is megtudhat.

Az Azure [Virtual Networks](./functions-create-vnet.md) használatával Azure functions használatával is csatlakozhat a helyszíni környezethez.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [További információ a PowerShell-függvények használatáról](functions-reference-powershell.md)
