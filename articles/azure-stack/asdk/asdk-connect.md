---
title: Csatlakozás az Azure Stack |} A Microsoft Docs
description: Ismerje meg, hogyan csatlakozhat a ASDK.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2018
ms.author: jeffgilb
ms.openlocfilehash: 55be312046f5cdea2c1481ed435b5859ab2c2540
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50026897"
---
# <a name="connect-to-the-azure-stack-development-kit"></a>Csatlakozás az Azure Stack fejlesztői készlete

Erőforrások kezelése, először csatlakoznia kell a az Azure Stack Development Kit (ASDK). Ez a cikk a szükséges lépéseket is kapcsolódni a ASDK ismertetünk. A következő kapcsolati lehetőségek egyikét használhatja:

* [A távoli asztali kapcsolat](#connect-with-remote-desktop). Ha távoli asztali kapcsolattal csatlakozik, egyetlen felhasználó gyorsan csatlakozhatnak a development Kitet.
* [Virtuális magánhálózati (VPN)](#connect-with-vpn). Ha egy VPN-en keresztül csatlakozik, több felhasználó egyidejűleg csatlakozhatnak az Azure Stack-infrastruktúra kívüli ügyfelek számára. Egy VPN-kapcsolat néhány beállítás szükséges.

<a name="connect-to-azure-stack-with-remote-desktop"></a>
##  <a name="connect-to-azure-stack-by-using-remote-desktop-connection"></a>Csatlakozás az Azure Stack a távoli asztali kapcsolat használatával

Több párhuzamos felhasználó operátor portálon vagy a távoli asztali kapcsolaton keresztül a felhasználói portál-erőforrások is kezelhetők.

1. Nyissa meg a távoli asztali kapcsolat (mstc.exe) és a fejlesztői csomag gazdaszámítógépre csatlakozzon **AzureStack\AzureStackAdmin** ASDK telepítés során megadott jelszó segítségével.  

2. Az állomáson development kit nyissa meg a Kiszolgálókezelőt (ServerManager.exe). Válassza ki **helyi kiszolgáló**, kapcsolja ki a **Internet Explorer fokozott biztonsági beállításai**, és zárja be a Kiszolgálókezelőt.

3. Jelentkezzen be a felügyeleti portált **AzureStack\CloudAdmin** vagy más Azure Stack-operátorokról hitelesítő adatokat használja. A felügyeleti portál ASDK cím [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).

4. Jelentkezzen be a felhasználói portálra, **AzureStack\CloudAdmin** vagy más Azure Stack felhasználói hitelesítő adatokat használja. A felhasználói portál ASDK cím [ https://portal.local.azurestack.external ](https://portal.local.azurestack.external).

> [!NOTE]
> Melyik fiók használatával kapcsolatos további információkért lásd: [ASDK Adminisztráció alapjai](asdk-admin-basics.md#what-account-should-i-use).

<a name="connect-to-azure-stack-with-vpn"></a>
## <a name="connect-to-azure-stack-by-using-vpn"></a>Csatlakozás az Azure Stackhez VPN segítségével

Egy VPN-kapcsolat az Azure Stack-portálok és a helyileg telepített Visual Studio és a PowerShell és hasonló eszközökkel való eléréséhez egy ASDK osztott alagút is létrehozhat. VPN-kapcsolatok révén több felhasználó egyidejűleg a ASDK által üzemeltetett Azure Stack-erőforrások csatlakozhatnak.

VPN-kapcsolat használata támogatott, mind az Azure ad és az Active Directory összevonási szolgáltatások (AD FS) központi telepítések.

> [!NOTE]
> Egy VPN-kapcsolat nem biztosít az Azure Stack-infrastruktúra virtuális gépek csatlakozni.

### <a name="prerequisites"></a>Előfeltételek
Mielőtt beállítaná a VPN-kapcsolat a ASDK, győződjön meg arról, az alábbi előfeltételek teljesülnek.

- Telepítés [Azure Stack-kompatibilis Azure PowerShell-lel](asdk-post-deploy.md#install-azure-stack-powershell) a helyi számítógépen.  
- Töltse le a [az Azure Stack működéséhez szükséges eszközök](asdk-post-deploy.md#download-the-azure-stack-tools).

### <a name="set-up-vpn-connectivity"></a>VPN-kapcsolat beállítása

A ASDK VPN-kapcsolat létrehozásához nyissa meg a Powershellt rendszergazdaként azon a helyi Windows-alapú számítógép. Ezután futtassa a következő szkriptet (frissítés a környezet értékei az IP-cím és jelszó):

```PowerShell
# Change directories to the default Azure Stack tools directory
cd C:\AzureStack-Tools-master

# Configure Windows Remote Management (WinRM), if it's not already configured.
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module.
Import-Module .\Connect\AzureStack.Connect.psm1

# Add the development kit host computer’s IP address as the ASDK certificate authority (CA) to the list of trusted hosts. Make sure you update the IP address and password values for your environment.

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<operator's password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user.
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

Ha a telepítés sikeres, **azurestack** a VPN-kapcsolatok listájában jelenik meg.

![Hálózati kapcsolatok](media/asdk-connect/image3.png)  

### <a name="connect-to-azure-stack"></a>Csatlakozás az Azure Stackhez

Kapcsolódás az Azure Stack-példány a következő módszerek egyikével:  

* Használja a `Connect-AzsVpn ` parancsot:
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  Amikor a rendszer kéri, az Azure Stack gazdagép megbízható, és telepítse a tanúsítványt a **AzureStackCertificateAuthority** a helyi számítógép tanúsítványtárolójában. 
  
  > [!IMPORTANT]
  > A rendszer kéri a PowerShell-ablakban által ablakméreteitől függően.

* Válassza ki a helyi számítógépen **hálózati beállítások** > **VPN** > **azurestack** > **csatlakoztatása**. A bejelentkezési parancssorba írja be a felhasználónevet (**AzureStack\AzureStackAdmin**) és a jelszót.

### <a name="test-vpn-connectivity"></a>VPN-kapcsolat tesztelése

A portál kapcsolat teszteléséhez, nyisson meg egy webböngészőt, és keresse meg a felhasználói portálra (https://portal.local.azurestack.external/) vagy a felügyeleti portálon (https://adminportal.local.azurestack.external/). Jelentkezzen be, és erőforrások létrehozásához.  

## <a name="next-steps"></a>További lépések

[hibaelhárítással](asdk-troubleshooting.md)
