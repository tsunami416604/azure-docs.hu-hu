---
title: Útmutató az Azure Security Center for IoT Linux C# ügynökének telepítéséhez és üzembe helyezéséhez| Microsoft dokumentumok
description: Ismerje meg, hogyan telepítheti az Azure Security Center for IoT-ügynök 32 bites és 64 bites Linuxra is.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: b0982203-c3c8-4a0b-8717-5b5ac4038d8c
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2019
ms.author: mlottner
ms.openlocfilehash: b675198756ff7bc0791d49fee3649717e3e4da7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75367416"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>C#-alapú Azure Security Center for IoT biztonsági ügynök üzembe helyezése Linuxon


Ez az útmutató bemutatja, hogyan telepítheti és telepítheti az Azure Security Center for IoT C#-alapú biztonsági ügynök Linuxon.

Ebből az útmutatóból a következőket tanulhatja meg: 
> [!div class="checklist"]
> * Telepítés
> * Az üzembe helyezés ellenőrzése
> * Ügynök eltávolítása
> * Hibaelhárítás 

## <a name="prerequisites"></a>Előfeltételek

Más platformok és ügynök ízek, [lásd: Válassza ki a megfelelő biztonsági ügynök](how-to-deploy-agent.md).

1. A biztonsági ügynök központi telepítéséhez helyi rendszergazdai jogok szükségesek azon a számítógépen, amelyen telepíteni szeretné. 

1. [Hozzon létre egy biztonsági modult](quickstart-create-security-twin.md) az eszközhöz.

## <a name="installation"></a>Telepítés 

A biztonsági ügynök központi telepítéséhez kövesse az alábbi lépéseket:

1. Töltse le a legújabb verziót a gépére a [GitHubról.](https://aka.ms/iot-security-github-cs)

1. Bontsa ki a csomag tartalmát, és keresse meg a _/Install_ mappát.

1. Futó engedélyek hozzáadása az **InstallSecurityAgent parancsfájlhoz** futással`chmod +x InstallSecurityAgent.sh` 

1. Ezután futtassa a következő parancsot **gyökérjogosultságokkal:** 

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```
   
   A hitelesítési paraméterekkel kapcsolatos további információkért lásd: [A hitelesítés konfigurálása](concept-security-agent-authentication-methods.md).

Ez a parancsfájl a következő műveleteket hajtja végre:

- Telepíti az előfeltételeket.

- Hozzáad egy szolgáltatásfelhasználót (az interaktív bejelentkezés le van tiltva).

- Az ügynököt **démonként telepíti** – feltételezi, hogy az eszköz a klasszikus központi telepítési modellhez **rendszerezve** használja.

- Úgy konfigurálja a **sudoers-t,** hogy az ügynök root-ként elvégezhet bizonyos feladatokat.

- Az ügynök konfigurálása a megadott hitelesítési paraméterekkel.


További segítségért futtassa a parancsfájlt a –help paraméterrel:`./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>Ügynök eltávolítása

Az ügynök eltávolításához futtassa a parancsfájlt `./InstallSecurityAgent.sh -u`a –u paraméterrel: . 

> [!NOTE]
> Az eltávolítás nem távolítja el a telepítés során telepített hiányzó előfeltételeket.

## <a name="troubleshooting"></a>Hibaelhárítás  

1. Ellenőrizze a központi telepítés állapotát a következő futtatásával:

    `systemctl status ASCIoTAgent.service`

2. Engedélyezze a naplózást.  
   Ha az ügynök nem indul el, kapcsolja be a naplózást, hogy további információkat kapjon.

   A naplózás bekapcsolása:

   1. Nyissa meg a konfigurációs fájlt szerkesztésre bármely Linux-szerkesztőben:

        `vi /var/ASCIoTAgent/General.config`

   1. A következő értékek szerkesztése: 

      ```
      <add key="logLevel" value="Debug"/>
      <add key="fileLogLevel" value="Debug"/>
      <add key="diagnosticVerbosityLevel" value="Some" /> 
      <add key="logFilePath" value="IotAgentLog.log"/>
      ```
       A **logFilePath** értéke konfigurálható. 

       > [!NOTE]
       > Javasoljuk, hogy a hibaelhárítás befejezése után kapcsolja **ki** a naplózást. A bejelentkezés elhagyása növeli **a** naplófájl méretét és az adathasználatot.

   1. Indítsa újra az ügynököt a következő futtatásával:

       `systemctl restart ASCIoTAgent.service`

   1. A hiba további információiért tekintse meg a naplófájlt.  

       A naplófájl helye:`/var/ASCIoTAgent/IotAgentLog.log`

       Módosítsa a fájl elérési útját a **logFilePath** 2. 

## <a name="next-steps"></a>További lépések

- Olvassa el az Azure Security Center for IoT szolgáltatás [áttekintését](overview.md)
- További információ az Azure Security Center for IoT [Architecture szolgáltatásról](architecture.md)
- A [szolgáltatás](quickstart-onboard-iot-hub.md) engedélyezése
- Olvassa el a [GYIK-et](resources-frequently-asked-questions.md)
- A [riasztások ismertetése](concept-security-alerts.md)
