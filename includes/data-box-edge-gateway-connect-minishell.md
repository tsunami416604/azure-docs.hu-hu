---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/06/2019
ms.author: alkohli
ms.openlocfilehash: 348f7bdd333da4f4a6cb41a438b7aee08d6a6bbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179581"
---
Az ügyfél operációs rendszerétől függően az eszközhöz való távoli csatlakozás eljárásai eltérőek.

### <a name="remotely-connect-from-a-windows-client"></a>Csatlakozás távoli csatlakozás Windows-ügyfélről

Mielőtt elkezdené, győződjön meg arról, hogy a Windows-ügyfél windows PowerShell 5.0-s vagy újabb operációs rendszert futtat.

Az alábbi lépésekkel távolról csatlakozhat Windows-ügyfélről.

1. Futtasson egy Windows PowerShell-munkamenetet rendszergazdaként.
2. Győződjön meg arról, hogy a Windows távfelügyeleti szolgáltatás a számítógépen fut. A parancssorba írja be a következőt:

    `winrm quickconfig`

3. Rendeljen változót az eszköz IP-címéhez.

    $ip = "> <device_ip"

    Cserélje `<device_ip>` le a készülék IP-címét.

4. Ha hozzá szeretné adni az eszköz IP-címét az ügyfél megbízható állomások listájához, írja be a következő parancsot:

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. Windows PowerShell-munkamenet indítása az eszközön:

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell`

6. Adja meg a jelszót, amikor a rendszer kéri. Használja ugyanazt a jelszót, amely et a helyi webes felhasználói felületre való bejelentkezéshez használja. Az alapértelmezett helyi webes felhasználói felület jelszava a *Jelszó1*. Ha sikeresen csatlakozik az eszközhöz a távoli PowerShell használatával, a következő mintakimenet jelenik meg:  

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\WINDOWS\system32> winrm quickconfig
    WinRM service is already running on this machine.
    PS C:\WINDOWS\system32> $ip = "10.100.10.10"
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell

    WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing. Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
    [10.100.10.10]: PS>
    ```

### <a name="remotely-connect-from-a-linux-client"></a>Csatlakozás linuxos ügyfélről

A csatlakozni kívánt Linux-ügyfélen:

- [Telepítse a legújabb PowerShell Core for Linux-ot a](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-linux?view=powershell-6) GitHubról az SSH átmotolási funkciójának leküzdése érdekében. 
- [Csak a `gss-ntlmssp` csomagot telepítse az NTLM modulból](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md). Ubuntu kliensek esetén használja a következő parancsot:
    - `sudo apt-get install gss-ntlmssp`

További információért látogasson el a [PowerShell sSH-n keresztüli átirányítást című e-kérdéséhez.](https://docs.microsoft.com/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core?view=powershell-6)

Az alábbi lépésekkel távolról csatlakozhat NFS-ügyfélről.

1. A PowerShell-munkamenet megnyitásához írja be a következőt:

    `sudo pwsh`
 
2. A távoli ügyféllel való csatlakozáshoz írja be a következőt:

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    Amikor a rendszer kéri, adja meg az eszközre való bejelentkezéshez használt jelszót.
 
> [!NOTE]
> Ez az eljárás mac os rendszeren nem működik.
