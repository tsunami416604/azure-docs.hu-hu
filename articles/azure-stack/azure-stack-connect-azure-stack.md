---
title: Csatlakozás az Azure Stack |} A Microsoft Docs
description: Ismerje meg, hogyan csatlakozhat az Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/28/2018
ms.author: mabrigg
ms.openlocfilehash: 1cdf013325afe4b217f5f56043e06f60a4933419
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49993169"
---
# <a name="connect-to-azure-stack-development-kit"></a>Csatlakozhat az Azure Stack fejlesztői készlete

*A következőkre vonatkozik: Azure Stack fejlesztői készlete*

Erőforrások kezelése, először csatlakoznia kell az Azure Stack Development Kit. Ez a cikk a szükséges lépéseket is kapcsolódni a fejlesztői készlet ismertetünk. A következő kapcsolati lehetőségek egyikét használhatja:

* [A távoli asztali kapcsolat](#connect-with-remote-desktop). Ha távoli asztali kapcsolattal csatlakozik, egyetlen felhasználó gyorsan csatlakozhatnak a development Kitet.
* [Virtuális magánhálózati (VPN)](#connect-with-vpn). Ha egy VPN-en keresztül csatlakozik, több felhasználó egyidejűleg csatlakozhatnak az Azure Stack-infrastruktúra kívüli ügyfelek számára. Egy VPN-kapcsolat néhány beállítás szükséges.

<a name="connect-to-azure-stack-with-remote-desktop"></a>
##  <a name="connect-to-azure-stack-by-using-remote-desktop-connection"></a>Csatlakozás az Azure Stack a távoli asztali kapcsolat használatával

Több párhuzamos felhasználó operátor portálon vagy a távoli asztali kapcsolaton keresztül a felhasználói portál-erőforrások is kezelhetők.

1. Távoli asztali kapcsolat megnyitásához, és csatlakozzon a development Kitet. Adja meg a felhasználónevet, **AzureStack\AzureStackAdmin**. Az Azure Stack beállításakor megadott operátor jelszót használja.  

2. A fejlesztői csomag számítógépen nyissa meg a Kiszolgálókezelőt. Válassza ki **helyi kiszolgáló**, törölje a **Internet Explorer fokozott biztonsági** jelölőnégyzetet, majd zárja be a Kiszolgálókezelőt.

3. Megnyitásához a [felhasználói portál](azure-stack-key-features.md#portal), lépjen a https://portal.local.azurestack.external/. Jelentkezzen be a felhasználói hitelesítő adatok használatával. Az Azure Stack megnyitásához [operátor portál](azure-stack-key-features.md#portal), lépjen a https://adminportal.local.azurestack.external/. Jelentkezzen be a Azure Active Directory (Azure AD) a telepítés során megadott hitelesítő adatok használatával.

<a name="connect-to-azure-stack-with-vpn"></a>
## <a name="connect-to-azure-stack-by-using-vpn"></a>Csatlakozás az Azure Stackhez VPN segítségével

Egy VPN-kapcsolat az Azure Stack Development Kit osztott alagút is létrehozhat. Használhatja a VPN-kapcsolatot az Azure Stack operátori portálon, a felhasználói portál és a helyileg telepített Azure Stack-erőforrások kezelése a Visual Studio és a PowerShell és hasonló eszközökkel való eléréséhez. Az Azure ad-ben támogatott VPN-kapcsolat és az Active Directory összevonási szolgáltatások (AD FS) központi telepítések. VPN-kapcsolatok csatlakozni az Azure Stack egyszerre több ügyfél számára lehetővé teszik.

> [!NOTE]
> Egy VPN-kapcsolat nem biztosít az Azure Stack-infrastruktúra virtuális gépek csatlakozni.

### <a name="prerequisites"></a>Előfeltételek

1. Telepítés [Azure Stack-kompatibilis Azure PowerShell-lel](azure-stack-powershell-install.md) a helyi számítógépen.  
2. Töltse le a [az Azure Stack működéséhez szükséges eszközök](azure-stack-powershell-download.md).

### <a name="set-up-vpn-connectivity"></a>VPN-kapcsolat beállítása

Hozzon létre egy VPN-kapcsolat a csomag, a helyi Windows-alapú számítógépre rendszergazdaként nyissa meg a Windows PowerShell. Ezután futtassa a következő szkriptet (frissítés a környezet értékei az IP-cím és jelszó):

```PowerShell
# Configure Windows Remote Management (WinRM), if it's not already configured.
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module.
Import-Module .\Connect\AzureStack.Connect.psm1

# Add the development kit computer’s host IP address and certificate authority (CA) to the list of trusted hosts. Make sure you update the IP address and password values for your environment.

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

![Hálózati kapcsolatok](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack"></a>Csatlakozás az Azure Stackhez

Kapcsolódás az Azure Stack-példány a következő módszerek egyikével:  

* Használja a `Connect-AzsVpn ` parancsot:
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  Amikor a rendszer kéri, az Azure Stack gazdagép megbízható, és telepítse a tanúsítványt a **AzureStackCertificateAuthority** a helyi számítógép tanúsítványtárolójában. (A rendszer kéri ablakméreteitől által a PowerShell-ablakban.)

* Válassza ki a helyi számítógépen **hálózati beállítások** > **VPN** > **azurestack** > **csatlakoztatása**. A bejelentkezési parancssorba írja be a felhasználónevet (**AzureStack\AzureStackAdmin**) és a jelszót.

### <a name="test-vpn-connectivity"></a>VPN-kapcsolat tesztelése

A portál kapcsolat teszteléséhez, nyisson meg egy webböngészőt, és keresse meg a felhasználói portálra (https://portal.local.azurestack.external/) vagy a kezelő portálon (https://adminportal.local.azurestack.external/). Jelentkezzen be, és erőforrások létrehozásához.  

## <a name="next-steps"></a>További lépések

[Virtuális gépek az Azure Stack-felhasználók számára elérhetővé](azure-stack-tutorial-tenant-vm.md)
