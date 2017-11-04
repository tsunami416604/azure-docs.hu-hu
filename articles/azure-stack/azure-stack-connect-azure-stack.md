---
title: "Csatlakozás Azure verem |} Microsoft Docs"
description: "Útmutató: Azure verem való kapcsolódáshoz."
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/25/2017
ms.author: sngun
ms.openlocfilehash: ba2359739b1d9cd265879227a499c94f93d8e4c6
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2017
---
# <a name="connect-to-azure-stack"></a>Csatlakozás az Azure Stackhez

*A következőkre vonatkozik: Azure szoftverfejlesztői készletet*

Az erőforrások kezeléséhez először csatlakoznia kell az Azure verem szoftverfejlesztői készlet. Ebben a cikkben azt ismertetik a lépéseket, amelyek a szoftverfejlesztői készlet való kapcsolódáshoz. A következő kapcsolati lehetőségek egyikét használhatja:

* [Távoli asztali kapcsolat](#connect-with-remote-desktop). Távoli asztali kapcsolaton keresztül kapcsolódik, amikor egy felhasználó gyorsan lehet csatlakoztatni a csomagban található.
* [Virtuális magánhálózat (VPN)](#connect-with-vpn). VPN-en keresztül kapcsolódik, amikor több felhasználó egyidejűleg lehet csatlakoztatni az Azure-verem infrastruktúra (telepítő igényel) kívüli ügyfelek számára.

<a name="connect-to-azure-stack-with-remote-desktop"></a>
##  <a name="connect-to-azure-stack-by-using-remote-desktop-connection"></a>Csatlakozás Azure verem távoli asztali kapcsolat használatával
Több egyidejű felhasználó kezelhetik az erőforrásokat üzemeltető portálon vagy a felhasználói portál távoli asztali kapcsolaton keresztül.

1. Távoli asztali kapcsolat megnyitásához, és kapcsolódjon a csomagban található. Adja meg a felhasználónevet, **AzureStack\AzureStackAdmin**. A megadott Azure verem beállításakor operátor jelszó használata.  

2. A development kit számítógépen nyissa meg a Kiszolgálókezelőt. Válassza ki **helyi kiszolgáló**, törölje a jelet a **Internet Explorer fokozott biztonsági** jelölőnégyzetet, majd zárja be a Kiszolgálókezelőt.

3. Lehetőségre a [felhasználói portál](azure-stack-key-features.md#portal)https://portal.local.azurestack.external/ lépjen. Jelentkezzen be a felhasználói hitelesítő adatok használatával. Az Azure-verem megnyitásához [operátor portal](azure-stack-key-features.md#portal)https://adminportal.local.azurestack.external/ lépjen. Jelentkezzen be a Azure Active Directory (Azure AD), amely a telepítés során megadott hitelesítő adatok használatával.

<a name="connect-to-azure-stack-with-vpn"></a>
## <a name="connect-to-azure-stack-by-using-vpn"></a>Csatlakozás Azure verem VPN használatával

Létrehozhat egy Azure verem szoftverfejlesztői készlet VPN-kapcsolat vegyes alagutat. VPN-kapcsolat segítségével hozzáférést az Azure-verem operátor, a felhasználói portálra, és helyileg telepített eszközök, például a Visual Studio és a PowerShell verem Azure-erőforrások kezeléséhez. VPN-kapcsolatot az Azure ad-ben, és az Active Directory összevonási szolgáltatások (AD FS) környezetekben támogatott. VPN-kapcsolatok csatlakozni Azure verem egyszerre több ügyfél számára lehetővé teszik. 

> [!NOTE] 
> VPN-kapcsolat nem csatlakozhasson Azure verem infrastruktúra virtuális gépeket. 

### <a name="prerequisites"></a>Előfeltételek

1. Telepítés [Azure verem-kompatibilis Azure PowerShell](azure-stack-powershell-install.md) a helyi számítógépen.  
2. Töltse le a [az Azure veremnek megfelelő működéséhez szükséges eszközök](azure-stack-powershell-download.md). 

### <a name="set-up-vpn-connectivity"></a>VPN-kapcsolat beállítása

A csomag a VPN-kapcsolat létrehozásához nyissa meg a Windows PowerShell a helyi Windows-alapú számítógépre rendszergazdaként. Ezután futtassa a következő parancsfájl (frissítés a környezet értékei az IP-cím és jelszó):

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

Ha a telepítés sikeres, **azurestack** megjelenik a listában a VPN-kapcsolatok.

![Hálózati kapcsolatok](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack"></a>Csatlakozás az Azure Stackhez

Csatlakozás az Azure veremben példányra, az alábbi módszerek egyikének használatával:  

* Használja a `Connect-AzsVpn ` parancs: 
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  Amikor a rendszer kéri, az Azure-verem gazdagép megbízható, és telepítse a tanúsítványt a **AzureStackCertificateAuthority** a helyi számítógép tanúsítványtárolójába. (A parancssorba előfordulhat, hogy szerint rejtve a PowerShell-ablakot.) 

* Jelölje ki a helyi számítógépen **hálózati beállítások** > **VPN** > **azurestack** > **Csatlakozás**. A bejelentkezési parancssorába írja be a felhasználónevet (**AzureStack\AzureStackAdmin**) és a jelszót.

### <a name="test-vpn-connectivity"></a>VPN-kapcsolat tesztelése

A portál kapcsolat ellenőrzéséhez nyisson meg egy webböngészőt, és keresse meg a felhasználói portál (https://portal.local.azurestack.external/) vagy a operátor portal (https://adminportal.local.azurestack.external/). Jelentkezzen be, és hozzon létre erőforrásokat.  

## <a name="next-steps"></a>Következő lépések

[Virtuális gépek Azure verem felhasználók számára történő elérhetővé](azure-stack-tutorial-tenant-vm.md)

