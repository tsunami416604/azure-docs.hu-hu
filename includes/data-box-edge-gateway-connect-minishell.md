---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/06/2019
ms.author: alkohli
ms.openlocfilehash: 348f7bdd333da4f4a6cb41a438b7aee08d6a6bbb
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554982"
---
Függően az ügyfél operációs rendszere távoli csatlakozás az eszköz az eljárások különböznek.

### <a name="remotely-connect-from-a-windows-client"></a>Távoli csatlakozás egy Windows ügyfél

Mielőtt elkezdené, győződjön meg arról, hogy a Windows-ügyfél Windows PowerShell 5.0-s vagy újabb.

Kövesse az alábbi lépéseket egy Windows ügyfél távolról csatlakozni.

1. Futtassa egy Windows PowerShell-munkamenetet rendszergazdaként.
2. Győződjön meg arról, hogy a Windows távoli felügyeleti szolgáltatás fut-e az ügyfélen. A parancssorba írja be a következőt:

    `winrm quickconfig`

3. Rendelje hozzá egy változót az eszköz IP-címe.

    $ip = "< device_ip >"

    Cserélje le `<device_ip>` az eszköz IP-címmel.

4. Az ügyfél megbízható gazdagépek listájához ad hozzá az eszköz IP-címét, írja be a következő parancsot:

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. Indítsa el a Windows PowerShell-munkamenetben az eszközön:

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell`

6. Adja meg a jelszót, amikor a rendszer kéri. Használja ugyanazt a jelszót, amellyel jelentkezzen be a helyi webes felületén. Az alapértelmezett helyi webes felhasználói felület jelszó az *jelszó1*. Amikor sikeresen csatlakozik az eszköz távoli PowerShell-lel, tekintse meg az alábbi kimeneti példa:  

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

### <a name="remotely-connect-from-a-linux-client"></a>Távoli csatlakozás egy Linux-ügyfél

Linux rendszerű futtató ügyfélen való csatlakozáshoz használni:

- [Telepítse a legújabb PowerShell Core for Linux](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-linux?view=powershell-6) a Githubról, a távoli eljáráshívás SSH szolgáltatás beolvasása. 
- [Telepítés csak a `gss-ntlmssp` csomag az NTLM-modulból](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md). Ubuntu-ügyfelek esetén használja a következő parancsot:
    - `sudo apt-get install gss-ntlmssp`

További információért ugorjon [PowerShell távoli eljáráshívás ssh-n keresztül](https://docs.microsoft.com/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core?view=powershell-6).

Távoli csatlakozás az NFS-ügyfelet az alábbi lépéseket követve.

1. Nyissa meg a PowerShell-munkamenetet, írja be:

    `sudo pwsh`
 
2. Kapcsolódik a távoli ügyfél használatával, gépelje be:

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    Amikor a rendszer kéri, adja meg az eszköz-ba való bejelentkezéshez használt jelszó.
 
> [!NOTE]
> Ez az eljárás nem működik a Mac OS.
