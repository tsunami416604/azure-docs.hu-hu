---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/06/2019
ms.author: alkohli
ms.openlocfilehash: 796b6280c2814e6358ce1942a230488cd484415d
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58405932"
---
Bármely eszköz hibaelhárítás érhet el a Microsoft Support csoporthoz kell. Jelentkezzen be az eszköz egy támogatási munkamenetet használatával Support szükségessé.

1. Windows PowerShell futtatása rendszergazdaként.
2. Rendelje hozzá egy változót az eszköz IP-címe. A parancssorba írja be a következőt:

    $ip = "< device_ip >"

    Cserélje le `<device_ip>` az eszköz IP-címmel.
 
3. Indítsa el a Windows PowerShell-munkamenetben az eszközön, és a futási térben minishell csatlakozni.

    ```
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Force
    $minishellSession= New-PSSession -ComputerName $ip -ConfigurationName "Minishell" -Credential ~\EdgeUser
    ```
    Amikor a rendszer kéri, adja meg az eszköz-ba való bejelentkezéshez használt jelszó.

4. A támogatási munkamenet futási térben csatlakozni.  

    ```
    Invoke-Command -Session $minishellSession -ScriptBlock { Enable-HcsSupportAccess }
    ```  
    Ez a parancs kimenete egy titkosított kulcsot. Másolja ezt a karakterláncot egy szövegszerkesztőbe, például a Jegyzettömbben.

5. Ez a kulcs küldése e-mailben a támogatási szakértőhöz. A támogatási szakértőhöz a támogatási munkamenetet hoz létre a megfelelő hozzáférési kulccsal.

6. A támogatási szakértőhöz fogja használni a hozzáférési kulcsot a következő parancsot a jelszót:

    ``` 
    $supportSession = New-PSSession -ComputerName $ip -Credential ~\EdgeSupport -ConfigurationName SupportSession
    Enter-PSSession -Session $supportSession
    ```
7. Ön mostantól támogatást munkamenetben. Az alábbi kimeneti példa mutatja be a támogatási munkamenethez való csatlakozáshoz:

    ```
    PS C:\WINDOWS\system32> $ip = "10.100.10.10";
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Force
    PS C:\WINDOWS\system32> $minishellSession= New-PSSession -ComputerName $ip -ConfigurationName "Minishell" -Credential ~\EdgeUser
    WARNING: The Windows PowerShell interface of your device is intended to
    be used only for the initial network configuration. Please
    engage Microsoft Support if you need to access this interface
    to troubleshoot any potential issues you may be experiencing.
    Changes made through this interface without involving Microsoft
    Support could result in an unsupported configuration.
    PS C:\WINDOWS\system32> Invoke-Command -Session $minishellSession -ScriptBlock { Enable-HcsSupportAccess }
    UAAAADcAMQAyAEYAQwBDAEEANwA0ADIARABCADUANgA1ADkANQA0AEIAQgA0ADgAMQBEADEAMQAxADMAMgAyADYAOAA3AEIANwA0ADgAMwBFAEMAiix6gA6zBIELa6vbb73CSO9/Yo/g85QRp2g5ngw773sKgBVcirk5sTHFuSQXWRirggGEip9NI5m54iPcVxdIEcoH+2vlvxCAJVWXOLGOB6WqWDtzR3XWSwJKig95LZfBjtPO1sM5TZLu65iCRCB4AV9nOezhAoy2lGdTuZOpXP2w5FIZPFvmgR4+4m+pfsD0NQSsw+PD3hNBHvUUyIhc4WTVaIJbzoJBzg06uJnc6C0Zo1YnYD6u8SoevejXbt3dgU7m36Vg3K0qPfCPA9WNLd71uiUsV1lMLwCabj60rAuRp/qJlPRXPU5PbGeayKNQJQfUkQYh6afUxtOXohke+A==
    PS C:\WINDOWS\system32> $supportSession = New-PSSession -ComputerName $ip -Credential ~\EdgeSupport -ConfigurationName SupportSession
    PS C:\WINDOWS\system32> Enter-PSSession -Session $supportSession
    [10.100.10.10]: PS C:\Users\EdgeSupport\Documents>
    ```
    Ha szeretné a támogatási munkamenet engedélyezésekor használt azonos titkosított hozzáférési kulcs lekérése, használja a `Get-HcsSupportAccessKey` parancsmagot.

    ```
    [10.128.24.33]: PS C:\Users\EdgeSupport\Documents> Get-HcsSupportAccessKey
    UAAAADcAMQAyAEYAQwBDAEEANwA0ADIARABCADUANgA1ADkANQA0AEIAQgA0ADgAMQBEADEAMQAxADMAMgAyADYAOAA3AEIANwA0ADgAMwBFAEMAiix6gA6zBIELa6vbb73CSO9/Yo/g85QRp2g5ngw773sKgBVcirk5sTHFuSQXWRirggGEip9NI5m54iPcVxdIEcoH+2vlvxCAJVWXOLGOB6WqWDtzR3XWSwJKig95LZfBjtPO1sM5TZLu65iCRCB4AV9nOezhAoy2lGdTuZOpXP2w5FIZPFvmgR4+4m+pfsD0NQSsw+PD3hNBHvUUyIhc4WTVaIJbzoJBzg06uJnc6C0Zo1YnYD6u8SoevejXbt3dgU7m36Vg3K0qPfCPA9WNLd71uiUsV1lMLwCabj60rAuRp/qJlPRXPU5PbGeayKNQJQfUkQYh6afUxtOXohke+A==
    [10.128.24.33]: PS C:\Users\EdgeSupport\Documents>
    ```

    A támogatási munkamenet 8 órán keresztül engedélyezett marad. A támogatási munkamenet bármikor letiltásához használja a `Disable-HcsSupportAccess` parancsmagot.

