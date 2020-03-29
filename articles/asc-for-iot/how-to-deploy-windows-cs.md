---
title: Az Azure Security Center for IoT ügynök Windows-telepítése | Microsoft dokumentumok
description: Ismerje meg, hogyan telepítheti az Azure Security Center for IoT-ügynök 32 bites vagy 64 bites Windows-eszközökre.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: acc99f260931de7fd8c7566a3ff6daf43f34c5ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68597214"
---
# <a name="deploy-an-azure-security-center-for-iot-c-based-security-agent-for-windows"></a>C#-alapú Azure Security Center for IoT biztonsági ügynök üzembe helyezése Windowson

Ez az útmutató bemutatja, hogyan telepítheti az Azure Security Center for IoT C#-alapú biztonsági ügynök a Windows.

Ebből az útmutatóból a következőket tanulhatja meg: 
> [!div class="checklist"]
> * Telepítés
> * Az üzembe helyezés ellenőrzése
> * Ügynök eltávolítása
> * Hibaelhárítás 

## <a name="prerequisites"></a>Előfeltételek

Más platformok és ügynök ízek, [lásd: Válassza ki a megfelelő biztonsági ügynök](how-to-deploy-agent.md).

1. Helyi rendszergazdai jogok azon a gépen, amelyen telepíteni szeretné. 

1. [Hozzon létre egy biztonsági modult](quickstart-create-security-twin.md) az eszközhöz.

## <a name="installation"></a>Telepítés 

A biztonsági ügynök telepítéséhez használja a következő munkafolyamatot:

1. Telepítse az Azure Security Center for IoT Windows C# ügynök az eszközön. Töltse le a legújabb verziót a gépére az Azure Security Center for IoT GitHub repository.Download the recent version to your machine from the Azure Security Center for IoT [GitHub repository.](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

1. Bontsa ki a csomag tartalmát, és keresse meg a /Install mappát.

1. Nyissa meg a Windows PowerShellt rendszergazdaként. 
1. Adjon hozzá futó engedélyeket az InstallSecurityAgent parancsfájlhoz a következő futtatásával:<br>
    ```
    Unblock-File .\InstallSecurityAgent.ps1
    ```
    
    majd fuss:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```
    
    Példa:
    
    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```
    
    A hitelesítési paraméterekről a [Hitelesítés konfigurálása](concept-security-agent-authentication-methods.md)című témakörben talál további információt.

Ez a parancsfájl a következő műveleteket végzi:

- Telepíti az előfeltételeket.

- Hozzáad egy szolgáltatásfelhasználót (az interaktív bejelentkezés le van tiltva).

- Az ügynököt **rendszerszolgáltatásként**telepíti.

- Az ügynök konfigurálása a megadott hitelesítési paraméterekkel.


További segítségért használja a Get-Help parancsot a PowerShellben <br>Súgó példa:  
    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>A telepítés állapotának ellenőrzése

- Ellenőrizze az ügynök telepítési állapotát a következő futtatásával:<br>
    ```sc.exe query "ASC IoT Agent"```

### <a name="uninstall-the-agent"></a>Ügynök eltávolítása

Az ügynök eltávolítása:

1. Futtassa a következő PowerShell-parancsfájlt úgy, hogy a **-mode** paraméter meg van állítva **az Eltávolítás beállítással.**  

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ``` 

## <a name="troubleshooting"></a>Hibaelhárítás

Ha az ügynök nem indul el, kapcsolja be a naplózást (a naplózás alapértelmezés szerint ki van *kapcsolva)* további információk értéséhez.

A naplózás bekapcsolása:

1. Nyissa meg a konfigurációs fájlt (General.config) szerkesztésre szabványos fájlszerkesztővel.

1. A következő értékek szerkesztése:

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/> 
   <add key="diagnosticVerbosityLevel" value="Some" /> 
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > Javasoljuk, hogy a hibaelhárítás befejezése után kapcsolja **ki** a naplózást. A bejelentkezés elhagyása növeli **a** naplófájl méretét és az adathasználatot. 

1. Indítsa újra az ügynököt a következő PowerShell vagy parancssor futtatásával:

    **PowerShell**
     ```
     Restart-Service "ASC IoT Agent"
     ```
     
   vagy

    **Cmd**
     ```
     sc.exe stop "ASC IoT Agent" 
     sc.exe start "ASC IoT Agent" 
     ```

1. A hiba hiba további információiért tekintse át a naplófájlt.

   Naplófájl helye:`%WinDir%/System32/IoTAgentLog.log`


## <a name="next-steps"></a>További lépések
- Olvassa el az Azure Security Center for IoT szolgáltatás [áttekintését](overview.md)
- További információ az Azure Security Center for IoT [Architecture szolgáltatásról](architecture.md)
- A [szolgáltatás](quickstart-onboard-iot-hub.md) engedélyezése
- Olvassa el a [GYIK-et](resources-frequently-asked-questions.md)
- A [riasztások ismertetése](concept-security-alerts.md)