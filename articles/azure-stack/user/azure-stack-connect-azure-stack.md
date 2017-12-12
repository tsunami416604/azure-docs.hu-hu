---
title: "Csatlakozás Azure verem |} Microsoft Docs"
description: "Kapcsolódás Azure verem"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/22/2017
ms.author: mabrigg
ms.openlocfilehash: 7479202a8afabf5a84560691a2bccf849206c077
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="connect-to-azure-stack"></a>Csatlakozás az Azure Stackhez

Erőforrások kezeléséhez, csatlakoznia kell az Azure verem szoftverfejlesztői készlet. Ez a cikk részleteket a csomag való kapcsolódáshoz szükséges lépéseket. A következő kapcsolati lehetőségek bármelyikét használhatja:

* [A távoli asztal](#connect-with-remote-desktop): lehetővé teszi, hogy gyorsan csatlakozni vagy a csomag több egyidejű felhasználó.
* [Virtuális magánhálózati (VPN)](#connect-with-vpn): lehetővé teszi több egyidejű felhasználók kapcsolódó ügyfelek kívül az Azure-verem infrastruktúra (konfigurációt igényel).

## <a name="connect-to-azure-stack-with-remote-desktop"></a>Csatlakozás Azure verem és a távoli asztal
Egy távoli asztali kapcsolattal egyidejűleg több felhasználó dolgozhat a portálra, ahol kezelheti az erőforrásokat.

1. A távoli asztali kapcsolat létrehozása és a csomag kapcsolódjon. Adja meg **AzureStack\AzureStackAdmin** felhasználónévként, és a rendszergazdai jelszó Azure verem a telepítés során megadottal.  

2. A fejlesztői csomag a számítógépről, nyissa meg a Kiszolgálókezelőt, kattintson a **helyi kiszolgáló**, kapcsolja ki az Internet Explorer fokozott biztonsági, és zárja be a Kiszolgálókezelő.

3. Nyissa meg a portált, keresse meg (https://portal.local.azurestack.external/), és jelentkezzen be a felhasználó hitelesítő adataival.


## <a name="connect-to-azure-stack-with-vpn"></a>Csatlakozás Azure verem és VPN

A felosztott alagút egy Azure verem szoftverfejlesztői készlet virtuális magánhálózati (VPN) kapcsolatot hozhat létre. A VPN-kapcsolaton keresztül hozzáférhet a felhasználói portálra, a felügyeleti portálon, és helyileg telepíteni az eszközöket, például a Visual Studio és a PowerShell verem Azure-erőforrások kezeléséhez. VPN-kapcsolat mindkét Azure Active Directory(AAD) esetén támogatott, és az Active Directory összevonási Services(AD FS)-alapú telepítések. VPN-kapcsolatok engedélyezése csatlakozni Azure verem egyszerre több ügyfélnek. 

> [!NOTE] 
> A VPN-kapcsolat nem biztosítja a kapcsolatot a virtuális gépek Azure-verem infrastruktúra. 

### <a name="prerequisites"></a>Előfeltételek

* Telepítés [Azure verem kompatibilis Azure PowerShell](azure-stack-powershell-install.md) a helyi számítógépen.  
* Töltse le a [az Azure veremnek megfelelő működéséhez szükséges eszközök](azure-stack-powershell-download.md). 

### <a name="configure-vpn-connectivity"></a>VPN-kapcsolat konfigurálása

A csomag a VPN-kapcsolat létrehozásához nyissa meg egy rendszergazda jogú PowerShell-munkamenetet a helyi Windows-alapú számítógépről, és futtassa a következő parancsfájlt (Ügyeljen rá, hogy a környezet IP cím és jelszó értéket):

```PowerShell 
# Configure winrm if it's not already configured
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module
Import-Module .\Connect\AzureStack.Connect.psm1 

# Add the development kit computer’s host IP address & certificate authority (CA) to the list of trusted hosts. Make sure to update the the IP address and password values for your environment. 

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<Administrator password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

Ha a telepítés sikeres, megjelenik **azurestack** a VPN-kapcsolatok listájában.

![Hálózati kapcsolatok](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack"></a>Csatlakozás az Azure Stackhez

Csatlakozás az Azure veremben példányra, az alábbi két módszer egyikével:  

* Használatával a `Connect-AzsVpn ` parancs: 
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  Amikor a rendszer kéri, az Azure-verem gazdagép megbízható, és telepítse a tanúsítványt a **AzureStackCertificateAuthority** kiszolgálóra a helyi számítógép tanúsítványtárolójába. (a kérdés jelenhet meg a PowerShell-munkamenet ablak mögé). 

* Nyissa meg a helyi számítógép **hálózati beállítások** > **VPN** > kattintson **azurestack** > **csatlakozás**. A bejelentkezési parancssorból írja be a felhasználónevet (AzureStack\AzureStackAdmin) és a jelszót.

### <a name="test-the-vpn-connectivity"></a>A VPN-kapcsolat tesztelése

A portál kapcsolat ellenőrzéséhez nyisson meg egy böngészőprogramot, keresse meg a felhasználói portál (https://portal.local.azurestack.external/), jelentkezzen be, és hozzon létre erőforrásokat.  

## <a name="next-steps"></a>Következő lépések



