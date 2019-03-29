---
title: ASC előzetes IoT-ügynök telepítése Windows |} A Microsoft Docs
description: Ismerje meg az ASC telepítéséről a IoT-ügynök 32 bites vagy 64 bites Windows-eszközökön.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2019
ms.author: mlottner
ms.openlocfilehash: 5c3293746fcc52570e708fd4bfab446981d49c24
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58621830"
---
# <a name="deploy-an-asc-for-iot-c-based-security-agent-for-windows"></a>Az IoT-ASC üzembe C#-security-ügynök a Windows-alapú

> [!IMPORTANT]
> Az IoT ASC jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez az útmutató azt ismerteti, hogyan telepítheti az ASC IoT C#-alapú biztonsági Windows-ügynököt.

Ebből az útmutatóból a következőket tanulhatja meg: 
> [!div class="checklist"]
> * Telepítés
> * Az üzembe helyezés ellenőrzése
> * Az ügynök eltávolítása
> * Hibaelhárítás 

## <a name="prerequisites"></a>Előfeltételek

Más platformok és az ügynök íz: [válassza ki a megfelelő biztonsági ügynök](how-to-deploy-agent.md).

1. Helyi rendszergazdai jogosultságok a telepíteni kívánt gépen. 

1. [Hozzon létre egy biztonsági modult](quickstart-create-security-twin.md) az eszközhöz.

## <a name="installation"></a>Telepítés 

A biztonsági ügynök telepítéséhez tegye a következőket:

1. Az ASC telepítheti a Windows IoT C# ügynök az eszközön, töltse le a legújabb verzióra a gépre az ASC-ről az IoT [GitHub-adattár](https://github.com/Azure/Azure-IoT-Security-Agent-CS).

2. Bontsa ki a csomag tartalmát, és keresse meg az Install-mappát.

3. Nyissa meg a Windows Powershellt rendszergazdaként. 
    1. A InstallSecurityAgent parancsfájl futtatásával futó engedélyek hozzáadása ```Unblock-File .\InstallSecurityAgent.ps1```
    
        és futtassa:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```
    
    Példa:
    
    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```
    
    Lásd: [hitelesítés konfigurálásának](concept-security-agent-authentication-methods.md) hitelesítési paraméterekkel kapcsolatos további részletekért.

A parancsfájl a következő műveleteket hajtja végre:

- Előfeltételek telepítése.

- A szolgáltatás felhasználó hozzáadása (az interaktív bejelentkezés le van tiltva).

- Telepíti az ügynököt, mint egy **rendszerszolgáltatás**.

- Konfigurálja az ügynököt, a megadott hitelesítési paraméterekkel.


További segítségre van szüksége használja a Get-Help parancsot a PowerShellben <br>Get-Help. példa:  
    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>Központi telepítési állapotának ellenőrzése

- Ellenőrizze az ügynök telepítési állapota futtatásával:<br>
    ```sc.exe query "ASC IoT Agent" ```

### <a name="uninstall-the-agent"></a>Az ügynök eltávolítása

Az ügynök eltávolítása:

1. Az alábbi PowerShell-parancsprogram futtatásához az **-mód** paraméter beállítása **Eltávolítás**.  

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ``` 

## <a name="troubleshooting"></a>Hibaelhárítás

Ha az ügynök nem indul el, kapcsolja be a naplózást (naplózás *ki* alapértelmezés szerint) további információért.

Naplózás bekapcsolása:

1. Nyissa meg a konfigurációs fájl (General.config) szerkesztésre, egy szabványos fájlszerkesztőjének használatával.

1. Szerkessze a következő értékeket:

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/> 
   <add key="diagnosticVerbosityLevel" value="Some" /> 
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > Javasoljuk, hogy naplózás bekapcsolása **ki** hibaelhárítási befejeződése után. Naplózás és **a** növeli a fájl méretét és az adatok használatának naplózása. 

1. Indítsa újra az ügynököt az alábbi PowerShell vagy parancssori futtatásával:

    **PowerShell**
     ```
     Restart-Service "ASC IoT Agent"
     ```
     
   vagy

    **CMD**
     ```
     sc.exe stop "ASC IoT Agent" 
     sc.exe start "ASC IoT Agent" 
     ```

1. Tekintse át a hibával kapcsolatos további információt a naplófájlban.

   Naplófájl helye: `%WinDir%/System32/IoTAgentLog.log`


## <a name="next-steps"></a>További lépések
- Olvassa el az IoT-szolgáltatást az ASC [áttekintése](overview.md)
- További információ az ASC a IoT [architektúra](architecture.md)
- Engedélyezze a [szolgáltatás](quickstart-onboard-iot-hub.md)
- Olvassa el a [– gyakori kérdések](resources-frequently-asked-questions.md)
- Megismerheti [riasztások](concept-security-alerts.md)